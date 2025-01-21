<script setup lang="ts">
import { ref, computed, onMounted } from "vue";

type Post = {
  id: number;
  title: string;
};

const data = ref<Post[]>([]);
const isLoading = ref(false);
const error = ref<string | null>(null);

const fetchData = async () => {
  isLoading.value = true;
  error.value = null;

  try {
    const response = await fetch("https://jsonplaceholder.typicode.com/posts");
    if (!response.ok) {
      throw new Error("Ошибка HTTP: " + response.status);
    }
    data.value = await response.json();
  } catch (err) {
    error.value = "Ошибка при загрузке данных";
  } finally {
    isLoading.value = false;
  }
};

const limitedData = computed(() => data.value.slice(0, 10));

onMounted(fetchData);
</script>

<template>
  <div>
    <h1>Список данных</h1>
    <div v-if="isLoading">Загрузка...</div>
    <div v-if="error">{{ error }}</div>
    <ul v-if="!isLoading && !error">
      <li v-for="item in limitedData" :key="item.id">{{ item.title }}</li>
    </ul>
  </div>
</template>
