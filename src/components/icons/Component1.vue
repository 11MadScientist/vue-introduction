<template>
  <div>
    <input type="text" v-model="firstName">
    <input type="text" v-model="lastName">
  </div>
</template>

<script setup>
const [firstName, firstNameModifiers] = defineModel('firstName', {
  set(value) {
    console.log(value);
    if (firstNameModifiers.uppercase) {
      return value.toUpperCase();
    }
    return value;
  }
});
const [lastName, lastNameModifiers] = defineModel('lastName', {
  set(value) {
    if (lastNameModifiers.uppercase) {
      return value.toUpperCase();
    }
    return value;
  }
});


</script>
