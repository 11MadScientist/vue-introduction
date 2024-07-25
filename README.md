<h1>Reactivity Fundamentals</h1>

<h3>Refs</h3>

`ref()`
In Composition API, the recommended way to declare reactive state is using the `ref()` function:
```
import { ref } from 'vue'

const count = ref(0)
```

`ref()` takes the argument and returns it wrapped within a ref object with a `.value` property:

```
const count = ref(0)

console.log(count) // { value: 0 }
console.log(count.value) // 0

count.value++
console.log(count.value) // 1
```
To access refs in a component's template, declare and return them from a component's `setup()` function:
```
import { ref } from 'vue'

export default {
  // `setup` is a special hook dedicated for the Composition API.
  setup() {
    const count = ref(0)

    // expose the ref to the template
    return {
      count
    }
  }
}
```
`<div>{{ count }}</div>`

Notice that we did not append `.value` when using the ref in the template. For convenience, refs are automatically unwrapped when used inside templates with few exceptions.

Ref unwrwapping in templates only applies if the ref is a top-level property in the template render context.

In the example below, `count` and `object` are top-level properties, but `object.id` is not:
```
const count = ref(0)
const object = { id: ref(1) } // value inside the object is ref
```
Therefore, this expression works as expected:
`{{ count + 1 }}`
...while this one does NOT:
`{{ object.id + 1 }}`

The rendered result will be `[object Object]1` because `object.id` is not unwrapped when evaluating the expression and remains a ref object. To fix this, we can destructure `id` into a top-level property:
`const { id } = object`

or access the value:
`{{ object.id.value + 1}}`

Another thing to note is that a ref does get unwrapped if it is the final evaluated value of a text interpolation (i.e a `{{}}` tag), so the following will render `1`:
`{{ object.id }}`

<h3>Ref Mutation</h3>

You can mutate a ref directly in event handlers:
```
<button @click="count++">
  {{ count }}
</button>
```
For more complex logic, we can declare functions that mutate refs in the same scope and expose them as methods alongside the state:
```
import { ref } from 'vue'

export default {
  setup() {
    const count = ref(0)

    function increment() {
      // .value is needed in JavaScript
      count.value++
    }

    // don't forget to expose the function as well.
    return {
      count,
      increment
    }
  }
}
```
Exposed methods can then be used as event handlers:
```
<button @click="increment">
  {{ count }}
</button>
```

<h3>Script Setup</h3>

Manually exposing state and methods via `setup()` can be verbose. Luckily, it can be avoided when using `Single File Components`. We can simply use `<script setup>`:

```
<script setup>
import { ref } from 'vue'

const count = ref(0)

function increment() {
  count.value++
}
</script>

<template>
  <button @click="increment">
    {{ count }}
  </button>
</template>
```
Top-level imports, variables and functions declared in `<script setup>` are automatically usable in the template of the same component. Think of the template as a JavaScript function declared in the same scope - it naturally has access to everything declared alongside it.

<h3>How Refs Works</h3>

When you use a ref in a template, and change the ref's value later, Vue automatically detects the change and updates the DOM accordingly. This is made possible with a `dependency-tracking` based reactivity system. When a component is rendered for the first time, Vue tracks every ref that was used during the render. Later on, when a ref is mutated, it will trigger a re-render for components that are tracking it.

In standard Javascript, there is no way to detect the access or mutation of plain variables. However, we can intercept the get and set operations of an object's properties using getter and setter methods.

The `.value` property gives Vue the opportunity to detect when the ref has been accessed or mutated. Under the hood, Vue performs the tracking in its getter, and performs triggering in its setter. Conceptually, you can think of a ref as an object that looks like this:
```
// pseudo code, not actual implementation
const myRef = {
  _value: 0,
  get value() {
    track()
    return this._value
  },
  set value(newValue) {
    this._value = newValue
    trigger()
  }
}
```
Another nice trait of refs is taht unlike plain variables, you can pass refs into functions while retaining access to the latest value and the reactivity connection. This is particularly useful when refactoring complex logic into reusable code.


<h3>Deep Reactivity</h3>

Refs can hold any value type, including deeply nested objects, arrays, or JavaScript built-in data structures like `Map`.
A ref will make its value deeply reactive. This means you can expect changes to be detected even when you mutate nested objects or arrays:

```
import { ref } from 'vue'

const obj = ref({
  nested: { count: 0 },
  arr: ['foo', 'bar']
})

function mutateDeeply() {
  // these will work as expected.
  obj.value.nested.count++
  obj.value.arr.push('baz')
}
```

It is also possible to opt-out of deep reactivity with `shallow refs`. For shallow refs, only .value access is tracked for reactivity. Shallow refs can be used for optimizing performance by avoiding the observation cost of large objects, or in cases where the inner state is managed by an external library.

