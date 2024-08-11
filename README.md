<h1>Attribute Bindings (Sample in App.vue)</h1>

Text Interpolation (mustache `{{}}`) is only for text, as was discussed in template syntax, you can't use its value.

In Vue.js, attribute bindings allow you to dynamically bind HTML attributes to data properties in your Vue instance. This is done using the 'v-bind' directive or the shorthand ':'. This enables attributes to change reactively with the data.

<h3>Using `v-bind`:</h3>

The v-bind directive instructs Vue to keep the element's id attribute in sync with the component's dynamicId property. If the bound value is null or undefined, then the attribute will be removed from the rendered element.

`<div v-bind:id="dynamicId"></div>`

<h3>Using shorthand `:`:</h3>

`<img :src="imageSrc" alt="Dynamic Image">`

<h3>Same-name Shorthand</h3>

If attribute has the same name with the Javascript value being bound, the syntax can be further shotened to omit the attribute value:
<!-- same as :id="id" -->
`<div :id></div>`

<!-- this also works -->
`<div v-bind:id></div>`

<h3>Boolean Attributes</h3>

`Boolean Attributes` are attributes that can indicate true / false values by their presence on an element. For example, `disabled` is one of the most commonly used boolean attributes.
`v-bind` works a bit differently in this case:
`<button :disabled="isButtonDisabled">Button</button>`
The disabled attribute will be indcluded if `isbuttonDisabled` has a truthy value. It will also be included if the value is an empty string, maintaining consistency with `<button disabled="">`. For other falsy values the attribute will be omitted.


<h3>Dynamically Binding Multiple Attributes​</h3>

If you have a JavaScript object representing multiple attributes that looks like this:
```
const objectOfAttrs = {
  id: 'container',
  class: 'wrapper',
  style: 'background-color:green'
}
```
You can bind them to a single element by using v-bind without an argument:
`<div v-bind="objectOfAttrs"></div>`

<h3>Class and Style Bindings</h3>

Since `class` and `style` are both attributes, we can use `v-bind` to assign them a string value dynamically, much like with other attributes. However, trying to generate those values using string concatenation can be annyoying and error-prone. For this reason, Vue provides special enhancements when `v-bind` is used with `class` and `style`. In addition to strings, the expressions can also evaluate to object or arrays.

<h3>Binding to Objects</h3>

We can pass an object to `:class` to dynamically toggle classes:
`<div :class="{ active: isActive }"></div>`

The above syntax means the presence of the `active` class will be determined by the "truthiness" of the data property `isActive`.

You can have multiple classes toggled by having more fields in the object. In addition, the `:class` directive can also co-exist with the plain `class` attribute. So given the following state:

```
const isActive = ref(true)
const hasError = ref(false)
```

and the following template:
```
<div
  class="static"
  :class="{ active: isActive, 'text-danger': hasError }"
></div>
```

will render:
```
<div class="static active"></div>
```

When `isActive` or `hasError` changes, the class list will be updated accordingly. For example, if `hasError` becomes `true`, the class list will become `"static active text-danger"`.

The bound object doesn't have to be inline:

```
const classObject = reactive({
  active: true,
  'text-danger': false
})
```
```
<div :class="classObject"></div>
```
This will render:
```
<div class="active"></div>
```

We can also bind to a computed property that returns an object. This is a common and powerful pattern:

```
const isActive = ref(true)
const error = ref(null)

const classObject = computed(() => ({
  active: isActive.value && !error.value,
  'text-danger': error.value && error.value.type === 'fatal'
}))
```
```
<div :class="classObject"></div>
```

<h3>Binding to Arrays</h3>

We can bind `:class` to an array to apply a list of classes:
```
const activeClass = ref('active')
const errorClass = ref('text-danger')
```
```
<div :class="[activeClass, errorClass]"></div>
```

Which will render:
```
<div class="active text-danger"></div>
```

If you would like to also toggle a class in the list conditionally, you can do it with a ternary expression:
```
<div :class="[isActive ? activeClass : '', errorClass]"></div>
```
This will always apply `errorClass`, but `activeClass` will only be applieed when `isActive` is truthy.

However,this can be a bit verbose if you have multiple conditional classes. That's why it's also possible to use the object syntax inside the array syntax:
```
<div :class="[{ [activeClass]: isActive }, errorClass]"></div>
```

<h3>With Components</h3>

When you use the `class` attribute on a component with a single root element, those classes will be added to the component's root element and merged with any existing class already on it.

For example, if we have a component named `MyComponent` with the following template:
```
<!-- child component template -->
<p class="foo bar">Hi!</p>
```

Then add some changes when using it:
```
<!-- when using the component -->
<MyComponent class="baz boo" />
```
The rendered HTML will be:
```
<p class="foo bar baz boo">Hi!</p>
```

The same is true for class bindings:
```
<MyComponent :class="{ active: isActive }" />
```

When `isActive` is truthy, the rendered HTML will be:
```
<p class="foo bar active">Hi!</p>
```

If your component has multiple root elmenents, you would need to define which element will receive this class. You can do this using the `$attrs` component property:
```
<!-- MyComponent template using $attrs -->
<p :class="$attrs.class">Hi!</p>
<span>This is a child component</span>
```
```
<MyComponent class="baz" />
```

Will render:
```
<p class="baz">Hi!</p>
<span>This is a child component</span>
```

<h3>Binding Inline Styles</h3>

<h5>Binding to Objects</h3>

`:style` supports binding to Javascript object values - it corresponds to an HTML element's `style` property:
```
const activeColor = ref('red')
const fontSize = ref(30)
```
```
<div :style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```

Although camelCase keys are recommended, `:style` also supports kebab-cased CSS property keys - for example:
```
<div :style="{ 'font-size': fontSize + 'px' }"></div>
```

It is often a good idea to bind to a style object directly so that the template is cleaner:
```
const styleObject = reactive({
  color: 'red',
  fontSize: '30px'
})
```
```
<div :style="styleObject"></div>
```

Again, object style binding is often used in conjunction with computed properties that return objects.

<h5>Binding to Arrays</h5>

We can bind `:style` to an array of multiple style objects. These objects will be merged and applied to the same element:

```
<div :style="[baseStyles, overridingStyles]"></div>
```

<h5>Autho-prefixing</h5>

When you use a CSS property that requires a `vendor prefix` in `:style`, Vue will automatically add the appropriate prefix. Vue does this by checking at runtime to see which style properties are supported by the current browser. If the browser doesn't support a particular property, then various prefixed variants will be tested to try to find one that is supported.

<h5>Multiple Values</h5>

You can provide an array of multiple (prefixed) values to a style property, for example:
```
<div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```

This will only render the last value in the array which the browser supports. In this example, it will render `display: flex` for browsers that support the unprefixed version of flexbox.

<h3>Binding each key automatically</h3>

Assume you have a `file` object in Vue component:
```
export default {
  data() {
    return {
      file: {
        src: 'https://example.com/image.png',
        alt: 'Example Image',
        width: '200',
        height: '200'
      }
    };
  }
};

```

You can bind this object to a tag ex: `img` (it can also be a component) using `v-bind`:
```
<template>
  <img v-bind="file">
</template>
```

The above is equivalent to writing:
```
<img src="https://example.com/image.png" alt="Example Image" width="200" height="200">
```

Vue automatically binds each key in the`file` object to the corresponding attribute in the `img`.