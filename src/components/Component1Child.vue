<template>
  <input type="text" v-model="childCopyTitle">
  <br><br>
  <h3>{{ withDefaultInjection }}</h3>
</template>

<script setup>
import {inject, watch, ref} from 'vue';

// injection
const {title, updateTitle} = inject('provide-title');

// injection with default value
const withDefaultInjection = inject('unprovide-message', 'default message chaching');

// create copy of title to be used as the v-model
const childCopyTitle = ref(title.value);

// using watcher to update the title using the function with the value of childCopyTitle
watch(childCopyTitle, (newChildCopyTitle) => {
  updateTitle(newChildCopyTitle);
})

</script>
