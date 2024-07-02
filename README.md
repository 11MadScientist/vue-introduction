<h1>Lifecycle Hooks and Template Refs (sample on App.vue)</h1>

<h3>Lifecycle Hooks</h3>

`Lifecycle Hooks` allows you to perform actions at specific stages of a component's lifecycle.
Herea re commonly used lifecycle Hooks:
1. `onBeforeMount`: Called before the component is mounted.
2. `onMounted`: Called after the component is mounted.
3. `onBeforeUpdate`: Called before the component updates.
4. `onUpdated`: Called after the component updates.
5. `onBeforeUnmount`: Called before the component is unmounted.
6. `onUnmounted`: Called after the component is unmounted.

<h3>Template Refs</h3>

`Template Refs` are used to directly access DOM elements or child components in the template.
You can use `ref` function from the Composition API to create template refs.

Example of using template refs to access a DOM element:
```
<template>
  <div>
    <input ref="inputRef" placeholder="Type something">
    <button @click="focusInput">Focus Input</button>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue';

export default {
  setup() {
    const inputRef = ref(null);

    const focusInput = () => {
      if (inputRef.value) {
        inputRef.value.focus();
      }
    };

    onMounted(() => {
      console.log('Component mounted and inputRef:', inputRef.value);
    });

    return {
      inputRef,
      focusInput,
    };
  },
};
</script>

```

<h3>Combining Lifecycle Hooks and Template Refs</h3>

You can combine `lifecycle hooks` and `template refs` to perform actions on DOM elements or child components at specific stages of the component's lifecycle. For Example, you might want to set the focus on an input element when the component is mounted.

Example:
```
<template>
  <div>
    <input ref="inputRef" placeholder="Type something">
  </div>
</template>

<script>
import { ref, onMounted } from 'vue';

export default {
  setup() {
    const inputRef = ref(null);

    onMounted(() => {
      if (inputRef.value) {
        inputRef.value.focus();
      }
    });

    return {
      inputRef,
    };
  },
};
</script>

```

By using `lifecycle hooks` and `template refs`, you can manage the component's lifecycle and interact with the DOM more effectively.