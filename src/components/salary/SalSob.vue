<template>
  <!--充值消费管理-->
  <div>
    <el-container>
      <el-header class="title">
        <div>
          <span>消费项目:</span>
          <el-input clearable style="width: 300px;" v-model="projects" placeholder="请输入内容"></el-input>
        </div>
        <div>
          <span>开票状态:</span>
          <el-select v-model="ticketOpen" placeholder="请选择">
            <el-option v-for="item in opening" :key="item.value" :label="item.label" :value="item.value" />
          </el-select>
        </div>
        <div>
          <span>操作账号:</span>
          <el-input clearable style="width: 300px;" v-model="operateAcc" placeholder="请输入内容"></el-input>
        </div>
        <el-button type="primary" icon="el-icon-search" @click="searchEmp">搜索</el-button>
      </el-header>
      <el-main>
        <div>
          <template>
            <el-table :data="consumeTable" style="width: 100%">
              <el-table-column prop="consume_projects" label="消费项目"></el-table-column>
              <el-table-column prop="recordNo" label="订单编号"></el-table-column>
              <el-table-column prop="userId" label="客户/用户ID" width="120"></el-table-column>
              <el-table-column prop="balance" label="余额" width="120"></el-table-column>
              <el-table-column prop="giveMoney" label="赠送金额" width="120"></el-table-column>
              <el-table-column prop="rechMoney" label="充值金额" width="120"></el-table-column>
              <el-table-column prop="consume" label="消费金额" width="120"></el-table-column>
              <el-table-column prop="paymentType" label="付款方式" :formatter="payMode" width="120"></el-table-column>
              <el-table-column prop="flow_number" label="付款流水号" width="150"></el-table-column>
              <el-table-column prop="isavailable" label="是否可开发票" :formatter="ifOpenInv" width="120"></el-table-column>
              <el-table-column prop="invoiceStatus" label="开票状态" :formatter="ticketStatue" width="120"></el-table-column>
              <el-table-column prop="operateUser" label="操作账号" width="150"></el-table-column>
              <!--<el-table-column prop="createTime" :formatter="formatDate" label="创建时间" width="150"></el-table-column>-->
              <!--<el-table-column prop="updateTime" label="修改时间" width="150"></el-table-column>-->
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
        currentPage: 1,
        totalCount: '',
        projects: '',
        ticketOpen: '',
        opening:[
          {value: '0',label: '未开发票'},
          {value: '1',label: '已开发票'},
        ],
        operateAcc: '',
        consumeTable:[]
      }
    },
    mounted: function () {
      var that = this;
      this.getRequest("/walletRecord/findAllWalletRecord").then(data=>{
        console.log(data)
        this.totalCount = data.data.data.totalCount;
        that.consumeTable = data.data.data.list;
      })
    },
    methods: {
      handleCurrentChange: function(val){
        var param = {pageNo: val};
        console.log(param)
        var _this = this;
        this.getRequest("/walletRecord/findAllWalletRecord?data="+JSON.stringify(param)).then(data=>{
          console.log(data)
          _this.consumeTable = data.data.data.list;
        })
      },
      formatDate: function(row, column){
//        var creatT = new Date(this.createTime)
      },
      payMode: function(row, column){
        return row.paymentType == 1 ? "微信" : row.paymentType == 2 ? "支付宝" : row.paymentType == 3 ? "银行卡" : "信用卡";
      },
      ifOpenInv: function(row, column){
        return row.isavailable == 1 ? "是" : "否";
      },
      ticketStatue: function(row, column) {
        return row.invoiceStatus == 1 ? "已开发票" : "未开发票";
      },
      searchEmp: function(){
        var that = this
        var params = {
          consume_projects: this.projects,
          invoiceStatus: this.ticketOpen,
          operateUser: this.operateAcc
        }
        console.log(params)
        this.getRequest("/walletRecord/findAllWalletRecord?data="+JSON.stringify(params)).then(data=>{
          console.log(data);
          that.consumeTable = data.data.data;
        })
      }
    }

  };
</script>
<style>
  .el-dialog__body {
    padding-bottom: 10px;
  }
  .el-table th{
    text-align: center;
  }
  .title{
    text-align: left;
  }
  .title>div{
    margin-right: 50px;
    display: inline-block;
    margin-top:20px;
  }
</style>
