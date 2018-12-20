<template>
  <!--免费使用-->
  <div>
    <el-container>
      <el-header style="padding: 0px;display:flex;align-items: center" class="header">
        <div>
          <span>用户名:</span>
          <el-input clearable style="width: 300px;" v-model="usernameI" placeholder="请输入内容"></el-input>
        </div>
        <div>
          <span>试用状态:</span>
          <el-select v-model="usingType" placeholder="请选择">
            <el-option v-for="item in using" :key="item.value" :label="item.label" :value="item.value" />
          </el-select>
        </div>
        <el-button type="primary" icon="el-icon-search" @click="searchEmp">搜索</el-button>
      </el-header>
      <el-main style="padding-left: 0px;padding-top: 0px">
        <div>
          <template>
            <el-table
              ref="multipleTable"
              :data="rechargeTable"
              tooltip-effect="dark"
              style="width: 100%"
              @selection-change="handleSelectionChange">
              <el-table-column prop="userId" label="用户名"></el-table-column>
              <el-table-column prop="trialType" label="试用状态" :formatter="trialStatus"></el-table-column>
              <el-table-column prop="trialDate" label="试用时间"></el-table-column>
              <el-table-column prop="startTime" :formatter="applyTime" label="开始时间"></el-table-column>
              <el-table-column prop="endTime" :formatter="applyTime1" label="结束时间"></el-table-column>
              <el-table-column label="操作" width="200px">
                <template slot-scope="scope">
                  <el-button size="mini" type="primary" @click="handleEdit(scope.row.id)">编辑</el-button>
                  <el-button size="mini" type="danger" @click="handleDelete(scope.row.id)">删除</el-button>
                </template>
              </el-table-column>
            </el-table>
          </template>
        </div>

        <el-dialog title="试用记录" :visible.sync="dialogFormVisible">
          <el-form :data="useForm" label-width="80px" class="formSub">
            <el-form-item label="试用状态">
              <el-input v-model="useForm.trialType"></el-input>
            </el-form-item>
            <el-form-item label="试用时间">
              <el-date-picker style="width:100%" v-model="useForm.trialDate" type="date" placeholder="选择日期"></el-date-picker>
            </el-form-item>
            <el-form-item label="开始时间">
              <el-date-picker style="width:100%" v-model="useForm.startTime" type="date" placeholder="选择日期"></el-date-picker>
            </el-form-item>
            <el-form-item label="结束时间">
              <el-date-picker style="width:100%" v-model="useForm.endTime" type="date" placeholder="选择日期"></el-date-picker>
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
        usernameI: '',
        usingType: '',
        using:[
          {value: '1',label: '试用中'},
          {value: '2',label: '已试用'}
        ],
        rechargeTable:[],
        useForm: {
          userId: '',
          trialType: '',
          trialDate: '',
          startTime: '',
          endTime: '',
          createTime: '',
          updateTime:''
        },
        dialogFormVisible: false
      }
    },
    mounted: function () {
      this.getRequest("/freeTrlalRecord/findAllFreeTrlalRecord").then(data=>{
         console.log(data)
        this.totalCount = data.data.data.totalCount;
        this.rechargeTable = data.data.data.list;
      })
    },
    methods: {
      handleCurrentChange: function(val){
        var param = {pageNo: val};
        console.log(param)
        var _this = this;
        this.getRequest("/freeTrlalRecord/findAllFreeTrlalRecord?data="+JSON.stringify(param)).then(data=>{
          console.log(data)
          _this.rechargeTable = data.data.data.list;
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
      trialStatus: function(row, column){
        return row.trialType == 1 ? "试用中" : "已试用";
      },
      searchEmp: function () {
        var that = this;
        var params = {
          username: this.usernameI,
          trialType: this.usingType
        }
        that.getRequest("/freeTrlalRecord/findAllFreeTrlalRecord?data="+JSON.stringify(params)).then(data=>{
          that.rechargeTable = data.data.data;
        })
      },
      handleEdit: function(id) {
        var that = this;
        this.dialogFormVisible = !this.dialogFormVisible
        this.getRequest("/freeTrlalRecord/findFreeTrlalRecordById/"+id).then(data=>{
          that.useForm = data.data.data;
        })

      },
      handleDelete: function(id){
        this.getRequest("/freeTrlalRecord/deleteFreeTrlalRecord/"+id).then(data=>{
          alert("删除成功，请刷新页面！")
        })
      },
      submitForm: function(){
        var str = this.useForm;
        this.putRequest("/freeTrlalRecord/updateFreeTrlalRecord?data="+JSON.stringify(str)).then(data => {
        })
        this.dialogFormVisible = !this.dialogFormVisible;
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
  .header>div{
    margin-right: 50px;
  }
</style>
