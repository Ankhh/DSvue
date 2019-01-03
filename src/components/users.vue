<template>
  <!-- 面包屑 -->
  <el-card class="box-card card">
    <el-breadcrumb separator="/">
      <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
      <el-breadcrumb-item>用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 搜索框 -->
    <el-row>
      <el-col>
        <el-input
          placeholder="请输入内容"
          clearable
          v-model="query"
          class="input-with-select search"
          @clear="getAllUser()"
        >
          <el-button slot="append" icon="el-icon-search" @click="searchUser()"></el-button>
        </el-input>
        <el-button type="success" plain @click="addUser()">添加用户</el-button>
      </el-col>
    </el-row>
    <!-- 
      表格
        el-table-column：每一列
        prop：每一行的数据名，来源于tableData数组中的对象值
    -->
    <el-table :data="tableData" style="width: 100%" height="400px">
      <el-table-column prop="id" label="#" width="80"></el-table-column>
      <el-table-column prop="username" label="用户名" width="110"></el-table-column>
      <el-table-column prop="email" label="邮箱" width="180"></el-table-column>
      <el-table-column prop="mobile" label="电话" width="160"></el-table-column>
      <el-table-column prop="create_time" label="创建日期" width="170">
        <template slot-scope="scope">{{scope.row.create_time | fmtDate}}</template>
      </el-table-column>
      <el-table-column prop="mg_state" label="用户状态" width="160">
        <template slot-scope="scope">
          <el-switch
            @change="changeStatus(scope.row)"
            v-model="scope.row.mg_state"
            active-color="#13ce66"
            inactive-color="#ff4949"
          ></el-switch>
        </template>
      </el-table-column>
      <el-table-column prop="date" label="操作" width="210">
        <template slot-scope="scope">
          <el-row>
            <el-button
              type="primary"
              icon="el-icon-edit"
              circle
              size="mini"
              @click="editUser(scope.row)"
            ></el-button>
            <el-button
              type="success"
              icon="el-icon-check"
              circle
              size="mini"
              @click="changeRoles(scope.row)"
            ></el-button>
            <el-button
              type="danger"
              icon="el-icon-delete"
              circle
              size="mini"
              @click="deleteUser(scope.row.id)"
            ></el-button>
          </el-row>
        </template>
      </el-table-column>
    </el-table>
    <!-- 分页 -->
    <el-pagination
      class="pagination"
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="pagenum"
      :page-sizes="[2,4,6,8]"
      :page-size="2"
      layout="total, sizes, prev, pager, next, jumper"
      :total="total"
    ></el-pagination>

    <!-- 添加用户表单 -->
    <el-dialog title="添加用户" :visible.sync="dialogFormVisibleAdd">
      <el-form :model="form">
        <el-form-item label="用户名" :label-width="formLabelWidth">
          <el-input v-model="form.username" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="密码" :label-width="formLabelWidth">
          <el-input v-model="form.password" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="邮箱" :label-width="formLabelWidth">
          <el-input v-model="form.email" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="电话" :label-width="formLabelWidth">
          <el-input v-model="form.mobile" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisibleAdd = false">取 消</el-button>
        <el-button type="primary" @click="handelAddUser()">确 定</el-button>
      </div>
    </el-dialog>
    <!-- 编辑用户表单 -->
    <el-dialog title="编辑用户" :visible.sync="dialogFormVisibleEdit">
      <el-form :model="form">
        <el-form-item label="用户名" :label-width="formLabelWidth">
          <el-input v-model="form.username" autocomplete="off" disabled></el-input>
        </el-form-item>
        <el-form-item label="邮箱" :label-width="formLabelWidth">
          <el-input v-model="form.email" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="电话" :label-width="formLabelWidth">
          <el-input v-model="form.mobile" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button>取 消</el-button>
        <el-button type="primary" @click="handelEditUser()">确 定</el-button>
      </div>
    </el-dialog>
    <!-- 分配角色表单 -->
    <el-dialog title="分配角色" :visible.sync="dialogFormVisibleRole">
      <el-form :model="form">
        <el-form-item label="用户名" :label-width="formLabelWidth">{{this.currUserName}}</el-form-item>
        <el-form-item label="角色" :label-width="formLabelWidth">
          <el-select v-model="currUserRoleId">
            <el-option label="请选择" :value="-1"></el-option>
            <el-option :label="v.roleName" :value="v.id" v-for="(v,i) in roles" :key="i"></el-option>
          </el-select>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisibleRole = false">取 消</el-button>
        <el-button type="primary" @click="setRole()">确 定</el-button>
      </div>
    </el-dialog>
  </el-card>
