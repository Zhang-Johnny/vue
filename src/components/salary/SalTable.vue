<template>
  <!--角色列表-->
  <div>
    <el-header style="padding: 0px;display:flex;justify-content:space-between;align-items: center">
      <div style="display: inline" v-show="headshow">
        <el-input
          placeholder="通过角色类型查询..."
          clearable
          style="width: 300px;margin: 0px;padding: 0px;"
          size="mini"
          prefix-icon="el-icon-search"
          v-model="roleType">
        </el-input>
        <el-button type="primary" size="mini" style="margin-left: 5px" icon="el-icon-search" @click="searchEmp()">搜索</el-button>
        <!--<el-button type="primary" size="mini" style="margin-left: 5px" icon="el-icon-edit" @click="addEmp()">添加</el-button>-->
      </div>
    </el-header>
    <el-main>
      <div class="mainbox" v-show="mainshow">
        <template>
          <el-table
            ref="multipleTable"
            :data="roleList"
            tooltip-effect="dark"
            style="width: 100%"
            @selection-change="handleSelectionChange">
            <el-table-column type="selection" width="55"></el-table-column>
            <el-table-column prop="nameZh" label="角色名称（中文）"></el-table-column>
            <el-table-column prop="name" label="角色名称（English）"></el-table-column>
            <el-table-column prop="enable" label="是否启用" :formatter="isEnabled"></el-table-column>
            <el-table-column label="操作">
              <template slot-scope="scope">
                <el-button size="mini" type="success" @click="rolesEdit(scope.row.id)">分配菜单</el-button>
                <el-button size="mini" type="danger" @click="deleteRole(scope.row.id)">删除</el-button>
              </template>
            </el-table-column>
          </el-table>
        </template>
      </div>

      <div class="listbox" v-show="showlist" style="text-align: left">
        <template>
          <div class="backtorole" @click="goBackRole">返回 </div>
          <el-tree
            :data="rolesDatas"
            show-checkbox
            node-key="id"
            :default-expanded-keys="[]"
            :default-checked-keys="[]"
            :props="defaultProps">
          </el-tree>
        </template>
      </div>

      <div class="block" style="margin-top: 35px;" v-show="pageShow">
        <el-pagination
          @current-change="handleCurrentChange"
          :current-page="currentPage"
          layout="total, prev, pager, next, jumper"
          :total=parseInt(totalCount)>
        </el-pagination>
      </div>
    </el-main>
  </div>
</template>
<script>
//  import Tree from 'tree.json';   treeData: tree.data
  export default{
    data(){
      return {
//        checkedIds: [],  //传入id数组作为初始状态选中  [2,3,4]
//        treeData: [{     //tree数组
//            "id": "1",
//            "label": "角色菜单",
//            "depthOpen": 0,
//            "children": []
//              /*{
//                "id": "2",
//                "label": "Node2"
//              },
//              {
//                "id": "3",
//                "label": "Node3"
//              },
//              {
//                "id": "4",
//                "label": "Node4",
//                "children": [
//                  {
//                    "id": "5",
//                    "label": "Node5"
//                  }
//                ]
//              }*/
//
//        }],
//        options: {},     //默认设置
        rolesDatas: [],
        defaultProps: {   //对应？？？？
          children: 'children',
          label: 'name'    //指定对象的属性值
        },
        roleType: '',
        roleList: [],
        currentPage: 1,
        totalCount: '',
        headshow: true,
        mainshow: true,
        showlist: false,
        pageShow: true
      }
    },
    mounted: function () {
      this.getRequest("/role/findAllRole").then(data=>{   //查询所有角色信息
        if(data.status == 200){
          this.totalCount = data.data.data.totalCount
          this.roleList = data.data.data.list;
        }
      })
    },
    methods: {
      isEnabled: function(row, column){
        return row.enable == true ? '已启用': '未启用';
      },
      handleSelectionChange(val) {
        this.multipleSelection = val;
      },
      addEmp: function(){

      },
      rolesEdit: function(id){
        var that = this;
        that.headshow = false;
        that.pageShow = false;
        that.mainshow = false;
        that.showlist =true;
        this.getRequest('/menu/loadAssignData?roleId'+id).then(data=>{   //查询已分配菜单数据
          if(data.status == 200) {
            var datas = data.data.data[0].children;
            console.log("datas",datas)
            var children1 = [];
            datas.forEach(function (item, index) {
              children1.push({     //对应字段
                "id": item.id,
                "name": item.name,
                "children": item.children
              })
            })
            console.log(children1)
            this.rolesDatas = children1;
          }
        })
      },
      deleteRole: function(id){
        this.getRequest('/user/assignRole?userId=1&roleIds'+id).then(data=>{
          console.log(data)
        })
      },
      goBackRole: function(){
        var _that = this;
        _that.headshow = true;
        _that.pageShow = true;
        _that.mainshow = true;
        _that.showlist =false;
      },
      handleCurrentChange(val){
        const param={pageNo: val}
        this.getRequest('/role/findAllRole?data='+JSON.stringify(param)).then(data=> {
          console.log(data)
          this.roleList = data.data.data.list;
        })
      }
    }
  }
</script>
<style>
  .user-info {
    font-size: 12px;
    color: #09c0f6;
  }
  .el-table th{
    text-align: center;
  }
  .backtorole{
    cursor: pointer;
    color: #2c3e50;
    font-weight: bold;
    font-size: 14px;
  }
</style>

