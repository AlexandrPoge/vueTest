<script setup lang="ts">
import {ref, onMounted, computed} from "vue";

type Place = {
  id: number;
  name: string;
  image: string;
  dishes: { ingredient: string; minPrice: string }[];
  price:string
  place:string
  time:string
};

const places = ref<Place[]>([]);
const loading = ref(true);
const currentPage = ref(1);
const itemsPerPage = ref(4);

const loadPlaces = async () => {
  try {
    const response = await fetch("./src/json/database.json");
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
      <div class="container-title">
        <h1>Бизнес-ланчи в Витебске</h1>
        <img class="carte-icon" src="../assets/icon/carte-Photoroom.svg" alt="">
      </div>
      <div class="controls">
        <button @click="prevPage" :disabled="currentPage === 1">
          <img class="left_arrow--rotate" src="../assets/icon/right.svg" alt="">
        </button>
        <button @click="nextPage" :disabled="currentPage * itemsPerPage >= places.length">
          <img src="../assets/icon/right.svg" alt="">
        </button>
      </div>
    </div>

    <div v-if="loading">Загрузка...</div>
    <div v-else>
      <div class="lunch-block">
        <div v-for="place in visiblePlaces" :key="place.id" class="lunch-container">
          <div class="lunch-block__photo">
            <img :src="place.image" :alt="place.name" />
            <div class="lunch-block__info">
              <h2 class="lunch-block__name">{{place.name}}</h2>
              <span id="place" class="lunch-block__name">{{place.place}}</span>
              <span id="time" class="lunch-block__name">{{place.time}}</span>
            </div>

          </div>

          <ul>
            <li class="lunch-item" v-for="dish in place.dishes" :key="dish.ingredient">
              <span class="lunch-item__ing">{{ dish.ingredient }}</span>
              <span class="lunch-item__min">{{ dish.minPrice }}</span>
            </li>
            <li class="lunch-item__price">{{ place.price }}</li>
          </ul>
        </div>
      </div>


    </div>
  </div>
</template>

<style scoped>

.container {
  background: #FFE4C4;
  width: 100%;
}
.container-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 3px;
}
.container-title {
  display: flex;
  align-items: center;
}
h1 {
  color: black;
  font-size: 50px;
  font-weight: bold;
  padding-right: 60px;
}
.carte-icon{
  width: 75px;
  height: 75px;
}

.lunch-block {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  margin-left: 10px;
}

.lunch-block__photo{
  position: relative;
}
.lunch-block__info{
  position: absolute;
  top: 0;
}
.lunch-block__name {
  display: flex;
  flex-direction: column;
  color: white;
  font-weight: bold;
  margin-left: 10px;
  font-size: 16px;
}


.lunch-container {
  width: 300px;
  border-radius: 8px;
  color:black;
  list-style-type:none;
}

.lunch-item {
  margin-bottom: 10px;
}
.lunch-item:last-child {
margin-bottom: 0px;
}
.lunch-item__price{
  color: black;
  font-weight: bold;
  font-size: 20px;
}
.lunch-item__ing:hover{
  text-decoration: underline dotted;
  color: red;
  cursor: pointer;
}
.lunch-item__min{
  background: darkgray;
  font-size: 12px;
  margin-left: 7px;
}

.lunch-item__price:hover {
  color: white;
  font-weight: bold;
  background-color: red;
  width: 70px;
}
.lunch-container img {
  width: 320px;
  height: 150px;
  border-radius: 5px;
}

.lunch-container h3 {
  font-size: 1.2em;
  margin: 10px 0;
  list-style-type: none;
}

.lunch-container ul {
  list-style-type: none;
  padding-left: 10px;
}


.left_arrow--rotate{
  transform: rotate(180deg);
}
.controls {
  display: flex;
}

.controls button:disabled {
  cursor: not-allowed;
}
.controls button {
  background: none;
  border: none;
  cursor: pointer;
  padding: 0.5rem;
}


@media (max-width: 768px) {
  .container-header {
    flex-direction: column;
    align-items: flex-start;
  }

  .container-title {
    margin-bottom: 1rem;
  }

  h1 {
    font-size: 1.8rem;
    padding-right: 0;
  }

  .lunch-container {
    max-width: 100%;
  }

  .controls {
    gap: 0.5rem;
  }
}
@media (max-width: 480px) {
  h1 {
    font-size: 20px;
  }

  .lunch-container img{
    width: 100%;
  }
  .lunch-item {
    font-size: 0.8rem;
  }

  .lunch-item__price {
    font-size: 0.9rem;
  }

  .lunch-item__min {
    font-size: 0.7rem;
  }

  .carte-icon {
    width: 50px;
    height: 50px;
  }

  .controls button img {
    width: 20px;
    height: 20px;
  }
}
</style>