<h3>DOM Update Timing</h3>

When you mutate reactive state, the DOM is updated automatically. However, it should be noted that the DOM updates are not applied synchronously. Instaed, Vue buffers them until teh "next tick" in the update cycle to ensure that each component updates only once no matter how many state changes you have made.

To wait for the DOM update to complete a state change, you can use the `nextTick()` global API:
```
import { nextTick } from 'vue'

async function increment() {
  count.value++
  await nextTick()
  // Now the DOM is updated
}
```

<h3>Reactive</h3>

`reactive()`

There is another way to declare reactive state, with the `reactive()` API. Unlike a ref which wraps the inner value in a special object, `reactive()` makes an object itself reactive:
```
import { reactive } from 'vue'

const state = reactive({ count: 0 })
```
Usage in template:
```
<button @click="state.count++">
  {{ state.count }}
</button>
```
Reactive objects are `Javascript Proxies` and behave just like normal objects. The difference is that Vue is able to intercept the access and mutation of all properties of a reactive object for reactivity tracking and triggering.

`reactive()` converts the object deeply: nested objects are also wrapped with `reactive()` when accessed. It is also called by `ref()` internally when the ref value is an object. Similar to `shallow refs`, there is also the `shallowReactive()` API for opting-out of deep reactivity.

<h3>Reactive Proxy vs. Original​</h3>

It is important to note that the returned value from `reactive()` is a `Proxy` of the original object, which is not equal to the original object:
```
const raw = {}
const proxy = reactive(raw)

// proxy is NOT equal to the original.
console.log(proxy === raw) // false
```
Only the proxy is reactive - mutating the original object will not trigger updates. Therefore, the best practice when working with Vue's reactivity system is to exclusively use the proxied version of your state.

To ensure consistent access to the proxy, calling `reactive()` on the same object always returns the same proxy, and calling `reactive()` on an existing proxy also returns that same proxy:
```
// calling reactive() on the same object returns the same proxy
console.log(reactive(raw) === proxy) // true

// calling reactive() on a proxy returns itself
console.log(reactive(proxy) === proxy) // true
```
This rule applies to nested objects as well. Due to deep reactivity, nested objects inside a reactive object are also proxies:
```
const proxy = reactive({})

const raw = {}
proxy.nested = raw

console.log(proxy.nested === raw) // false
```

<h3>Limitations of Reactive</h3>

The `reactive()` API has a few limitations:
1. Limited values types: it only works for object types (objects, arrays, and collection types such as `Map` and `Set`). It cannot hold primitive types such as `string`, `number`, or `boolean`.

2. Cannot replace entire object: since Vue's reactivity tracking works over property access, we must always keep the same reference to the reactive object. This means we can't easily "replace" a reactive object because the reactivity connection to the first reference is lost:
```
let state = reactive({ count: 0 })

// the above reference ({ count: 0 }) is no longer being tracked
// (reactivity connection is lost!)
state = reactive({ count: 1 })
```

3. Not destructure-friendly: when we destructure a reactive object's primitive type properly into local variables, or when we pass that property to a function, we will lose th reactivity connection:
```
const state = reactive({ count: 0 })

// count is disconnected from state.count when destructured.
let { count } = state
// does not affect original state
count++

// the function receives a plain number and
// won't be able to track changes to state.count
// we have to pass the entire object in to retain reactivity
callSomeFunction(state.count)
```

Due to these limitations, we recommend using `ref()` as the primary API for declaring reactive state.

<h3>Additional Ref Unwrapping Details</h3>
<h5>As a Reactive Object Property</h5>

A ref is automatically unwrapped when accessed or mutated as a property of a reactive object. In other words, it behaves like a normal proeprty:
```
const count = ref(0)
const state = reactive({
  count
})

console.log(state.count) // 0

state.count = 1
console.log(count.value) // 1
```
If a new ref is assigned to a property linked to an existing ref, it will replace the old ref:
```
const otherCount = ref(2)

state.count = otherCount
console.log(state.count) // 2
// original ref is now disconnected from state.count
console.log(count.value) // 1
```
Ref unsrapping only happens when nested inside a deep reactive object. It does not apply when it is accessed as a property of a `shallow reactive object`.

<h3>Caveat in Arrays and Collections</h3>

Unlike reactive objects, there is no unwrapping performed when the ref is accessed as an element of a reactive array or a native collection type like `Map`:
```
const books = reactive([ref('Vue 3 Guide')])
// need .value here
console.log(books[0].value)

const map = reactive(new Map([['count', ref(0)]]))
// need .value here
console.log(map.get('count').value)
```