<template>
  <p ref="pElementRef">Hello World</p>
</template>

<script setup>
import {ref, reactive, onMounted} from 'vue'

const pElementRef = ref();

onMounted(() => {
  pElementRef.value.textContent = "Nice to meet you.";
});

</script>

<style scoped>
</style>
