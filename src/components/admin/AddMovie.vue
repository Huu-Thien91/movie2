<template>
    <div class="container">
        <div class="movie-management">
            <h1>🎥 Quản lý Phim</h1>
            <div class="tabs">
                <button @click="selectTab('movies')" :class="{ active: currentTab === 'movies' }">Phim Lẻ</button>
                <button @click="selectTab('series')" :class="{ active: currentTab === 'series' }">Phim Bộ</button>
            </div>

            <div v-if="currentTab === 'movies'" class="tab-content">
                <h2>Quản lý Phim Lẻ</h2>
                <button @click="showForm = true" class="add-button">Thêm Phim</button>
                <table class="data-table">
                    <thead>
                        <tr>
                            <th>ID</th>
                            <th>Tên phim</th>
                            <th>Mô tả</th>
                            <th>Trạng thái</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(movie, index) in paginatedMovies" :key="movie.movieId">
                            <td>{{ movie.movieId }}</td>
                            <td>{{ movie.title }}</td>
                            <td>{{ movie.description }}</td>
                            <td>{{ movie.status }}</td>
                        </tr>
                    </tbody>
                </table>

                <!-- Form thêm phim -->
                <div v-if="showForm" class="add-movie-form">
                    <h2>Thêm Phim</h2>
                    <form @submit.prevent="addFilm">
                        <label>Tiêu đề:</label>
                        <input v-model="newMovie.title" type="text" required />

                        <label>Mô tả:</label>
                        <textarea v-model="newMovie.description" required></textarea>

                        <label>Đạo diễn ID:</label>
                        <input v-model="newMovie.directorId" type="number" required />

                        <label>Năm phát hành:</label>
                        <input v-model="newMovie.yearReleased" type="number" required />

                        <label>Rating:</label>
                        <input v-model="newMovie.rating" type="number" required />

                        <label>Poster:</label>
                        <input type="file" @change="handleFileChange('posterFile')" />

                        <label>Avatar:</label>
                        <input type="file" @change="handleFileChange('avatarFile')" />

                        <label>Diễn viên:</label>
                        <multiselect v-model="newMovie.actors" :options="actorOptions" label="name" track-by="id"
                            placeholder="Chọn diễn viên" :multiple="true">
                        </multiselect>

                        <button type="submit">Thêm Phim</button>
                        <button type="button" @click="showForm = false">Hủy</button>
                    </form>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from "axios";
import Multiselect from "vue-multiselect";
import "vue-multiselect/dist/vue-multiselect.min.css";

