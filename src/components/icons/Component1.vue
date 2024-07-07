<template>
  <div>
    {{ title }}
    <br>
    {{ msg }}
    <br>
    {{ notRequiredProp }}
  </div>
</template>

<script setup>
const props = defineProps({
  title: String,
  msg: {
    type: String,
    required: true
  },
  notRequiredProp: {
    type: String,
    required: false,
    default: "Hello there, I'm not required, so I have a default value."
  }

});

</script>
