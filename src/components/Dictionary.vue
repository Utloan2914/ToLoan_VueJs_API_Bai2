<template>
  <div class="dictionary-container">
    <h1 class="dictionary-title">Từ điển Anh - Việt</h1>
    <div class="input-group">
      <input type="text" v-model="word" placeholder="Nhập từ cần tra" class="dictionary-input" />
      <button @click="translateWord" class="dictionary-button">Tra từ</button>
    </div>
    <p v-if="translation" class="dictionary-result">{{ translation }}</p>
    <p v-else-if="error" class="dictionary-error">Không tìm thấy từ này trong từ điển.</p>
  </div>
</template>

<style scoped>
/* Container */
.dictionary-container {
  max-width: 600px;
  margin: 50px auto;
  padding: 20px;
  text-align: center;
  background-color: #f9f9f9;
  border: 1px solid #ddd;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

/* Title */
.dictionary-title {
  font-size: 2rem;
  color: #333;
  margin-bottom: 20px;
}

/* Input group */
.input-group {
  display: flex;
  justify-content: center;
  margin-bottom: 20px;
}

.dictionary-input {
  flex: 1;
  max-width: 400px;
  padding: 10px 15px;
  border: 1px solid #ccc;
  border-radius: 5px 0 0 5px;
  font-size: 1rem;
}

.dictionary-button {
  padding: 10px 20px;
  border: none;
  background-color: #007bff;
  color: #fff;
  font-size: 1rem;
  cursor: pointer;
  border-radius: 0 5px 5px 0;
  transition: background-color 0.3s ease;
}

.dictionary-button:hover {
  background-color: #0056b3;
}

/* Result and Error */
.dictionary-result {
  font-size: 1.2rem;
  color: #28a745;
  margin-top: 20px;
}

.dictionary-error {
  font-size: 1.2rem;
  color: #dc3545;
  margin-top: 20px;
}
</style>


<script>
export default {
  data() {
    return {
      word: '',
      translation: '',
      error: false
    }
  },
  methods: {
    async translateWord() {
      try {
        const response = await fetch(`http://localhost:8080/api/dictionary/search?word=${this.word}`)
        const data = await response.json()
        if (data) {
          this.translation = `${this.word}: ${data.translation}`
          this.error = false
        } else {
          this.error = true
          this.translation = ''
        }
      } catch (error) {
        this.error = true
        this.translation = ''
      }
    }
  }
}
</script>