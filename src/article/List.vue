<template>
  <div class="article_list">
    <!-- 按钮 -->
    <div class="btns">
      <el-button type="primary" size="small" @click="toPublishArticle">发布文章</el-button>
    </div>
    <!-- 表格 -->
    <el-table :data="articles" style="width: 100%" >
      <el-table-column prop="id" label="编号" width="180"></el-table-column>
      <el-table-column prop="title" label="标题" ></el-table-column>
      <el-table-column prop="publishTime" :formatter="dateFormat" label="发布时间" width="180"></el-table-column>
      <el-table-column prop="user.username" label="作者" width="180"></el-table-column>
      <el-table-column prop="category.name" label="所属栏目" width="180"></el-table-column>
      <el-table-column
        fixed="right"
        label="操作"
        width="150">
        <template slot-scope="scope">
          <el-button @click="toReview(scope.row)" type="text" size="small">查看</el-button>
          <el-button type="text" size="small" @click="toEdit(scope.row)">编辑</el-button>
          <el-button type="text" size="small" @click="todelete(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 分页 -->
    <br>
    <div class="btsc">
      <el-button type="primary" size="small" icon="el-icon-search">搜索</el-button>
    </div>
  </div>
</template>
<script>
import request from '@/utils/request'
import moment from 'moment'
export default {
  data() {
    return {
      articles:[]
    }
  },
  created() {
    this.reloadData();
  },
  methods: {
    reloadData(){
      let url = "http://localhost:6677/article/cascadeFindAll"
      request.get(url)
      .then(result=> {
        this.articles = result.data
      })
    },
    toReview(){

    },
    toEdit(record){
      this.$router.push({path:'/article/editor',query:record})
    },

    toPublishArticle() {
      // 跳转到编辑页面
      this.$router.push({ path: '/article/editor' })
    },

    todelete(id) {
      this.$confirm('此操作将永久删除该文章, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        let url = "http://localhost:6677/article/deleteById"
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
    },
    dateFormat:function(row, column) {  
      var date = row[column.property];  
      if (date == undefined) {  
         return "";  
       }  
      return moment(date).format("YYYY-MM-DD HH:mm:ss");  
    }   
  }
}
</script>
<style scoped>

</style>
