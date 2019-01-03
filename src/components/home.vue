<template>
  <el-container class="container">
    <!-- 头部 -->
    <el-header>
      <el-row>
        <el-col :span="3">
          <img src="../assets/logo.png">
        </el-col>
        <el-col :span="20" class="middleHead">
          <h2>电商后台管理系统</h2>
        </el-col>
        <el-col :span="1">
          <a href="#" @click="handleLogout()">退出</a>
        </el-col>
      </el-row>
    </el-header>
    <el-container>
      <!-- 侧边栏 -->
      <el-aside width="200px" style="background-color: rgb(238, 241, 246)">
        <el-menu router default-active="2" unique-opened>
          <el-submenu index="1">
            <template slot="title">
              <i class="el-icon-message"></i>用户管理
            </template>
            <el-menu-item index="users">用户列表</el-menu-item>
          </el-submenu>
          <el-submenu index="2">
            <template slot="title">
              <i class="el-icon-menu"></i>权限管理
            </template>
            <el-menu-item index="2-1">角色列表</el-menu-item>
            <el-menu-item index="2-2">权限列表</el-menu-item>
          </el-submenu>
          <el-submenu index="3">
            <template slot="title">
              <i class="el-icon-setting"></i>商品管理
            </template>
            <el-menu-item index="3-1">商品列表</el-menu-item>
            <el-menu-item index="3-2">分类参数</el-menu-item>
            <el-menu-item index="3-3">商品分类</el-menu-item>
          </el-submenu>
          <el-submenu index="4">
            <template slot="title">
              <i class="el-icon-message"></i>订单管理
            </template>
            <el-menu-item index="4-1">订单列表</el-menu-item>
          </el-submenu>
          <el-submenu index="5">
            <template slot="title">
              <i class="el-icon-message"></i>数据统计
            </template>
            <el-menu-item index="5-1">数据报表</el-menu-item>
          </el-submenu>
        </el-menu>
      </el-aside>
      <!-- 内容区域 -->
      <el-main>
        <router-view></router-view>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  // 设置首页权限验证
  beforeCreate() {
    if (!localStorage.getItem("token")) {
      this.$router.push({
        name: "login"
      });
      this.$message.warning("请先登录");
    }
  },

  methods: {
    // 退出登录功能
    handleLogout() {
      localStorage.clear();
      this.$router.push({
        name: "login"
      });
      // 退出成功
      this.$message.success("退出成功");
    }
  }
};
</script>

<style>
.container {
  height: 100%;
}
.el-header {
  background-color: #b3c0d1;
  color: #333;
  line-height: 60px;
}
.el-aside{
  color: white;
}
.middleHead {
  text-align: center;
}
.middleHead h2 {
  color: #f6f7f8;
}
</style>
