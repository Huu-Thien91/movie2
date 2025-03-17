<script setup>
import { ref } from 'vue';
import { useRouter } from 'vue-router';

const form = ref({
  username: '',
  email: '',
  password: ''
});

const errors = ref({});
const router = useRouter();

const validateForm = () => {
  errors.value = {};

  if (!form.value.username) {
    errors.value.username = 'Tên người dùng là bắt buộc';
  }

  if (!form.value.email) {
    errors.value.email = 'Email là bắt buộc';
  } else if (!/\S+@\S+\.\S+/.test(form.value.email)) {
    errors.value.email = 'Email không hợp lệ';
  }

  if (!form.value.password) {
    errors.value.password = 'Mật khẩu là bắt buộc';
  }

  return Object.keys(errors.value).length === 0;
};
const gotoLogin = () => {
    router.push('/login');
}
const submitForm = () => {
  if (validateForm()) {
    console.log('Form đăng ký đã được gửi:', form.value);
    // Gửi dữ liệu tới server hoặc xử lý đăng ký ở đây

    // Điều hướng tới trang đăng nhập sau khi đăng ký thành công
    router.push('/login');
  }
};
</script>

<template>
  <div class="auth-container">
    <h1>Đăng Ký</h1>
    <form @submit.prevent="submitForm">
      <div class="form-group">
        <label for="username">Tên người dùng</label>
        <input type="text" id="username" v-model="form.username" />
        <p class="error" v-if="errors.username">{{ errors.username }}</p>
      </div>

      <div class="form-group">
        <label for="email">Email</label>
        <input type="email" id="email" v-model="form.email" />
        <p class="error" v-if="errors.email">{{ errors.email }}</p>
      </div>

      <div class="form-group">
        <label for="password">Mật khẩu</label>
        <input type="password" id="password" v-model="form.password" />
        <p class="error" v-if="errors.password">{{ errors.password }}</p>
      </div>

      <button type="submit">Đăng Ký</button>
      <button type="button" @click="gotoLogin">Đăng Nhập</button>
    </form>
  </div>
</template>

<style scoped>
@import "/src/assets/css/auth.css";
.msg-error{
    color: red;
}
</style>