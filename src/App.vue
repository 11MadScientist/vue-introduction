<template>
  <div class="parent-container">
    <div class="container">
      <!--Text Interpolation-->
      <h1>Using text interpolation: <span>{{ name }}</span></h1>
    </div>
    <br>
    <div class="container">
      <!--Raw HTML-->
      <h1>Using v-html directive: <span v-html="name"></span></h1>
    </div>
  </div>

</template>

<script setup>
import { ref, reactive } from 'vue'

const name = ref("<span style='color:red'>John Doe</span>");
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
