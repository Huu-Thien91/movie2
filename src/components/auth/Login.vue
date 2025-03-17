<script setup>
import { ref } from 'vue';
import { useRouter } from 'vue-router';
const loginForm = ref({
  email: '',
  password: ''
});

const loginErrors = ref({});
const router = useRouter();

const validateLoginForm = () => {
  loginErrors.value = {};

  if (!loginForm.value.email) {
    loginErrors.value.email = 'Email là bắt buộc';
  } else if (!/\S+@\S+\.\S+/.test(loginForm.value.email)) {
    loginErrors.value.email = 'Email không hợp lệ';
  }

  if (!loginForm.value.password) {
    loginErrors.value.password = 'Mật khẩu là bắt buộc';
  }

  return Object.keys(loginErrors.value).length === 0;
};

const submitLoginForm = () => {
  if (validateLoginForm()) {
    console.log('Form đăng nhập đã được gửi:', loginForm.value);
    // Gửi dữ liệu tới server hoặc xử lý đăng nhập ở đây
    router.push('/admin/dashboard');
  }
};

const goToRegister = () => {
  router.push('/register');
};
const goToDashboard = () => {
  router.push('/admin/dashboard');
};

const forgotPassword = () => {
  router.push('/forgotpassword')
  // Logic cho quên mật khẩu
  console.log('Quên mật khẩu');
};
</script>

<template>
  <div class="auth-container">
    <h1>Đăng Nhập</h1>
    <form @submit.prevent="submitLoginForm">
      <div class="form-group">
        <label for="login-email">Email</label>
        <input type="email" id="login-email" v-model="loginForm.email" />
        <p class="error" v-if="loginErrors.email">{{ loginErrors.email }}</p>
      </div>

      <div class="form-group">
        <label for="login-password">Mật khẩu</label>
        <input type="password" id="login-password" v-model="loginForm.password" />
        <p class="error" v-if="loginErrors.password">{{ loginErrors.password }}</p>
      </div>

      <button type="submit">Đăng Nhập</button>
    </form>

    <button type="button" @click="goToRegister">Đăng Ký</button>
    <button type="button" @click="forgotPassword">Quên Mật Khẩu</button>
  </div>
</template>

<style scoped>
@import "/src/assets/css/auth.css";
.msg-error{
    color: red;
}
</style>