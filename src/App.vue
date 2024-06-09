<script setup>
import CharacterList from './components/CharacterList.vue';
import CharacterFilter from './components/CharacterFilter.vue';
import {ref} from "vue";

const filters = ref({});

const applyFilter = (newFilters) => {
  filters.value = newFilters;
};

</script>

<template>
  <header>
    <h1>Rick and Morty Characters</h1>
  </header>

  <main>
    <CharacterFilter @apply-filter="applyFilter" />
    <CharacterList :filters="filters" />
  </main>
</template>
