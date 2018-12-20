<template>
  <!--发票资质-->
  <div>
    <el-container>
      <el-header style="padding: 0px;display:flex;justify-content:space-between;align-items: center">
        <div style="display: inline">
          <el-input
            placeholder="通过税务号查询..."
            clearable
            style="width: 300px;margin: 0px;padding: 0px;"
            size="mini"
            prefix-icon="el-icon-search"
            v-model="taxNumber">
          </el-input>
          <el-button type="primary" size="mini" style="margin-left: 5px" icon="el-icon-search" @click="searchEmp(taxNumber)">搜索</el-button>
        </div>
      </el-header>
      <el-main style="padding-left: 0px;padding-top: 0px">
        <div>
          <template>
            <el-table
              ref="multipleTable"
              :data="quaItems"
              tooltip-effect="dark"
              style="width: 100%"
              @selection-change="handleSelectionChange">
              <el-table-column type="selection" width="55"></el-table-column>
              <el-table-column prop="accountName" label="开户名称"></el-table-column>
              <el-table-column prop="taxNumber" label="纳税人识别号"></el-table-column>
              <el-table-column prop="context" label="发票内容"></el-table-column>
              <el-table-column prop="bank" label="开户银行"></el-table-column>
              <el-table-column prop="bankNumber" label="银行账号"></el-table-column>
              <el-table-column prop="retinfoAdd" label="注册地址"></el-table-column>
              <el-table-column prop="mailingAdd" label="邮寄地址"></el-table-column>
              <el-table-column prop="addressee" label="收件人"></el-table-column>
              <el-table-column prop="telephone" label="联系电话"></el-table-column>
              <el-table-column label="操作">
                <template slot-scope="scope">
                  <el-button size="mini" type="danger" @click="handleDelete(scope.row.id)">删除</el-button>
                </template>
              </el-table-column>
            </el-table>
          </template>
        </div>

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
        taxNumber: '',
        quaItems: [],
        currentPage: 1,
        totalCount: ''
      }
    },
    mounted: function () {
      var _this = this;
      this.getRequest("/invoiceAptitude/findAllInvoiceAptitude").then(data=>{
        console.log(data)
        this.totalCount = data.data.data.totalCount;
        this.quaItems = data.data.data.list;
      })
    },
    methods: {
      handleCurrentChange: function(val){
        var param = {pageNo: val};
        console.log(param)
        var _this = this;
        this.getRequest("/invoiceAptitude/findAllInvoiceAptitude?data="+JSON.stringify(param)).then(data=>{

          console.log(data)
          _this.quaItems = data.data.data.list;
        })
      },
      handleSelectionChange(val) {
        this.multipleSelection = val;
      },
      searchEmp: function (taxNumber) {
        var that = this;
        this.getRequest("/invoiceAptitude/findInvoiceAptitudeByNumber/"+taxNumber).then(data=>{
          console.log(data);
          that.quaItems = data.data.data;
        })
      },
      handleDelete: function (id) {
        this.getRequest("/invoiceAptitude/deleteInvoiceAptitude/"+id).then(data=>{
          console.log(data);
          alert("删除成功，请刷新页面！")
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
