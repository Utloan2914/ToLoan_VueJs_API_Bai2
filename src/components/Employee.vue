<template>
  <div class="container">
    <h1>Danh Sách Nhân Viên</h1>
    <table class="employee-table">
      <thead>
      <tr>
        <th>STT</th>
        <th>Tên</th>
        <th>Ngày sinh</th>
        <th>Giới tính</th>
        <th>Lương</th>
        <th>Số điện thoại</th>
        <th>Thao tác</th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="(employee, index) in employees" :key="employee.id">
        <td>{{ index + 1 }}</td>
        <td>{{ employee.name }}</td>
        <td>{{ employee.birthDate }}</td>
        <td>{{ employee.gender }}</td>
        <td>{{ employee.salary.toLocaleString() }}₫</td>
        <td>{{ employee.phone }}</td>
        <td>
          <button @click="openEditForm(employee)">Cập nhật</button>
          <button @click="deleteEmployee(employee.id)">Xóa</button>
        </td>
      </tr>
      </tbody>
    </table>
    <button @click="openAddForm">Thêm Mới</button>

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
      formVisible: false,
      isEditing: false,
      formData: {
        id: null,
        name: "",
        birthDate: "",
        gender: "",
        salary: 0,
        phone: "",
      },
    };
  },
  methods: {
    async fetchEmployees() {
      try {
        const response = await fetch("http://localhost:8080/employees");
        if (!response.ok) throw new Error("Lỗi khi tải danh sách nhân viên");
        this.employees = await response.json();
      } catch (error) {
        console.error(error.message);
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
      };
      this.formVisible = true;
    },
    openEditForm(employee) {
      this.isEditing = true;
      this.formData = { ...employee };
      this.formVisible = true;
    },
    closeForm() {
      this.formVisible = false;
    },
    async submitForm() {
      try {
        const url = this.isEditing
            ? `http://localhost:8080/employees/${this.formData.id}`
            : "http://localhost:8080/employees";
        const method = this.isEditing ? "PUT" : "POST";

        const response = await fetch(url, {
          method,
          headers: {"Content-Type": "application/json"},
          body: JSON.stringify(this.formData),
        });

        if (!response.ok) {
          throw new Error(
              this.isEditing ? "Lỗi khi cập nhật nhân viên" : "Lỗi khi thêm nhân viên"
          );
        }

        this.fetchEmployees();
        this.closeForm();
      } catch (error) {
        console.error(error.message);
      }
    },
    async deleteEmployee(id) {
      try {
        const response = await fetch(`http://localhost:8080/employees/${id}`, {
          method: "DELETE",
        });
        if (!response.ok) throw new Error("Lỗi khi xóa nhân viên");
        this.employees = this.employees.filter((emp) => emp.id !== id);
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
  max-width: 800px;
  margin: 20px auto;
  font-family: Arial, sans-serif;
}

.employee-table {
  width: 100%;
  border-collapse: collapse;
}

.employee-table th,
.employee-table td {
  border: 1px solid #ddd;
  padding: 8px;
}

.employee-table th {
  background-color: #f2f2f2;
}

button {
  margin: 5px;
  padding: 10px;
  border: none;
  border-radius: 5px;
  background-color: #4caf50;
  color: white;
  cursor: pointer;
}

button:hover {
  background-color: #45a049;
}

.form-container {
  margin: 20px 0;
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 5px;
  background-color: #f9f9f9;
}

form div {
  margin-bottom: 10px;
}

form label {
  display: block;
  margin-bottom: 5px;
}
</style>