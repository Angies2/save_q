<template>
<div>
<div class="lty-head-wrap">
  <div class="lty-logo">
    <img src="/survey/static/img/logo.jpg" height="43" width="122" alt="林同棪">
  </div>
  <div class="lty-h-title">林同棪国际在线考试系统</div>
</div>
<div class="lty-content-wrap">
    <div class="lty-content">
        <div class="lty-cnt-center">
          <div class="lty-cnt-cr">
            <el-tabs type="border-card">
              <el-tab-pane label="登陆">
                <!-- 登陆 -->
                 <p class="lty-welcom">欢迎登录</p>
                  <el-form :model="ruleForm" status-icon :rules="rules" ref="ruleForm" label-width="100px" class="demo-ruleForm">
                    <el-form-item label="用户名" prop="user">
                      <el-input type="input" v-model="ruleForm.user" auto-complete="off"></el-input>
                      <i class="lty-icon-user"></i>
                    </el-form-item>
                    <el-form-item label="密码" prop="password">
                      <el-input type="password" v-model="ruleForm.password" auto-complete="off"></el-input>
                    </el-form-item>
                    <el-form-item label="考试课程" prop="course">
                      <el-select v-model="ruleForm.course" placeholder="请选择考试课程">
                        <el-option v-for="(st,index) in selectcourse"
                                               :key="st.id"
                                               :label="st.name"
                                               :value="st.id">
                        </el-option>
                      </el-select>
                    </el-form-item>
                    <el-form-item>
                      <el-button type="primary" @click="submitForm('ruleForm')">登 陆</el-button>
                      <el-button @click="resetForm('ruleForm')">重 置</el-button>
                    </el-form-item>
                    <div class="remind">如若忘记密码请联系管理员</div>
                  </el-form>
              </el-tab-pane>
              <el-tab-pane label="注册">
                <!-- 注册 -->
                <p class="lty-register">注册</p>
                  <el-form :model="ruleForm2" status-icon :rules="rules2" ref="ruleForm2" label-width="100px" class="demo-ruleForm">
                    <el-form-item label="用户名" prop="user">
                      <el-input type="input" v-model="ruleForm2.user" auto-complete="off"></el-input>  
                      <i class="lty-icon-user"></i>
                    </el-form-item>
                    <el-form-item label="实名" prop="name">
                      <el-input type="input" v-model="ruleForm2.name" auto-complete="off"></el-input>
                      <i class="lty-icon-user"></i>
                    </el-form-item>
                    <el-form-item label="部门" prop="dept">
                      <el-select v-model="ruleForm2.dept" placeholder="请选择部门">
                         <el-option v-for="(st,index) in deptcourse"
                                               :key="st.id"
                                               :label="st.name"
                                               :value="st.id">
                        </el-option>
                      </el-select>
                      <i class="lty-icon-user"></i>
                    </el-form-item>
                    <el-form-item label="密码" prop="password">
                      <el-input type="input" v-model="ruleForm2.password" auto-complete="off"></el-input>
                    </el-form-item>
                    <el-form-item>
                      <el-button type="primary" @click="register('ruleForm2')">注册</el-button>
                      <el-button @click="resetForm('ruleForm2')">取消</el-button>
                    </el-form-item>
                  </el-form>
              </el-tab-pane>
            </el-tabs>
          </div>
        </div>
        <div class="lty-footer">
          林同棪国际工程技术有限公司&nbsp;&nbsp;&nbsp;版权所有©2018 
        </div>
    </div>
    
