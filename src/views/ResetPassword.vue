<template>
      <div class="user_form">
        <el-card class="box-card">
          <div slot="header" class="clearfix">
            <span>重置密码</span>
          </div>
          <div >
            <div style="margin-bottom:14px">
              <el-alert
                      :title="alertMsg"
                      :type="alertType" :style="alertStyle">
              </el-alert>
            </div>
            <el-form ref="resetPasswordForm" :model="resetPasswordForm" :rules="rules" >
              <el-form-item label="邮箱" prop="email">
                <el-input v-model="resetPasswordForm.email" type="text" placeholder="邮箱" disabled=""></el-input>
              </el-form-item>

              <el-form-item label="新密码" prop="password">
                <el-input v-model="resetPasswordForm.password" type="password" show-password placeholder="密码"></el-input>
              </el-form-item>
              <el-form-item label="确认新密码" prop="password_confirmation">
                <el-input v-model="resetPasswordForm.password_confirmation" type="password" show-password placeholder="确认密码"></el-input>
              </el-form-item>
            </el-form>
              <p>
              <el-button type="primary" size="medium" :disabled="btnDisabled" @click="resetPassword('resetPasswordForm')" style="width:100%">重 置</el-button>
            </p>
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
        } else if (value !== this.resetPasswordForm.password) {
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
        resetPasswordForm: {
          email: '',
          password: '',
          password_confirmation: ''
        },

        rules: {
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
      resetPassword(formName) {
        this.$refs[formName].validate((valid) => {
          if (valid) {
            let self = this
            self.btnDisabled = true;
            this.axios.post('/user/password-reset', {
              token: this.$route.query.token,
              email: this.$route.query.email,
              password: self.resetPasswordForm.password,
              password_confirmation: self.resetPasswordForm.password_confirmation
            }).then(function (response) {
              if (response.data.code === 0) {
                self.$message({
                  showClose: true,
                  message: '修改密码成功',
                  type: 'success',
                  duration: 1000,
                  onClose() {
                    self.btnDisabled = false; 
                    this.GLOBAL.clearToken()                
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
    mounted: function() {
      this.GLOBAL.checkLogin()
      // 检查参数是否正确
      this.resetPasswordForm.email = this.$route.query.email
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
