<template>
  <div>
    <h1>{{ title }}</h1>
    <Component1/>
  </div>
  
</template>

<script setup>
import {provide, ref, reactive} from 'vue';
import Component1 from './components/Component1.vue';


/**
 * a function to let the injector component to update the provided reactive variable.
 * @param {*} title 
 */

const updateTitle = (newTitle) => {
  title.value = newTitle;
};

/**
 * the variable to be passed
 */
const title = ref("Movie Title");

/**
 * provider
 */
provide("provide-title", {
  title,
  updateTitle
});



</script>

<style scoped>
</style>
