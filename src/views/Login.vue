<template>
  <div class="user_form">
    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <span>登录</span>
      </div>
      <div >
        <el-form ref="loginForm" :model="loginForm" :rules="rules" >
          <el-form-item label="邮箱" prop="email">
            <el-input  type="text" v-model="loginForm.email" placeholder="邮箱"></el-input>
          </el-form-item>
          <el-form-item label="密码" prop="password">
            <el-input type="password" v-model="loginForm.password" placeholder="密码" @keyup.enter="login('loginForm')"></el-input>
          </el-form-item>
          <p>
            <el-button type="primary" :disabled="btnDisabled" size="medium" style="width:100%" @click="login('loginForm')">登录</el-button>
          </p>
        </el-form>
        <div style="text-align: center;padding:0 2px 15px;">
          <div style="float:right">
            <el-link type="primary"  :underline="false" href="/#/register">注册</el-link>
          </div>
          <div style="float:left">
            <el-link type="primary"  :underline="false" href="/#/forgotPassword">忘记密码 ?</el-link>
          </div>
        </div>

      </div>
    </el-card>
  </div>
</template>
<script>
  export default {
    data() {
      return {
        btnDisabled: false,
        alertMsg: '',
        alertType: 'info',
        alertStyle: {
          display: 'none'
        },
        loginForm: {
          email: '',
          password: ''
        },
        rules: {
          email: [
            {required: true, message: '请输入邮箱', trigger: 'blur' },
            {type: 'email', message: '请输入正确的邮箱地址', trigger: ['blur']},
          ],
          password: [
            {required: true, message: '请输入密码', trigger: 'blur' },
          ],
        }
      }
    },
    methods: {
      login(formName) {
        this.$refs[formName].validate((valid) => {
          if (valid) {
            let self = this
            self.btnDisabled = true;
            this.axios.post('/user/login', {
              email: this.loginForm.email,
              password: this.loginForm.password,
            }).then(function (response) {
              if (response.data.code === 0) {
                self.$message({
                  showClose: true,
                  message: '登录成功',
                  type: 'success',
                  duration: 800,
                  onClose() {
                    self.btnDisabled = false;
                    self.GLOBAL.setToken(response.data.data.token);
                    self.$router.push('/#/');
                  }
                });

              } else if(response.data.code === 1029) {
                console.log(response)
                let message = '';
                for(let key in response.data.errors) {
                  message += response.data.errors[key]
                }
                self.$message({
                  showClose: true,
                  message: message,
                  type: 'warning',
                  duration: 1000,
                  onClose() {
                    self.btnDisabled = false
                  }
                });
              }
            }).catch(function (error) {
              console.log(error);
            });
          } else {
            console.log('error submit!!');
            return false;
          }
        })

      },

    },
    mounted: function() {
      // this.GLOBAL.remoteCheckLogin()
      this.GLOBAL.checkLogin()
    }
  }
</script>
<style>
  .el-form-item__label{display:none}
  .el-form-item__error{
    top:1em;
    right:10px;
    left:auto
  }
  .el-form-item{
    margin-bottom:14px;
  }
</style>
