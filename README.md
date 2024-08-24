<h1>Components (sample on App.vue)</h1>

<h3>Defining a Component</h3>

When using a build step, we typically define each Vue component in a dedicated file usingthe `.vue` extension - known as a "Single-File Component" (SFC):
```
<script setup>
import { ref } from 'vue'

const count = ref(0)
</script>

<template>
  <button @click="count++">You clicked me {{ count }} times.</button>
</template>
```

When not using a build step, Vue component can be defined as a plain JavaScript object containing Vue-specific options:
```
import { ref } from 'vue'

export default {
  setup() {
    const count = ref(0)
    return { count }
  },
  template: `
    <button @click="count++">
      You clicked me {{ count }} times.
    </button>`
  // Can also target an in-DOM template:
  // template: '#my-template-element'
}
```

The template is inlined as a JavaScript string here, which Vue will compile on the fly. You can also use an ID selector pointing to an element (usually native `<template>` elements)
- Vue will use its content as the template source.

The example above defines a single component and exports it as a default export of a `.js` file, but you can use named exports to export multiple components from the same file.

<br><br><h3>Using a Component</h3>

To use a child component, we need to import it in the parent component. Assuming we placed our counter component inside a file called `ButtonCounter.vue`, the component will be exposed as the file's default export:
```
<script setup>
import ButtonCounter from './ButtonCounter.vue'
</script>

<template>
  <h1>Here is a child component!</h1>
  <ButtonCounter />
</template>
```

With `<script setup>`, imported components are automatically made available to the template.

It is also possible to globally register a component, making it available to all components in a given app without having to import it.

Components can be reused as many times as you want:
```
<h1>Here are many child components!</h1>
<ButtonCounter />
<ButtonCounter />
<ButtonCounter />
```

Notice that when clicking on the buttons (run the codes above), each one maintains its own, separate `count`.
That's because each time you use a component, a new instance of it is created.

In SFCs, it's recommended to use `PascalCase`(name begins with an uppercase letter. For example:MyFirstVariable) tag names for child components to differentiate from native HTML elements. Although native HTML tag names are case-insensitive, Vue SFC is a compiled format so we are able to use case-sensitive tag names in it. We are also able to use `/>` to close a tag.

If you are authoring your templates directly in a DOM (e.g. as the content of a native `<template>` element), the template will be subject to the browser's native HTML parsing behavior. In such cases, you will need to use `kebab-case` and explicit closing tags for comonents:
```
<!-- if this template is written in the DOM -->
<button-counter></button-counter>
<button-counter></button-counter>
<button-counter></button-counter>
```

<br><br><h3>Passing Props</h3>

If we are building a blog, we will likely need a component representing a blog post. We want all the blog posts to share the same visual layout, but with different content. Such as component won't be useful unless you can pass data to it, such as the title and content of the specific post we want to display. That's where props come in.

Props are custom attributes you can register on a component. To pass a title to our blog post component, we must declare it in the list of props this component accepts, using the `defineProps` macro:
```
<!-- BlogPost.vue -->
<script setup>
defineProps(['title'])
</script>

<template>
  <h4>{{ title }}</h4>
</template>
```

`defineProps` is a compile-time macro that is only available inside `<script setup>` and does not need to be explicitly imported. Declared props are automatically exposed to the template. `defineProps` also returns an object that contains all the props passed to the component, so that we can access them in JavaScript if needed:
```
const props = defineProps(['title'])
console.log(props.title)
```

If you are not using `<script setup>`, props should be declared using the `props` option, and the props object will be passed to `setup()` as the first argument:
```
export default {
  props: ['title'],
  setup(props) {
    console.log(props.title)
  }
}
```

A component can have as many props as you like and , by default, any value can be passed to any prop.

Once a prop is registered, you can pass data to it as a custom attribute, like this:
```
<BlogPost title="My journey with Vue" />
<BlogPost title="Blogging with Vue" />
<BlogPost title="Why Vue is so fun" />
```

In a typical app, however, you'll likely have an array of posts in your parent component:
```
const posts = ref([
  { id: 1, title: 'My journey with Vue' },
  { id: 2, title: 'Blogging with Vue' },
  { id: 3, title: 'Why Vue is so fun' }
])
```

