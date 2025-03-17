<template>
  <div class="container mt-5">
    <div class="row">
      <!-- Tổng quan số lượng -->
      <div class="col-lg-3 col-md-6 mb-4">
        <div class="card shadow-lg rounded">
          <div class="card-header bg-primary text-white text-center">
            <h5 class="card-title mb-0">Tổng Quan</h5>
          </div>
          <div class="card-body">
            <ul class="list-group list-group-flush">
              <li class="list-group-item">Tổng số người dùng: <strong>{{ totalUsers }}</strong></li>
              <li class="list-group-item">Số lượng phim lẻ: <strong>{{ totalMovies }}</strong></li>
              <li class="list-group-item">Số lượng phim bộ: <strong>{{ totalSeries }}</strong></li>
              <li class="list-group-item">Số lượng tập phim: <strong>{{ totalEpisodes }}</strong></li>
              <li class="list-group-item">Số lượng diễn viên: <strong>{{ totalActors }}</strong></li>
              <li class="list-group-item">Số lượng đạo diễn: <strong>{{ totalDirectors }}</strong></li>
              <li class="list-group-item">Số lượng thể loại: <strong>{{ totalGenres }}</strong></li>
              <li class="list-group-item">Tổng doanh thu: <strong>{{ totalRevenue }} VNĐ</strong></li>
            </ul>
          </div>
        </div>
      </div>

      <!-- Biểu đồ thống kê -->
      <div class="col-lg-9 col-md-6">
        <div class="row">
          <!-- Biểu đồ lượt xem theo thời gian -->
          <div class="col-lg-6 col-md-12 mb-4">
            <div class="card shadow-lg rounded">
              <div class="card-header bg-success text-white">
                <h5 class="card-title mb-0">Lượt Xem Phim Theo Thời Gian</h5>
              </div>
              <div class="card-body">
                <LineChart :chartData="viewData" />
              </div>
            </div>
          </div>

          <!-- Biểu đồ doanh thu theo tháng -->
          <div class="col-lg-6 col-md-12 mb-4">
            <div class="card shadow-lg rounded">
              <div class="card-header bg-warning text-white">
                <h5 class="card-title mb-0">Doanh Thu Theo Tháng</h5>
              </div>
              <div class="card-body">
                <BarChart :chartData="revenueData" />
              </div>
            </div>
          </div>

          <!-- Biểu đồ số lượng người dùng mới -->
          <div class="col-lg-6 col-md-12 mb-4">
            <div class="card shadow-lg rounded">
              <div class="card-header bg-info text-white">
                <h5 class="card-title mb-0">Số Lượng Người Dùng Mới</h5>
              </div>
              <div class="card-body">
                <LineChart :chartData="newUsersData" />
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// Import các thành phần cần thiết từ vue-chartjs và chart.js
import { BarElement, CategoryScale, Chart as ChartJS, Legend, LinearScale, LineElement, Title, Tooltip } from 'chart.js';
import { Bar, Line } from 'vue-chartjs';

// Đăng ký các thành phần của chart.js
ChartJS.register(Title, Tooltip, Legend, LineElement, BarElement, CategoryScale, LinearScale);

export default {
  name: 'Dashboard',
  components: {
    LineChart: {
      name: 'LineChart',
      components: { Line },
      props: {
        chartData: {
          type: Object,
          required: true
        }
      },
      data() {
        return {
          chartOptions: {
            responsive: true,
            plugins: {
              legend: {
                position: 'top',
              },
              title: {
                display: true,
                text: 'Lượt Xem Phim Theo Thời Gian',
              },
            },
            scales: {
              x: {
                beginAtZero: true
              }
            }
          }
        };
      },
      template: '<Line :data="chartData" :options="chartOptions" />'
    },
    BarChart: {
      name: 'BarChart',
      components: { Bar },
      props: {
        chartData: {
          type: Object,
          required: true
        }
      },
      data() {
        return {
          chartOptions: {
            responsive: true,
            plugins: {
              legend: {
                position: 'top',
              },
              title: {
                display: true,
                text: 'Doanh Thu Theo Tháng',
              },
            },
            scales: {
              x: {
                beginAtZero: true
              }
            }
          }
        };
      },
      template: '<Bar :data="chartData" :options="chartOptions" />'
    }
  },
  data() {
    return {
      // Data for dashboard overview
      totalUsers: 1200,
      totalMovies: 450,
      totalSeries: 150,
      totalEpisodes: 1200,
      totalActors: 500,
      totalDirectors: 50,
      totalGenres: 20,
      totalRevenue: 2500000000,  // Example revenue

      // Data for charts
      viewData: {
        labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun'],
        datasets: [
          {
            label: 'Lượt xem',
            data: [150, 200, 300, 350, 450, 500],
            fill: true,
            backgroundColor: 'rgba(75,192,192,0.4)',
            borderColor: 'rgba(75,192,192,1)',
            borderWidth: 2
          }
        ]
      },

      revenueData: {
        labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun'],
        datasets: [
          {
            label: 'Doanh thu',
            data: [300000, 400000, 500000, 600000, 700000, 800000],
            backgroundColor: 'rgba(255,99,132,0.2)',
            borderColor: 'rgba(255,99,132,1)',
            borderWidth: 2
          }
        ]
      },

      newUsersData: {
        labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun'],
        datasets: [
          {
            label: 'Người dùng mới',
            data: [20, 30, 50, 80, 100, 120],
            fill: true,
            backgroundColor: 'rgba(153,102,255,0.2)',
            borderColor: 'rgba(153,102,255,1)',
            borderWidth: 2
          }
        ]
      }
    };
  },
  mounted() {
    this.fetchDashboardData();
  },
  methods: {
    // Hàm lấy dữ liệu từ API (sử dụng fetch, axios, v.v.)
    async fetchDashboardData() {
      const response = await fetch('https://api.example.com/dashboard');
      const data = await response.json();

      // Cập nhật dữ liệu tổng quan từ API
      this.totalUsers = data.totalUsers;
      this.totalMovies = data.totalMovies;
      this.totalSeries = data.totalSeries;
      this.totalEpisodes = data.totalEpisodes;
      this.totalActors = data.totalActors;
      this.totalDirectors = data.totalDirectors;
      this.totalGenres = data.totalGenres;
      this.totalRevenue = data.totalRevenue;

      // Cập nhật dữ liệu biểu đồ từ API
      this.viewData.datasets[0].data = data.viewData;
      this.revenueData.datasets[0].data = data.revenueData;
      this.newUsersData.datasets[0].data = data.newUsersData;
    }
  }
};
</script>

<style scoped>
.card {
  margin-bottom: 20px;
}

.card-header {
  font-weight: bold;
}

.card-body {
  font-size: 14px;
}

.list-group-item {
  font-size: 13px;
}
</style>
