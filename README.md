<h1>List Rendering (sample on App.vue)</h1>

allows you to dynamically render a list of items using the `v-for` directive. This directive is used to iterate over an array or object and render a block of HTML for each item.

Basic Syntax: 
```
<template>
  <ul>
    <li v-for="item in items" :key="item.id">
      {{ item.name }}
    </li>
  </ul>
</template>

<script>
export default {
  data() {
    return {
      items: [
        { id: 1, name: 'Item 1' },
        { id: 2, name: 'Item 2' },
        { id: 3, name: 'Item 3' }
      ]
    };
  }
};
</script>
```