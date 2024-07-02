<template>
  <p>Todo id: {{ todoId }}</p>
  <button @click="todoId++" :disabled="!todoData">Fetch next todo</button>
  <p v-if="!todoData">Loading...</p>
  <pre v-else>{{ todoData }}</pre>
</template>

<script setup>
import {ref, reactive, watch} from 'vue'

const todoId = ref(1);
const todoData = ref();

const fetchData = async() => {
  todoData.value = null;
  const res = await fetch(
    `https://jsonplaceholder.typicode.com/todos/${todoId.value}`
  );
  todoData.value = await res.json();
};

watch(todoId, (newTodoId, oldTodoId) => {
  fetchData();
}, {immediate: true});
</script>

<style scoped>
</style>
