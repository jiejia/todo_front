<template>
      <div class="user_form">
        <el-card class="box-card">
          <div slot="header" class="clearfix">
            <span>忘记密码</span>
          </div>
          <div >
            <div style="margin-bottom:14px">
              <el-alert
                      :title="alertMsg"
                      :type="alertType" :style="alertStyle">
              </el-alert>
            </div>
            <el-form ref="forgetPasswordForm" :model="forgetPasswordForm" :rules="rules" >
              <el-form-item label="email" prop="email">
                <el-input v-model="forgetPasswordForm.email" type="email" placeholder="Email"></el-input>
              </el-form-item>
            </el-form>
              <p>
              <el-button type="primary" size="medium" :disabled="btnDisabled" @click="forgetPassword('forgetPasswordForm')" style="width:100%">发 送</el-button>
            </p>
            <div style="text-align: center;padding:0 2px 15px;">
              <div style="float:right">
                <el-link type="primary"  :underline="false" href="/#/login">返回登录</el-link>
              </div>
              <div style="float:left">
                <el-link type="primary"  :underline="false" href="/#/register">注册</el-link>
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
        forgetPasswordForm: {
          email: '',
        },

        rules: {
          email: [
            {required: true, message: '请输入email'},
            {type: 'email', message: '请输入正确的邮箱地址', trigger: ['blur']},
          ],
        }

      }
    },
    methods: {
      forgetPassword(formName) {
        this.$refs[formName].validate((valid) => {
          if (valid) {
            let self = this
            self.btnDisabled = true;
            let token = self.GLOBAL.getToken()
            this.axios.post('/user/send-password-email', {
              email: this.forgetPasswordForm.email,
            }).then(function (response) {
              if (response.data.code === 0) {
                self.$message({
                  showClose: true,
                  message: '发送成功',
                  type: 'success',
                  duration: 1000,
                  onClose() {
                    self.btnDisabled = false;
                  }
                });

              } else if(response.data.code === 1029) {
                let message = '';
                for(let key in response.data.errors) {
                  message += response.data.errors[key]
                }
                self.$message({
                  showClose: true,
                  message: message,
                  type: 'warning',
                  duration: 2000,
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
       this.GLOBAL.checkLogin()
    },
    beforeMount() {
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
