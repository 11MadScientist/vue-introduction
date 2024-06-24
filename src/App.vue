<template>
  <div>
    <h1>{{ season.type }}</h1>
    <button><h3 @click="changeSeason()">Click to change the season</h3></button>
    <h5>Times season changed: {{ count }} </h5>
  </div>
 
</template>

<script setup>
import {ref, reactive} from 'vue'

// we use reactive to objects specially those who have deep attributes (nexted attributes)
const season = reactive({type: "DEFAULT"});

// we use ref on simple items
const count = ref(0);

const seasons = [
  "SUMMER",
  "FALL",
  "WINTER",
  "SPRING"
];

const changeSeason = () => {
  count.value++;
  let nextSeason = count.value % 4;
  season.type = seasons[nextSeason];
};

</script>

<style scoped>

</style>
