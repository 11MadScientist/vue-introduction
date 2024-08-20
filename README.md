<h1>Lifecycle Hooks and Template Refs (sample on App.vue)</h1>

<h3>Lifecycle Hooks</h3>

`Lifecycle Hooks` allows you to perform actions at specific stages of a component's lifecycle.
Here are commonly used lifecycle Hooks:
1. `onBeforeMount`: Called before the component is mounted.
2. `onMounted`: Called after the component is mounted.
3. `onBeforeUpdate`: Called before the component updates.
4. `onUpdated`: Called after the component updates.
5. `onBeforeUnmount`: Called before the component is unmounted.
6. `onUnmounted`: Called after the component is unmounted.

<h4>Registering Lifecycle Hooks</h4>

For example, the `onMounted` hook can be used to run code after component has finished the initial rendering and created DOM nodes:
```
<script setup>
import { onMounted } from 'vue'

onMounted(() => {
  console.log(`the component is now mounted.`)
})
</script>
```

When calling `onMounted`, Vue automatically associates the registered callback function with the current active component instance. This requires these hooks to be registered synchronously during component setup. For example, do not do this:
```
setTimeout(() => {
  onMounted(() => {
    // this won't work.
  })
}, 100)
```

Do note this doesn't mean that the call must be placed lexically inside `setup()` or `<script setup>`. `onMounted()` can be called in an external function as long as the call stack is synchronous and originates from within `setup()`.


<h3>Template Refs</h3>

While Vue's declarative rendering model abstracts away most of the direct DOM operations for you, there may still be cases where we need direct access to the underlying DOM elements. To achieve this, we can use the special `ref` attribute:
```
<input ref="input">
```
`ref` is a special attribute, similar to the `key` attribute discussed in `v-for`. It allows us to obtain a direct reference to a specific DOM element or child component instance after it's mounted. This may be useful when you want to, for example, programatically focus an input on component mount, or initialize a 3rd party library on an element.

<h4>Accessing the Refs</h4>

To obtain the reference with Composition API, we need to declare a ref with a name that matches the template ref attribute's value:
```
<script setup>
import { ref, onMounted } from 'vue'

// declare a ref to hold the element reference
// the name must match template ref value
const input = ref(null)

onMounted(() => {
  input.value.focus()
})
</script>

<template>
  <input ref="input" />
</template>
```
If not using `<script setup>`, make sure to also return the ref from `setup()`:
```
export default {
  setup() {
    const input = ref(null)
    // ...
    return {
      input
    }
  }
}
```

Note that you can only access the ref after the component is mounted. If you try to access `input` in template expression, it will be `null` on the first render. This is because the element doesn't exist until after the first render!

If you are trying to watch the changes of a template ref, make sure to account for the case where the ref has `null` value:
```
watchEffect(() => {
  if (input.value) {
    input.value.focus()
  } else {
    // not mounted yet, or the element was unmounted (e.g. by v-if)
  }
})
```

<h4>

Refs inside `v-for`

</h4>

When `ref` is used inside `v-for`, the corresponding ref should contain an Array value, which will be populated with the elements after mount:
```
<script setup>
import { ref, onMounted } from 'vue'

const list = ref([
  /* ... */
])

const itemRefs = ref([])

onMounted(() => console.log(itemRefs.value))
</script>

<template>
  <ul>
    <li v-for="item in list" ref="itemRefs">
      {{ item }}
    </li>
  </ul>
</template>
```
It should be noted that the ref array does not guarantee the same order as the source array.

<h4>Function Refs</h4>

Instead of a string key, the `ref` attribute can also be bound to a function, which will be called on each component update and gives you full flexibility on where to store the element reference. The function receives the element reference in the first argument:
```
<input :ref="(el) => { /* assign el to a property or ref */ }">
```
Note we are using a dynamic `:ref` binding so we can pass it a function instead of a ref name string. When the element is unmounted, the argument will be null. You can, of course, use a method instead of an inline function.

<h4>Ref on Component</h4>

`ref` can also be used on a child component. In this case the reference will be that of a component instance:
```
<script setup>
import { ref, onMounted } from 'vue'
import Child from './Child.vue'

const child = ref(null)

onMounted(() => {
  // child.value will hold an instance of <Child />
})
</script>

<template>
  <Child ref="child" />
</template>
```
If the child component is using Options API or not using `<script setup>`, the referenced instance will be identical to the child component's `this`, which means the parent component will have full access to every property and method of the child component. This makes it easy to create tightly coupled implementation details between the parent and the child, so component refs should be only used when absolutely needed - in most cases, you should try to implement parent / child interactions using the standard props and emit interfaces first.

An exception here is that components using `<script setup>` are private by default: a parent component referencing a child component using `<script setup>` won't be able to access anything unless the child component chooses to expose a public interface using the `defineExpose` macro:
```
<script setup>
import { ref } from 'vue'

const a = 1
const b = ref(2)

// Compiler macros, such as defineExpose, don't need to be imported
defineExpose({
  a,
  b
})
</script>
```
When a parent gets an instance of this component via template refs, the retrieved instance will be of the shape `{a:number, b:number}` (refs are automatically unwrapped just like normal instances).

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