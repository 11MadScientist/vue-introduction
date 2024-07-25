<template>
  <div class="parent-container">
    <p @click="increment">{{ obj.id}}</p>
  </div>

</template>

<script setup>
import { ref, reactive, nextTick } from 'vue'
const obj = {id: ref(1)};

async function increment() {
  obj.id.value++;
  await nextTick();
  console.log("DOM is now updated");
}

</script>

<style scoped>
.parent-container {
  width: 1000px;
  display: flex;
  flex-direction: column;
}

.container {
  display: block;
  width: 100%;
}
</style>
