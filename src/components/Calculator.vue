<template>
  <div class="container mx-auto mt-10 p-6 bg-white shadow-md rounded-lg">
    <h1 class="text-3xl font-extrabold mb-6 text-blue-700 text-center">Máy Tính Đơn Giản</h1>
    <form @submit.prevent="calculate" class="space-y-4">
      <div>
        <label for="firstNumber" class="block text-sm font-semibold text-gray-800">Số thứ nhất:</label>
        <input
            v-model="firstNumber"
            type="number"
            id="firstNumber"
            class="mt-1 block w-full px-4 py-2 border border-gray-300 rounded-lg shadow-sm focus:ring-blue-500 focus:border-blue-500 focus:outline-none"
            placeholder="Nhập số thứ nhất"
        />
      </div>
      <div>
        <label for="secondNumber" class="block text-sm font-semibold text-gray-800">Số thứ hai:</label>
        <input
            v-model="secondNumber"
            type="number"
            id="secondNumber"
            class="mt-1 block w-full px-4 py-2 border border-gray-300 rounded-lg shadow-sm focus:ring-blue-500 focus:border-blue-500 focus:outline-none"
            placeholder="Nhập số thứ hai"
        />
      </div>
      <div>
        <label for="operator" class="block text-sm font-semibold text-gray-800">Phép toán:</label>
        <select
            v-model="operator"
            id="operator"
            class="mt-1 block w-full px-4 py-2 border border-gray-300 rounded-lg shadow-sm focus:ring-blue-500 focus:border-blue-500 focus:outline-none bg-white"
        >
          <option value="%2B">Cộng</option>
          <option value="-">Trừ</option>
          <option value="*">Nhân</option>
          <option value="/">Chia</option>
        </select>
      </div>
      <button
          type="submit"
          class="w-full px-4 py-2 bg-blue-600 text-white rounded-lg shadow-lg hover:bg-blue-700 focus:ring focus:ring-blue-300 focus:outline-none transition duration-200"
      >
        Tính toán
      </button>
    </form>
    <div v-if="result !== null" class="mt-6 bg-green-100 text-green-800 px-4 py-3 rounded-lg shadow">
      <h2 class="text-xl font-bold text-center">Kết quả: {{ result }}</h2>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      firstNumber: 0,
      secondNumber: 0,
      operator: "Cộng",
      result: null,
    };
  },
  methods: {
    async calculate() {
      try {
        const response = await fetch(
            `http://localhost:8080/api/calculate?firstNumberStr=${this.firstNumber}&secondNumberStr=${this.secondNumber}&operator=${this.operator}`
        );
        if (!response.ok) {
          throw new Error("Lỗi khi gọi API");
        }
        const data = await response.json();
        this.result = data.result;
      } catch (error) {
        alert(error.message);
      }
    },
  },
};
</script>

<style>
body {
  font-family: 'Inter', sans-serif;
  background-color: #f3f4f6;
  margin: 0;
  padding: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.container {
  max-width: 400px;
  width: 100%;
  padding: 20px;
  background-color: #ffffff;
  border-radius: 10px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
  transition: transform 0.2s ease;
}

.container:hover {
  transform: scale(1.02);
}

h1 {
  color: #1d4ed8;
  text-align: center;
  margin-bottom: 20px;
}

label {
  color: #374151;
  display: block;
  margin-bottom: 5px;
}

input, select {
  width: 100%;
  padding: 10px;
  border: 1px solid #d1d5db;
  border-radius: 5px;
  transition: box-shadow 0.2s ease-in-out, border-color 0.2s ease-in-out;
  margin-bottom: 15px;
}

input:focus, select:focus {
  border-color: #1d4ed8;
  box-shadow: 0 0 0 3px rgba(29, 78, 216, 0.5);
  outline: none;
}

button {
  background-color: #1d4ed8;
  color: white;
  border: none;
  border-radius: 5px;
  padding: 10px;
  cursor: pointer;
  width: 100%;
  transition: background-color 0.2s ease, transform 0.2s ease;
}

button:hover {
  background-color: #2563eb;
  transform: translateY(-2px);
}

button:active {
  background-color: #1e40af;
  transform: translateY(0);
}

.mt-6 {
  margin-top: 1.5rem;
}

.bg-green-100 {
  background-color: #d1fae5;
}

.text-green-800 {
  color: #065f46;
}

.shadow {
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.text-center {
  text-align: center;
}

.text-xl {
  font-size: 1.25rem;
}

.font-bold {
  font-weight: bold;
}

.container {
  max-width: 500px;
  margin: auto;
  padding: 20px;
  background-color: #ffffff;
  border-radius: 10px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
  transition: transform 0.2s ease;
}

.container:hover {
  transform: scale(1.02);
}

h1 {
  color: #1d4ed8;
}

label {
  color: #374151;
}

input, select {
  transition: box-shadow 0.2s ease-in-out, border-color 0.2s ease-in-out;
}

input {
  border: 1px solid #d1d5db;
}

input:focus, select:focus {
  border-color: #1d4ed8;
  box-shadow: 0 0 0 3px rgba(29, 78, 216, 0.5);
}

button {
  background-color: #1d4ed8;
  color: white;
  border: none;
  border-radius: 5px;
  padding: 10px;
  cursor: pointer;
  transition: background-color 0.2s ease, transform 0.2s ease;
}

button:hover {
  background-color: #2563eb;
  transform: translateY(-2px);
}

button:active {
  background-color: #1e40af;
  transform: translateY(0);
}

.mt-6 {
  margin-top: 1.5rem;
}

.bg-green-100 {
  background-color: #d1fae5;
}

.text-green-800 {
  color: #065f46;
}

.shadow {
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.text-center {
  text-align: center;
}

.text-xl {
  font-size: 1.25rem;
}

.font-bold {
  font-weight: bold;
}

</style>
