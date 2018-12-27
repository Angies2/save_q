<template>
<div>
  <div class="header">
    <img src="/survey/static/img/logo.jpg" height="43" width="122" alt="林同棪">
    <div class="language-ch">{{info.name}}</div>
    <div>
       <span class="count-message">参与人数:&nbsp;&nbsp;&nbsp;{{info.count}}</span>
        <span class="agv-message">平均分:&nbsp;&nbsp;&nbsp;{{info.score}}</span>
        <span class="pass-message">通过率:&nbsp;&nbsp;&nbsp;{{info.pass}}</span>
        <a class="btn-download" :href="address" v-if="info.count>0">下载表格</a>
      <el-button class="btn-logout" type="primary" @click="logout">退出</el-button>
    </div>

  </div>
  <div class="content">
    <el-table :data="tableData" stripe border style="width: 100%;margin-top: 100px;" height="650">
      <el-table-column fixed  type="index" label="编号" width="100"  align="center"></el-table-column>
      <el-table-column  prop="name" label="姓名" align="center" sortable></el-table-column>
      <el-table-column prop="dept" label="部门" align="center" sortable></el-table-column>
      <el-table-column prop="score" label="分数" align="center" sortable></el-table-column>
      <el-table-column prop="pass" label="是否通过" align="center" sortable> </el-table-column>
    </el-table>
  </div>
  <div class="lty-footer">
    林同棪国际工程技术有限公司&nbsp;&nbsp;&nbsp;版权所有©2018 
  </div>
</div>
</template>
<script>
  export default {
    data() {
      return {
        info:{},
        tableData: [],
        address:''
      };
    },
    created(){
      var params = {
          user:sessionStorage.getItem('user'),
          course:this.$route.query.course
      };
      this.$http.post("/getresult",params).then(function(res){
        var msg = res.body
        if(msg.success){
            this.tableData = msg.data.results;
            this.info = msg.data.info;
            this.address = msg.data.fp;
        }else{
          _this.$message({
            message: msg.message,
            type: 'error',
            duration:2000
          });
        }
      });
    },
    methods:{
      logout(){
        this.$router.push({path:"/"});
      },
    }
  };
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.header {
  display: inline-block;
  width: 100%;
  height: 90px;
  font-size: 18px;
  margin-bottom: 18px;
  position: fixed;
  top: 0px;
  background-color: #fff;
  z-index: 999;
}
.header img {
  position: absolute;
  margin-left: 45px;
  margin-top: 17px;
}
.header div {
  text-align: center;
}
.header .language-ch {
  font-size: 20px;
  margin-top: 20px;
  font-weight: bold;
}
.btn-logout {
  float: right;
  margin-right: 51px;
  margin-top: -5px;
}
.lty-footer{
  color: #666;
  font-size: 14px;
  padding: 40px 0;
  position: fixed;
  bottom: 0;
  text-align: center;
  width: 100%;
}
.content {
  padding: 20px;
  background-color: #f1f2f5;
  height: 814px;
}
.count-message,
.agv-message,
.pass-message {
  display: inline-block;
  width: 300px;
  margin-top: 10px;
}
.btn-download {
  color: #126ece;
  font-size: 16px;
}
</style>