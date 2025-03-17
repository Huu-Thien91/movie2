<template>
  <div class="comment-management">
    <h1>Quản lý Bình luận & Đánh giá</h1>

    <div v-if="loading" class="loading">Đang tải...</div>

    <!-- Tìm kiếm và lọc -->
    <div class="search-box">
      <input type="text" v-model="searchQuery" placeholder="Tìm kiếm bình luận..." @input="searchComments">
      <select v-model="filterStatus" @change="filterComments">
        <option value="">Tất cả trạng thái</option>
        <option value="1">Bình thường</option>
        <option value="0">Vi phạm</option>
      </select>
    </div>

    <!-- Danh sách bình luận -->
    <table v-if="!loading">
      <thead>
        <tr>
          <th>ID</th>
          <th>Người dùng</th>
          <th>Phim</th>
          <th>Bình luận</th>
          <th>Đánh giá</th>
          <th>Ngày tạo</th>
          <th>Trạng thái</th>
          <th>Hành động</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="comment in paginatedComments" :key="comment.CommentID">
          <td>{{ comment.CommentID }}</td>
          <td>{{ comment.UserName }}</td>
          <td>{{ comment.MovieTitle }}</td>
          <td>{{ comment.CommentText }}</td>
          <td>{{ comment.Rating }}</td>
          <td>{{ comment.CreatedAt }}</td>
          <td>{{ comment.Status === 1 ? 'Bình thường' : 'Vi phạm' }}</td>
          <td>
            <button @click="deleteComment(comment.CommentID)" class="btn-delete">
              Xóa
            </button>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Phân trang -->
    <div class="pagination">
      <button @click="changePage(currentPage - 1)" :disabled="currentPage === 1">Trang trước</button>
      <span>Trang {{ currentPage }} / {{ totalPages }}</span>
      <button @click="changePage(currentPage + 1)" :disabled="currentPage === totalPages">Trang sau</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      comments: [],
      searchQuery: '',
      filterStatus: '',
      loading: true,
      currentPage: 1,
      commentsPerPage: 5,
    };
  },
  computed: {
    filteredComments() {
      return this.comments.filter(comment => {
        const matchesSearch = comment.UserName.includes(this.searchQuery) || comment.CommentText.includes(this.searchQuery);
        const matchesStatus = this.filterStatus ? comment.Status === parseInt(this.filterStatus) : true;
        return matchesSearch && matchesStatus;
      });
    },
    totalPages() {
      return Math.ceil(this.filteredComments.length / this.commentsPerPage);
    },
    paginatedComments() {
      const start = (this.currentPage - 1) * this.commentsPerPage;
      const end = start + this.commentsPerPage;
      return this.filteredComments.slice(start, end);
    }
  },
  methods: {
    fetchComments() {
      this.loading = true;

      setTimeout(() => {
        this.comments = [
          { CommentID: 1, UserName: 'Nguyễn Văn A', MovieTitle: 'Phim 1', CommentText: 'Phim hay!', Rating: 4.5, CreatedAt: '2025-03-12', Status: 1 },
          { CommentID: 2, UserName: 'Trần Thị B', MovieTitle: 'Phim 2', CommentText: 'Phim dở!', Rating: 1.5, CreatedAt: '2025-03-13', Status: 0 },
          // Add more mock comments here
        ];
        this.loading = false;
      }, 1000);
    },
    deleteComment(commentId) {
      if (confirm('Bạn có chắc muốn xóa bình luận này?')) {
        this.comments = this.comments.filter(comment => comment.CommentID !== commentId);
      }
    },
    changePage(page) {
      if (page < 1 || page > this.totalPages) return;
      this.currentPage = page;
    },
    searchComments() {
      this.currentPage = 1; // Reset to first page when search query changes
    },
    filterComments() {
      this.currentPage = 1; // Reset to first page when filter status changes
    }
  },
  mounted() {
    this.fetchComments();
  }
};
</script>

<style scoped>
.comment-management {
  padding: 20px;
}

.search-box {
  margin-bottom: 20px;
}

input {
  padding: 5px;
  margin-right: 10px;
}

select {
  padding: 5px;
}

.pagination {
  text-align: center;
  margin-top: 20px;
}

.pagination button {
  padding: 5px 10px;
  margin: 0 5px;
  cursor: pointer;
}

.pagination button:disabled {
  background-color: grey;
  cursor: not-allowed;
}

table {
  width: 100%;
  border-collapse: collapse;
}

th, td {
  padding: 10px;
  border: 1px solid #ddd;
}

th {
  background-color: #f4f4f4;
}

button.btn-delete {
  background-color: red;
  color: white;
  border: none;
  padding: 5px 10px;
  cursor: pointer;
  border-radius: 5px;
}

button.btn-delete:hover {
  background-color: darkred;
}

.loading {
  text-align: center;
  font-size: 16px;
  color: grey;
}
</style>