Then want to render a component for each one,m using `v-for`:
```
<BlogPost
  v-for="post in posts"
  :key="post.id"
  :title="post.title"
 />
```

Notice how `v-bind` syntax (`:title="post.title"`) is used to pass dynamic prop values. This is especially useful when you don't know the exact content you're going to render ahead of time.

<h4>See the props branch to dig deeper into this topic</h4>

<br><br><h3>Listening to Events</h3>

As we develop our `<BlogPost>` component, some features may require communicating back up to the parent. For example, we may decide to include an accessibility feature to enlarge the text of blog posts, while leaving the rest of the page as its default size.

In the parent, we can support this feature by adding a `postFontSize` ref:
```
const posts = ref([
  /* ... */
])

const postFontSize = ref(1)
```

Which can be used in the template to control the font size of all blog posts:
```
<div :style="{ fontSize: postFontSize + 'em' }">
  <BlogPost
    v-for="post in posts"
    :key="post.id"
    :title="post.title"
   />
</div>
```

Now let's add a button to the `<BlogPost>` component's template:
```
<!-- BlogPost.vue, omitting <script> -->
<template>
  <div class="blog-post">
    <h4>{{ title }}</h4>
    <button>Enlarge text</button>
  </div>
</template>
```

The button doesn't do anything yet - we want clicking the button to communicate to the parent that it should enlarge the text of all posts. To solve this problem, components provide a custom events system. The parent can choose to listen to any event on the child component instance with `v-on` or `@`, just as we would with a native DOM event:
```
<BlogPost
  ...
  @enlarge-text="postFontSize += 0.1"
 />
```

Then the child component can `emit` an event on itself by calling the built-in `$emit` method, passing the name of the event:
```
<!-- BlogPost.vue, omitting <script> -->
<template>
  <div class="blog-post">
    <h4>{{ title }}</h4>
    <button @click="$emit('enlarge-text')">Enlarge text</button>
  </div>
</template>
```

Thanks to the `@enlarge-text="postFontSize+= 0.1"` listener, the parent will receive the event and update the value of `postFontSize`.

We can optionally declare emitted events using the `defineEmits` macro:
```
<!-- BlogPost.vue -->
<script setup>
defineProps(['title'])
defineEmits(['enlarge-text'])
</script>
```

This documents all the events that a component emits and optionally validates them. It allows Vue to avoid implicitly applying them as native listeners to the child component's root element.

Similar to `defineProps`, `defineEmits` is only usable in `<script setup>` and doesn't need to be imported. It returns an `emit` function that is equivalent to the `$emit` method. It can be used to emit events in the `<script setup>` section of a component, where `$emit` isn't directly accesible:
```
<script setup>
const emit = defineEmits(['enlarge-text'])

emit('enlarge-text')
</script>
```

If you are not using `<script setup>`, you can declare emitted events using the `emits` option. You can access the `emit` function as a property of the setup context (passed to `setup()` as the second argument):
```
export default {
  emits: ['enlarge-text'],
  setup(props, ctx) {
    ctx.emit('enlarge-text')
  }
}
```

<h4>See the emits branch to dig deeper into this topic</h4>

<br><br><h3>Content Distribution with Slots</h3>

Just like with HTML elements, it's often useful to be able to pass content to a component, like this:
```
<AlertBox>
  Something bad happened.
</AlertBox>
```

Using the `<slot>` element:
```
<!-- AlertBox.vue -->
<template>
  <div class="alert-box">
    <strong>This is an Error for Demo Purposes</strong>
    <slot />
  </div>
</template>

<style scoped>
.alert-box {
  /* ... */
}
</style>
```

As you'll see above, we use the `<slot>` as a placeholder where we want the content to go.

<h4>See the slots branch to dig deeper into this topic</h4>

<br><br><h3>Dynamic Components</h3>

