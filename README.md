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

