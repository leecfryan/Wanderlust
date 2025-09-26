<script setup>
import { ref } from 'vue'

const username = ref('')
const password = ref('')
const errors = ref({})

const validateForm = (event) => {
  event.preventDefault()
  errors.value = {}

  // Username validation
  if (!username.value) {
    errors.value.username = 'Username is required'
  } else if (username.value.length < 3) {
    errors.value.username = 'Username must be at least 3 characters'
  }

  // Password validation
  if (!password.value) {
    errors.value.password = 'Password is required'
  } else if (password.value.length < 6) {
    errors.value.password = 'Password must be at least 6 characters'
  }

  // If no errors, proceed with login
  if (Object.keys(errors.value).length === 0) {
    console.log('Form is valid, proceeding with login...')
    // Add your login logic here
  }
}
</script>

<template>
  <div class="login">
    <h1>Login Page</h1>
    <form @submit="validateForm">
      <div class="form-group">
        <label for="username">Username:</label>
        <input 
          type="text" 
          id="username" 
          v-model="username"
          :class="{ 'error': errors.username }"
        />
        <span class="error-message" v-if="errors.username">{{ errors.username }}</span>
      </div>

      <div class="form-group">
        <label for="password">Password:</label>
        <input 
          type="password" 
          id="password" 
          v-model="password"
          :class="{ 'error': errors.password }"
        />
        <span class="error-message" v-if="errors.password">{{ errors.password }}</span>
      </div>

      <button type="submit">Login</button>
    </form>
  </div>
</template>


<style scoped>
.login {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
}

.login h1 {
  margin-bottom: 24px;
  font-weight: 600;
}

form {
  padding: 32px 24px;
  border-radius: 12px;
  box-shadow: 0 4px 24px rgba(255,255,255,0.08);
  display: flex;
  flex-direction: column;
  min-width: 300px;
}

label {
  margin-top: 12px;
  margin-bottom: 4px;
  color: #555;
  font-size: 1rem;
}

input[type="text"],
input[type="password"] {
  padding: 10px;
  border: 1px solid #bfc9d9;
  border-radius: 6px;
  margin-bottom: 12px;
  font-size: 1rem;
  outline: none;
  transition: border-color 0.2s;
}

input:focus {
  border-color: #6a8dff;
}

button[type="submit"] {
  padding: 10px 0;
  background: #6a8dff;
  color: #fff;
  border: none;
  border-radius: 6px;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  margin-top: 8px;
  transition: background 0.2s;
}

button[type="submit"]:hover {
  background: #4e6edb;
}
</style>