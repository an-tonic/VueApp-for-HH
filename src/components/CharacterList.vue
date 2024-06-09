<script setup>
import {onMounted, ref, watch} from 'vue';
import axios from 'axios';

const props = defineProps({
  filters: {
    type: Object,
    default: () => ({})
  }
});

const characters = ref([]);
const currentPage = ref(1);
const totalPages = ref(0);

const fetchCharacters = async () => {
  const params = {
    page: currentPage.value,
    ...props.filters
  };

  try {
    const response = await axios.get('https://rickandmortyapi.com/api/character', {params});
    const charactersData = response.data.results;
    characters.value = await Promise.all(
        charactersData.map(async character => {
          // Extract episode ID
          const episodeResponse = await axios.get(character.episode[0]);
          const episodeName = episodeResponse.data.name;
          return {
            ...character,
            firstEpisodeName: episodeName
          };
        })
    );
    totalPages.value = response.data.info.pages;
  } catch (error) {
    characters.value = [];
    totalPages.value = 1;
    console.error(error);
  }
};

const nextPage = () => {
  if (currentPage.value < totalPages.value) {
    currentPage.value++;
  }
};

const prevPage = () => {
  if (currentPage.value > 1) {
    currentPage.value--;
  }
};

onMounted(fetchCharacters);

watch(currentPage, () => {
  fetchCharacters();
});
watch(() => props.filters, () => {
  currentPage.value = 1
  fetchCharacters();
})

</script>

<template>
  <div v-if="characters.length === 0" class="no-characters-message">
    No characters found.
  </div>
  <div v-else class="character-list">
    <article class="character-card" v-for="character in characters" :key="character.id">
      <img :src="character.image" :alt="character.name"/>
      <div class="character-info">
        <h1 class = "character-title">{{ character.name }}</h1>
        <p class="status">
          <span class="status_icon" :class="{ alive: character.status === 'Alive', dead: character.status === 'Dead' }"></span>
          {{ character.status }} - {{ character.species }}
        </p>
        <p>Last known location:</p>
        <p>{{ character.location.name }}</p>
        <p>First seen in:</p>
        <p>{{character.firstEpisodeName}}</p></div>
    </article>

  </div>
  <div class="pagination">
    <button @click="prevPage" :disabled="currentPage === 1">Previous</button>
    <span>Page {{ currentPage }} of {{ totalPages }}</span>
    <button @click="nextPage" :disabled="currentPage === totalPages">Next</button>
  </div>
</template>

<style scoped>
.character-list {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
}
.no-characters-message {
  text-align: center;
  margin-top: 2rem;
  font-weight: bold;
}

.character-card {
  border: 1px solid #ccc;
  overflow: hidden;
  border-radius: 8px;
  width: 200px;
  background: rgb(60, 62, 68);
}

.character-card img {
  width: 100%;

}

.character-title{
  font-family:  Segoe UI Black,serif;
  font-weight: 800;
  color: rgb(245, 245, 245);
  margin: 5px;
}

.status{
  display: flex;
  -webkit-box-align: center;
  align-items: center;
  text-transform: capitalize;
}

.status_icon{
  height: 0.5rem;
  width: 0.5rem;
  margin-right: 0.375rem;
  border-radius: 50%;
  display: block;
}
.alive {
  background: rgb(85, 204, 68);
}

.dead {
  background: rgb(214, 61, 46);
}

.character-info{
  margin: 10px;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}
.character-info p{
  color: rgb(245, 245, 245);
  font-family: Segoe UI, sans-serif;
  margin: 6px;
}

.pagination {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 1rem;
}

.pagination button {
  margin: 0 1rem;
}
</style>
