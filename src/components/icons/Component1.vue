<template>
  <div>
    <!-- using method to emit an event-->
   <button @click="clickHandler">Click Me</button>

   <!-- using emit in template -->
   <button @click="$emit('clickResponse2', 
   `clicked the child component button ${inc--} times`)">Click Me 2</button>
  </div>
</template>

<script setup>
import {ref} from 'vue';
//defines the emits that the child can do
const emits = defineEmits([
  'clickResponse',
  'clickResponse2'
]);

const inc = ref(0);

const clickHandler = () => {
  // emits the `click-response` which will now inform the parent
  emits('clickResponse', `clicked the child component button ${inc.value++} times`);
}

</script>
