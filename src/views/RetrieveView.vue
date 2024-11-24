<template>
  <div class="wrapper">
    <div class="main-content-agile">
      <div class="login-form">
        <h2 class="title">找回密码</h2>
        <el-form :model="user" :rules="rules" ref="retrieveForm" label-width="0px">
          <el-form-item prop="username">
            <el-input
                placeholder="请输入账号"
                v-model="user.username"
                clearable
            />
          </el-form-item>
          <el-form-item prop="newPassword">
            <el-input
                placeholder="请输入新密码"
                v-model="user.newPassword"
                type="password"
                show-password
            />
          </el-form-item>
          <el-form-item prop="confirmPassword">
            <el-input
                placeholder="确认新密码"
                v-model="user.confirmPassword"
                type="password"
                show-password
            />
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="submitForm">重置密码</el-button>
          </el-form-item>
        </el-form>
        <el-button @click="goBack"  style="margin-top: 20px;">返回</el-button>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from "vue";
import { ElMessage } from "element-plus";
import axios from "axios";
import router from '@/router/index.js'


export default {
  setup() {
    const user = ref({
      username: "",
      newPassword: "",
      confirmPassword: "",
    });

    const retrieveForm = ref(null);

    const rules = {
      username: [{ required: true, message: "请输入账号", trigger: "blur" }],
      newPassword: [
        { required: true, message: "请输入新密码", trigger: "blur" },
        { min: 6, message: "密码长度不能少于6位", trigger: "blur" },
      ],
      confirmPassword: [
        {
          validator: (rule, value, callback) => {
            if (!value) {
              callback(new Error("请再次输入新密码"));
            } else if (value !== user.value.newPassword) {
              callback(new Error("两次密码输入不一致"));
            } else {
              callback();
            }
          },
          trigger: "blur",
        },
      ],
    };

    const submitForm = async () => {
      retrieveForm.value.validate(async (valid) => {
        if (valid) {
          const userData = {
            userName: user.value.username,
            password: user.value.newPassword, // 重置密码时使用 newPassword
          };
          try {
            const response = await axios.post("/api/user/retrieve", userData);
            if (response.data.code === 200) {
              ElMessage.success("密码重置成功，请重新登录！");
              router.push("/login");
            } else {
              ElMessage.error(`密码重置失败：${response.data.message}`);
            }
          } catch (error) {
            console.error("重置密码失败：", error);
            ElMessage.error("重置密码失败，请稍后再试！");
          }
        }
      });
    };
    const goBack = () => {
      router.back();
    };

    return {
      user,
      rules,
      retrieveForm,
      submitForm,
      goBack,
    };
  },
};
</script>

<style scoped>
.wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background: linear-gradient(135deg, #667eea, #764ba2) repeat;
}

.header-w3l h1 {
  color: #fff;
  font-size: 2.5em;
  font-weight: 600;
}

.main-content-agile {
  width: 100%;
  display: flex;
  justify-content: center;
}

.login-form {
  width: 75%;
  max-width: 800px;
  background-color: #fff;
  padding: 40px;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  margin: auto;
}

.title {
  color: #333;
  text-align: center;
  margin-bottom: 30px;
}

.form-group {
  margin-bottom: 20px;
}

.form-group input {
  width: 96%;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 16px;
  margin-bottom: 20px;
  height: 30px;
}

.form-group button {
  width: 99%;
  padding: 10px;
  border: none;
  border-radius: 4px;
  background-color: #5a67d8;
  color: #ffffff;
  font-size: 16px;
  cursor: pointer;
  height: 50px;
}
</style>



