<script setup>
import { ref } from 'vue';

const name = ref('');
const status = ref('');


const emit = defineEmits(['apply-filter']);

const applyFilter = () => {
  emit('apply-filter', { name: name.value, status: status.value});
};
</script>

<template>
  <div class="filter">
    <input v-model="name" placeholder="Name" />
    <select v-model="status">
      <option value="">All Statuses</option>
      <option value="alive">Alive</option>
      <option value="dead">Dead</option>
      <option value="unknown">Unknown</option>
    </select>
    <button @click="applyFilter">Apply</button>
  </div>
</template>

<style scoped>
.filter {
  display: flex;
  gap: 1rem;
  margin-bottom: 2rem;
}

.filter input,
.filter select {
  padding: 0.5rem;
  font-size: 1rem;
}

.filter button {
  padding: 0.5rem 1rem;
  font-size: 1rem;
}
</style>
