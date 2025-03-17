<template>
    <div>
      <button @click="goBack" class="back-button">← Quay lại</button>
      <div class="finance-management">
        <h1>Quản lý Tài chính</h1>
        <div class="tabs">
          <button @click="selectTab('revenue')" :class="{ active: currentTab === 'revenue' }">Doanh thu</button>
          <button @click="selectTab('payments')" :class="{ active: currentTab === 'payments' }">Thanh toán</button>
          <button @click="selectTab('reports')" :class="{ active: currentTab === 'reports' }">Báo cáo tài chính</button>
        </div>
  
        <div v-if="currentTab === 'revenue'" class="tab-content">
          <h2>Doanh thu</h2>
          <div class="revenue-summary">
            <p class="total-revenue">Tổng doanh thu: {{ totalRevenue }}</p>
          </div>
          <ul class="transaction-list">
            <li v-for="transaction in transactions" :key="transaction.id">
              {{ transaction.date }} - {{ transaction.amount }} VND
            </li>
          </ul>
        </div>
  
        <div v-if="currentTab === 'payments'" class="tab-content">
          <h2>Thanh toán</h2>
          <ul class="payment-list">
            <li v-for="payment in payments" :key="payment.id">
              {{ payment.method }} - {{ payment.date }} - {{ payment.amount }} VND
            </li>
          </ul>
        </div>
  
        <div v-if="currentTab === 'reports'" class="tab-content">
          <h2>Báo cáo tài chính</h2>
          <ul class="report-list">
            <li v-for="report in reports" :key="report.id">
              <a :href="report.url" target="_blank">{{ report.date }} - Xem báo cáo</a>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref } from 'vue';
  import { useRouter } from 'vue-router';
  
  const router = useRouter();
  
  // Tab hiện tại
  const currentTab = ref('revenue');
  
  // Dữ liệu mẫu
  const totalRevenue = ref('1,000,000 VND');
  const transactions = ref([
    { id: 1, date: '2023-01-01', amount: '500,000' },
    { id: 2, date: '2023-02-01', amount: '300,000' },
    { id: 3, date: '2023-03-01', amount: '200,000' },
  ]);
  
  const payments = ref([
    { id: 1, method: 'Credit Card', date: '2023-01-01', amount: '500,000' },
    { id: 2, method: 'Paypal', date: '2023-02-01', amount: '300,000' },
    { id: 3, method: 'Bank Transfer', date: '2023-03-01', amount: '200,000' },
  ]);
  
  const reports = ref([
    { id: 1, date: '2023-01-01', url: '#' },
    { id: 2, date: '2023-02-01', url: '#' },
    { id: 3, date: '2023-03-01', url: '#' },
  ]);
  
  // Hàm chọn tab
  const selectTab = (tab) => {
    currentTab.value = tab;
  };
  
  // Hàm quay lại
  const goBack = () => {
    router.go(-1);
  };
  </script>
  
  <style scoped>
  body, html {
    margin: 0;
    padding: 0;
    font-family: 'Arial', sans-serif;
  }
  
  .back-button {
    margin: 20px;
    background-color: #3498DB;
    color: white;
    padding: 10px 20px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    transition: background-color 0.3s ease;
  }
  
  .back-button:hover {
    background-color: #2980B9;
  }
  
  .finance-management {
    max-width: 1000px;
    margin: 0 auto;
    padding: 20px;
    background-color: #f5f5f5;
    border-radius: 10px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  }
  
  h1 {
    text-align: center;
    margin-bottom: 20px;
    color: #333;
    font-size: 28px;
    font-weight: bold;
  }
  
  .tabs {
    display: flex;
    justify-content: center;
    margin-bottom: 20px;
  }
  
  .tabs button {
    padding: 10px 20px;
    border: none;
    background-color: #3498DB;
    color: white;
    margin: 0 5px;
    cursor: pointer;
    border-radius: 4px;
    transition: background-color 0.3s ease;
  }
  
  .tabs button:hover {
    background-color: #2980B9;
  }
  
  .tabs button.active {
    background-color: #1ABC9C;
  }
  
  .tab-content {
    text-align: left;
    padding: 20px;
    background-color: white;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }
  
  .tab-content h2 {
    margin-bottom: 10px;
  }
  
  .revenue-summary {
    background-color: #e0f7fa;
    padding: 20px;
    border-radius: 8px;
    text-align: center;
    margin-bottom: 20px;
  }
  
  .total-revenue {
    font-size: 24px;
    color: #00796b;
  }
  
  .transaction-list, .payment-list, .report-list {
    list-style-type: none;
    padding: 0;
  }
  
  .transaction-list li, .payment-list li, .report-list li {
    padding: 10px;
    border-bottom: 1px solid #ddd;
  }
  
  .report-list a {
    color: #3498db;
    text-decoration: none;
  }
  
  .report-list a:hover {
    text-decoration: underline;
  }
  </style>