<template>
  <div>
    <van-nav-bar title="用户注册" left-text="返回" left-arrow @click-left="onClickLeft"/>
    <van-cell-group>
      <van-field
        v-model="username"
        required
        clearable
        label="用户名"
        placeholder="请输入用户名"
        :error-message="usernameErrorMsg"
      />
      <van-field
        v-model="password"
        type="password"
        label="密码"
        placeholder="请输入密码"
        required
        :error-message="passwordErrorMsg"
      />
    </van-cell-group>
    <div class="button">
      <van-button type="primary" size="large" @click="register" :loading="loading">马上注册</van-button>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import URL from "@/serviceAPI.config.js";
import { Toast } from "vant";
export default {
  data() {
    return {
      username: "",
      password: "",
      loading: false, //是否开启用户注册的Loading状态
      usernameErrorMsg: "", //当用户名出现错误时的提示信息
      passwordErrorMsg: "" //当密码出现错误时的提示信息
    };
  },
  methods: {
    onClickLeft() {
      this.$router.go(-1);
      Toast("返回");
    },
    register() {
      this.checkForm() && this.axiosRegister();
    },
    axiosRegister() {
      this.loading = true;
      axios({
        url: URL.registerUser,
        method: "post",
        data: {
          userName: this.username,
          password: this.password
        }
      })
        .then(response => {
          if (response.data.code == 200) {
            Toast.success("注册成功");
            this.$router.push("/");
          } else {
            Toast.fail("注册失败");
            this.loading = false;
          }
        })
        .catch(error => {
          Toast.fail("注册失败");
          this.loading = false;
        });
    },
    //****表单验证方法
    checkForm() {
      let result = true;
      if (this.username.length < 5) {
        this.usernameErrorMsg = "用户名不能少于5位";
        result = false;
      } else {
        this.usernameErrorMsg = "";
      }
      if (this.password.length < 6) {
        this.passwordErrorMsg = "密码不能少于6位";
        result = false;
      } else {
        this.passwordErrorMsg = "";
      }

      return result;
    }
  }
};
</script>

<style scoped>
.button {
  padding: 10px;
}
</style>