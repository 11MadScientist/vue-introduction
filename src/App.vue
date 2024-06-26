<template>
  <div>
    <div class="container">
    <h1>Using v-bind and v-on together.</h1>
    <input type="text" :value="text" @input="onInput">
    <h3 class="reflect">{{ text }}</h3>
  </div>

  <div class="container">
    <h1>Using v-model.</h1>
    <input v-model="vmodeltext">
    <h3 class="reflect">{{ vmodeltext }}</h3>
  </div>
  </div>
</template>

<script setup>

import {ref, reactive} from 'vue'

const text = ref("");

const vmodeltext = ref("");

const onInput = (e) => {
  text.value = e.target.value
};


</script>

<style scoped>
.reflect {
  color: white;
}

.container {
  display: block;
}
</style>
