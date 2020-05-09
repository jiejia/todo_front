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
            <el-form ref="registerForm" :model="registerForm" :rules="rules" >
              <el-form-item label="email" prop="email">
                <el-input v-model="registerForm.email" type="email" placeholder="Email"></el-input>
              </el-form-item>
            </el-form>
              <p>
              <el-button type="primary" size="medium" :disabled="btnDisabled" @click="register('registerForm')" style="width:100%">发 送</el-button>
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

          email: [
            {required: true, message: '请输入email'},
            {type: 'email', message: '请输入正确的邮箱地址', trigger: ['blur']},
          ],
          password_confirmation: [
            { validator: passwordConfirm, trigger: 'change' }
          ],
        }

      }
    },
    methods: {
      register(formName) {

      }
    },
    mounted: function() {
      this.GLOBAL.remoteCheckLogin()
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
