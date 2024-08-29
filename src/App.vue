<template>
  <div>
    <h1>First Name: {{ first }}</h1>
    <h1>Last Name: {{ last }}</h1>

    <Component1 
      v-model:first-name.uppercase="first" 
      v-model:last-name.uppercase="last" 
    />
  </div>
  
</template>

<script setup>
import { ref } from 'vue';
import Component1 from './components/icons/Component1.vue';

const first = ref("FIRST");
const last = ref("LAST");
</script>

<style scoped></style>
