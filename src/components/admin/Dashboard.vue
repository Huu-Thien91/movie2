<template>
    <div class="dashboard">
      <aside class="sidebar">
        <h2>Admin Dashboard</h2>
        <ul>
          <li><router-link to="/admin/movies">Quản lý phim</router-link></li>
          <li><router-link to="/admin/finance">Quản lí tài chính</router-link></li>
          <li><router-link to="/admin/contentmanagement">Quản lí nội dung</router-link></li>
          <li><router-link to="/admin/vipmanagenment">Quản lí tài khoản VIP và bảo mật</router-link></li>
          <li><router-link to="/admin/statisticsandreports">Thống kê và báo cáocáo</router-link></li>
          <li><router-link to="/admin/transactions">Lịch sử giao dịch</router-link></li>
          <li><router-link to="/admin/account">Quản lí hệ thống và bảo mật (Admin)</router-link></li>
          <li><router-link to="/admin/user">Tài khoản người dùng</router-link></li>
          <li><router-link to="/admin/setting">Cài đặt chung</router-link></li>
          <li><router-link to="/login">Đăng xuất</router-link></li>
        </ul>
      </aside>
      <main class="main-content">
        <router-view></router-view>
        <ChartComponent></ChartComponent>
      </main>
    </div>
  </template>
  
  <script setup>
import ChartComponent from "@/components/admin/ChartComponent.vue";
import { RouterLink, RouterView } from 'vue-router';
  </script>
  
<style scoped>
@import "/src/assets/css/admin.css";
</style>
  