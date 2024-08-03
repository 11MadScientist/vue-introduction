<h1>Computed Property (sample on App.vue)</h1>

Is a reactive property that automatically recompute their values when their dependencies change.
Computed properties are useful for performing extensive operations only when necessary and for deriving state from other state in a way that is declarative and reactive.

Example Syntax:
```
<script setup>
import { reactive, computed } from 'vue'

const author = reactive({
  name: 'John Doe',
  books: [
    'Vue 2 - Advanced Guide',
    'Vue 3 - Basic Guide',
    'Vue 4 - The Mystery'
  ]
})

// a computed ref
const publishedBooksMessage = computed(() => {
  return author.books.length > 0 ? 'Yes' : 'No'
})
</script>

<template>
  <p>Has published books:</p>
  <span>{{ publishedBooksMessage }}</span>
</template>
```

The computed function expects to be passed a getter function, and the returned value is a computed ref. Similar to normal refs, you can access the computed result as `publishedBooksMessage.value`. Computed refs are also auto-unwrapped in templates so you can reference them without `.value` in template expressions.

`computed()` automatically tracks its reactive dependencies, Vue is aware that the computation of `publishedBooksMessage` depends on `author.books`, so it will update any bindings that depend on `publishBooksMessage` when `author.books` changes.

<h3>Computed Caching vs Methods</h3>

You may have noticed we can achieve the same result by using Methods instead of Computed property:

```
<p>{{ calculateBooksMessage() }}</p>

// in component
function calculateBooksMessage() {
  return author.books.length > 0 ? 'Yes' : 'No'
}
```
Instead of a computed property, we can define the same function as a method. For the end result, the two approaches are indeed exactly the same. However, the difference is that computed properties are `cached` based on their reactive dependencies. A computed property will `only re-evaluate` when some of its reactive dependencies have changed. This means as long as `author.books` has not changed, multiple access to `publishBooksMessage` will immediately return the prevviously computed result without having to run the getter function again.

This also means the following computed property will never update, because `Date.now()` is not a reactive dependency:
`const now = computed(() => Date.now());`
In comparison, a method invocation will always run the function whenever a re-render happens.

Why do we need caching? Imagine we have an expensive computed property `list`, which requires looping through a huge array and doing a log of computations. Then we may have other computed properties that in turn depend on `list`. Without caching, we would be executing `list`'s getter many more times than necessary! In cases where you don't want caching, use a method call instead.

<h3>Writable Computed</h3>

Computed properties are by default getter-only. If you attempt to assign a new value to a computed property, you will receive a runtime warning. In the rare cases where you need a "writable" computed property, you can create one by providing both a getter and a setter:
```
<script setup>
import { ref, computed } from 'vue'

const firstName = ref('John')
const lastName = ref('Doe')

const fullName = computed({
  // getter
  get() {
    return firstName.value + ' ' + lastName.value
  },
  // setter
  set(newValue) {
    // Note: we are using destructuring assignment syntax here.
    [firstName.value, lastName.value] = newValue.split(' ')
  }
})
</script>
```
Now when you run `fullName.value = 'John Doe'`, the setter will be invoked and `firstName` and `lastName` will be updated accordingly.

<h3>Best Practices</h3>

<h5>Getters should be side-effect free</h5>

It is important to remember that computed getter functions should only perform "pure computation" and be free of side effects. For example, don't mutate other state, make async requests, or mutate the DOM inside acomputed getter! Think of a computed property as declaratively describing howto derive a value based on other values - its only responsibility should be computing and returning that value. `watchers` should be used to perform side effects in reaction to state changes.

<h5>Avoid mutating computed value</h5>

The returned value from a computed property is derived state. Think of it as a temporary snapshot - every time the source state changes, a new snapshot is created. It does not make sense to mutate a snapshot, so a computed return value should be treated as read-only and never be mutated - instead, update the source state it depends on to trigger new computations.