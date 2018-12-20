<template>
  <!--客户查询-->
  <div>
    <el-container>
      <el-header style="padding: 0px;display:flex;justify-content:space-between;align-items: center">
        <div style="display: inline">
          <el-input
            placeholder="通过客户账号查询..."
            clearable
            style="width: 300px;margin: 0px;padding: 0px;"
            size="mini"
            prefix-icon="el-icon-search"
            v-model="userAccount">
          </el-input>
          <el-button type="primary" size="mini" style="margin-left: 5px" icon="el-icon-search" @click="searchEmp">搜索</el-button>
          <el-button type="primary" size="mini" icon="el-icon-edit" @click="dialogAddFormVisible = true">添加</el-button>
        </div>
      </el-header>
      <el-main style="padding-left: 0px;padding-top: 0px">
        <div>
          <template>
            <el-table
              ref="multipleTable"
              :data="userItems"
              tooltip-effect="dark"
              style="width: 100%"
              @selection-change="handleSelectionChange">
              <el-table-column type="selection" width="55"></el-table-column>
              <el-table-column prop="username" label="登录名" width="150"></el-table-column>
              <el-table-column prop="password" label="密码(加密)" width="100"></el-table-column>
              <el-table-column prop="company" label="单位公司" width="180"></el-table-column>
              <el-table-column prop="mobilePhone" label="联系方式" width="120"></el-table-column>
              <el-table-column prop="bindMobile" label="账户所绑定的手机号" width="150"></el-table-column>
              <el-table-column prop="lastLoginIp" label="最后登录IP" width="120"></el-table-column>
              <el-table-column prop="lastLoginTime" label="最后登录时间" width="150" :formatter="applyTime"></el-table-column>
              <el-table-column prop="loginCount" label="登录总次数" width="100"></el-table-column>
              <el-table-column prop="profilePicture" label="头像" width="100"></el-table-column>
              <el-table-column prop="enabled" label="是否启用" :formatter="isUsing" width="100"></el-table-column>
              <el-table-column prop="rechMoney" label="充值剩余总金额" width="150"></el-table-column>
              <el-table-column prop="giveMoney" label="赠送剩余总金额" width="150"></el-table-column>
              <el-table-column fixed="right" label="操作" width="180">
                <template slot-scope="scope">
                  <el-button size="mini" type="primary" @click="handleEdit(scope.row.id)">编辑</el-button>
                  <el-button size="mini" type="danger" @click="handleDelete(scope.row.id)">删除</el-button>
                </template>
              </el-table-column>
            </el-table>
          </template>
        </div>

        <el-dialog title="信息操作" :visible.sync="dialogFormVisible">
          <el-form :data="infoForm" label-width="180px" class="formSub">
            <el-form-item label="登录名">
              <el-input v-model="infoForm.username"></el-input>
            </el-form-item>
            <el-form-item label="密码">
              <el-input v-model="infoForm.password"></el-input>
            </el-form-item>
            <el-form-item label="单位公司">
              <el-input v-model="infoForm.company"></el-input>
            </el-form-item>
            <el-form-item label="联系方式">
              <el-input v-model="infoForm.mobilePhone"></el-input>
            </el-form-item>
            <el-form-item label="账户所绑定的手机号">
              <el-input v-model="infoForm.bindMobile"></el-input>
            </el-form-item>
            <el-form-item label="最后登录IP">
              <el-input v-model="infoForm.lastLoginIp"></el-input>
            </el-form-item>
            <el-form-item label="最后登录时间">
              <el-input v-model="infoForm.lastLoginTime"></el-input>
            </el-form-item>
            <el-form-item label="头像">
              <el-input v-model="infoForm.profilePicture"></el-input>
            </el-form-item>
            <el-form-item label="充值剩余总金额">
              <el-input v-model="infoForm.rechMoney"></el-input>
            </el-form-item>
            <el-form-item label="赠送剩余总金额">
              <el-input v-model="infoForm.giveMoney"></el-input>
            </el-form-item>
          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button type="primary" @click="submitForm">保存</el-button>
            <el-button @click="dialogFormVisible = false">取消</el-button>
          </div>
        </el-dialog>

        <el-dialog title="信息操作—添加" :visible.sync="dialogAddFormVisible">
          <el-form :data="infoFormAdd" label-width="180px" class="formSub">
            <el-form-item label="登录名">
              <el-input v-model="infoFormAdd.username"></el-input>
            </el-form-item>
            <el-form-item label="密码">
              <el-input v-model="infoFormAdd.password"></el-input>
            </el-form-item>
            <el-form-item label="单位公司">
              <el-input v-model="infoFormAdd.company"></el-input>
            </el-form-item>
            <el-form-item label="联系方式">
              <el-input v-model="infoFormAdd.mobilePhone"></el-input>
            </el-form-item>
            <el-form-item label="账户所绑定的手机号">
              <el-input v-model="infoFormAdd.bindMobile"></el-input>
            </el-form-item>
            <el-form-item label="最后登录IP">
              <el-input v-model="infoFormAdd.lastLoginIp"></el-input>
            </el-form-item>
            <el-form-item label="最后登录时间">
              <el-input v-model="infoFormAdd.lastLoginTime"></el-input>
            </el-form-item>
            <el-form-item label="登录总次数">
              <el-input v-model="infoFormAdd.loginCount"></el-input>
            </el-form-item>
            <el-form-item label="头像">
              <el-input v-model="infoFormAdd.profilePicture"></el-input>
            </el-form-item>
            <el-form-item label="充值剩余总金额">
              <el-input v-model="infoFormAdd.rechMoney"></el-input>
            </el-form-item>
            <el-form-item label="赠送剩余总金额">
              <el-input v-model="infoFormAdd.giveMoney"></el-input>
            </el-form-item>
          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button type="primary" @click="sureForm">确定</el-button>
            <el-button @click="dialogAddFormVisible = false">取消</el-button>
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
        userAccount: '',
        userItems: [],
        infoForm: {
          username: '',
          password: '',
          company: '',
          mobilePhone: '',
          bindMobile: '',
          lastLoginIp: '',
          lastLoginTime: '',
          loginCount: '',
          profilePicture: '',
          rechMoney: '',
          giveMoney: ''
        },
        infoFormAdd: {
          username: '',
          password: '',
          company: '',
          mobilePhone: '',
          bindMobile: '',
          lastLoginIp: '',
          lastLoginTime: '',
          profilePicture: '',
          rechMoney: '',
          giveMoney: ''
        },
        dialogFormVisible: false,
        dialogAddFormVisible: false
      }
    },
    mounted: function () {
      var _this = this;
      this.getRequest("/groupUser/findAllGroupUser").then(data=>{
        console.log(data)
        this.totalCount = data.data.data.totalCount;
        _this.userItems = data.data.data.list;
      })
    },
    methods: {
      handleCurrentChange: function(val){
        var param = {pageNo: val};
        var that = this;
        this.getRequest("/groupUser/findAllGroupUser?data="+JSON.stringify(param)).then(data=>{
          that.userItems = data.data.data.list;
        })
      },
      applyTime: function (value) {
        var date = new Date(value.lastLoginTime);
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
      isUsing: function(row, column){
        return row.enabled == 1 ? "是" : "否";
      },
      handleSelectionChange(val) {
        this.multipleSelection = val;
      },
      searchEmp: function(){
        var that = this;
        var param = {username: this.userAccount}
        this.getRequest("/groupUser/findAllGroupUser?data="+JSON.stringify(param)).then(data=>{
          console.log(data)
          that.userItems = data.data.data.list;
        })
      },
      sureForm: function(){
        var form = this.infoFormAdd;
        console.log(form)
//        this.userItems.push(this.infoFormAdd);
        this.putRequest("/groupUser/groupReg?data="+JSON.stringify(form)).then(data=>{
          console.log(111)
          console.log(data)

        })
        this.dialogAddFormVisible = !this.dialogAddFormVisible;
      },
      handleEdit: function(id){
        this.dialogFormVisible = !this.dialogFormVisible;
        var that = this;
        this.getRequest("/groupUser/findGroupUserById/"+id).then(data =>{
          that.infoForm = data.data.data;
        })
      },
      handleDelete: function(id){
        this.getRequest("/groupUser/deleteGroupUser/"+id).then(data=>{
          console.log(data);
          alert("删除成功")
        })
      },
      submitForm: function () {
        var _str = this.infoForm;
        console.log(_str);
        this.putRequest("/groupUser/updateGroupUser?data="+JSON.stringify(_str)).then(data => {
          console.log(data);
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
  .el-form-item__label{
    width: 180px;
  }
</style>
