<template>
  <div>
   <button @click="clickHandler">Click Me</button>
  </div>
</template>

<script setup>
import {ref} from 'vue';
//defines the emits that the child can do
const emits = defineEmits([
  'click-response'
]);

const inc = ref(0);

const clickHandler = () => {
  // emits the `click-response` which will now inform the parent
  emits('click-response', `clicked the child component button ${inc.value++} times`);
}

</script>
