<template>
  <div id="app">

    <router-view/>
  </div>
</template>

<style lang="scss">
body{
  font-family: "Helvetica Neue",Helvetica,"PingFang SC","Hiragino Sans GB","Microsoft YaHei","微软雅黑",Arial,sans-serif;
  font-size:14px;

}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav {
  padding: 30px;

  a {
    font-weight: bold;
    color: #2c3e50;

    &.router-link-exact-active {
      color: #42b983;
    }
  }
}

.user_form{
  margin:50px auto 0;
  width:400px;
}
@media screen and (max-width: 468px) {
  .user_form {width: 300px}
}

.text {
  font-size: 14px;

}

.item {
  margin-bottom: 18px;
}

.clearfix:before,
.clearfix:after {
  display: table;
  content: "";
}
.clearfix:after {
  clear: both
}

.box-card {
  width:100%;
  text-align:left
}

/** 覆盖css **/
.el-card__header{
  padding: 15px 15px;
  font-size:16px
}
.el-dialog .el-dialog__header{
  padding:5px 20px;
  font-size:14px;
  line-height:40px;
  height:40px
}
.el-dialog .el-dialog__title{
  font-size:14px
}
.el-dialog__headerbtn{
  top:15px;font-size:16px
}
.el-dialog__body{
  padding:0px 20px 0px;
}.el-dialog__footer{
   padding:10px 20px 15px
 }
.el-menu-item, .el-submenu__title{

}
</style>
