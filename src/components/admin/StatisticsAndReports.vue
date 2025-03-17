<template>
    <div>
        <button @click="goBack" class="back-button">← Quay lại</button>
  <div class="statistics-reports">
    <h1>Thống kê và báo cáo</h1>
    <div class="tabs">
      <button @click="selectTab('views')" :class="{ active: currentTab === 'views' }">Thống kê lượt xem</button>
      <button @click="selectTab('issues')" :class="{ active: currentTab === 'issues' }">Báo cáo sự cố</button>
    </div>

    <!-- Thống kê lượt xem -->
    <div v-if="currentTab === 'views'" class="tab-content">
      <h2>Thống kê lượt xem</h2>
      <div class="stats-overview">
        <div class="stat-item">
          <h3>Lượt xem hôm nay</h3>
          <p>{{ dailyViews }}</p>
        </div>
        <div class="stat-item">
          <h3>Thời gian xem trung bình</h3>
          <p>{{ averageWatchTime }} phút</p>
        </div>
        <div class="stat-item">
          <h3>Người dùng hoạt động</h3>
          <p>{{ activeUsers }}</p>
        </div>
      </div>
    </div>

    <!-- Báo cáo sự cố -->
    <div v-if="currentTab === 'issues'" class="tab-content">
      <h2>Báo cáo sự cố</h2>
      <ul class="issue-list">
        <li v-for="issue in reportedIssues" :key="issue.id">
          <strong>Ngày:</strong> {{ issue.date }} - <strong>Mô tả:</strong> {{ issue.description }}
          <button @click="resolveIssue(issue.id)" class="resolve-button">Đã xử lý</button>
        </li>
      </ul>
    </div>
  </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import {useRouter} from 'vue-router';
const router = useRouter();

// Tab hiện tại
const currentTab = ref('views');

// Thống kê lượt xem
const dailyViews = ref(1240);
const averageWatchTime = ref(45); // phút
const activeUsers = ref(320);

// Báo cáo sự cố
const reportedIssues = ref([
  { id: 1, date: '2023-05-01', description: 'Lỗi tải phim chậm' },
  { id: 2, date: '2023-05-02', description: 'Không phát được phim' },
  { id: 3, date: '2023-05-03', description: 'Giao diện bị lỗi trên điện thoại' },
]);

// Hàm chọn tab
const selectTab = (tab) => {
  currentTab.value = tab;
};

// Hàm xử lý báo cáo sự cố
const resolveIssue = (id) => {
  reportedIssues.value = reportedIssues.value.filter(issue => issue.id !== id);
  alert('Đã xử lý sự cố!');
};
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

.statistics-reports {
  max-width: 900px;
  margin: 0 auto;
  padding: 20px;
  background-color: #f8f9fa;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  animation: fadeIn 1s ease-in-out;
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
  background-color: #3498db;
  color: white;
  margin: 0 5px;
  cursor: pointer;
  border-radius: 4px;
  transition: background-color 0.3s ease;
}

.tabs button:hover {
  background-color: #2980b9;
}

.tabs button.active {
  background-color: #1abc9c;
}

.tab-content {
  background-color: white;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  padding: 20px;
  margin-top: 10px;
}

h2 {
  margin-bottom: 20px;
  color: #333;
}

.stats-overview {
  display: flex;
  justify-content: space-between;
  gap: 15px;
}

.stat-item {
  flex: 1;
  background-color: #e3f2fd;
  padding: 15px;
  border-radius: 8px;
  text-align: center;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.stat-item h3 {
  margin-bottom: 10px;
  font-size: 18px;
  color: #007bb5;
}

.stat-item p {
  font-size: 22px;
  font-weight: bold;
  color: #333;
}

.issue-list {
  list-style: none;
  padding: 0;
}

.issue-list li {
  background-color: #fff3cd;
  padding: 15px;
  margin-bottom: 10px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.resolve-button {
  padding: 8px 12px;
  background-color: #28a745;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.resolve-button:hover {
  background-color: #218838;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(-20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
</style>