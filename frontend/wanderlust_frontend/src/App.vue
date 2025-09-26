<script setup>
import Login from './components/Login.vue';
</script>

<template>
  <main>
    <Login />
  </main>
</template>

<style>
/* Global styles (remove scoped attribute) */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen,
    Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  line-height: 1.6;
  color: #333;
  background-color: #f5f5f5;
}

main {
  width: 100%;
  min-height: 100vh;
}

/* Remove all the existing header and logo styles as they're not needed for the login page */
</style>
