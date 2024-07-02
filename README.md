<h1>Watchers (sample on App.vue)</h1>

`Watchers` are used to perform actions in response to changes in reactive data. They are particularly useful for executing asynchronous operations, complex data transformations, or other side effects when reactive data changes.

<h3>Basic Usage of Watchers</h3>

You can create a watcher using `watch` function from the composition API. The `watch` function takes two arguements: the reactive data to watch and the callback function to execute when the data changes.

Example:
```
<template>
  <div>
    <input v-model="name" placeholder="Enter your name">
    <p>{{ name }}</p>
  </div>
</template>

<script>
import { ref, watch } from 'vue';

export default {
  setup() {
    const name = ref('');

    watch(name, (newValue, oldValue) => {
      console.log(`Name changed from ${oldValue} to ${newValue}`);
    });

    return {
      name,
    };
  },
};
</script>
```
Explaination
1. Reactive variable: `name` is defined as a reactive variable using `ref`.
2. Watcher: `watch` function is used to watch changes to `name`. The callback function logs the old and new values whenever `name` changes.


<h3>Watching Multiple Sources</h3>

You can watch multiple sources by passing an array of sources to the`watch` function.

Example:
```
<template>
  <div>
    <input v-model="firstName" placeholder="First Name">
    <input v-model="lastName" placeholder="Last Name">
    <p>Full Name: {{ fullName }}</p>
  </div>
</template>

<script>
import { ref, computed, watch } from 'vue';

export default {
  setup() {
    const firstName = ref('');
    const lastName = ref('');

    const fullName = computed(() => {
      return `${firstName.value} ${lastName.value}`;
    });

    watch([firstName, lastName], ([newFirstName, newLastName], [oldFirstName, oldLastName]) => {
      console.log(`First Name changed from ${oldFirstName} to ${newFirstName}`);
      console.log(`Last Name changed from ${oldLastName} to ${newLastName}`);
    });

    return {
      firstName,
      lastName,
      fullName,
    };
  },
};
</script>
```

Explanation
1. Reactive Variables: `firstName` and `lastName` are defined as reactive variables using ref.
2. Computed Property: `fullName` is defined as a computed property that concatenates `firstName` and `lastName`.
3. Watcher: The `watch` function is used to watch changes to both `firstName` and `lastName`.The callback function logs the old and new values for each.

<h3>Deep Watchers</h3>

By default, `watch` only watches the reference itself, not the nested properties. To watch for changes inside an object or array, you need to use `deep` option.

Example:
```
<template>
  <div>
    <input v-model="user.name" placeholder="Name">
    <input v-model="user.age" placeholder="Age">
    <p>Name: {{ user.name }}</p>
    <p>Age: {{ user.age }}</p>
  </div>
</template>

<script>
import { ref, watch } from 'vue';

export default {
  setup() {
    const user = ref({
      name: '',
      age: ''
    });

    watch(user, (newValue, oldValue) => {
      console.log(`User changed from`, oldValue, `to`, newValue);
    }, { deep: true });

    return {
      user,
    };
  },
};
</script>
```

Explaination
1. Reactive Object: `user` is defined as a reactive object using `ref`.
2. Deep Watcher: the `watch` function is used with the `deep` option set to `true` to watch changes inside the `user` object. The callback function logs the old and new values whenever any property inside the`user` changes.

<h3>Immediate Watchers</h3>

The `watch` function also provides an `immediate` option, which triggers the callback immediately when the watcher is created.

Example:
```
<template>
  <div>
    <p>{{ message }}</p>
  </div>
</template>

<script>
import { ref, watch } from 'vue';

export default {
  setup() {
    const message = ref('Hello, Vue 3!');

    watch(message, (newValue, oldValue) => {
      console.log(`Message changed from ${oldValue} to ${newValue}`);
    }, { immediate: true });

    return {
      message,
    };
  },
};
</script>
```

Explaination:
1. Reactive Variable: `message` is defined as a reactive variable using `ref`.
2. Immediate watcher: `watch` function is used with the `immediate` option to set to `true` to trigger the callback immediately when the watcher is created.

Summary
- `Basic Watcher` perform actions in repsonse to changes in reactive data.
- `Mulitple Sources` watch multiple reactive data sources by passing an array.
- `Deep Watcher` uses the `deep` option to watch for changes inside an object or array.
- `Immediate Watcher` uses the `immediate` option to trigger the callback immediately when watcher is created.