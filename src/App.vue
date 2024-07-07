<template>
  <Component1 title="Title this is." :msg="msg"/>
</template>

<script setup>
import Component1 from './components/icons/Component1.vue';

const msg = "Hello, this is message from the Parent vue file";
</script>

<style scoped>
</style>