export default {
    components: {
        Multiselect,
    },
    data() {
        return {
            newMovie: {
                title: "",
                description: "",
                directorId: 0,
                yearReleased: 0,
                rating: 0,
                posterFile: null,
                avatarFile: null,
                actors: [],
            },
            actorOptions: [], // Danh sách diễn viên
            allMovies: [], // Toàn bộ danh sách phim
            paginatedMovies: [], // Danh sách phân trang
            totalPages: 0, // Tổng số trang
            page: 1, // Trang hiện tại
            itemsPerPage: 5, // Số phim mỗi trang
            currentTab: "movies", // Tab hiện tại
            showForm: false, // Hiển thị form thêm phim
        };
    },
    methods: {
        async fetchMovies() {
            try {
                const response = await axios.get("http://localhost:5289/api/AdminMovie", {
                    params: { sortBy: "Title", search: "" },
                });
                this.allMovies = Array.isArray(response.data) ? response.data : [];
                this.updatePagination(); // Cập nhật danh sách phân trang
            } catch (error) {
                console.error("Lỗi khi tải danh sách phim:", error.message);
            }
        },
        async fetchActors() {
            try {
                const response = await axios.get("http://localhost:5289/api/AdminActor", {
                    params: { search: "", sortBy: "ActorId", sortDirection: "asc", pageSize: 50 },
                });
                this.actorOptions = response.data.map((actor) => ({
                    id: actor.actorId,
                    name: actor.nameAct,
                }));
            } catch (error) {
                console.error("Lỗi khi tải danh sách diễn viên:", error.message);
            }
        },
        handleFileChange(type) {
            const fileInput = event.target.files[0];
            if (type === "posterFile") this.newMovie.posterFile = fileInput;
            if (type === "avatarFile") this.newMovie.avatarFile = fileInput;
        },
        async addFilm() {
            try {
                const { title, description, directorId, yearReleased, rating, posterFile, avatarFile, actors } = this.newMovie;
                if (!title || !description || !directorId || !yearReleased || !rating || !posterFile || !avatarFile) {
                    alert("Vui lòng nhập đầy đủ thông tin!");
                    return;
                }

                // Chuẩn bị dữ liệu gửi lên API
                const formData = new FormData();
                formData.append("Title", title);
                formData.append("Description", description);
                formData.append("DirectorId", directorId);
                formData.append("YearReleased", yearReleased);
                formData.append("Rating", rating);
                formData.append("posterFile", posterFile);
                formData.append("AvatarUrlFile", avatarFile);
                formData.append("actorIds", actors.map((actor) => actor.id).join(","));

                // Gửi API để thêm phim
                const response = await axios.post(
                    "http://localhost:5289/api/AdminMovie/AddFilm",
                    formData,
                    { headers: { "Content-Type": "multipart/form-data" } }
                );

                console.log("Phim được thêm thành công:", response.data);

                // Cập nhật danh sách phim
                this.allMovies.push(response.data); // Thêm phim mới vào cuối danh sách
                this.totalPages = Math.ceil(this.allMovies.length / this.itemsPerPage); // Tính lại tổng số trang
                this.page = this.totalPages; // Chuyển đến trang cuối
                this.updatePagination(); // Cập nhật danh sách phân trang cho trang cuối

                alert("Thêm phim thành công!");
                this.showForm = false; // Ẩn form thêm phim
            } catch (error) {
                console.error("Lỗi khi thêm phim:", error.response?.data?.message || error.message);
                alert(`Đã xảy ra lỗi: ${error.response?.data?.message || error.message}`);
            }
        },

        updatePagination() {
            const startIndex = (this.page - 1) * this.itemsPerPage;
            const endIndex = startIndex + this.itemsPerPage;
            this.paginatedMovies = this.allMovies.slice(startIndex, endIndex);
        }
    },
    watch: {
        showForm(newVal) {
            if (newVal) this.fetchActors(); // Tải danh sách diễn viên khi form hiển thị
        },
    },
    mounted() {
        this.fetchMovies(); // Lấy danh sách phim khi khởi tạo
    },
};
</script>


<style scoped>
@import "/src/assets/css/admin.css";

/* Form Overlay */
.form-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.7);
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 1000;
}

.form-group {
    flex: 1;
}

.form-container {
    background: #fff;
    padding: 30px;
    border-radius: 10px;
    width: 600px;
    max-width: 90%;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
}

.filter-button {
    padding: 10px 15px;
    background-color: #3498db;
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    transition: background-color 0.3s ease;
}

.filter-button:hover {
    background-color: #2980b9;
}


.form-group {
    margin-bottom: 15px;
}

.filters {
    display: flex;
    gap: 20px;
    margin-bottom: 20px;
}

.movie-form {
    display: flex;
    flex-direction: column;
    /* Stack columns vertically */
}

.form-columns {
    display: flex;
    justify-content: space-between;
}

.form-column {
    width: 48%;
}

.left-column {
    margin-right: 20px;
    /* Space between columns */
}

.form-group label {
    display: block;
    margin-bottom: 5px;
    font-weight: bold;
    color: #333;
}

.form-group input,
.form-group select {
    width: 100%;
    padding: 10px;
    border: 1px solid #ccc;
    border-radius: 5px;
    font-size: 14px;
    transition: border-color 0.3s;
}

