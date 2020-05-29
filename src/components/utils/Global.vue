<template>

</template>

<script>
  import axios from 'axios';
  import moment from 'moment';
  import router from '../../router'

  // http request 拦截器
  axios.interceptors.request.use(
    config => {
      let that = this
      config.headers.Authorization = global.getToken()
      // alert(config.headers.Authorization)
      if (! config.headers.Authorization) {
        // router.push({name: 'login'});
      }
      return config;
    });

  // http response 拦截器
  axios.interceptors.response.use(
    response => {
      // 无权限直接跳转至登录页面
      if (response.data.code == '401') {
          router.push({name: 'login'});        
      }

      return response;
    },
    error => {
      if (error.response) {
        switch (error.response.status) {
          case 401:
            // 返回 401 清除token信息并跳转到登录页面
            // alert(401)
            router.push({name: 'login'});
        }
      }
      return Promise.reject(error) // 返回接口返回的错误信息
  });

  let global = {
    debug: true,
    axios,
    moment,
    state: {
      token: null,
      categories: [
        // {
        //     name: '默认分类',
        //     id: 0
        // }
      ],
      tasks: [

      ],
      category: {
        id: 0,
        title: '默认分类'
      },
      tokenName: 'accessToken',
      currentCategoryName : 'currentCategoryName',
      currentCategoryIdName : 'currentCategoryIdName',
    },
    setToken(token) {
      // if (this.debug) console.log('setMessageAction triggered with', token)
      this.state.message = token
      window.localStorage.setItem(this.state.tokenName, token)
    },
    clearToken() {
      // if (this.debug) console.log('clearMessageAction triggered')
      window.localStorage.removeItem(this.state.tokenName)
    },
    getToken() {
      this.state.token = window.localStorage.getItem(this.state.tokenName)
      ///alert(this.state.token);
      if (this.state.token === null) {
        return false;
      }
      return this.state.token;
    },
    resetCategories() {
      this.state.categories = [];
    },
    resetTasks() {
      this.state.tasks = []
    },
    getTasks: function(id, categoryTitle, keyword) {
      let self = this;

      this.state.category.title = categoryTitle
      this.state.category.id = id;
      console.log(this.state.category.id);
      self.axios.post('/task/list-or-search', {
        category_id: id,
        s: keyword
      }).then(function (response) {
        self.state.tasks.splice(0);
        response.data.data.records.forEach((item,index,array)=>{
          //执行代码
          item.created_at = self.moment.unix(item.created_at).format('YYYY-MM-DD HH:mm:ss');
          item.deadline = self.moment.unix(item.deadline).format('YYYY-MM-DD HH:mm:ss');
          self.state.tasks.push(item);
        })
      });
    },
    getCategories: function (){
      this.axios.post('/task/category/list-or-search', {
      }).then(function (response) {
        self.state.categories.splice(0);
        response.data.data.records.forEach((item,index,array)=>{
          //执行代码
          self.state.categories.push(item);
          // console.log(item);
        })
      }).catch(function (error) {

      });
    },
    checkLogin: function() {
      let token = this.getToken()
      //  alert(token)
      if (token !== false) {
        this.axios.defaults.headers.common['Authorization'] = token
        this.axios.post('/user/check-login', {
          authorization: token
        }).then(function (response) {
          if (response.data.code === 0 && response.data.data.login === 1) {
            router.push({name: 'Home'});
          } else if(response.data.code === 1029 || response.data.data.login == 0) {
                self.$message({
                  showClose: true,
                  message: "请先登录",
                  type: 'warning',
                  duration: 1000,
                  onClose() {
                    router.push({name: 'Login'});
                  }
                });            
          }
        }).catch(function (error) {
          console.log(error);
        });
      } else {

      }
    }
  }
  export default global
</script>

<style scoped>

</style>
