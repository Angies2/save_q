<template>
  <div>
    <div class="header">
      <img src="/survey/static/img/logo.jpg" height="43" width="122" alt="林同棪">
      <div class="language-ch">{{courseTitle}}</div>
      <div>
        <span class="person-message">姓名:&nbsp;&nbsp;&nbsp;{{name}}</span>
        <span class="person-message">部门:&nbsp;&nbsp;&nbsp;{{dept}}</span>
        <span class="score-message" v-if="score">本次成绩:&nbsp;&nbsp;&nbsp;<strong class="score-detail">{{score}}分</strong></span>
        <el-button class="btn-logout" type="primary" @click="logout">退出</el-button>
      </div>
    </div>
      <div class="content">
          <el-form ref="form" :model="form" label-position="top">
            <div v-for="(f,index1) in form.data">
              <div class="title">{{f.name}}</div>
              <el-form-item  :class="{'myred':redarry[question.qid]&&redarry[question.qid].correct}"   v-for="(question,index2) in f.questions" 
              :label="question.text"
              :inline-message="redarry[question.qid]&&redarry[question.qid].answer"
              :prop="question.qid">
              <p v-if="redarry[question.qid]&&remain==0" ><span class="hd-sure">{{redarry[question.qid]&&redarry[question.qid].correct?'[正确]':''}}</span><span class="hd-false">{{redarry[question.qid]&&redarry[question.qid].correct?'':'[错误]'}}</span>   参考答案：{{redarry[question.qid]&&redarry[question.qid].answer}}</p>
                <el-radio-group v-model="question.check" v-if="question.type">
                  <el-radio  v-for="(q,index3) in question.choice"
                        :label="q.id">
                      {{q.value}}
                      
                  </el-radio>
                </el-radio-group>
                <el-checkbox-group v-model="question.check" v-else>
                  <el-checkbox  v-for="(q,index3) in question.choice" 
                        :label="q.id"
                        :key="q.id">
                      {{q.value}}
                  </el-checkbox>
                </el-checkbox-group>
              </el-form-item>
          </div>
          <el-form-item class="submit-q">
            <el-button type="primary" @click="onSubmit('form')">提交</el-button>
          </el-form-item>
        </el-form>
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
        name:"无",
        dept:"无",
        form: {},
        user:'',
        course:'',
        courseTitle:'test',
        redarry:{},
        score:'',
        remain:'-1'

      }
    },
    created(){
      var _this = this;
      var username = sessionStorage.getItem('user');
        this.name = this.$route.query.name;
        this.dept = this.$route.query.dept;
        this.course = this.$route.query.course;
        this.user = username;
        var course = this.$route.query.course;
      if(username){
        this.$http.get("/getquestions?eid="+course).then(function(res){
          var msg = res.body;
          if(msg.success){
            this.form = msg;
            this.courseTitle = msg.name;
        }else{
          _this.$message({
            message: msg.message,
            type: 'error',
            duration:2000
          });
        }
        })
      }else{
        this.$router.push({path:"/"})
      }
    },      

    methods:{
      onSubmit(submitForm) {
        var _this = this;
        var data = this.form.data;
        var sForm = {};
        for(var i = 0; i < data.length; i++) {
          var question = data[i].questions;
          for(var j = 0; j < question.length; j++) {
            if(question[j].check==''){
              _this.$message({
                  message: '你有未做完的题！！',
                  type: 'error',
                  duration:2000
                });
              return false;
            }else {
              sForm[question[j].qid] = question[j].check;
              
            }  
          }
        }
        //loading
        var loading = this.$loading({
          lock: true,
          text: 'Loading',
          spinner: 'el-icon-loading',
          background: 'rgba(0, 0, 0, 0.7)'
        });
        sForm.user = this.user;
        sForm.course = this.course;
        var _this = this;
        this.$http.post("/submit",sForm).then(function(res){
          var msg = res.body;
          if(msg.success){
            loading.close();
            this.score = msg.data.score;
            var len = msg.data.results;
            this.remain = msg.data.remain;
            for(var key in len){
              _this.$set(_this.redarry,key,len[key])
            }

            if(msg.data.remain>=0){
              this.$alert('<h3 style="text-align:center;width:100%">'+msg.data.pass+'</h3><span>您的分数是：<span style="color:#f90f0f;font-size:16px;">'+ msg.data.score+'分</span>&nbsp;&nbsp;您的最高分为:<span style="color:#f90f0f;font-size:16px;">'+ msg.data.max+'分</span>&nbsp;&nbsp;您剩余考试次数为<span style="color:#f90f0f;font-size:16px;">'+ msg.data.remain+'次</span></span>', '提示', {
                dangerouslyUseHTMLString: true
            })
          }else{
            this.$alert('<span>您的分数是：<span style="color:#f90f0f;font-size:16px;">'+ msg.data.score+'分</span>&nbsp;&nbsp;您的最高分为:<span style="color:#f90f0f;font-size:16px;">'+ msg.data.max+'分</span></span>', '提示', {
                dangerouslyUseHTMLString: true
            })
          }

          }else{
            loading.close();
            _this.$message({
              message: msg.message,
              type: 'error',
              duration:2000
            });
          }
        });

      },
      logout(){
        this.$router.push({path:"/"});
      },

    },
    components:{

    }
  };
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.content {
  width: 95%;
  background-color: #f2f6fc;
  margin: 0 auto;
  padding: 2px 0;
  border-radius: 10px;
  margin-top: 118px;
}
.header {
  display: inline-block;
  width: 100%;
  height: 100px;
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
  margin-top: 27px;
}
.header div {
  text-align: center;
}
.header .language-ch {
  font-size: 20px;
  margin-top: 20px;
  font-weight: bold;
}
.el-form-item {
  margin-bottom: 9px;
  padding-left: 16px;
}
.el-form {
  margin-top: 30px;
}
.input-year .el-input {
  width: 293px;
}
.el-select {
  width: 293px;
}
.long-item .el-radio-group {
  margin-left: 10px;
}
.long-item .el-row {
  margin-bottom: 5px;
}
.lty-footer{
  color: #666;
  font-size: 14px;
  text-align: center;
  padding: 40px 0;
}
.submit-q {
  display: inline-block;
  width: 99%;
  text-align: center;
}
.submit-q .el-button {
  padding: 12px 28px;
  margin-right: 111px;
}
.el-form-item:nth-child(2n){
  background: #b9e8f3;
}
/*.el-form-item:last-child {
  background: #f2f6fc;
}*/
.item {
  margin-left: 0 !important;
}
.title {
  margin: 15px;
  font-weight: bold;
  font-size: 16px;
  }
.person-message {
  display: inline-block;
  width: 300px;
  margin-top: 10px;
}
.btn-logout {
  float: right;
  margin-right: 51px;
}
.score-message {
  position: fixed;
  top: 55px;
}   
.score-detail {
  color: #f7061d;
  font-size: 16px;
}
.hd-sure{
  color: blue;
}
.hd-false{
  color: red;
}
</style>