.form-group input:focus,
.form-group select:focus {
    border-color: #4caf50;
    outline: none;
}


h2 {
    margin-bottom: 20px;
    font-size: 1.5rem;
    text-align: center;
}

.form-group label {
    display: block;
    margin-bottom: 5px;
    font-weight: bold;
}

.button-group {
    display: flex;
    justify-content: space-between;
    /* Space buttons apart */
    margin-top: 20px;
}

.submit-button {
    width: 48%;
    background: #4caf50;
    color: #fff;
    border: none;
    padding: 12px;
    border-radius: 5px;
    cursor: pointer;
    font-size: 16px;
}


.cancel-button {
    width: 48%;
    background: #f44336;
    color: #fff;
    border: none;
    padding: 12px;
    border-radius: 5px;
    cursor: pointer;
}

.submit-button:hover,
.cancel-button:hover {
    opacity: 0.9;
}

.movie-management {
    background-color: #fff;
    border-radius: 10px;
    padding: 30px;
    box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.1);
    animation: fadeIn 1s ease-in-out;
    width: 120%;
}

.tabs {
    display: flex;
    gap: 20px;
}

.tabs button,
.add-button {
    padding: 12px 50px;
    font-size: 18px;
    background-color: #f0f0f0;
    border: none;
    border-radius: 6px;
    cursor: pointer;
}

.tabs button.active,
.add-button {
    background-color: #4caf50;
    color: white;
}

.tab-content {
    margin-top: 30px;
}

.search-bar {
    display: flex;
    justify-content: flex-end;
    margin-bottom: 20px;
}

.search-bar input {
    width: 300px;
    padding: 10px;
    font-size: 16px;
    border: 1px solid #ddd;
    border-radius: 6px;
}

.data-table {
    width: 100%;
    border-collapse: collapse;
    margin-top: 20px;
}

.data-table th,
.data-table td {
    padding: 12px;
    border: 1px solid #ddd;
    text-align: center;
}

.data-table th {
    background-color: #f4f4f4;
}

.edit-button,
.delete-button {
    background-color: #4caf50;
    color: white;
    border: none;
    padding: 8px 12px;
    border-radius: 6px;
    cursor: pointer;
    margin-right: 5px;
}

.add-button {
    background-color: rgb(12, 187, 245);
}

.delete-button {
    background-color: #f44336;
}

.edit-button:hover {
    background-color: #45a049;
}

.delete-button:hover {
    background-color: #e53935;
}

.movie-form {
    display: flex;
    flex-direction: column;
    gap: 15px;
    margin-top: 20px;
}

.movie-form .form-group {
    display: flex;
    flex-direction: column;
}

.movie-form .form-group label {
    margin-bottom: 5px;
    font-weight: bold;
}

.movie-form .form-group input,
.movie-form .form-group select {
    padding: 10px;
    font-size: 16px;
    border: 1px solid #ddd;
    border-radius: 6px;
}

.submit-button {
    background-color: #4caf50;
    color: white;
    border: none;
    padding: 12px 20px;
    font-size: 16px;
    border-radius: 6px;
    cursor: pointer;
    align-self: flex-start;
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

.submit-button:hover {
    background-color: #45a049;
}

.movie-list,
.series-list {
    margin-bottom: 20px;
}

.movie-list ul,
.series-list ul {
    list-style-type: none;
    padding: 0;
}

.movie-list li,
.series-list li {
    padding: 10px;
    background-color: #f0f0f0;
    margin-bottom: 5px;
    border-radius: 6px;
}

.pagination {
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 10px;
}

.pagination button {
    padding: 8px 12px;
    border: none;
    background-color: #3498db;
    color: white;
    cursor: pointer;
}

.pagination button:disabled {
    background-color: #bdc3c7;
    cursor: not-allowed;
}

.pagination span {
    font-weight: bold;
}

.link-film-url {
    width: 100px !important;
    height: auto !important;
}
</style>