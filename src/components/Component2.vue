<template>
  <h1>This is Component 2 Root 1</h1>
  <div>
    This is root 2
  </div>
  <!-- the $attrs is binded in the third root, 
       which means this the attributes will fallthrough here-->
  <h2 v-bind="$attrs">This is root 3</h2>
</template>

<script setup>
defineOptions({
  inheritAttrs: false,
});
</script>
<style scoped>
.highlight-blue {
  background-color: blue;
}
</style>
