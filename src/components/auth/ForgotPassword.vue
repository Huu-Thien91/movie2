<script setup>
import { ref } from 'vue';

const form = ref({
  email: ''
});

const errors = ref({});

const validateForm = () => {
  errors.value = {};

  if (!form.value.email) {
    errors.value.email = 'Email is required';
  } else if (!/\S+@\S+\.\S+/.test(form.value.email)) {
    errors.value.email = 'Email is invalid';
  }

  return Object.keys(errors.value).length === 0;
};

const submitForm = () => {
  if (validateForm()) {
    console.log('Form submitted:', form.value);
    // Gửi dữ liệu tới server hoặc xử lý quên mật khẩu ở đây
  }
};
</script>

<template>
  <div class="forgot-password-container">
    <h1>Quên Mật Khẩu</h1>
    <form @submit.prevent="submitForm">
      <div class="form-group">
        <label for="email">Email</label>
        <input type="email" id="email" v-model="form.email" />
        <p class="error" v-if="errors.email">{{ errors.email }}</p>
      </div>

      <button type="submit">Submit</button>
    </form>
  </div>
</template>

<style scoped>
.forgot-password-container {
  max-width: 400px;
  margin: 50px auto;
  padding: 20px;
  background-color: #f9f9f9;
  border-radius: 8px;
  box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
}

h1 {
  text-align: center;
  margin-bottom: 20px;
}

.form-group {
  margin-bottom: 15px;
}

label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
}

input[type="email"] {
  width: calc(100% - 20px);
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  font-size: 16px;
}

.error {
  color: red;
  font-size: 14px;
  margin-top: 5px;
}

button {
  width: 100%;
  padding: 10px;
  background-color: #04aa6d;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;
}

button:hover {
  background-color: #038c58;
}
</style>