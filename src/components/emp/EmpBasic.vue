<template>
  <!--发票订单-->
  <div>
    <el-container>
      <el-header style="padding: 0px;display:flex;justify-content:space-between;align-items: center">
        <div style="display: inline">
          <el-input
            placeholder="通过发票号码查询..."
            clearable
            style="width: 300px;margin: 0px;padding: 0px;"
            size="mini"
            prefix-icon="el-icon-search"
            v-model="keywords">
          </el-input>
          <el-button type="primary" size="mini" style="margin-left: 5px" icon="el-icon-search" @click="searchEmp(keywords)">搜索</el-button>
        </div>
      </el-header>
      <el-main style="padding-left: 0px;padding-top: 0px">
        <div>
          <template>
            <el-table
              ref="multipleTable"
              :data="items"
              tooltip-effect="dark"
              style="width: 100%"
              @selection-change="handleSelectionChange">
              <el-table-column type="selection" width="55"></el-table-column>
              <el-table-column prop="xhxUser.loginCount" label="登陆账号" width="180"></el-table-column>
              <el-table-column prop="xhxUser.username" label="用户名" width="120"></el-table-column>
              <el-table-column prop="orderTime" :formatter="applyTime"   label="申请时间" width="150" value-format="yyyy-MM-dd"></el-table-column>
              <el-table-column prop="invoiceno" label="发票号码" width="120"></el-table-column>
              <el-table-column prop="invoiceHeader" label="发票抬头"></el-table-column>
              <el-table-column prop="context" label="发票内容"></el-table-column>
              <el-table-column prop="orderAmount" label="发票金额" width="120"></el-table-column>
              <el-table-column prop="invoiceNature" :formatter="invoiceType" label="发票性质" width="120"></el-table-column>
              <el-table-column prop="deliveryType" :formatter="sendType" label="递送方式" width="120" ></el-table-column>
              <el-table-column label="操作" width="200px">
                <template slot-scope="scope">
                  <el-button size="mini" type="primary" @click="handleEdit(scope.row.id)">编辑</el-button>
                  <el-button size="mini" type="danger" @click="handleDelete(scope.row.id)">删除</el-button>
                </template>
              </el-table-column>
            </el-table>
          </template>
        </div>

        <el-dialog title="发票管理" :visible.sync="dialogFormVisible">
          <el-form :data="editForm" label-width="80px" class="formSub">
            <el-form-item label="发票号码">
              <el-input v-model="editForm.invoiceno"></el-input>
            </el-form-item>
            <el-form-item label="发票抬头">
              <el-input v-model="editForm.invoiceHeader"></el-input>
            </el-form-item>
            <el-form-item label="发票内容">
              <el-input v-model="editForm.context"></el-input>
            </el-form-item>
            <el-form-item label="发票性质">
              <el-input v-model="editForm.invoiceNature == 0 ? '纸质' : '电子'"></el-input>
            </el-form-item>
            <el-form-item label="递送方式">
              <el-input v-model="editForm.deliveryType == 0 ? '快递' : '下载'"></el-input>
            </el-form-item>
          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button type="primary" @click="submitForm">保存</el-button>
            <el-button @click="dialogFormVisible = false">取消</el-button>
          </div>
        </el-dialog>

        <div class="block" style="margin-top: 35px;">
          <el-pagination
            @current-change="handleCurrentChange"
            :current-page="currentPage"
            layout="total, prev, pager, next, jumper"
            :total=parseInt(totalCount)>
          </el-pagination>
        </div>

      </el-main>
    </el-container>
  </div>
</template>
<script>
  export default {
    data() {
       return {
               items: [],
               editForm: {
                 invoiceno: '',
                 invoiceHeader: '',
                 context: '',
                 invoiceNature: '',
                 deliveryType: '',
               },
               keywords: '',
               dialogFormVisible: false,
               currentPage: 1,
               totalCount: ''
             }
    },
    mounted: function () {
      this.getData();
    },
    methods: {
      getData: function(){
        this.getRequest("/invoiceOrder/findAllInvoiceOrder").then(data=> {
          this.totalCount = data.data.data.totalCount;     //总条数
          this.items = data.data.data.list;
        })
      },
      applyTime: function (value) {
        var date = new Date(value.orderTime);
        var year = date.getFullYear();
        var month = date.getMonth() + 1;
        var day = date.getDate();
        var hours = date.getHours();
        var minutes = date.getMinutes();
        if (month < 10) {
          month = "0" + month;
        }
        if (day < 10) {
          day = "0" + day;
        }
        return year + "-" + month + "-" + day + " " + hours + ":" + minutes;
      },
      sendType: function(row, column){
        return row.deliveryType == 0 ? "快递" : "下载";
      },
      invoiceType: function(row, column){
        return row.invoiceNature == 0 ? "纸质" : "电子";
      },
      searchEmp: function(keywords){
        this.getRequest("/invoiceOrder/findInvoiceOrderByNumber/"+keywords).then(data=>{
          console.log(data)
          this.items = [data.data.data]
        })
      },
      handleSelectionChange(val) {
        this.multipleSelection = val;
      },
      handleEdit: function(id){
        this.dialogFormVisible = !this.dialogFormVisible;
        var that = this;
        this.getRequest("/invoiceOrder/findInvoiceOrderById/"+id).then(data=>{
          that.editForm = data.data.data;
        })

      },
      handleDelete: function(id){
        this.getRequest("/invoiceOrder/deleteInvoiceOrder/"+id).then(data=>{
          console.log(data)
          alert("删除成功，请刷新页面！")
        })
      },
      submitForm: function(){
        var str = this.editForm
        this.putRequest("/invoiceOrder/updateInvoiceOrder?data="+JSON.stringify(str)).then(data => {
          console.log(data)
        })
        this.dialogFormVisible = !this.dialogFormVisible;
      },
      handleCurrentChange: function(val) {
        var _that = this;
        var param = {pageNo: val};
        this.getRequest("/invoiceOrder/findAllInvoiceOrder?data="+JSON.stringify(param)).then(data=>{
          _that.items = data.data.data.list;
        })
      }
    }
  }
</script>
<style>
  .el-dialog__body {
    padding-top: 0px;
    padding-bottom: 0px;
  }

  .slide-fade-enter-active {
    transition: all .8s ease;
  }

  .slide-fade-leave-active {
    transition: all .8s cubic-bezier(1.0, 0.5, 0.8, 1.0);
  }

  .slide-fade-enter, .slide-fade-leave-to {
    transform: translateX(10px);
    opacity: 0;
  }
  .el-table th{
    text-align: center;
  }
</style>
