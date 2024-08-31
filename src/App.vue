<template>
  <div>
    <!-- the class will fallthrough the single root div in Component1.
         the class of Component1 and of provided by parent will merge-->
    <Component1 class="highlight-red"/>
    <br><br>
    <!-- Component2 has multiple root, needs to configure which root the 
         attributes will fallthrough-->
    <Component2 class="highlight-blue"/>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import Component1 from './components/Component1.vue';
import Component2 from './components/Component2.vue';

const first = ref("FIRST");
const last = ref("LAST");
</script>

<style scoped>

</style>
