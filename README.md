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

Inside the `v-for` scope, template expressions have access to all parent scope properties. In addition, `v-for` also supports an optional second alias for the index of the current item:

```
const parentMessage = ref('Parent')
const items = ref([{ message: 'Foo' }, { message: 'Bar' }])
```

```
<li v-for="(item, index) in items">
  {{ parentMessage }} - {{ index }} - {{ item.message }}
</li>
```
output:
```
Parent - 0 - Foo
Parent - 1 - Bar
```

The variable scoping of `v-for` is similar to the following javascript:
```
const parentMessage = 'Parent'
const items = [
  /* ... */
]

items.forEach((item, index) => {
  // has access to outer scope `parentMessage`
  // but `item` and `index` are only available in here
  console.log(parentMessage, item.message, index)
})
```
Notice how the `v-for` value matches the function signature of the `forEach` callback. In fact, you can use destructuring on the `v-for` item alias similar to destructuring function arguments:

```
<li v-for="{ message } in items">
  {{ message }}
</li>

<!-- with index alias -->
<li v-for="({ message }, index) in items">
  {{ message }} {{ index }}
</li>
```

For nested `v-for`, scoping also works similar to nested functions. Each `v-for` scope has access to parent scopes:
```
<li v-for="item in items">
  <span v-for="childItem in item.children">
    {{ item.message }} {{ childItem }}
  </span>
</li>
```

You can also use `of` as the delimiter of `in`, so that it is closer to Javascript's syntax for iterators:

```
<div v-for="item of items"></div>
```

<h3>

`v-for` with an Object

</h3>

You can also use `v-for` to iterate through the properties of an object. The iteration order will be based on the result of calling `Object.keys()` on the object:

```
const myObject = reactive({
  title: 'How to do lists in Vue',
  author: 'Jane Doe',
  publishedAt: '2016-04-10'
})
```
```
<ul>
  <li v-for="value in myObject">
    {{ value }}
  </li>
</ul>
```

You can also provide a second alias for the property's name (a.k.a key):
```
<li v-for="(value, key) in myObject">
  {{ key }}: {{ value }}
</li>
```
And anothe fo the index:
```
<li v-for="(value, key, index) in myObject">
  {{ index }}. {{ key }}: {{ value }}
</li>
```

<h3>

`v-for` with a Range

</h3>

`v-for` can also take an integer. In this case it will repeat the template that many times, based on a range of `1...n`.

```
<span v-for="n in 10">{{ n }}</span>
```
Note here `n` starts with an initial value of `1` instead of `0`.

<h3>

`v-for` on `<template>`

</h3>

Similar to template `v-if`, you can also use a `<template>` tag wit h`v-for` to render a block of multiple elements. For example:

```
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider" role="presentation"></li>
  </template>
</ul>
```

<h3>

`v-for` with `v-if`

</h3>

(Note: it's not recommended to use `v-if` and `v-for` on the same element due to implicit precedence.)

When they exist on the same node, `v-if` has a highe priority than `v-for`. That means the `v-if` condition will not have access to the variables from the scope of `v-for`:

```
<!--
This will throw an error because property "todo"
is not defined on instance.
-->
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo.name }}
</li>
```

This can be fixed by moving `v-for` to a wrapping `<template>` tag (which is also more explicit):
```
<template v-for="todo in todos">
  <li v-if="!todo.isComplete">
    {{ todo.name }}
  </li>
</template>
```

<h3> 

Maintaining State with `key`

</h3>

When Vue is updating a list of elements rendered with `v-for`, by default it uses an "in-place patch" strategy. If the order of the data items has changed, instead of moving the DOM elements to match the order of the items, Vue will patch each element in-place and make sure it reflects what should be rendered at that particular index.

This default mode is efficient, but only suitable when your list render output does not rely on child component state or temporary DOM state (e.g form input variables).

To give Vue a hit so that it can track each node's identity, and thus reuse and reorder existing elements, you need to provide a unique `key` attribute for each item:

```
<div v-for="item in items" :key="item.id">
  <!-- content -->
</div>
```

When using `<template v-for>`, the `key` should be placed on the `<template>` container:
```
<template v-for="todo in todos" :key="todo.name">
  <li>{{ todo.name }}</li>
</template>
```
(Note: `key` here is a special attribute being bound with `v-bind`. It should not be confused with the property key variable when using `v-for` with an object)

It is recommended to provide `key` attribute with `v-for` whenever possible, unless the iterated DOM content is simple (i.e contains no components or stateful DOM elements), or you are intentionally relying on the default behavior for performance gains.

The `key` binding expects primitive values - i.e strings and numbers. Do not use objects as `v-for` keys.

<h3>

`v-for` with a Component

</h3>

You can directly use `v-for` on a component, like any normal element:

```
<MyComponent v-for="item in items" :key="item.id" />
```

However, this won't automatically pass any data to the component, because components have isolated scopes of their own. In order to pass the iterated data into the component, we should also use props:
```
<MyComponent
  v-for="(item, index) in items"
  :item="item"
  :index="index"
  :key="item.id"
/>
```
The reason for not automatically injecting `item` into the component is because that makes the component tightly coupled to how `v-for` works. Being explicit about where its data comes from makes the component reusable in other situations.

<h3>Array Change Detection</h3>

<h4>Mutation Methods</h4>

Vue is able to detect when a reactive array's mutation methods are called and trigger necessary updates. These mutation methods are:
- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`

<h4>Replacing an Array</h4>

Mutation methods, as the name suggests, mutate the original array they are called on. In comparison, there are also non-mutating methods, e.g `filter()`, `concat()` and `slice()`, which do not mutate the original array but always return a new array. When working with non-mutating methods, we should replace the old array with the new one:

```
// `items` is a ref with array value
items.value = items.value.filter((item) => item.message.match(/Foo/))
```

You might think this will cause Vue to throw away the existing DOM and re-render the entire list - luckily, that is not the case. vue implements some smart heuristics to maximize DOM element use, so replacing an array with another array containing overlapping objects is a very efficient operation.

<h3>Displaying Filtered/Sorted Results</h3>

Sometimes we want to display a filtered or sorted version of an array without actually mutating or resetting the original data. In this case, you can create a computed property that returns the filtered or sorted array.
For example:
```
const numbers = ref([1, 2, 3, 4, 5])

const evenNumbers = computed(() => {
  return numbers.value.filter((n) => n % 2 === 0)
})
```
```
<li v-for="n in evenNumbers">{{ n }}</li>
```
In situations where computed properties are not feasable (e.g. inside nested `v-for` loops), you can use a method:

```
const sets = ref([
  [1, 2, 3, 4, 5],
  [6, 7, 8, 9, 10]
])

function even(numbers) {
  return numbers.filter((number) => number % 2 === 0)
}
```
```
<ul v-for="numbers in sets">
  <li v-for="n in even(numbers)">{{ n }}</li>
</ul>
```

Be careful with `reverse()` and `sort()` in a computed property! These two methods will mutate the original array, which should be avoided in computed getters. Create a copy of the original array before calling these methods:

```
- return numbers.reverse()
+ return [...numbers].reverse()
```

