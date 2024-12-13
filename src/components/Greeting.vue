<template>
  <div class="greeting-app">
    <h1>Greeting Application</h1>
    <input
        type="text"
        v-model="name"
        placeholder="Enter your name"
        class="input-box"
    />
    <button @click="fetchGreeting" class="submit-button">Greet Me</button>
    <p v-if="greetingMessage" class="message">{{ greetingMessage }}</p>
    <p v-if="error" class="error">{{ error }}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      name: "",
      greetingMessage: "",
      error: null,
    };
  },
  methods: {
    async fetchGreeting() {
      this.error = null;
      this.greetingMessage = "";

      try {
        const response = await fetch(
            `http://localhost:8080/greeting?name=${this.name}`
        );

        if (!response.ok) {
          this.error = "Failed to fetch greeting. Please try again.";
          return;
        }

        const data = await response.text();
        this.greetingMessage = data;
      } catch (err) {
        this.error = "An error occurred: " + err.message;
      }
    }


  },
};
</script>

<style>
.greeting-app {
  max-width: 500px;
  margin: 50px auto;
  text-align: center;
  padding: 20px;
  border: 2px solid #ccc;
  border-radius: 10px;
  background-color: #f9f9f9;
}
h1 {
  font-size: 1.8rem;
  margin-bottom: 20px;
}
.input-box {
  width: calc(100% - 40px);
  padding: 10px;
  font-size: 1rem;
  margin-bottom: 15px;
  border: 1px solid #ccc;
  border-radius: 5px;
}
.submit-button {
  padding: 10px 20px;
  font-size: 1rem;
  border: none;
  border-radius: 5px;
  background-color: #4caf50;
  color: white;
  cursor: pointer;
  transition: background-color 0.3s;
}
.submit-button:hover {
  background-color: #45a049;
}
.message {
  margin-top: 20px;
  font-size: 1.2rem;
  color: #333;
}
.error {
  margin-top: 20px;
  font-size: 1.2rem;
  color: red;
}
</style>
