<template>
  <div class="home">
    <div id="left-bar">
      <div class="left-bar-top">
        <el-dropdown  @command="userCommand">
          <span class="el-dropdown-link">
            <div class="avatar"><img alt="Vue logo" src="../assets/avatar.png"> <span>jiejia</span></div>
          </span>
          <el-dropdown-menu slot="dropdown">
            <el-dropdown-item command="sync">同步</el-dropdown-item>
            <el-dropdown-item command="setting">设置</el-dropdown-item>
            <el-dropdown-item command="logout">退出登录</el-dropdown-item>
          </el-dropdown-menu>
        </el-dropdown>
      </div>
      <div class="scrollbar">
        <div style="padding:0 10px;">
          <template>
            <el-tabs v-model="leftBarTab">
              <el-tab-pane label="清单" name="list">
                <div class="category-list">
                  <ul>
                    <li v-for="item in categories">
                      <el-link href="#" :underline="false" @click.native="selectCategory($event, item)"><i class="el-icon-s-grid" ></i> {{item.name}} <span class="category-meta">
                        <i class="el-icon-s-flag" :style="'color:' + item.color + ';font-size:12px'"></i></span><span  class="category-meta">{{item.count}}</span></el-link>
                    </li>
                  </ul>
                  <ul class="add-category">
                    <li><a href="#" @click="categoryForm.visible = true"><i class="el-icon-folder-add"></i> 添加清单</a></li>
                  </ul>
                </div>

              </el-tab-pane>
              <el-tab-pane label="标签" name="tags">

              </el-tab-pane>
            </el-tabs>
          </template>
        </div>


      </div>
    </div>
    <div id="main-content">
      <h2>{{current.categoryName}}</h2>
      <div class="task-title">
        <el-input class="task-title-input"
          :placeholder="'添加任务至 “' + current.categoryName + '”，回车即可保存'"
          v-model="taskTitle"
          @focus="taskTitleFocus($event)" @blur="taskTitleBlur($event)" @keyup.enter.native="addTask()">
        </el-input>
      </div>
      <div class="task-title-list">
        <ul class="unfinished">
          <li @click="selectTask($event, task)" v-for="task in tasks" v-if="task.status==0" :key="task.id">
            <el-checkbox @change="triggerTask($event, task)" class="title-checkbox"></el-checkbox>
            <div class="title-wrap">
              <div class="title-text">
                <div contenteditable="true" v-on:keyup.enter="save"  v-on:blur="listTaskTitleBlur($event, task)" class="title">{{task.title}}</div>
              </div>
            </div>
          </li>
        </ul>
        <el-collapse accordion class="finished-collapse">
          <el-collapse-item>
            <template slot="title" style="padding-left:30px">
              已完成
            </template>
            <ul class="finished">
              <li @click="selectTask($event, task)" v-for="task in tasks" v-if="task.status==1" :key="task.id">
                <el-checkbox @change="triggerTask($event, task)" class="title-checkbox"></el-checkbox>

                <div contenteditable="true" class="title" v-on:blur="listTaskTitleBlur($event, task)">{{task.title}}</div>
              </li>
            </ul>
          </el-collapse-item>
        </el-collapse>
      </div>

    </div>
    <div id="right-content">
      <div class="task-datetime"></div>
      <div class="task-detail" v-bind:style=" {visibility: current.taskFormVisible }">
        <div class="title"><el-input v-model="current.taskName" placeholder="请输入内容" v-on:blur="titleChangeBlur($event)"></el-input></div>
        <div class="content">
          <el-input
            type="textarea"
            :rows="21"
            placeholder="请输入内容"
            v-model="current.taskContent" v-on:blur="titleChangeBlur($event)">
          </el-input>

        </div>
      </div>
    </div>

    <el-dialog title="添加清单" :visible.sync="categoryForm.visible" :modal="false" style=" box-shadow: 0 4px 8px rgba(0, 0, 0, .12), 0 0 12px rgba(0, 0, 0, .04)">
      <el-form ref="categoryForm" :model="categoryForm" :rules="rules">
        <el-form-item label="名称" :label-width="categoryForm.width">
          <el-input maxlength="10" v-model="categoryForm.name" autocomplete="off" placeholder="名称"></el-input>
        </el-form-item>
        <el-form-item label="颜色" :label-width="categoryForm.width">
          <el-color-picker
            v-model="categoryForm.color"
            :predefine="categoryForm.predefineColors">
          </el-color-picker>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="categoryForm.visible = false">关 闭</el-button>
        <el-button type="primary" @click="addCategory('categoryForm')" :disabled="categoryForm.btnDisabled">保 存</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
  // @ is an alias to /src

  export default {
    data(){
      return {
        categoryFormVisible: false,
        taskTitle: '',
        tastList: [],
        leftBarTab: 'list',
        current: {
          categoryName: '',
          taskId: 0,          
          taskName: '',
          taskContent: '',
          categoryId: 1,
          taskFormVisible: 'hidden',
        },
        categoryForm: {
          name: '',
          color: '#ff4501',
          visible: false,
          width: '100px',
          btnDisabled: false,
          predefineColors: [
            '#ff4500',
            '#ff8c00',
            '#ffd700',
            '#90ee90',
            '#00ced1',
            '#1e90ff',
            '#c71585',
            '#c71515'
          ]
        },
        rules: {
          name: [
            {required: true, message: '请输入清单名'},
            { min: 1, max: 10, message: '长度在 3 到 10 个字符', trigger: 'change' }
          ],
          color: [
            {required: true, message: '请选择颜色'},
          ],
        },
        categories: [
        ],
        rawCategories: [

        ],
        tasks: [
          {title: '没有任务', content: '', status: 1},
        ],

      }
    },
    name: 'Home',
    components: {
    },
    methods: {
      selectCategory: function(e, category) {
        let list = e.currentTarget.parentElement.parentElement.getElementsByTagName('a');
        let i;
        for (i = 0; i < list.length; i++) {
          list[i].className = "";
        }
        e.currentTarget.className = 'focus';
        // e.currentTarget.getElementsByClassName('title-checkbox').checked = true
        this.current.taskFormVisible = 'hidden'
        this.current.categoryName = category.name;
        this.current.categoryId = category.id;
        category.status = true

        window.localStorage.setItem('currentCategory', category.name)
        window.localStorage.setItem('currentCategoryId', category.id)

        this.getTasks()
      },
      selectTask: function(e, task) {
        let list = document.getElementsByClassName('task-title-list')[0].getElementsByTagName('li');
        let i;
        for (i = 0; i < list.length; i++) {
          // console.log(list[i]);
          list[i].className = "";
        }
        console.log(task)
        e.currentTarget.className = 'focus';
        this.current.taskFormVisible = 'visible'
        this.current.taskId = task.id;
        this.current.taskName = task.title;
        this.current.taskContent = task.content;
        // console.log(task)
      },
      titleChangeBlur: function(e) {
        return 
        let self = this
          this.axios.post('/task/create-or-update', {
            id: this.current.taskId,
            title: this.current.taskName,
            content: this.current.taskContent,
          }).then(function (response) {
          // console.log(response)
            if (response.data.code === 0) {
            } else if(response.data.code === 1029) {
              console.log(response)
              let message = '';
              for(let key in response.data.errors) {
                message += "<p>" + response.data.errors[key] + '<\p>'
              }
              self.$message({
                dangerouslyUseHTMLString: true,
                showClose: true,
                message: message,
                type: 'warning',
                duration: 1000,
                onClose() {
                }
              });
            }
            self.getTasks()
          }).catch(function (error) {
            console.log(error);
          });    
      },

      listTaskTitleBlur: function(e, task) {
          let self = this
          this.axios.post('/task/create-or-update', {
            id: this.current.taskId,
            title: this.current.taskName,
            content: this.current.taskContent,
          }).then(function (response) {
          // console.log(response)
            if (response.data.code === 0) {
            } else if(response.data.code === 1029) {
              console.log(response)
              let message = '';
              for(let key in response.data.errors) {
                message += "<p>" + response.data.errors[key] + '<\p>'
              }
              self.$message({
                dangerouslyUseHTMLString: true,
                showClose: true,
                message: message,
                type: 'warning',
                duration: 1000,
                onClose() {
                }
              });
            }
            self.getTasks()
          }).catch(function (error) {
            console.log(error);
          });    
      },

      triggerTask: function(e, task) {
        // console.log(e)
        if (e) {
          let self = this
          let message = '';
          
          if (task.status == 1) {
            status = 0;
            message = '还原已任务'
          } else {
            status = 1;
            message = '任务已完成'
          }

          this.axios.post('/task/create-or-update', {
            id: task.id,
            status: status,
          }).then(function (response) {
           // console.log(response)
            if (response.data.code === 0) {
              self.getTasks()  
              self.getCategories()
            } else if(response.data.code === 1029) {
              console.log(response)
              let message = '';
              for(let key in response.data.errors) {
                message += "<p>" + response.data.errors[key] + '<\p>'
              }
              self.$message({
                dangerouslyUseHTMLString: true,
                showClose: true,
                message: message,
                type: 'warning',
                duration: 1000,
                onClose() {
                }
              });
            }
          }).catch(function (error) {
            console.log(error);
          });    
          
        }
      },
      taskTitleFocus: function(e) {
        e.currentTarget.className = 'focus el-input__inner';
      },
      taskTitleBlur: function(e) {
        e.currentTarget.className = 'el-input__inner';
      },
      save: function(e) {

        if (e.charCode === 13) {
          
          e.preventDefault();
          return false;
        }

      },
      userCommand(command) {
        // console.log(command)
        // this.$message('click on item ' + command);
        if (command == 'logout') {
          // alert('logout')
          this.GLOBAL.clearToken()
          // this.$message('登出成功')
          this.$router.push('login')
        }
      },
      addCategory(formName) {
        this.$refs[formName].validate((valid) => {
          if (valid) {
            let self = this
            self.categoryForm.btnDisabled = true;
            console.log(self.categoryForm)
            this.axios.post('/task/category/create-or-update', {
              name: self.categoryForm.name,
              color: self.categoryForm.color,
            }).then(function (response) {
              // console.log(response)
              if (response.data.code === 0) {
                self.$message({
                  showClose: true,
                  message: '添加清单成功',
                  type: 'success',
                  duration: 500,
                  onClose() {
                    self.categoryForm.visible = false
                    self.categoryForm.btnDisabled = false;
                    self.categoryForm.name = ''
                    self.categoryForm.color = '#ff4501'
                  }
                });
                self.getCategories()
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
                    self.categoryForm.btnDisabled = false
                  }
                });
              } else if (response.data.code === 401) {
                this.$router.push({name: 'login'});
              }
            }).catch(function (error) {
              console.log(error);
            });
          } else {
            // console.log('error submit!!');
            // return false;
          }
        });
      },
      addTask: function() {
        let self = this
        this.axios.post('/task/create-or-update', {
          category_id: self.current.categoryId,
          title: self.taskTitle,
        }).then(function (response) {
          //console.log(response)
          if (response.data.code === 0) {
            self.$message({
              showClose: true,
              message: '添加任务成功',
              type: 'success',
              duration: 500,
              onClose() {
                self.getTasks()
              }
            });
            self.taskTitle = ''
            self.getCategories()
          } else if(response.data.code === 1029) {
            console.log(response)
            let message = '';
            for(let key in response.data.errors) {
              message += "<p>" + response.data.errors[key] + '<\p>'
            }
            self.$message({
              dangerouslyUseHTMLString: true,
              showClose: true,
              message: message,
              type: 'warning',
              duration: 1000,
              onClose() {
              }
            });
          }
        }).catch(function (error) {
          console.log(error);
        });
      },
      getCategories: function (){
        let self = this
        // console.log(this.categories )
        this.GLOBAL.axios.post('/task/category/list-or-search', {
        }).then(function (response) {
           // console.log(response)
          self.categories.splice(0);
          response.data.data.records.forEach((item,index,array)=>{
            self.categories.push(item);
              // console.log(item);
              if (item.is_default == 1) {
                self.current.categoryId = item.id
                self.current.categoryName = item.name
              }
          })

          if (window.localStorage.getItem('currentCategory') != null) {
            self.current.categoryId = window.localStorage.getItem('currentCategoryId')
            self.current.categoryName = window.localStorage.getItem('currentCategory')
          }
          this.getTasks()

          // alert(self.current.categoryId)
          
        }).catch(function (error) {

        });
      },
      getTasks: function() {
        let self = this
        this.GLOBAL.axios.post('/task/list-or-search', {
          category_id: self.current.categoryId,
        }).then(function (response) {
          console.log(response)
          self.tasks.splice(0);
          response.data.data.records.forEach((item,index,array)=>{
            self.tasks.push(item);
          })
        }).catch(function (error) {
            // let message = '';
            // for(let key in response.data.errors) {
            //   message += "<p>" + response.data.errors[key] + '<\p>'
            // }
            self.$message({
              dangerouslyUseHTMLString: true,
              showClose: true,
              message: error,
              type: 'warning',
              duration: 1000,
              onClose() {
              }
            });
        });
      }
    },
    beforeCreate: function() {
      this.GLOBAL.checkLogin()
    },
    mounted: function() {
      this.getCategories()
      // this.current.categoryId = this.categories.
    }
  }
