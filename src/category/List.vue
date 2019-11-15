<template>
  <div class="category_list">
    <!-- 按钮 -->
    <div class="btns">
      <el-button type="primary" size="small" @click="toAdd">新增</el-button>
      <el-button type="danger" size="small" @click="toBatchDelete">批量删除</el-button>
    </div>
    <!-- 表格 -->
    <el-table :data="categorys" style="width: 100%" @selection-change="handleSelectionChange" >
      <el-table-column type="selection" width="55"> </el-table-column>
      <el-table-column prop="id" label="编号" width="180"></el-table-column>
      <el-table-column prop="name" label="栏目名称" width="300" ></el-table-column>
      <el-table-column prop="description" label="栏目描述"></el-table-column>
      <el-table-column prop="parentId" label="父栏目" width="180"></el-table-column>
      <el-table-column
        fixed="right"
        label="操作"
        width="150">
        <template slot-scope="scope">
           <el-button @click="toDelete(scope.row.id)" type="text" size="small">删除</el-button>
           <el-button @click="toEdit(scope.row)" type="text" size="small">编辑</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 模态框 -->
    <el-dialog title="新增栏目信息" :visible.sync="dialogFormVisible" @close="dialogCloseHandler">
      <el-form :model="form">
        <el-form-item label="栏目名称" label-width="80px">
          <el-input v-model="form.name"  autocomplete="off" ></el-input>
        </el-form-item>
        <el-form-item label="所属栏目" label-width="80px">
          <el-select v-model="form.parentId" placeholder="请选择所属栏目" clearable>
            <el-option v-for="c in categorys" :key="c.id" :label="c.name" :value="c.id"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="栏目描述" label-width="80px">
          <el-input type="textarea" v-model="form.description" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button size="small" @click="dialogCloseHandler">取 消</el-button>
        <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
      </div>
    </el-dialog>
    <!-- /模态框 -->
  </div>
</template>
<script>
import request from '@/utils/request'
import qs from 'querystring'
export default {
  data() {
    return {
        dialogFormVisible:false,
        form:{},
        ids:[],
        categorys:[]
    }
  },
  created() {
    this.reloadData();
  },
  methods: {
      handleSelectionChange(val){
      this.ids = val.map(item => item.id);
    },

     submitHandler(){
      request.request({
        url:'http://127.0.0.1:6677/category/saveOrUpdate',
        method:'post',
        headers:{
          'Content-Type':'application/x-www-form-urlencoded'
        },
        data:qs.stringify(this.form)
      })
      .then(response=>{
        // 提示信息
        this.$message({
          message:response.message,
          type:"success"
        })
        // 关闭模态框
        this.dialogFormVisible = false;
        // 重载数据
        this.reloadData();
        
      })
    },
    dialogCloseHandler(){
         this.dialogFormVisible = false;
         this.reloadData();
    },
    reloadData(){
      let url = "http://localhost:6677/category/findAll"
      request.get(url)
      .then(result=> {
        this.categorys = result.data
      })
    },
    toEdit(record){
      this.dialogFormVisible = true;
      // 为form绑定要修改的值
      this.form = record;
    },
    // 批量删除
    toBatchDelete(){
      let url = "http://127.0.0.1:6677/category/batchDelete"
      request.request({
          url,
          method:'post',
          headers:{
            'Content-Type':'application/json'
          },
          data:JSON.stringify(this.ids)
      })   
      .then(response=>{
        this.$message({
          message:response.message,
          type:"success"
        })
        this.reloadData();
      })
    },
    toAdd() {
      this.form = {};
      // 跳转
      this.dialogFormVisible = true;
    },

    toDelete(id) {
      this.$confirm('此操作将永久删除该文章, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        let url = "http://127.0.0.1:6677/category/deleteById"
        request.get(url,{params:{id}})
        .then(
          response=>{
            this.$message({
                type: 'success',
                message: response.message
            })
            this.reloadData();
          }
        )
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        });          
      });
    }
  }
}
</script>
<style scoped>

</style>
