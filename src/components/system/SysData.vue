<template>
  <!--风险监控菜单-->
  <div>
    <el-header style="padding: 0px;display:flex;justify-content:space-between;align-items: center">
      <div style="display: inline">
        <el-input
          placeholder="通过警戒内容查询..."
          clearable
          style="width: 300px;margin: 0px;padding: 0px;"
          size="mini"
          prefix-icon="el-icon-search"
          v-model="userAccount">
        </el-input>
        <el-button type="primary" size="mini" style="margin-left: 5px" icon="el-icon-search" @click="searchEmp">搜索</el-button>
        <el-button type="primary" size="mini" icon="el-icon-edit" @click="dialogFormVisibleAdd = true">添加</el-button>
      </div>
    </el-header>
    <el-main>
      <div>
        <template>
          <el-table
            :data="riskList"
            style="width: 100%">
            <el-table-column prop="menuName" label="警戒内容"></el-table-column>
            <el-table-column prop="monitorPrice" label="监控单价"></el-table-column>
            <el-table-column prop="discount" label="单价折扣"></el-table-column>
            <el-table-column prop="createTime" :formatter="applyTime" label="创建时间"></el-table-column>
            <el-table-column prop="updateTime" :formatter="applyTimeT" label="修改时间"></el-table-column>
            <el-table-column label="操作">
              <template slot-scope="scope">
                <el-button size="mini" type="primary" @click="handleEdit(scope.row.id)">编辑</el-button>
                <el-button size="mini" type="danger" @click="handleDelete(scope.row.id)">删除</el-button>
              </template>
            </el-table-column>
          </el-table>
        </template>
      </div>

      <el-dialog title="风险监控" :visible.sync="dialogFormVisible">
        <el-form :data="riskForm" label-width="80px" class="formSub">
          <el-form-item label="警戒内容">
            <el-input v-model="riskForm.menuName"></el-input>
          </el-form-item>
          <el-form-item label="监控单价">
            <el-input v-model="riskForm.monitorPrice"></el-input>
          </el-form-item>
          <el-form-item label="单价折扣">
            <el-input v-model="riskForm.discount"></el-input>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button type="primary" @click="submitForm">保存</el-button>
          <el-button @click="dialogFormVisible = false">取消</el-button>
        </div>
      </el-dialog>

      <el-dialog title="风险监控—添加" :visible.sync="dialogFormVisibleAdd">
        <el-form :data="riskFormAdd" label-width="80px" class="formSub">
          <el-form-item label="警戒内容">
            <el-input v-model="riskFormAdd.menuName"></el-input>
          </el-form-item>
          <el-form-item label="监控单价">
            <el-input v-model="riskFormAdd.monitorPrice"></el-input>
          </el-form-item>
          <el-form-item label="单价折扣">
            <el-input v-model="riskFormAdd.discount"></el-input>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button type="primary" @click="addForm">保存</el-button>
          <el-button @click="dialogFormVisibleAdd = false">取消</el-button>
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
  </div>
</template>
<script>
  export default {
    data() {
      return {
        totalCount: '',
        currentPage: 1,
        userAccount: '',
        riskList: [],
        riskForm: {
          menuName: '',
          monitorPrice: '',
          discount: ''
        },
        riskFormAdd: {
          menuName: '',
          monitorPrice: '',
          discount: ''
        },
        dialogFormVisible: false,
        dialogFormVisibleAdd: false
      }
    },
    mounted: function(){
      var _this = this;
      this.getRequest("/riskMonitor/findAllRiskMonitor").then(data=>{
        this.totalCount = data.data.data.totalCount;
        _this.riskList = data.data.data.list;
      })
    },
    methods: {
      handleCurrentChange: function(val){
        var param = {pageNo: val};
        var that = this;
        this.getRequest("/riskMonitor/findAllRiskMonitor?data="+JSON.stringify(param)).then(data=>{
          that.riskList = data.data.data.list;
        })
      },
      applyTime: function (value) {
        var date = new Date(value.createTime);
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
      applyTimeT: function (value) {
        var date = new Date(value.updateTime);
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
      searchEmp: function(){
        var _this = this;
        var param={menuName: this.userAccount};
        this.getRequest("/riskMonitor/findAllRiskMonitor?data="+JSON.stringify(param)).then(data=>{
          console.log(data)
          _this.riskList = data.data.data;
        })
      },
      addForm: function () {
        var str = this.riskFormAdd;
        console.log(str)
        this.getRequest("/riskMonitor/insertRiskMonitor?data="+JSON.stringify(str)).then(data=>{
          console.log(data)
        })

      },
      handleEdit: function(id){
        this.dialogFormVisible = !this.dialogFormVisible;
        var _this = this;
        this.getRequest("/riskMonitor/findRiskMonitorById/"+id).then(data=>{
          _this.riskForm = data.data.data;
        })
      },
      handleDelete: function(id){
        var that = this;
        this.getRequest("/riskMonitor/deleteRiskMonitor/"+id).then(data=>{
          console.log(data)
        })
      },
      submitForm: function(){
        var str = this.riskForm;
        this.putRequest("/riskMonitor/updateRiskMonitor?data="+JSON.stringify(str)).then(data=>{
          console.log(data)
        })
        this.dialogFormVisible = !this.dialogFormVisible;
      }
    }
  }
</script>
<style>
  .buttonAdd{
    text-align: left;
    margin-top: 20px;
  }
  .el-table th{
    text-align: center;
  }
</style>