</script>
<style scoped lang="scss">
  .home{
    text-align: left
  }
  #left-bar{
    position:absolute;
    top:0;
    left:0;
    bottom:0;
    width:260px;
    background-color: rgba(44,66,118,.06);
    border-right: 1px solid rgba(0,0,0,.07);

  }
  #main-content{
    position:absolute;
    top:0;
    left:260px;
    right:36%;
    bottom:0;
    background-color: #fff;
    padding:24px;
  }
  #right-content{
    top:0;
    position:absolute;
    right:0;
    width:36%;
    bottom:0;
    border-left: 1px solid rgba(0,0,0,.06);
  }
  .category-list ul{
    list-style:none;
    padding:0;
    font-size:18px;
    margin:0 0 0;
  }
  .category-list
  {
  }
  .category-list ul li{

  }
  .category-list ul li a span .category-meta{
    float:right;
    padding-right:10px;
    font-size:12px;
    line-height:25px;
    color:#999
  }
  .category-list ul li a,.category-list ul li a:visited{
    border-radius: 2px;
    display:block;
    width:100%;
    padding:9px 0;
    text-indent:5px;
    font-size:14px;
    height:25px;
    line-height:25px;
    font-weight:400;
    text-decoration: none;
    color:#262626;
  }
  .category-list ul li a:hover{
    background-color: #E9EDF5;
    color:#262626;
  }
  .category-list ul li a:focus{
    background-color: #DEE3F4;
    color:#262626;
    font-weight:bold;
  }
  .category-list ul li a.focus{
    background-color: #DEE3F4;
    color:#262626;
    font-weight:bold;
    text-decoration: none;
  }

  /** 滚动条 **/
  .scrollbar{
    position:absolute;
    top:70px;
    left:0;
    bottom:0;
    width:260px;
    overflow-y: scroll;
    /*border-top:1px solid #ccc;*/

  }
  .scrollbar::-webkit-scrollbar {
    /*滚动条整体样式*/
    width : 4px;  /*高宽分别对应横竖滚动条的尺寸*/
    height: 1px;
    background-color: rgba(0,0,0,.3);
    opacity: 1;
  }
  .scrollbar::-webkit-scrollbar-thumb {
    /*滚动条里面小方块*/
    box-shadow   : inset 0 0 7px rgba(0,0,0,.3);
    background   :rgba(0,0,0,.3);
  }
  .scrollbar::-webkit-scrollbar-track {
    /*滚动条里面轨道*/
    background   : #ccc;
  }

  #main-content h2{
    padding:1px 0 15px;
    margin:0;
  }

  .task-title-list ul{
    list-style:none;
    margin:25px 0 10px;
    padding:0;
  }
  .task-title-list ul .title-text{
    white-space: nowrap;
    word-break: keep-all;

  }
  .task-title-list ul .title-wrap {

  }
