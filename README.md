<h1>Form Bindings (Sample in App.vue)</h1>

Using `v-bind` and `v-on` together, we can create two-way bindings on form input elements:

`<input :value="text" @input="onInput">`
`<h3 class="reflect">{{ vmodeltext }}</h3>`

`function onInput(e) {
  text.value = e.target.value
}`

as you input the value in textbox, vue will update the h3 text

to simplify two way bindings, Vue provides a directive, `v-model`,
which is essentially syntactic sugar for the above:

`<input v-model="vmodeltext">`
`<h3 class="reflect">{{ vmodeltext }}</h3>`

`v-model` automatically syncs the `<input>`'s value with the bound state, so we no longer need to use an event handler for that.

`v-model` works not only on text inputs, but also on other input types such as checkboxes, radio buttons, and select dropdowns.

