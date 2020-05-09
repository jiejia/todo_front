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
            <el-form ref="registerForm" :model="passwordForm" :rules="rules" >
              <el-form-item label="邮箱" prop="username">
                <el-input v-model="passwordForm.email" type="text" placeholder="用户名" disabled=""></el-input>
              </el-form-item>

              <el-form-item label="新密码" prop="password">
                <el-input v-model="passwordForm.password" type="password" show-password placeholder="密码"></el-input>
              </el-form-item>
              <el-form-item label="确认新密码" prop="password_confirmation">
                <el-input v-model="passwordForm.password_confirmation" type="password" show-password placeholder="确认密码"></el-input>
              </el-form-item>
            </el-form>
              <p>
              <el-button type="primary" size="medium" :disabled="btnDisabled" @click="register('registerForm')" style="width:100%">重 置</el-button>
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
        passwordForm: {
          username: '',
          email: '',
          password: '',
          password_confirmation: ''
        },

        rules: {
          username: [
            {required: true, message: '请输入用户名'},
            { min: 3, max: 12, message: '长度在 3 到 12 个字符', trigger: 'change' }
          ],
          email: [
            {required: true, message: '请输入email'},
            {type: 'email', message: '请输入正确的邮箱地址', trigger: ['blur', 'change']},
          ],
          password: [
            {required: true, message: '请输入密码'},
            { min: 3, max: 20, message: '长度在 6 到 20 个字符', trigger: 'change' },
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
