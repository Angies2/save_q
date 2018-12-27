<template>
  <el-container>
<el-header style="text-align: right; font-size: 12px">
    <div class="lty-logo">
      <img src="./logo.jpg" height="43" width="122" alt="林同棪"> 
    </div>
    <div class="lty-h-title">林同棪国际BIM应用工程师职业技能培训报名系统</div>
</el-header>
</el-container>
</template>
<script>
  export default {
    data() {
      return {

      };
    }
  };
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .lty-h-title{
    float: left;
    color: #fff;
    font-size: 18px;

  }
  .lty-logo{
    width: 122px;
    height: 43px;
    float: left;
    margin: 8px 30px 0 -10px;
  }
  .el-menu{
    margin-top: 20px;
  }
  .el-header, .el-footer {
    background-color: #B3C0D1;
    color: #333;
    text-align: center;
    line-height: 60px;
  }
  
  .el-aside {
    background-color: #D3DCE6;
    color: #333;
    text-align: center;
  }
  .el-main {
    background-color: #E9EEF3;
    color: #333;
  }
  .lty-card{
    margin-top: 50px;
    font-size: 14px;
  }
  .lty-clum{
    width: 80%;
    overflow: hidden;
    min-height: 178px;
    margin-bottom: 10px;
    position: relative;
    padding:0 0 15px 10px;
    background-color: #fff;
  }
  .lty-headwrap{
    width: 100%;
    height: 50px; 
  }
  .lty-classtitle{
    float: left;
    font-size: 20px;
    margin:20px 0 0 27px;
  }
  .lty-baoming{
    float: right;
    font-size: 14px;
    margin:20px 20px 0 0;
    cursor: pointer;
    color: #409EFF;
  }
  .lty-more{
    width: 25px;
    height: 37px;
    position: absolute;
    left: 10px;
    top: 5px;
    cursor: pointer;
    padding-top: 13px;
    font-size: 12px;
  }
   .lty-content{
    font-size: 14px;
    color: #666;
    width: 80%;
    overflow: hidden;
    padding-left: 28px;
  }
  .lty-content span{
    color: #333;
  }

</style>