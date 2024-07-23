<template>
  <div>
    <h1 :class="seasonColor">{{ season.type }}</h1>
    <button :id :disabled="isButtondisabled"><h3 @click="changeSeason()">Click to change the season</h3></button>
    <h5 v-bind="objectOfAttrs">Times season changed: {{ count }} </h5>
  </div>
 
</template>

<script setup>
/**
 * we are using attribute binding to bind class to seasonColor
 * the class of the element will now change based on the value of seasonColor
 * making the style versatile per season.
 */

import {ref, reactive} from 'vue'

const season = reactive({type: "DEFAULT"});
const count = ref(0);
const seasonColor = ref("");

// same name shorthand
const id = ref("buttonId");

// boolean attributes
const isButtondisabled = ref(true);

// Dynamically binding multiple attributes
const objectOfAttrs = {
  id: 'container',
  class: 'wrapper',
  style: 'background-color:green'
}

const seasons = [
  "SUMMER",
  "FALL",
  "WINTER",
  "SPRING"
];

const seasonColors = [
  "summer-yellow",
  "fall-orange",
  "winter-white",
  "spring-green"
];

const changeSeason = () => {
  let nextSeason = count.value % 4;
  count.value++;
  season.type = seasons[nextSeason];
  seasonColor.value = seasonColors[nextSeason];
};

</script>

<style scoped>
.summer-yellow {
  color: #fffa9f;
}
.fall-orange {
  color: #92361a;
}

.winter-white {
  color: #f8f8f8;
}

.spring-green {
  color: #6c984b;
}
</style>
