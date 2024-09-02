<template>
  <div>
    <h1>Normal Slot and Named Slots</h1>
    <Component1>
      <!--this goes to the slot with no name since this has no designated slot-->
      <p>This is passed into the child's slot</p>
      <!--named slots-->
      <template #title>
        <p>The Queen's blade</p>
      </template>
      <template #body>
        There are 2 ways to piss someone off, one is to not finish the sentence of, and ..
      </template>
    </Component1>
  </div>
 

  <div>
    <h1>Scoped Slot</h1>
    <Component1 v-slot="{msg}">
      <p>{{ msg }}</p>
    </Component1>
  </div>

  <div>
    <h1>Named Scoped Slot</h1>
    <Component1>
      <template #scoped="{msgScoped}">
        {{ msgScoped }}
      </template>
    </Component1>
  </div>

  <div>
    <h1>Slot with default value</h1>
    <Component1></Component1>
  </div>
</template>

<script setup>
import {ref} from 'vue';
import Component1 from './components/Component1.vue';

const childMsg = ref(null);

const childResponse = (msg) => {
  childMsg.value = msg;
};
</script>

<style scoped>
</style>
