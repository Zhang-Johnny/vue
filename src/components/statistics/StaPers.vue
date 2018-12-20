<template>
  <!--前台用户管理-->
  <div>
    <el-container>
      <el-header style="padding: 0px;display:flex;justify-content:space-between;align-items: center">
        <div style="display: inline">
          <el-input
            placeholder="通过用户名查询..."
            clearable
            style="width: 300px;margin: 0px;padding: 0px;"
            size="mini"
            prefix-icon="el-icon-search"
            v-model="userName">
          </el-input>
          <el-button type="primary" size="mini" style="margin-left: 5px" icon="el-icon-search" @click="searchEmp">搜索</el-button>
        </div>
      </el-header>
      <el-main style="padding-left: 0px;padding-top: 0px">
        <div>
          <template>
            <el-table
              ref="multipleTable"
              :data="webitems"
              tooltip-effect="dark"
              style="width: 100%"
              @selection-change="handleSelectionChange">
              <el-table-column type="selection" width="55"></el-table-column>
              <el-table-column prop="username" label="登录名" width="180"></el-table-column>
              <el-table-column prop="" label="密码" width="120"></el-table-column>
              <el-table-column prop="parentId" label="客户ID" width="120"></el-table-column>
              <el-table-column prop="userType" label="用户类型" width="120"></el-table-column>
              <el-table-column prop="bindMobile" label="账户所绑定的手机号" width="200"></el-table-column>
              <el-table-column prop="lastLoginIp" label="最后登录IP" width="200"></el-table-column>
              <el-table-column prop="lastLoginTime" :formatter="applyTime" label="最后登录时间" width="200"></el-table-column>
              <el-table-column prop="loginCount" label="登录总次数" width="120"></el-table-column>
              <el-table-column prop="profilePicture" label="头像" width="120"></el-table-column>
              <el-table-column prop="enabled" label="是否启用" width="120" ></el-table-column>
              <!--<el-table-column prop="createTime" label="创建时间" width="150" ></el-table-column>-->
              <!--<el-table-column prop="updateTime" label="修改时间" width="150" ></el-table-column>-->
              <el-table-column label="操作" width="100" fixed="right">
                <template slot-scope="scope">
                  <!--<el-button size="mini" @click="handleEdit(scope.row.id)">编辑</el-button>-->
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
        currentPage: 1,
        totalCount: '',
        userName: '',
        webitems: []
      }
    },
    mounted: function () {
      var _this = this;
      var param = {userType: 1}
      this.getRequest("/userplain/user/findAllUser?data="+JSON.stringify(param)).then(data=>{
        this.totalCount = data.data.data.totalCount;
        _this.webitems = data.data.data.list;
      })
    },
    methods: {
      handleCurrentChange: function(val){
        var param = {pageNo: val, userType: 1};
        var that = this;
        this.getRequest("/userplain/user/findAllUser?data="+JSON.stringify(param)).then(data=>{
          that.webitems = data.data.data.list;
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
      handleSelectionChange(val) {
        this.multipleSelection = val;
      },
      searchEmp: function () {
        var that = this;
        var usern = {userType: 1, username: this.userName};
        this.getRequest("/userplain/user/findAllUser?data="+JSON.stringify(usern)).then(data=>{
          that.webitems = data.data.data.list;
        })
      },
      handleDelete: function(id) {
        this.getRequest("/userplain/user/deleteUser/"+id).then(data=>{
          console.log(data)
          alert("删除成功")
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
