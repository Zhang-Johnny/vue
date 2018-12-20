<template>
  <!--订单查询-->
  <div>
    <div id = "top">
      <div class="block">
          <span>订购时间:</span>
          <el-date-picker v-model="orderStime" type="daterange" unlink-panels range-separator="至" start-placeholder="开始日期" end-placeholder="结束日期" :picker-options="pickerOptions" />
      </div>
      <div class="dropdownBox1">
        <span>订单类型:</span>
        <el-select v-model="orderType" placeholder="请选择">
            <el-option v-for="item in options" :key="item.value" :label="item.label" :value="item.value" />
        </el-select>
      </div><br>
      <div class="inputBox">
        <span>订单名称:</span>
        <el-input clearable style="width: 350px;" v-model="orderName" placeholder="请输入内容"></el-input>
      </div>
      <div class="dropdownBox2">
        <span>发票状态:</span>
        <el-select v-model="invState" placeholder="请选择">
            <el-option v-for="item in options2" :key="item.value" :label="item.label" :value="item.value" />
        </el-select>
      </div>
      <el-button class="button1" type="primary" icon="el-icon-search" @click="searchEmp">搜索</el-button>
      <el-button type="primary">导出<i class="el-icon-upload el-icon--right"></i></el-button>
    </div>

    <div id="bottom">
      <el-table ref="multipleTable" :data="orderTable" tooltip-effect="dark" style="width: 100%" @selection-change="handleSelectionChange">
        <el-table-column type="selection" />
        <el-table-column prop="orderNo" label="订单编号" />
        <el-table-column prop="orderName" label="订单名称" />
        <el-table-column prop="orderType" :formatter="oType" label="订单类型" />
        <el-table-column prop="orderTime" :formatter="applyTime" label="订购时间" />
        <el-table-column prop="orderAmount" label="消费金额" />
        <el-table-column prop="invoiceStatus" :formatter="invType" label="发票" />
        <el-table-column label="操作" width="200px">
          <template slot-scope="scope">
            <el-button size="mini" type="primary" @click="handleEdit(scope.row.id)">编辑</el-button>
            <el-button size="mini" type="danger" @click="handleDelete(scope.row.id)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>

    <div>
      <el-dialog title="订单编辑" :visible.sync="dialogFormVisible">
        <el-form :model="orderForm" label-width="80px" class="formSub">
          <el-form-item label="订单编号">
            <el-input v-model="orderForm.orderNo"></el-input>
          </el-form-item>
          <el-form-item label="订单名称">
            <el-input v-model="orderForm.orderName"></el-input>
          </el-form-item>
          <el-form-item label="订单类型">
            <!--<el-input v-model="orderForm.orderType == 1 ? '会员订单' : orderForm.orderType == 2 ? '报告订单': '服务订单'"></el-input>-->
            <el-select v-model="orderForm.orderType == 1 ? '会员订单' : orderForm.orderType == 2 ? '报告订单' : '服务订单'" placeholder="请选择订单类型" class="ordertype">
              <el-option v-for="item in options" :key="item.value" :label="item.label" :value="item.value" />
            </el-select>
          </el-form-item>
          <el-form-item label="订购时间">
            <el-date-picker
              style="width:100%"
              v-model="orderForm.orderTime"
              type="date"
              placeholder="选择日期">
            </el-date-picker>
          </el-form-item>
          <el-form-item label="消费金额">
            <el-input v-model="orderForm.orderAmount"></el-input>
          </el-form-item>
          <el-form-item label="发票">
            <el-input v-model="orderForm.invoiceStatus == 0 ? '未开发票' : orderForm.invoiceStatus == 1 ? '已开发票/无余额' : '未开发票/有余额'"></el-input>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="dialogFormVisible = false">取 消</el-button>
          <el-button type="primary" @click="saveData">保 存</el-button>
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

    </div>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        currentPage: 1,
        totalCount: '',
        orderStime: '',
        orderType: '',
        options: [
          {value: '1',label: '会员订单'},
          {value: '2',label: '报告订单'},
          {value: '3',label: '服务订单'}
        ],
        orderName: '',
        invState: '',
        orderTable: [],
        multipleSelection: [],
        pickerOptions: {
          shortcuts: [{
            text: '最近一周',
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 7);
              picker.$emit('pick', [start, end]);
            }
          }, {
            text: '最近一个月',
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 30);
              picker.$emit('pick', [start, end]);
            }
          }, {
            text: '最近三个月',
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 90);
              picker.$emit('pick', [start, end]);
            }
          }]
        },
        options2: [
          {value: '0',label: '未开发票'},
          {value: '1',label: '已开发票/无余额'},
          {value: '2',label: '未开发票/有余额'}
        ],
        dialogFormVisible: false,
        dialogVisible: false,
        orderForm: {
          orderNo: '',
          orderName: '',
          orderType: '',
          orderTime: '',
          orderAmount: '',
          invoiceStatus: ''
        },
        formLabelWidth: '100px'
      }
    },
    mounted: function(){
      this.initData();

    },
    methods: {
      initData(){
        this.getRequest("/merchandise/findAllMerchandise").then(resp=> {
          if (resp && resp.status == 200) {
            console.log(resp)
            this.totalCount = resp.data.data.totalCount
            var data = resp.data.data.list;
            this.orderTable = data;
            /*data.forEach(item=>{
              this.options = [];
              this.options.push({
                value: item.orderType,
                label: item.orderType
              })
            })*/
          }
        })
      },
      handleCurrentChange: function(val){
        var param = {
          pageNo: val
        }
        console.log(param)
        var _this = this;
        this.getRequest("/merchandise/findAllMerchandise?data="+JSON.stringify(param)).then(data=>{
          console.log(data)
          _this.orderTable = data.data.data.list;
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
      toggleSelection(rows) {
        if (rows) {
          rows.forEach(row => {
            this.$refs.multipleTable.toggleRowSelection(row);
          });
        } else {
          this.$refs.multipleTable.clearSelection();
        }
      },
      handleSelectionChange(val) {
        this.multipleSelection = val;
      },
      oType: function(row, column) {
        return row.orderType == 1 ? "会员订单" : row.orderType == 2 ?"报告订单" : "服务订单";
      },
      invType: function(row){
        return row.invoiceStatus == 0 ? "未开发票" : row.invoiceStatus == 1 ?"已开发票/无余额" : "未开发票/有余额";
      },
      searchEmp: function(){
        var param=
          {orderName: this.orderName,
           orderType: this.orderType,
           invState: this.invState
          };
        this.getRequest("/merchandise/findMerchandiseByObject?data="+JSON.stringify(param)).then(data=>{
          this.orderTable = data.data.data;
        })
      },
      handleEdit: function (id) {
        this.dialogFormVisible = !this.dialogFormVisible;
        var _that = this;
        this.getRequest("merchandise/findMerchandiseById/"+id).then(data=>{
          _that.orderForm = data.data.data;
        })
      },
      handleDelete: function(id){
//        this.dialogVisible = !this.dialogVisible;
        this.getRequest("/merchandise/deleteMerchandise/"+id).then(data=>{
          console.log(data);
          alert("删除成功，请刷新页面！")
        })
      },
      saveData: function () {
        var str = this.orderForm;
        this.putRequest("/merchandise/updateMerchandise?data="+JSON.stringify(str)).then(data => {
          console.log(data)
          alert("保存成功")
        })
        this.dialogFormVisible = !this.dialogFormVisible;
      }
    }
  }
</script>

<style>
  .el-table th{
    text-align: center;
  }
  #top{
    text-align:left;
    margin-top:20px;
  }
  .block{
    display:inline-block;
  }
  .dropdownBox1{
    display:inline-block;
    margin-left:100px;
  }
  .dropdownBox2{
    display:inline-block;
    margin-left:100px;
  }
  .inputBox{
    display:inline-block;
    margin-top:20px;
  }
  .button1{
    margin-left:100px;
  }
  #bottom{
    margin-top: 30px;
  }
  .ordertype{
    width: 831.5px;
  }
</style>
