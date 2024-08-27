<template>
  <h1>{{ childMsg || "No message from child yet" }}</h1>
  <br>
  <!-- uses the `v-on` with the emit name to listen to it-->
  <Component1 
    @click-response="(msg) => {childResponse(msg)}" 
    @click-response-2="(msg) => {childResponse(msg)}"/>
</template>

<script setup>
import {ref} from 'vue';
import Component1 from './components/icons/Component1.vue';

const childMsg = ref(null);

const childResponse = (msg) => {
  childMsg.value = msg;
};
</script>

<style scoped>
</style>
