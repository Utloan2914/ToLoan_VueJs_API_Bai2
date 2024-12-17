<template>
  <div class="container">
    <h1>Danh Sách Nhân Viên</h1>

    <div class="search-container">
      <input v-model="search.name" placeholder="Tìm kiếm theo tên" />
      <input v-model="search.birthDateFrom" type="date" />
      <input v-model="search.birthDateTo" type="date" />
      <select v-model="search.gender">
        <option value="">Tất cả</option>
        <option value="Nam">Nam</option>
        <option value="Nữ">Nữ</option>
      </select>
      <input v-model="search.salary" type="number" placeholder="Mức lương" />
      <input v-model="search.phone" placeholder="Số điện thoại" />
      <button @click="searchEmployees">Tìm kiếm</button>
    </div>

    <table class="employee-table">
      <thead>
      <tr>
        <th>STT</th>
        <th>Tên</th>
        <th>Ngày sinh</th>
        <th>Giới tính</th>
        <th>Lương</th>
        <th>Số điện thoại</th>
        <th>Bộ phận</th>
        <th>Thao tác</th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="(employee, index) in filteredEmployees" :key="employee.id">
        <td>{{ index + 1 }}</td>
        <td>{{ employee.name }}</td>
        <td>{{ employee.birthDate }}</td>
        <td>{{ employee.gender }}</td>
        <td>{{ employee.salary}}₫</td>
        <td>{{ employee.phone }}</td>
        <td>{{ employee.department }}</td>
        <td>
          <button @click="openEditForm(employee)">Cập nhật</button>
          <button @click="deleteEmployee(employee.id)">Xóa</button>
        </td>
      </tr>
      </tbody>
    </table>

    <button @click="openAddForm">Thêm Mới</button>
    <div class="pagination">
      <button :disabled="currentPage === 0" @click="goToPage(currentPage - 1)">
        Trước
      </button>

      <button
          v-for="page in totalPages"
          :key="page"
          @click="goToPage(page - 1)"
          :class="{ active: page - 1 === currentPage }"
      >
        {{ page }}
      </button>

      <button :disabled="currentPage === totalPages - 1" @click="goToPage(currentPage + 1)">
        Sau
      </button>
    </div>

    <div v-if="formVisible" class="form-container">
      <h2>{{ isEditing ? "Cập nhật Nhân Viên" : "Thêm Nhân Viên" }}</h2>
      <form @submit.prevent="submitForm">
        <div>
          <label>Tên:</label>
          <input v-model="formData.name" type="text" required />
        </div>
        <div>
          <label>Ngày sinh:</label>
          <input v-model="formData.birthDate" type="date" required />
        </div>
        <div>
          <label>Giới tính:</label>
          <select v-model="formData.gender" required>
            <option value="Nam">Nam</option>
            <option value="Nữ">Nữ</option>
          </select>
        </div>
        <div>
          <label>Lương:</label>
          <input v-model="formData.salary" type="number" min="0" required />
        </div>
        <div>
          <label>Số điện thoại:</label>
          <input
              v-model="formData.phone"
              type="tel"
              required
              pattern="^0[0-9]{9}$"
              title="Số điện thoại phải bắt đầu bằng số 0 và bao gồm 10 chữ số"
          />
        </div>
        <div>
          <label>Bộ phận:</label>
          <select v-model="formData.department" required>
            <option value="Nhân viên">Nhân viên</option>
            <option value="Thực tập sinh">Thực tập sinh</option>
            <option value="Giám đốc">Giám đốc</option>
          </select>
        </div>
        <button type="submit">{{ isEditing ? "Cập nhật" : "Thêm" }}</button>
        <button type="button" @click="closeForm">Hủy</button>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      employees: [],
      filteredEmployees: [],
      formVisible: false,
      isEditing: false,
      formData: {
        id: null,
        name: "",
        birthDate: "",
        gender: "",
        salary: 0,
        phone: "",
        department: "Nhân viên",
      },
      search: {
        name: "",
        birthDateFrom: "",
        birthDateTo: "",
        gender: "",
        salary: null,
        phone: "",
      },
      currentPage: 0,
      totalPages: 1,
    };
  },
  methods: {
    async fetchEmployees(page = 0, size = 5) {
      try {
        const response = await fetch('http://localhost:8080/employees');
        const data = await response.json();
        this.employees = data.data.content.map(item => ({
          id: item.employee.id,
          name: item.employee.name,
          birthDate: item.employee.dob,
          gender: item.employee.gender,
          salary: item.employee.salary,
          phone: item.employee.phone,
          department: item.department.name,
        }));
        this.totalPages = data.data.totalPages;
        this.currentPage = page;
        this.filteredEmployees = this.employees;
      } catch (error) {
        console.error("Lỗi khi tải danh sách nhân viên:", error.message);
      }
    }
    ,
    async searchEmployees() {
      try {
        const params = new URLSearchParams({
          name: this.search.name || '',
          dobFrom: this.search.birthDateFrom ? this.search.birthDateFrom.toISOString().split('T')[0] : '',
          dobTo: this.search.birthDateTo ? this.search.birthDateTo.toISOString().split('T')[0] : '',
          gender: this.search.gender || '',
          salaryRange: this.search.salary ? this.search.salary.toString() : '',
          phone: this.search.phone || '',
          departmentId: this.search.department || '',
        }).toString();

        const response = await fetch(`http://localhost:8080/employees/search?${params}`);
        const data = await response.json();
        this.employees = data.data.content.map(item => ({
            id: item.employee.id,
            name: item.employee.name,
            birthDate: item.employee.dob,
            gender: item.employee.gender,
            salary: item.employee.salary,
            phone: item.employee.phone,
            department: item.department.name,
          }));
          this.filteredEmployees = this.employees;
        this.currentPage = data.data.number;
        this.totalPages = data.data.totalPages;
      } catch (error) {
        console.error(error.message);
      }
    },
    goToPage(page) {
      if (page >= 0 && page < this.totalPages) {
        this.fetchEmployees(page);
      }
    },

    openAddForm() {
      this.isEditing = false;
      this.formData = {
        id: null,
        name: "",
        birthDate: "",
        gender: "",
        salary: 0,
        phone: "",
        department: "Nhân viên",
      };
      this.formVisible = true;
    },
    openEditForm(employee) {
      this.isEditing = true;
      this.formData = {...employee};
      this.formVisible = true;
    },
    closeForm() {
      this.formVisible = false;
    },
    async submitForm() {
      try {
        const url = this.isEditing
            ? `http://localhost:8080/employees/update/${this.formData.id}`
            : "http://localhost:8080/employees/add";
        const method = this.isEditing ? "PUT" : "POST";

        const response = await fetch(url, {
          method,
          headers: {"Content-Type": "application/json"},
          body: JSON.stringify(this.formData),
        });

        if (!response.ok) {
          throw new Error(this.isEditing ? "Lỗi khi cập nhật nhân viên" : "Lỗi khi thêm nhân viên");
        }

        await this.fetchEmployees();
        this.closeForm();
      } catch (error) {
        console.error(error.message);
      }
    },
    async deleteEmployee(id) {
      try {
        const response = await fetch(`http://localhost:8080/employees/delete/${id}`, {
          method: "DELETE",
        });
        if (!response.ok) throw new Error("Lỗi khi xóa nhân viên");
        this.fetchEmployees();
      } catch (error) {
        console.error(error.message);
      }
    },
  },
  mounted() {
    this.fetchEmployees();
  },
};
</script>

