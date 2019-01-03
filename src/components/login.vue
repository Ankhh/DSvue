<template>
  <div class="box">
    <el-form label-position="top" label-width="80px" :model="formdata" class="form">
      <h2>用户登录</h2>
      <el-form-item label="用户名">
        <el-input v-model="formdata.username" name="username"></el-input>
      </el-form-item>
      <el-form-item label="密码">
        <el-input v-model="formdata.password" name="password"></el-input>
      </el-form-item>
      <el-button type="primary" class="btn" @click.prevent="handleLogin()">登录</el-button>
    </el-form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      formdata: {
        username: "",
        password: ""
      }
    };
  },
  methods: {
    async handleLogin() {
      const res = await this.$http.post("login", this.formdata);
      console.log(res);
      const {
        data: {
          data,
          meta: { msg, status }
        }
      } = res;
      if (status === 200) {
        // 保存token的值
        localStorage.setItem("token", data.token);
        this.$router.push({
          name: "home"
        });
      } else {
        this.$message.error(msg);
      }
    }
  }
};
</script>

<style>
.box {
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #324152;
}
.form {
  width: 600px;
  padding: 20px;
  background-color: #ffffff;
  border-radius: 8px;
}
.btn {
  width: 100%;
}
</style>
