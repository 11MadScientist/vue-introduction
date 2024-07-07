<h1>Props (sample on App.vue)</h1>

`Props` are a way to pass data from a parent component to a child component. Props are short for `"properties", and they allow you to customize and configure child components by passing data down from the parent.

<h3>Defining Props in the Child Component</h3>

First, you need to define the props in the child Component. You can do this using the `props` option in the component's setup function or as part of the component's option object.
ex:
```
<template>
  <h2>{{ title }}</h2>
  <h2>{{ msg || 'No props passed yet' }}</h2>
</template>
<script setup>
const props = defineProps({
  msg: String
})
</script>
```

Note: `defineProps()` is a compile-time macro and doesn't need to be imported. Once declared, the msg prop can be used in the child component's template. It can also be accessed in Javascript via the returned object of `defineProps()`.

<h3>Passing Props from the Parent Component</h3>

In the parent component, you can pass props to the child component by adding attributes to the child componenet's tag and binding them to the parent component's data
ex:
```
<template>
  <ChildComponent title="This is Title" :msg="msg"/>
</template>
<script setup>
  import ChildComponent from './components/icons/Component1.vue';
  const msg = "Message from parent passed to child";
</script>
```
Note: to pass a dynamic value, we can use the `v-bind` syntax (`:msg="msg"`)

<h3>Prop Validation</h3>

Vue allows you to specify prop validation by defining the expected type of the prop and whether it is required

ex:
```
<script setup>
const props = defineProps({
  msg: {
    type: String,
    required: true
  }
});
</script>
```

<h3>Default Prop Values</h3>

You can specify the default value for props that are not required.
ex:
```
<script setup>
const props = defineProps({
  msg: {
    type: String,
    required: false,
    default: "Hello there, I'm not required"
  }
});
</script>
```