</style>
<style lang="scss">
  .task-title .el-input__inner{
    border:1px solid #fff;
    background-color: rgba(44,66,118,.06);
  }
  .task-title .focus{
    border:1px solid rgba(75,111,222,.54);
  }

  /** 任务列表 **/
  .task-title-list .title {
       width:calc(100% - 30px);
       float:right;
       border:none;
       height:37px;
       line-height:37px;
     }
  .task-title-list .title[contenteditable]:focus{outline: none;}
  .task-title-list .title-checkbox{
    width:30px;
    float:left
  }
  .task-title-list .title:focus{
    border:none;
  }
  .task-title-list .el-input__inner{
    border:1px solid #fff;
  }
  .task-title-list ul.unfinished{
    margin-bottom:0;
  }
  .task-title-list ul li{
    height:37px;
    line-height:37px;
    padding-left:10px;
    border-radius: 2px;
    cursor: pointer;
    border-bottom:1px solid #E9EDF5;
  }
  .task-title-list ul.unfinished li:hover,
  .task-title-list ul.finished li:hover{
    background-color: #E9EDF5;
  }
  .task-title-list ul.unfinished li.focus,
  .task-title-list ul.finished li.focus{
    background-color: #DEE3F4;
  }

  /** 已完成 **/
  .task-title-list ul.finished{
    margin:0;
    color:rgba(0,0,0,.36);
  }

  .task-title-list .finished-collapse .el-collapse-item__header{
    font-size:12px;
    color:rgba(0,0,0,.36);
    padding-left:40px;
    height:37px;
    line-height:37px;
    font-weight:600;

  }
  .task-title-list .el-collapse{
    border-top:none;
  }
  .task-title-list .el-collapse-item__wrap{
    border-bottom:0;
  }
  .task-title-list .el-checkbox__input.is-checked .el-checkbox__inner, .el-checkbox__input.is-indeterminate .el-checkbox__inner
  {
    background-color: #DCDFE6;
    border-color: #DCDFE6;

  }
  /** 任务详情 **/
  .task-datetime{
    height:40px;
    border-bottom:1px solid rgba(0, 0, 0, 0.06);
    padding:15px
  }
  .task-detail{
    padding:24px;
  }
  .task-detail .title{
    height:35px;
    line-height:35px;
    font-size:16px;
    margin-bottom:20px;

  }
  .task-detail .content{
    height:35px;
    line-height:35px;
    font-size:16px;
  }

  /** 选项卡 **/
  #left-bar .el-tabs__item{
    padding:0 0;
  }
  /** 左侧边栏顶部信息 **/
  .left-bar-top{
    height:70px;
    z-index:1000;
    padding:20px 10px 10px;
  }
  .left-bar-top .avatar span{
    display:block;
    vertical-align: middle;
    line-height:35px;
    height:35px;
    float:left;
    padding-left:10px;
    cursor: pointer;
  }
  .left-bar-top .avatar img{
    float:left;
    height:35px;
    width:35px;
    border-radius: 35px;
    cursor: pointer;
  }
  .el-dropdown-menu__item{
    width:120px;
  }
  .el-tabs__nav{
    width:100%;
  }
  #left-bar .el-tabs__item{
    width:50%;
    text-align: center;
  }

  /** 添加清单 **/
  .category-list ul.add-category{
    margin-top:4px;
  }
  .category-list  ul.add-category li:hover{
    background-color: #E9EDF5;
  }
</style>