<style>
.container {
  max-width: 1200px;
  margin: 20px auto;
  font-family: Arial, sans-serif;
  background-color: #f9f9f9;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  padding: 20px;
}

h1 {
  text-align: center;
  color: #333;
}

.search-container {
  margin-bottom: 20px;
  display: flex;
  justify-content: space-between;
  flex-wrap: nowrap;
}

.search-container input,
.search-container select {
  padding: 10px;
  margin: 5px;
  border: 1px solid #ccc;
  border-radius: 4px;
  width: calc(16% - 10px);
}

.search-container button {
  padding: 10px 15px;
  border: none;
  border-radius: 4px;
  background-color: #4caf50;
  color: white;
  cursor: pointer;
  margin: 5px;
  width: calc(10% - 10px);
}

.employee-table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}

.employee-table th,
.employee-table td {
  border: 1px solid #ddd;
  padding: 12px;
  text-align: left;
}

.employee-table th {
  background-color: #4caf50;
  color: white;
}

.employee-table tr:nth-child(even) {
  background-color: #f2f2f2;
}

.employee-table tr:hover {
  background-color: #e0e0e0;
}

button {
  margin: 5px;
  padding: 10px 15px;
  border: none;
  border-radius: 4px;
  background-color: #2196F3;
  color: white;
  cursor: pointer;
}

button:hover {
  background-color: #1976D2;
}

.form-container {
  margin: 20px auto;
  padding: 30px;
  max-width: 600px;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  background-color: #f9f9f9;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.form-container h2 {
  text-align: center;
  color: #333;
  margin-bottom: 20px;
}

form div {
  margin-bottom: 20px;
}

form label {
  display: block;
  margin-bottom: 8px;
  font-weight: 600;
  color: #555;
}

form input,
form select {
  width: 100%;
  padding: 12px;
  border: 1px solid #ccc;
  border-radius: 6px;
}

form button {
  width: 100%;
  padding: 12px;
  border: none;
  border-radius: 6px;
  background-color: #4caf50;
  color: #fff;
  font-size: 1.1rem;
  cursor: pointer;
}

form button:hover {
  background-color: #45a049;
}
.pagination {
  display: flex;
  justify-content: center;
  margin-top: 20px;
}

.pagination button {
  margin: 0 5px;
  padding: 8px 12px;
  border: 1px solid #ccc;
  background-color: #f4f4f4;
  color: #333;
  cursor: pointer;
  border-radius: 4px;
}

.pagination button.active {
  background-color: #4caf50;
  color: white;
}

.pagination button:disabled {
  background-color: #ddd;
  cursor: not-allowed;
}

</style>