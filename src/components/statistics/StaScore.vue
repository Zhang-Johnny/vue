<template>
  <!--用户风险监控-->
  <div>
    <el-container>
      <el-header style="padding: 0px;display:flex;justify-content:space-between;align-items: center">
        <div style="display: inline">
          <el-input
            placeholder="通过用户账号查询..."
            clearable
            style="width: 300px;margin: 0px;padding: 0px;"
            size="mini"
            prefix-icon="el-icon-search"
            v-model="userAcc">
          </el-input>
          <el-button type="primary" size="mini" style="margin-left: 5px" icon="el-icon-search" @click="searchEmp">搜索</el-button>
        </div>
      </el-header>
      <el-main style="padding-left: 0px;padding-top: 0px">
        <div>
          <template>
            <el-table
              ref="multipleTable"
              :data="riskItems"
              tooltip-effect="dark"
              style="width: 100%"
              @selection-change="handleSelectionChange">
              <el-table-column type="selection" width="55"></el-table-column>
              <el-table-column prop="xhxUser.username" label="用户名" width="120"></el-table-column>
              <el-table-column prop="riskId" label="风险ID" width="120"></el-table-column>
              <el-table-column prop="companyId" label="公司ID" width="120"></el-table-column>
              <el-table-column prop="orderId" label="订单编号" width="120"></el-table-column>
              <el-table-column prop="companyName" label="公司名称" width="200"></el-table-column>
              <el-table-column prop="roType" label="类别" width="120"></el-table-column>
              <el-table-column prop="receiveMode" :formatter="riskType" label="风险接收方式" width="120"></el-table-column>
              <el-table-column prop="mails" label="接收邮件" width="120"></el-table-column>
              <el-table-column prop="sendCycle" :formatter="sendCircle" label="发送周期" width="120" ></el-table-column>
              <el-table-column prop="startTime" :formatter="applyTime" label="开始时间" width="150"></el-table-column>
              <el-table-column prop="endTime" :formatter="applyTime1" label="结束时间" width="150"></el-table-column>
              <!--<el-table-column prop="createTime" label="创建时间" width="150"></el-table-column>-->
              <!--<el-table-column prop="updateTime" label="修改时间" width="150"></el-table-column>-->
              <el-table-column label="操作" width="200px" fixed="right">
                <template slot-scope="scope" >
                  <el-button size="mini" type="primary" @click="handleEdit(scope.row.id)">编辑</el-button>
                  <el-button size="mini" type="danger" @click="handleDelete(scope.row.id)">删除</el-button>
                </template>
              </el-table-column>
            </el-table>
          </template>
        </div>

        <el-dialog title="用户风险监控" :visible.sync="dialogFormVisible" class="dialogRisk">
          <el-form :data="riskForm" label-width="100px" class="formSub">
            <el-form-item label="订单编号">
              <el-input v-model="riskForm.orderId"></el-input>
            </el-form-item>
            <el-form-item label="公司名称">
              <el-input v-model="riskForm.companyName"></el-input>
            </el-form-item>
            <el-form-item label="类别">
              <el-input v-model="riskForm.roType"></el-input>
            </el-form-item>
            <el-form-item label="风险接收方式">
              <el-input v-model="riskForm.receiveMode == 1 ? '邮件' : riskForm.receiveMode == 2 ? 'APP消息' : '微信'"></el-input>
            </el-form-item>
            <el-form-item label="接收邮件">
              <el-input v-model="riskForm.mails"></el-input>
            </el-form-item>
            <el-form-item label="发送周期">
              <el-input v-model="riskForm.sendCycle == 1 ? '每周' : riskForm.sendCycle == 2 ? '每月' : riskForm.sendCycle == 3 ? '季度' : '不需要'"></el-input>
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
        currentPage: 1,
        totalCount: '',
        userAcc: '',
        riskItems: [],
        riskForm: {
          companyName: '',
          roType: '',
          receiveMode: '',
          mails: '',
          sendCycle: '',
          startTime: '',
          endTime: ''
        },
        dialogFormVisible: false
      }
    },
    mounted: function () {
      var that = this;
      this.getRequest("/riskOrder/findAllRiskOrder").then(data=>{
        this.totalCount = data.data.data.totalCount
        that.riskItems = data.data.data.list;
      })
    },
    methods: {
      handleCurrentChange: function(val){
        var param = {pageNo: val};
        console.log(param)
        var _this = this;
        this.getRequest("/riskOrder/findAllRiskOrder?data="+JSON.stringify(param)).then(data=>{
          console.log(data)
          _this.riskItems = data.data.data.list;
        })
      },
      applyTime: function (value) {
        var date = new Date(value.startTime);
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
      applyTime1: function (value) {
        var date = new Date(value.endTime);
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
      handleSelectionChange(val) {
        this.multipleSelection = val;
      },
      riskType: function(row, column){
        return row.receiveMode == 1 ? "邮件" : row.receiveMode == 2 ? "APP消息" : "微信";
      },
      sendCircle: function(row, column){
        return row.sendCycle == 1 ? "每周" : row.sendCycle == 2 ? "每月" : row.sendCycle == 3 ? "季度" : "不需要";
      },
      searchEmp: function(){
        var that = this;
        var param = {username: this.userAcc};
        this.getRequest("/riskOrder/findAllRiskOrder?data="+JSON.stringify(param)).then(data=>{
          that.riskItems = data.data.data;
        })
      },
      handleEdit: function(id){
        var that = this;
        this.dialogFormVisible = !this.dialogFormVisible;
        this.getRequest("/riskOrder/findRiskOrderById/"+id).then(data=>{
          console.log(data)
          that.riskForm = data.data.data;
        })
      },
      handleDelete: function(id){
        this.getRequest("/riskOrder/deleteRiskOrder/"+id).then(data=>{
          console.log(data)
          alert("删除成功，请刷新页面！")
        })
      },
      submitForm: function(){
        var str = this.riskForm
        this.putRequest("/riskOrder/updateRiskOrder?data="+JSON.stringify(str)).then(data => {
          console.log(data)
        })
        this.dialogFormVisible = !this.dialogFormVisible;
      }
    }
  }
</script>
<style>
  .el-form-item__label{
    width: 130px;
  }
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
