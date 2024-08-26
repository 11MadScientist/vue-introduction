<h1>Props (sample on App.vue)</h1>

<h3>Props Declaration</h3>

Vue components require explicit props declaration so that Vue knows what external props passed to the component should be treated as fallthrough attributes (go to fallthrough-attributes branch to learn more).

In SFCs using `<script setup>`, props can be declared usign the `defineProps()` macro:
```
<script setup>
const props = defineProps(['foo'])

console.log(props.foo)
</script>
```

In non-`<script setup>` components, props are declared using the `props` option:
```
export default {
  props: ['foo'],
  setup(props) {
    // setup() receives props as the first argument.
    console.log(props.foo)
  }
}
```

Notice the argument passed to `defineProps()` is the same as the value provided to the `props` options: the same props options API is shared between the two declaration styles.

In addition to declaring props using an array of strings, we can also use the object syntax:
```
// in <script setup>
defineProps({
  title: String,
  likes: Number
})
```
```
// in non-<script setup>
export default {
  props: {
    title: String,
    likes: Number
  }
}
```

For each property in the object declaration syntax, the key is the name of the prop, while the value should be the constructor function of the expected tyupe.

This not only documents your component, but will also warn other developers using your component in the browser console if they pass the wrong type. (Can learn more on this topic in Prop Validation section below).

If you are using TypeScript with `<script setup>`, it's also possible to declare props using pure type annotations:
```
<script setup lang="ts">
defineProps<{
  title?: string
  likes?: number
}>()
</script>
```

<br><br><h3>Prop Passing Details</h3>

<h4>Prop Name Casing</h4>

We declare long prop names using camelCase because this avoids having to use quotes when using them as property keys, and allows us to reference them directly in template expressions because they are valid JavaScript identifiers:
```
defineProps({
  greetingMessage: String
})
```
```
<span>{{ greetingMessage }}</span>
```

Technically, you can also use camelCase when passing props to a child component (except in in-DOM templates). However, the convention is using kebab-case in all cases to align with HTML attributes:
```
<MyComponent greeting-message="hello" />
```

We use PascalCase for component tags when possible because it improves template readability by differentiating Vue components from native elements. However, there isn't as much practical benefit in using camelCase when passing props, so we choose to follow each language's conventions.

<br><h4>Static vs. Dynamic Props</h4>

So far, you've seen props passed as static values, like in:
```
<BlogPost title="My journey with Vue" />
```

You've also seen props assigned dynamically with `v-bind` or its `:` shortcut, such as in:
```
<!-- Dynamically assign the value of a variable -->
<BlogPost :title="post.title" />

<!-- Dynamically assign the value of a complex expression -->
<BlogPost :title="post.title + ' by ' + post.author.name" />
```

<br><h4>Passing Different Value Types</h4>

In the two examples above, we happen to pass string values, but any type of value can be passed to a prop.

<h4>Number</h4>

```
<!-- Even though `42` is static, we need v-bind to tell Vue that -->
<!-- this is a JavaScript expression rather than a string.       -->
<BlogPost :likes="42" />

<!-- Dynamically assign to the value of a variable. -->
<BlogPost :likes="post.likes" />
```

<h4>Boolean</h4>

```
<!-- Including the prop with no value will imply `true`. -->
<BlogPost is-published />

<!-- Even though `false` is static, we need v-bind to tell Vue that -->
<!-- this is a JavaScript expression rather than a string.          -->
<BlogPost :is-published="false" />

<!-- Dynamically assign to the value of a variable. -->
<BlogPost :is-published="post.isPublished" />
```

<h4>Array</h4>

```
<!-- Even though the array is static, we need v-bind to tell Vue that -->
<!-- this is a JavaScript expression rather than a string.            -->
<BlogPost :comment-ids="[234, 266, 273]" />

<!-- Dynamically assign to the value of a variable. -->
<BlogPost :comment-ids="post.commentIds" />
```

<h4>Object</h4>

```
<!-- Even though the object is static, we need v-bind to tell Vue that -->
<!-- this is a JavaScript expression rather than a string.             -->
<BlogPost
  :author="{
    name: 'Veronica',
    company: 'Veridian Dynamics'
  }"
 />

<!-- Dynamically assign to the value of a variable. -->
<BlogPost :author="post.author" />
```

<br><h4>Binding Multiple Properties Using an Object</h4>

If you want to pass all the properties of an object as props, you can use `v-bind` without an argument (`v-bind` instaed of `:prop-name`). For example, given a `post` object:
```
const post = {
  id: 1,
  title: 'My Journey with Vue'
}
```

The following template:
```
<BlogPost v-bind="post" />
```

Will be equivalent to:
```
<BlogPost :id="post.id" :title="post.title" />
```

<br><br><h3>One-Way Data Flow</h3>

All props form a one-way-down binding between the child property and the parent one: when the parent property updates, it will flow down to the child, but not the other way around. This prevents child components from accidentaly mutating the parent's state, which can make your app's data flow harder to understand.

In addition, everytime the parent component is updated, all props in the child component will be refreshed with the latest value. this means you should not attempt to mutate a prop inside a child component. If you do, Vue will warn you in the console:
```
const props = defineProps(['foo'])

// ❌ warning, props are readonly!
props.foo = 'bar'
```

