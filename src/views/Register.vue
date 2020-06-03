<template>
      <div class="user_form">
        <el-card class="box-card">
          <div slot="header" class="clearfix">
            <span>注册</span>
          </div>
          <div >
            <div style="margin-bottom:14px">
              <el-alert
                      :title="alertMsg"
                      :type="alertType" :style="alertStyle">
              </el-alert>
            </div>
            <el-form ref="registerForm" :model="registerForm" :rules="rules" >
              <el-form-item label="用户名" prop="username">
                <el-input v-model="registerForm.username" type="text" placeholder="用户名"></el-input>
              </el-form-item>
              <el-form-item label="email" prop="email">
                <el-input v-model="registerForm.email" type="email" placeholder="Email"></el-input>
              </el-form-item>
              <el-form-item label="密码" prop="password">
                <el-input v-model="registerForm.password" type="password" show-password placeholder="密码" ></el-input>
              </el-form-item>
              <el-form-item label="确认密码" prop="password_confirmation">
                <el-input v-model="registerForm.password_confirmation" type="password" show-password placeholder="确认密码"></el-input>
              </el-form-item>
            </el-form>
              <p>
              <el-button type="primary" size="medium" :disabled="btnDisabled" @click="register('registerForm')" style="width:100%">注册</el-button>
            </p>
            <div style="text-align: center;padding:0 2px 15px;">
              <div style="float:right">
                <el-link type="primary"  :underline="false" href="/#/login">返回登录</el-link>
              </div>
              <div style="float:left">
                <el-link type="primary"  :underline="false" href="/#/forgotPassword"></el-link>
              </div>
            </div>
          </div>
        </el-card>
      </div>
</template>
<script>
  export default {
    data() {
      let passwordConfirm = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请再次输入密码'));
        } else if (value !== this.registerForm.password) {
          callback(new Error('两次输入密码不一致!'));
        } else {
          callback();
        }
      };
      return {
        btnDisabled: false,
        alertMsg: '',
        alertType: 'info',
        alertStyle: {
          display: 'none'
        },
        registerForm: {
          username: '',
          email: '',
          password: '',
          password_confirmation: ''
        },

        rules: {
          username: [
            {required: true, message: '请输入用户名'},
            { min: 3, max: 12, message: '长度在 3 到 12 个字符', trigger: 'blur' }
          ],
          email: [
            {required: true, message: '请输入email'},
            {type: 'email', message: '请输入正确的邮箱地址', trigger: ['blur']},
          ],
          password: [
            {required: true, message: '请输入密码'},
            { min: 3, max: 20, message: '长度在 6 到 20 个字符', trigger: 'blur' },
          ],
          password_confirmation: [
            { validator: passwordConfirm, trigger: 'blur' }
          ],
        }

      }
    },
    methods: {
      register(formName) {
        this.$refs[formName].validate((valid) => {
          if (valid) {
            let self = this
            self.btnDisabled = true;
            this.axios.post('/user/store', {
              username: self.registerForm.username,
              email: self.registerForm.email,
              password: self.registerForm.password,
              password_confirmation: self.registerForm.password_confirmation
            }).then(function (response) {
              if (response.data.code === 0) {
                self.$message({
                  showClose: true,
                  message: '注册成功',
                  type: 'success',
                  duration: 1000,
                  onClose() {
                    self.btnDisabled = false,
                      self.$router.push({name: 'login'});
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
        });
      }
    },
    beforeCreate: function() {
      this.GLOBAL.checkLogin()
      this.registerForm.email = ''
    },   
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
