<h1>Slots (sample on App.vue)</h1>

Slots are a powerful feature that allows you to compose components by passing content from a parent component to a child component. This is especially useful for creating reusable components that need to be customized with different content.

<h3>Basic Slot Example</h3>

Slots are used by defining a `<slot>` element in the child component where the content should be injected.
ex (Child Component):
```
<template>
  <div>
    <h1>Child Component</h1>
    <slot></slot>
  </div>
</template>
```

ex (Parent Component):
```
<template>
  <div>
    <ChildComponent>
      <p>This is some content from the parent component!</p>
    </ChildComponent>
  </div>
</template>
```

In this example, the content inside the `<ChildComponent>` tags in the parent component is passed to the child component and is displayed where the `<slot>` element is defined.

<h3>Named Slots</h3>

Named slots allow you to define multiple slots in a component, each with a unique name.

ex (Child component):
```
<template>
  <div>
    <h1>Child Component</h1>
    <slot name="header"></slot>
    <slot name="content"></slot>
  </div>
</template>
```

ex (Parent Component):
```
<template>
  <div>
    <ChildComponent>
      <template #header>
        <h2>Header Content from Parent</h2>
      </template>
      <template #content>
        <p>Main Content from Parent</p>
      </template>
    </ChildComponent>
  </div>
</template>
```
In this example, the parent component provides content for the `header` and `content` named slots defined in the child component.

<h3>Scoped Slots</h3>

Scoped slots allow you to pass data from the child component back to the parent component. This is useful for creating flexible components that need to provide data to the content they render.

ex (Child Component):
```
<template>
  <div>
    <h1>Child Component</h1>
    <slot :message="message"></slot>
  </div>
</template>

<script setup>
import {ref} from 'vue';

const message = ref("This is the message from the child.");

</script>
```

ex (Parent Component):
```
<div>
  <h1>Scoped Slot</h1>
  <Component1 v-slot="{msg}">
    <p>{{ msg }}</p>
  </Component1>
</div>
```

<h3>Scoped and Named Slots</h3>

Child Comopnent:
`<slot name="scoped" :msgScoped="msgScoped"></slot>`

Parent Component:
```
<div>
  <h1>Named Scoped Slot</h1>
  <Component1>
    <template #scoped="{msgScoped}">
      {{ msgScoped }}
    </template>
  </Component1>
</div>
```


<h3>Default Slot Content</h3>

You can also define default content for a slot that will be displayed if no content is provided by the parent component.
ex (Child Component):
```
<!-- slot with default value-->
<slot>
<p>There is no passed elemement/value in slot, so this is the default</p>
</slot>
```
If the parent component provides content for the slot, it will replace the default content. If not, the default content will be displayed.

