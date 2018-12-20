<template>
  <!--权限管理-->
  <div>
    <el-container>
      <el-header style="padding: 0px;display:flex;justify-content:space-between;align-items: center">
        <div style="display: inline">
          <el-input
            placeholder="通过角色类型查询..."
            clearable
            style="width: 300px;margin: 0px;padding: 0px;"
            size="mini"
            prefix-icon="el-icon-search"
            v-model="roleType">
          </el-input>
          <el-button type="primary" size="mini" style="margin-left: 5px" icon="el-icon-search" @click="searchEmp()">搜索</el-button>
        </div>
      </el-header>
      <el-main>
        <div>
          <template>
            <el-table
              ref="multipleTable"
              :data="roleList"
              tooltip-effect="dark"
              style="width: 100%; text-align: center"
              @selection-change="handleSelectionChange">
              <el-table-column type="selection" width="55"></el-table-column>
              <el-table-column prop="username" label="用户名"></el-table-column>
              <el-table-column prop="userType" label="用户类型"></el-table-column>
              <el-table-column prop="bindMobile" label="绑定电话"></el-table-column>
              <el-table-column prop="lastLoginIp" label="最后登录IP"></el-table-column>
              <el-table-column prop="lastLoginTime" label="最后登录时间"></el-table-column>
              <el-table-column prop="loginCount" label="登录次数"></el-table-column>
              <el-table-column prop="profilePicture" label="头像"></el-table-column>
              <el-table-column prop="enabled" label="是否启用"></el-table-column>
              <el-table-column label="操作">
                <template slot-scope="scope">
                  <el-button size="mini" type="primary" @click="assignRoles(scope.row.id)">分配角色</el-button>
                </template>
              </el-table-column>
            </el-table>
          </template>
        </div>


        <el-dialog title="分配角色" :visible.sync="dialogFormVisible" width="30%" style="text-align: left">
           <!--<template>
              <el-transfer
                v-model="rightItems"
                :data="leftItems"
                :titles="titles">
              </el-transfer>
           </template>-->
          <div class="item-control itemControl">
            <div>未分配角色列表</div>
            <ul>
              <li v-for="(iteml, index) in itemL">
                <span class="assigned"><el-checkbox>{{iteml.nameZh}}</el-checkbox></span>
                <!--<span class="unassigned"><input type="checkbox"><label>{{iteml.nameZh}}</label></span>-->
              </li>
            </ul>
          </div>
          <div class="todata">
            <button @click="dataToRight" @mouseover="btncolor" :class="btnclass" style="margin-bottom: 15px;"><i class="el-icon-arrow-right"></i></button>
            <button @click="dataToLeft" :class="btnclass"><i class="el-icon-arrow-left"></i></button>
          </div>
          <div class="item-control itemControl">
            <div>已分配角色列表</div>
            <ul>
              <li v-for="(itemr, index) in itemR">
                <span class="assigned"><el-checkbox>{{itemr.nameZh}}</el-checkbox></span>
              </li>
            </ul>
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
  export default{
    data(){
      return {
        itemL: [],
        itemR: [],
        roleType: '',
        roleList: [],
        currentPage: 1,
        totalCount: '',
        dialogFormVisible: false,
        btnclass: 'btn-class'
//        rightItems: [],  // 右  已有角色
//        leftItems: [],  //左   未分配角色
//        titles: ['未分配角色', '已分配角色']
      }
    },
    mounted: function () {
      var _this = this;
      this.getRequest('/user/findAllUser').then(data=>{  //查询所有用户信息
        console.log(data)
        if(data.status == 200){
          this.totalCount = data.data.data.totalCount;
          _this.roleList = data.data.data.list;
        }
      })
    },
    methods: {
      btncolor: function(){
        var that = this;
        if(that.btnclass = 'btn-class'){
            that.btnclass='btn-class1'
        }
      },
      handleSelectionChange(val) {
        this.multipleSelection = val;
      },
      assignRoles:function(id){
        var _that = this;
        this.dialogFormVisible = !this.dialogFormVisible;
        this.getRequest('/user/assign?userId='+id).then(data=>{   //16.2.	查询用户已有角色和没有的角色，用于分配页面
          console.log(data)
          const leftRole = data.data.data.leftList;
          const rightRole = data.data.data.rightList;
          _that.itemL = leftRole;
          _that.itemR = rightRole;
//          _that.leftItems = leftt(leftRole);
//          _that.rightItems = rightt(rightRole);

        })
      },
      handleCurrentChange: function(val){
        const param = {pageNo: val};
          console.log(param)
          this.getRequest('/user/findAllUser?data='+JSON.stringify(param)).then(data=>{
            console.log(data)
        })
      },
      dataToRight: function(){

      },
      dataToLeft: function(){

      }
    }
  }
/*  function leftt(datas){
    const data=[];
    datas.forEach((item, index)=>{
      data.push({
        key: item.id,
        label: item.nameZh
      })
    })
    return data;
  }
  function rightt(datas){
    const data = [];
    datas.forEach((item, index)=>{
      data.push(item.id)
    })
    return data;
  }*/
</script>
<style>
  li{
    list-style-type: none;
  }
  ul{
    padding: 0;
  }
  .user-info {
    font-size: 12px;
    color: #09c0f6;
  }
  .el-table th {
    text-align: center;
  }
  .todata{
    vertical-align: middle;
    margin-top: 140px;
    display: inline-block;
  }
  .todata button{
    outline: none;
    cursor: pointer;
    display: block;
    padding: 10px;
    margin: 0 auto;
    border: 1px solid #dcdfe6;
    border-radius: 50%;
    color: #c0c4cc;
  }
  .todata i{
    width: 14px;
    height: 14px;
  }
  .item-control{
    display: inline-block;
    vertical-align: top;
    margin: 0 45px;
  }
  .itemControl{
    width: 115px;
    min-height: 380px;
    border:1px solid rgba(218,227,234,1);
    padding: 5px 15px;
  }
  .item-control>div{
    font-size: 16px;
    font-weight: 600;
  }
  .btn-class{
    background-color: #f5f7fa;
  }
  .btn-class1{
    background-color: #53A3FF;
  }
</style>