Sometimes, it's useful to dynamically switch between components, like in a tabbed interface (open the link):
```
https://play.vuejs.org/#eNqNVdFu2jAU/RUre6CVCLRrtYcMqnVTpW0PW7X1rZmm4NyAS2JHtsOoEP++Y5uEoJYKkJB97/HxOTc3l010W9ejVUNREk0M16K2zJBt6ptUiqpW2rKvqiJWaFWxwWjsNg4+6NL3yljT5f3uEHCr+UKs9hS7/SFowzQVbLsDhRRLJVfSWMYbrUnah2zGpg53NnAyBuepbBE2mxnkNjjk9Q7dwmvxq92VqdymcjIONmEQG0tVXWaWsGNskosV42VmzDSNcqpUGvk4MrPGWoUDboPPKi6UBujs79Ddfc6E9BrSqIMkS3oGAtF+sGV/HCARB9bBEPYzbqEwOfA6nTpStv3TY/jES8GXYDgoSv+WnWTGNptwHKa9h3G4zhm32HKF0ktQsEQ4SU7/454Vl3a18PQ3k3F3JJRrjHphNRn3qoitsc+lW45cCcMzKZS0cZFVonxOmMmkiQ1pUXx0uZnSOemEXdZrZlQpcvaOiHqpWGe5aEzC3tdrH66zPBdyjsAFzlzhx4erTM+FjK2qQUZVPzZTcF4l7LrFNrgfGkriNmESnnxUrUgXpfoXrxOWNVYhiOLByf5hBT+dgg8QcNmS7uRCQFxSYTvdVy/zWswXxwAHpeCc+xQejFFI1UpISzrAM76ca9XIPGHvigv3fc10DLp+3N/dhWGwZy9ZuBoEkwf0dOG+L/Cj0LcnHNhBjjvsarqrZ3hXfS9Fw6gdPG5OHX9pQ6OGfvdzq+vYIw0L5m5knU4dZt4J3L1Zdzp7OzBP4LcG868Q89GTURI3+BpjNuCkKEn/rK3AfEyjJFTf5bISDf7dx6xuwqT0ZxbEl6/En8zaxdLoXhNeGkzRqMtZNBTZkL77/YPWWHfJSuVNCfQbyV+EHmicxgD7jNaB7B7Oq/3m/yPQGw/mbm1JmtaUE+qQW49PI5T5yxvW93KvRtf+HLos2v4HdCJYbw==
```

The above is made possible by Vue's `<component>` element with the special `is` attribute:
```
<!-- Component changes when currentTab changes -->
<component :is="tabs[currentTab]"></component>
```

In the example above, the value passed to `:is` can contain either:
- the name string of a registered component, OR
- the actual imported component object

You can also use the `is` attribute to create regular HTML elements.

When switching between multiple components with `<component :is="...">`, a component will be unmounted when it is switched away from. We can force the inactive components to stay "alive" with the built-in `<KeepAlive>` component.

<br><br><h3>in-DOM Template Parsing Caveats</h3>

If you are writing your Vue templates directly in the DOM (literally using vue in the HTML of the webpage), Vue will have to retrieve the template string from the DOM. This leads to some caveats due to browser's native HTML parsing behavior.

Tip: It should be noted that the limitations discussed below only apply if you are writing your templates directly in the DOM. They do NOT apply if you are using string templates from the following sources:
- Single-File Components
- Inlined template strings (e.g. `template: '..'`)
- `<script type="text/x-template">`


<h4>Case Insensitivity</h4>

HTML tags and attribute names are case-insensitive, so browsers will interpret any uppercase characters as lowercase. That means when you're using in-DOM templates, PascalCase component names and camelCased prop names or `v-on` event names all need to use their kebab-cased (hyphen-delimited) equivalents:
```
// camelCase in JavaScript
const BlogPost = {
  props: ['postTitle'],
  emits: ['updatePost'],
  template: `
    <h3>{{ postTitle }}</h3>
  `
}
```
```
<!-- kebab-case in HTML -->
<blog-post post-title="hello!" @update-post="onUpdatePost"></blog-post>
```


<h4>Self Closing Tags</h4>

We have been using self-closing tags for comopnents in previous code samples:
```
<MyComponent />
```

This is because Vue's template parser respects `/>` as an indication to end any tag, regardless of its type.

In in-DOM templates, however, we must always include explicit closing tags:
```
<my-component></my-component>
```

