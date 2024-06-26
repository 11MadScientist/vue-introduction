<template>
  <div>
    <div class="container" v-if="isAllowedToAccessCheckbox">
      <h1>Check the checkbox for the input to appear.</h1>
      <input type="checkbox" v-model="isChecked">
      <h3 class="reflect">{{ text }}</h3>
    </div>

    <div class="container" v-show="isChecked">
      <h1>This is shown when checkbox is checked.</h1>
      <input v-model="vmodeltext">
      <h3 class="reflect">{{ vmodeltext }}</h3>
    </div>
  </div>
</template>

<script setup>

import {ref, reactive} from 'vue'

const isChecked = ref(false);

const vmodeltext = ref("");


// if set to false, the div which holds the checkbox won't show
const isAllowedToAccessCheckbox = true;


</script>

<style scoped>
.reflect {
  color: white;
}

.container {
  display: block;
}
</style>
