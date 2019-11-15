<template>
  <div class="article_editor">
    <el-button type="text" @click="back">返回</el-button>

    <el-form ref="form" :model="form" label-width="80px">
      <el-form-item label="所属栏目">
        <el-select v-model="form.categoryId" placeholder="请选择所属栏目" clearable>
          <el-option v-for="c in categorys" :key="c.id" :label="c.name" :value="c.id"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="文章标题">
        <el-input v-model="form.title"></el-input>
      </el-form-item>
      <el-form-item label="正文">
        <el-input type="textarea" v-model="form.content" rows="10"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="onSubmit">立即创建</el-button>
        <el-button @click="back">取消</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>
<script>
import request from '@/utils/request'
import qs from 'querystring'
export default {
  data() {
    return {
      form:{},
      categorys:[]
    }
  },
  created(){
   this.form = this.$route.query;
   this.loadCategorys();
  },
  methods: {
    loadCategorys(){
      let url = "http://localhost:6677/category/findAll"
      request.get(url)
      .then(result=> {
        this.categorys = result.data
      })
    },
    back() {
      this.$router.go(-1);
    },
    onSubmit() {
      let url = "http://127.0.0.1:6677/article/saveOrUpdate"
      request({
        method:"post",
        url,
        headers:{
          'Content-Type':'application/x-www-form-urlencoded'
        },
        data:qs.stringify(this.form)
      })
      .then(result=>{
        this.$message({
          message:result.message,
          type:"success"
        });
        this.back()
      })
    }
    
  }
}
</script>