This is because the HTML spec only allows a few specific elements to omit closing tags, the most common being `<input>` and `<img>`. For all other elements, if you omit the closing tag, the native HTML parser will think you never terminated the opening tag. For example, the following snippet:
```
<my-component /> <!-- we intend to close the tag here... -->
<span>hello</span>
```

will be parsed as:
```
<my-component>
  <span>hello</span>
</my-component> <!-- but the browser will close it here. -->
```

<h4>Element Placement Restrictions</h4>

Some HTML elements, such as `<ul>`, `<ol>`, `<table>` and `<select>` have restrictions on what elements can appear inside them, and some elements such as `<li>`, `<tr>`, and `<option>` can only appear inside certain other elements.

This will lead to issues when using components with elements that have such restrictions. For example:
```
<table>
  <blog-post-row></blog-post-row>
</table>
```

The custom component `<blog-post-row>` will be hoisted out as invalid content, causing errors in the eventual rendered output. We can use the special `is` attribute as a workaround:
```
<table>
  <tr is="vue:blog-post-row"></tr>
</table>
```

Tip: When used on native HTML elements, the value of `is` must be prefixed with `vue:` in order to be interpreted as a Vue component. This is required to avoid confusion with native customized built-in elements.

<br><br><h1>Component Registration</h1>

<br><br><h3>Global Registration</h3>

We can make components available globally in the current Vue application using the `.component()` method:
```
import { createApp } from 'vue'

const app = createApp({})

app.component(
  // the registered name
  'MyComponent',
  // the implementation
  {
    /* ... */
  }
)
```

If using SFCs, you will be registering the imported `.vue` files:
```
import MyComponent from './App.vue'

app.component('MyComponent', MyComponent)
```

The `.component()` method can be chained:
```
app
  .component('ComponentA', ComponentA)
  .component('ComponentB', ComponentB)
  .component('ComponentC', ComponentC)
```

Globally registered components can be used in the template of any component within this application:
```
<!-- this will work in any component inside the app -->
<ComponentA/>
<ComponentB/>
<ComponentC/>
```

<br><br><h3>Local Registration</h3>

While convinient, global registration has a few drawbacks:
1. Global registration prevents build systems from removing unused components (a.k.a "tree-shaking"). If you globally register a component but end up not using it anywhere in your app, it will still be included in the final bundle.

2. Global registration makes dependency relationship less explicit in large applications. It makes it difficult to locate a child component's implementation from a parent component using it. This can affect long-term maintainability similar to using too many global variables.

Local registration scopes the availability of the registered components to the current component only. It makes the dependency relationship more explicit, and is more tree-shaking friendly.

When using the SFC with `<script setup>`, imported components can be locally used without registration:
```
<script setup>
import ComponentA from './ComponentA.vue'
</script>

<template>
  <ComponentA />
</template>
```

In non-`<script setup>`, you will need to use the `components` option:
```
import ComponentA from './ComponentA.js'

export default {
  components: {
    ComponentA
  },
  setup() {
    // ...
  }
}
```

For each property in the `components` object, the key will be the registered name of the component, while the value will contain the implementation of the component. The above example is using ES2015 property shorthand and is equivalent to:
```
export default {
  components: {
    ComponentA: ComponentA
  }
  // ...
}
```

Note that locally registered components are not also available in descendant components. In this case, `ComponentA` will be made available to the current component only, not any of its child or descendant components.

<br><br><h3>Component Name Casing</h3>

Throughout the guide, we are using PascalCase names when registering components.
This is because:
1. PascalCase names are valid JavaScript identifiers. This makes it easier to import and register components in JavaScript. It also helps IDEs with auto-completion.

2. `<PascalCase/>` makes it more obvious that this is a Vue component instead of a native HTML element in templates. It also differentiates Vue components from custom elements (web components).

This is the recommended style when working with SFC or string templates. However, PascalCase tags are not usable in in-DOM templates.

Luckily, Vue supports resolving kebab-case tags to components registering using PascalCase. This means a component registered as `MyComponent` can be referenced in the template via both `<MyComponent>` and `<my-component>`. This allows us to use the same JavaScript component registration code regardless of template source.