</div>
</div>
</template>
<script>
  export default {
    data() {
      return {
        selectcourse:[],//课程下拉
        deptcourse:[],//部门下拉
        ruleForm: {
          password: '',
          user:'',
          course:''
        },
        ruleForm2: {
          password: '',
          user:'',
          name:'',
          dept:''

        },
        rules: {
          password: [
            { required: true, message: '请输入用户密码', trigger: 'blur,change' }
          ],
          user: [
            { required: true, message: '请输入用户名', trigger: 'blur,change' }
          ],
          course: [
          { required: true, message: '请选择考试课程', trigger: 'blur,change' }
          ]
        },
        rules2: {
          password: [
            { required: true, message: '请输入用户密码', trigger: 'blur,change' }
          ],
          user: [
            { required: true, message: '请输入用户名', trigger: 'blur,change' }
          ],
          name: [
          { required: true, message: '请输入实名', trigger: 'blur,change' }
          ],
          dept: [
          { required: true, message: '请选择考试课程', trigger: 'blur,change' }
          ]
        }
      };
    },
    created(){
      this.$http.get("/getexam").then(function(res){
        var msg = res.body
        if(msg.success){
            this.selectcourse = msg.exam;
            this.deptcourse = msg.dept;
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
      // 登陆
      submitForm(formName) {
        var _this = this;
        //loading
        const loading = this.$loading({
          lock: true,
          text: 'Loading',
          spinner: 'el-icon-loading',
          background: 'rgba(0, 0, 0, 0.7)'
        });
        this.$refs[formName].validate((valid) => {
          if (valid) {
              this.$http.post("/verify",this.ruleForm).then(function(res){
                var msg = res.body;
                var params = {user:msg.user,name:msg.name,dept:msg.dept,course:this.ruleForm.course};
                if(msg.success){
                  sessionStorage.setItem('user',_this.ruleForm.user)
                  loading.close();
                  if(msg.admin){
                    this.$router.push({path:"/exam",query:params})
                  }else{
                    this.$router.push({path:"/admin",query:params})
                  }
                }else{
                  _this.$message({
                    message: msg.message,
                    type: 'error',
                    duration:2000
                  });
                  loading.close();
                }
              });
          } else {
            console.log('error submit!!');
            loading.close();
            return false;
          }
        });
      },
      // 注册
      register(formName){
        var _this = this;
        this.$refs[formName].validate((valid) => {
          if (valid) {
            this.$http.post("/adduser",this.ruleForm2).then(function(res){
              var msg = res.body
              if(msg.success){
                this.$message({
                  message: msg.message,
                  type: 'success'
                });
                  this.$router.push({path:"/"})
              }else{
                _this.$message({
                  message: msg.message,
                  type: 'error',
                  duration:2000
                });
              }
            });

          } else {
            console.log('error submit!!');
            return false;
          }
        });
      },
      // 重置
      resetForm(formName) {
        this.$refs[formName].resetFields();
      }

    }
  };
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.lty-head-wrap{
  width: 1094px;
  margin: 0 auto;
  padding: 20px 0;
}
.lty-h-title{
  float: left;
  color: #666;
  font-size: 18px;
  padding-top: 22px;
}
.lty-logo{
  width: 122px;
  height: 43px;
  float: left;
  margin: 8px 30px 0 10px;
}
.lty-content-wrap{
  width: 100%;
  margin-top: 60px;
  background-color:#fff;
}
.lty-content{
  width: 100%;
  height: 600px;
  background:url('./bg.jpg');
}
.lty-content-title{
  height: 60px;
  line-height: 60px;
  border-bottom: 1px solid #d8d8d8;
  text-align: center;
  color: #409EFF;
}

.lty-li{
  width:370px;
  overflow: hidden;
}
.lty-li-l{
  width: 80px;
  float: left;
}
.lty-li-r{
  float: left;
}
.lty-cnt-center{
  width: 1094px;
  height: 600px;
  margin: 0 auto;
}
.lty-cnt-cr{
  opacity:0.9;
  float: left;
  background-color: #fff;
  width: 460px;
  overflow: hidden;
  margin: 130px 317px 0 290px;
  border-radius: 8px;
}
.lty-welcom,
.lty-register {
  text-align: center;
  padding: 25px;
  font-size: 20px;
}
.lty-footer{
  color: #666;
  font-size: 14px;
  text-align: center;
  padding-top: 30px;
}
.el-select {
  width: 274px;
}   
.remind {
  text-align: center;
  color: #3a8ee6;
}
.demo-ruleForm {
  margin-right: 56px;
}
</style>