</template>

<script>
export default {
  data() {
    return {
      query: "",
      tableData: [],
      pagenum: 1,
      pagesize: 2,
      total: -1, //写一个不存在的数据,
      form: {
        username: "",
        password: "",
        email: "",
        mobile: ""
      },
      formLabelWidth: "80px",
      dialogFormVisibleAdd: false,
      dialogFormVisibleEdit: false,
      dialogFormVisibleRole: false,
      currUserRoleId: -1,
      roles: [],
      currUserName: "",
      currUserId: -1
    };
  },
  created() {
    this.getTableData();
  },
  methods: {
    // 获取表格数据
    async getTableData() {
      const AUTH_TOKEN = localStorage.getItem("token");
      this.$http.defaults.headers.common["Authorization"] = AUTH_TOKEN;
      const res = await this.$http.get(
        `users?query=${this.query}&&pagenum=${this.pagenum}&&pagesize=${
          this.pagesize
        }`
      );

      const {
        data: {
          data: { total, users },
          meta: { status, msg }
        }
      } = res;
      if (status === 200) {
        this.tableData = users;
        // console.log(this.tableData);
        this.total = total;
        this.$message.success(msg);
      }
    },
    // 分页相关的数据
    handleSizeChange(val) {
      console.log(`每页 ${val} 条`);
      this.pagesize = val;
      this.pagenum = 1;
      this.getTableData();
    },
    handleCurrentChange(val) {
      this.pagenum = val;
      console.log(`当前页: ${val}`);
      this.getTableData();
    },
    // 搜索框
    searchUser() {
      this.pagenum = 1;
      this.getTableData();
    },
    // 点击清空按钮时，重新获取所有数据
    getAllUser() {
      this.getTableData();
    },
    // 添加用户按钮
    addUser() {
      this.dialogFormVisibleAdd = true;
      this.form = {};
    },
    // 处理添加用户请求
    async handelAddUser() {
      const res = await this.$http.post("users", this.form);
      console.log(res);
      const {
        meta: { msg, status },
        data
      } = res.data;
      if (status === 201) {
        this.$message.success(msg);
        this.dialogFormVisibleAdd = false;
        this.getTableData();
      } else {
        this.$message.warning(msg);
      }
    },
    // 渲染+处理删除按钮
    deleteUser(id) {
      this.$confirm("此操作将永久删除该文件, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(async () => {
          const res = await this.$http.delete(`users/${id}`);
          const {
            meta: { msg, status }
          } = res.data;
          if (status === 200) {
            this.pagenum = 1;
            this.getTableData();
            this.$message.success(msg);
          }
        })
        .catch(() => {
          this.$message.warning("取消删除");
        });
    },
    // 渲染编辑按钮
    editUser(user) {
      this.form = user;
      console.log(this.form);
      this.dialogFormVisibleEdit = true;
    },
    // 处理编辑请求
    async handelEditUser() {
      const res = await this.$http.put(`users/${this.form.id}`, {
        email: this.form.email,
        mobile: this.form.mobile
      });
      // console.log(res);
      const {
        meta: { msg, status }
      } = res.data;
      if (status === 200) {
        this.dialogFormVisibleEdit = false;
        this.$message.success(msg);
        this.getTableData();
      }
    },
    // 修改用户状态
    async changeStatus(user) {
      const res = await this.$http.put(
        `users/${user.id}/state/${user.mg_state}`
      );
    },
    // 渲染分配角色页面
    async changeRoles(user) {
      this.currUserId = user.id;
      this.currUserName = user.username;
      // 获取所有的角色
      const res = await this.$http.get(`roles`);
      this.roles = res.data.data;
      // 根据用户id查询用户信息
      const res2 = await this.$http.get(`users/${user.id}`);
      this.currUserRoleId = res2.data.data.rid;
      this.dialogFormVisibleRole = true;
    },
    // 处理角色分配请求
    async setRole() {
      const res = await this.$http.put(`users/${this.currUserId}/role`, {
        rid: this.currUserRoleId
      });
      this.dialogFormVisibleRole = false;
    }
  }
};
</script>

<style>
.card {
  height: 100%;
}
.search {
  margin-top: 20px;
  width: 400px;
}
.pagination {
  margin-top: 20px;
}
</style>
