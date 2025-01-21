<script setup lang="ts">
import {ref, onMounted, computed} from "vue";

type Place = {
  id: number;
  name: string;
  image: string;
  dishes: string[];
};

const places = ref<Place[]>([]);
const loading = ref(true);
const currentPage = ref(1);
const itemsPerPage = ref(4);

const loadPlaces = async () => {
  try {
    const response = await fetch("./src/json/data.json");
    if (!response.ok) {
      throw new Error("Ошибка загрузки данных");
    }
    places.value = await response.json();
  } catch (error) {
    console.error(error);
  } finally {
    loading.value = false;
  }
};

const visiblePlaces = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage.value;
  const end = start + itemsPerPage.value;
  return places.value.slice(start, end);
});

const nextPage = () => {
  if (currentPage.value * itemsPerPage.value < places.value.length) {
    currentPage.value++;
  }
};

const prevPage = () => {
  if (currentPage.value > 1) {
    currentPage.value--;
  }
};

onMounted(() => {
  loadPlaces();
});
</script>

<template>
  <div class="container">
    <div class="container-header">
    <h1>Заведения с ланчами</h1>
    <div class="controls">
      <button @click="prevPage" :disabled="currentPage === 1">Назад</button>
      <button @click="nextPage" :disabled="currentPage * itemsPerPage >= places.length">Вперед</button>
    </div>
    </div>

    <div v-if="loading">Загрузка...</div>
    <div v-else>
      <div class="lunch-block">
        <div v-for="place in visiblePlaces" :key="place.id" class="lunch-item">
          <img :src="place.image" :alt="place.name" />
          <h3>{{ place.name }}</h3>
          <ul>
            <li v-for="dish in place.dishes" :key="dish">{{ dish }}</li>
          </ul>
        </div>
      </div>


    </div>
  </div>
</template>

<style scoped>


.container-header {
  display: flex;
  justify-content: space-between;
  padding: 3px;
  width: 70%;
}

.lunch-block {
  display: flex;
  justify-content: space-between;
  flex-wrap: wrap;
  gap: 10px;
  width: 70%;
}

.lunch-item {
  width: 200px;
  border: 1px solid #ccc;
  border-radius: 8px;
  padding: 10px;
  text-align: center;
}

.lunch-item img {
  width: 100%;
  height: auto;
  border-radius: 5px;
}

.lunch-item h3 {
  font-size: 1.2em;
  margin: 10px 0;
}

.lunch-item ul {
  list-style-type: none;
  padding-left: 0;
}


.controls button {
  padding: 10px 20px;
  font-size: 1em;
  margin: 0 10px;
  cursor: pointer;
}

.controls button:disabled {
  background-color: #ddd;
  cursor: not-allowed;
}
</style>
