<template>
  <div class="container">
    <h1 class="title">Список продуктів</h1>

    <div class="search-container">
      <input
          v-model="searchQuery"
          type="text"
          placeholder=" Пошук..."
          class="search-input"
      />
    </div>

    <!-- Кнопка для повернення на головну -->
    <NuxtLink to="/" class="back-button">
      ⬅ На головну
    </NuxtLink>

    <table class="product-table">
      <thead>
      <tr>
        <th @click="sortBy('title')">Назва</th>
        <th @click="sortBy('description')">Опис</th>
        <th @click="sortBy('price')">Ціна</th>
        <th @click="sortBy('rating')">Оцінка</th>
        <th @click="sortBy('brand')">Бренд</th>
        <th @click="sortBy('category')">Категорія</th>
        <th>Фото</th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="product in paginatedProducts" :key="product.id">
        <td>{{ product.title }}</td>
        <td>{{ product.description }}</td>
        <td>{{ product.price }}$</td>
        <td :class="product.rating < 4.5 ? 'low-rating' : 'high-rating'">
          {{ product.rating }}
        </td>
        <td>{{ product.brand }}</td>
        <td>{{ product.category }}</td>
        <td>
          <img :src="product.thumbnail" alt="product" class="product-image" />
        </td>
      </tr>
      </tbody>
    </table>

    <div class="pagination">
      <button @click="prevPage" :disabled="page === 1">⬅ Назад</button>
      <span>Сторінка {{ page }} з {{ totalPages }}</span>
      <button @click="nextPage" :disabled="page === totalPages">➡ Далі</button>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, watch } from 'vue'

const products = ref([])
const searchQuery = ref('')
const sortKey = ref('')
const sortAsc = ref(true)
const page = ref(1)
const perPage = 5

onMounted(async () => {
  const res = await fetch('https://dummyjson.com/products')
  const json = await res.json()
  products.value = json.products
})

const normalize = (str) => {
  return String(str)
      .toLowerCase()
      .normalize("NFD")
      .replace(/[\u0300-\u036f]/g, '')
      .replace(/[^\w\s.]/gi, '')
      .trim()
}

const filteredProducts = computed(() => {
  const query = normalize(searchQuery.value)
  if (!query) return products.value

  const terms = query.split(/\s+/)

  return products.value.filter(product => {
    const fields = [
      product.title,
      product.description,
      product.brand,
      product.category,
      String(product.price),
      String(product.rating)
    ].map(normalize)

    return terms.every(term =>
        fields.some(field => field.includes(term))
    )
  })
})

const sortedProducts = computed(() => {
  const items = [...filteredProducts.value]
  if (!sortKey.value) return items

  return items.sort((a, b) => {
    const aVal = a[sortKey.value]
    const bVal = b[sortKey.value]
    if (typeof aVal === 'number' && typeof bVal === 'number') {
      return sortAsc.value ? aVal - bVal : bVal - aVal
    } else {
      return sortAsc.value
          ? String(aVal).localeCompare(String(bVal))
          : String(bVal).localeCompare(String(aVal))
    }
  })
})

watch([searchQuery, sortKey, sortAsc], () => {
  page.value = 1
})

const totalPages = computed(() =>
    Math.ceil(sortedProducts.value.length / perPage)
)

const paginatedProducts = computed(() => {
  const start = (page.value - 1) * perPage
  return sortedProducts.value.slice(start, start + perPage)
})

const nextPage = () => {
  if (page.value < totalPages.value) page.value++
}

const prevPage = () => {
  if (page.value > 1) page.value--
}

const sortBy = (key) => {
  if (sortKey.value === key) {
    sortAsc.value = !sortAsc.value
  } else {
    sortKey.value = key
    sortAsc.value = true
  }
}
</script>

<style scoped>
body {
  background: linear-gradient(135deg, #37474f, #263238);
  font-family: 'Arial', sans-serif;
  color: #fff;
  margin: 0;
  padding: 0;
}


.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 2rem;
  background: rgba(255, 255, 255, 0.8); /* Напівпрозорий фон */
  border-radius: 12px;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
}

.title {
  text-align: center;
  font-size: 2.5rem;
  margin-bottom: 1.5rem;
  color: #004e89;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
}


.back-button {
  display: block;
  width: fit-content;
  padding: 10px 20px;
  background-color: #0077b6;
  color: white;
  text-decoration: none;
  border-radius: 6px;
  font-size: 1.2rem;
  text-align: center;
  margin: 20px auto; /* Центрує кнопку */
}

.back-button:hover {
  background-color: #00acc1;
}

.back-button:active {
  transform: scale(1);
}


.search-container {
  display: flex;
  justify-content: center;
  margin-bottom: 30px;
}

.search-input {
  width: 80%;
  padding: 12px 20px;
  font-size: 1.2rem;
  border: 1px solid #0077b6;
  border-radius: 10px;
  background-color: #e0f7fa;
  color: #004e89;
  transition: background-color 0.3s ease;
}

.search-input:focus {
  background-color: #b2ebf2;
  border-color: #00acc1;
  outline: none;
}

.product-table {
  width: 100%;
  border-collapse: collapse;
  background-color: #ffffff;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  border-radius: 10px;
  overflow: hidden;
}

.product-table th,
.product-table td {
  padding: 14px 20px;
  text-align: center;
  border-bottom: 1px solid #f1f1f1;
  font-size: 1.1rem;
  color: #333;
}

.product-table th {
  background-color: #0077b6;
  color: #fff;
  cursor: pointer;
  user-select: none;
}

.product-table tbody tr {
  transition: background-color 0.3s ease;
}

.product-table tbody tr:hover {
  background-color: #e0f7fa;
}

.product-table td {
  vertical-align: middle;
}

.low-rating {
  color: #ff6347;
  font-weight: bold;
}

.high-rating {
  color: #32cd32;
  font-weight: bold;
}

.product-image {
  width: 80px;
  height: 80px;
  object-fit: cover;
  border-radius: 50%;
  border: 2px solid #0077b6;
}

.pagination {
  margin-top: 30px;
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 1rem;
}

.pagination button {
  padding: 10px 18px;
  border: none;
  background-color: #0077b6;
  color: white;
  border-radius: 6px;
  cursor: pointer;
  font-size: 1.2rem;
  transition: background-color 0.3s ease;
}

.pagination button:hover {
  background-color: #00acc1;
}

.pagination button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

.pagination span {
  font-size: 1.2rem;
  font-weight: bold;
  color: #004e89;
}
</style>
