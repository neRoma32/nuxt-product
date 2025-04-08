<template>
  <div class="main-container">
    <h1 class="main-title">Ласкаво просимо на нашу сторінку!</h1>
    <NuxtLink to="/products" class="main-button">
      ➡ Перейти до списку продуктів
    </NuxtLink>
  </div>
</template>

<script setup>

</script>

<style scoped>

body {
  background: linear-gradient(135deg, #6a11cb, #2575fc);
  font-family: 'Arial', sans-serif;
  color: #fff;
  margin: 0;
  padding: 0;
}


.main-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
  text-align: center;
  padding: 0 20px;
}


.main-title {
  font-size: 3rem;
  font-weight: bold;
  margin-bottom: 50px;
  color: #1E90FF;
  text-shadow: 3px 3px 6px rgba(0, 0, 0, 0.3);
}

.main-button {
  font-size: 1.8rem;
  font-weight: bold;
  color: #fff;
  background-color: #2575fc;
  padding: 20px 40px;
  border-radius: 10px;
  text-decoration: none;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.3);
  transition: background-color 0.3s ease, transform 0.3s ease;
}

.main-button:hover {
  background-color: #6a11cb;
  transform: translateY(-5px);
}

.main-button:active {
  transform: translateY(0);
}
</style>