There are usually two cases where it's tempting to mutate a prop:
1. The prop is used to pass in an initial value; the child component wants to use it as a local data property afterwards. In this case, it's best to define a local data property that uses the prop as its initial value:
```
const props = defineProps(['initialCounter'])

// counter only uses props.initialCounter as the initial value;
// it is disconnected from future prop updates.
const counter = ref(props.initialCounter)
```

2. The prop is passed in as a raw value that needs to be transformed. In this case, it's best to define a computed property using the prop's value:
```
const props = defineProps(['size'])

// computed property that auto-updates when the prop changes
const normalizedSize = computed(() => props.size.trim().toLowerCase())
```

<br><h4>Mutating Object / Array Props</h4>

When objects and arrays are passed as props, while the child component cannot mutate the prop binding, it will be able to mutate the object or array's nested properties. This is because JavaScript objects and arrays are passed by reference, and it is unreasonably expensive for Vue to prevent such mutations.

The main drawback of such mutations is that it allows the child component to affect parent state in a way that isn't very obvious to the parent component, potentially making it more difficult to reason about the data flow in the future. As a best practice, you should avoid such mutations unless the parent and child are tightly coupled by design. In most cases, the child should emit an event to let the parent perform the mutations.

<br><br><h3>Prop Validation</h3>

Components can specify requirements for their props, such as the types you've already seen. If a requirement is not met, Vue will warn you in the browser's JavaScript console. This is especially useful when developing a component that is intended to be used by others.

To specify prop validations, you can provide an object with validation requirements to the `defineProps()` macro, instead of an array of strings. For example:
```
defineProps({
  // Basic type check
  //  (`null` and `undefined` values will allow any type)
  propA: Number,
  // Multiple possible types
  propB: [String, Number],
  // Required string
  propC: {
    type: String,
    required: true
  },
  // Required but nullable string
  propD: {
    type: [String, null],
    required: true
  },
  // Number with a default value
  propE: {
    type: Number,
    default: 100
  },
  // Object with a default value
  propF: {
    type: Object,
    // Object or array defaults must be returned from
    // a factory function. The function receives the raw
    // props received by the component as the argument.
    default(rawProps) {
      return { message: 'hello' }
    }
  },
  // Custom validator function
  // full props passed as 2nd argument in 3.4+
  propG: {
    validator(value, props) {
      // The value must match one of these strings
      return ['success', 'warning', 'danger'].includes(value)
    }
  },
  // Function with a default value
  propH: {
    type: Function,
    // Unlike object or array default, this is not a factory
    // function - this is a function to serve as a default value
    default() {
      return 'Default function'
    }
  }
})
```

Tip: Code inside the `defineProps()` argument cannot access other variables declared in `<script setup>`, because the entire expression is moved to an outer function scope when compiled.

Additional details:
- All props are optional by default, unless `required: true` is specified.
- An absent optional prop other than `Boolean` will have `undefined` value.
- The `Boolean` absent props will be cast to `false`. You can change this by setting a `default` for it - i.e: `default: undefined` to behave as a non-Boolean prop.
- If a `default` value is specified, it will be used if the resolved prop value is `undefined` - this includes both when prop is abasent, or an explicit `undefined` value is passed.

When prop validation fails, Vue will produce a console warning (if using the development build).

If using Type-based props declarations(TS), Vue will try its best to compile the type annotations into equivalent runtime prop declarations. For example, `defineProps<{msg: string}>` will be compiled into `{msg: {type: String, required: true }}`.

<br><h4>Runtime Type Checks</h4>

The `type` can be one of the following native constructors:
- `String`
- `Number`
- `Boolean`
- `Array`
- `Object`
- `Date`
- `Function`
- `Symbol`
- `Error`

In addition, `type` can also be a custom class or constructor function and the assertion will be made with an `instanceof` check. For example, given the following class:
```
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName
    this.lastName = lastName
  }
}
```

You could use it as a prop's type:
```
defineProps({
  author: Person
})
```

Vue will use `instanceof Person` to validate whether the value of the `author` prop is indeed an instance of the `Person` class.

<br><h4>Nullable Type</h4>

If the type is required but nullable, you can use the array syntax that includes `null`:
```
defineProps({
  id: {
    type: [String, null],
    required: true
  }
})
```

<br><br><h3>Boolean Casting</h3>

Props with `Boolean` type have special casting rules to mimic the behavior of native boolean attributes. Given a `<MyComponent>` with the following declaration:
```
defineProps({
  disabled: Boolean
})
```

The component can be used like this:
```
<!-- equivalent of passing :disabled="true" -->
<MyComponent disabled />

<!-- equivalent of passing :disabled="false" -->
<MyComponent />
```

When a prop is declared to allow multiple types, the casting rules for `Boolean` will also be applied. However, there is an edge when both `String` and `Boolean` are allowed - the Boolean casting rule only applies if `Boolean` appears before `String`:
```
// disabled will be casted to true
defineProps({
  disabled: [Boolean, Number]
})

// disabled will be casted to true
defineProps({
  disabled: [Boolean, String]
})

// disabled will be casted to true
defineProps({
  disabled: [Number, Boolean]
})

// disabled will be parsed as an empty string (disabled="")
defineProps({
  disabled: [String, Boolean]
})
```