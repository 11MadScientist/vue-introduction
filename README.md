<h1>Form Bindings (Sample in App.vue)</h1>

When dealing with forms on the frontend, we often need to sync the state of the form input elements with corresponding state in JavaScript. It can be cumbersome to manually wire up value bindings and change event listeners:

```
<input
  :value="text"
  @input="event => text = event.target.value">
```

The `v-model` directive helps us simplify the above to:
```
<input v-model="text">
```

In addition, `v-model` can be used on inputs of different types, `<textare>` and `<select>` elements. It automatically expands to different DOM property and event pairs based on the element it is used on:

- `<input>` with text types and `<textarea>` elements use `value` property and `input` event;
- `<input type="checkbox">` and `<input type="radio">` use `checked` property an `change` event;
- `<select>` uses `value` as a prop and `change` as an event.

Note: `v-model` will ignore the initial `value`, `checked` or `selected` attributes found on any form elements. It will always treat the current bound JavaScript state as the source of truth. You should declare the initial value on the JavaScript side, using ractivity APIs.

<h3>Basic Usage</h3>

<h4>Text</h4>

```
<p>Message is: {{ message }}</p>
<input v-model="message" placeholder="edit me" />
```
Note: For languages that require IME (Chinese, Japanese, Korean, etc.), you'll notice that `v-model` doesn't get updated during IME composition. If you want to respond to these updates as well, use your own `input` event listener and `value` binding instead of using `v-model`.

<h4>Multiline Text</h4>

```
<span>Multiline message is:</span>
<p style="white-space: pre-line;">{{ message }}</p>
<textarea v-model="message" placeholder="add multiple lines"></textarea>
```
Note: interpolation inside `<textarea>` won't work. Use `v-model` instaed.

```
<!-- bad -->
<textarea>{{ text }}</textarea>

<!-- good -->
<textarea v-model="text"></textarea>
```

<h4>Checkbox</h4>

Single checkbox, boolean value:
```
<input type="checkbox" id="checkbox" v-model="checked" />
<label for="checkbox">{{ checked }}</label>
```

We can also bind multiple checkboxes to the same array or Set value:
```
const checkedNames = ref([])
```
```
<div>Checked names: {{ checkedNames }}</div>

<input type="checkbox" id="jack" value="Jack" v-model="checkedNames" />
<label for="jack">Jack</label>

<input type="checkbox" id="john" value="John" v-model="checkedNames" />
<label for="john">John</label>

<input type="checkbox" id="mike" value="Mike" v-model="checkedNames" />
<label for="mike">Mike</label>
```
In this case, the `checkedNames` array will always contain the values from the current checked boxes.

<h4>Radio</h4>

```
<div>Picked: {{ picked }}</div>

<input type="radio" id="one" value="One" v-model="picked" />
<label for="one">One</label>

<input type="radio" id="two" value="Two" v-model="picked" />
<label for="two">Two</label>
```

<h4>Select</h4>

Single select:
```
<div>Selected: {{ selected }}</div>

<select v-model="selected">
  <option disabled value="">Please select one</option>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
```
Note: If the inital value of your `v-model` expression does not match any of the options, the `<select>` element will render in an "unselected" state. On IOS this will cause the user not being able to select the first item because iOS does not fire a change event in this case. It is therefore recommended to provide a disabled option with an empty value, as demonstrated in the example above.

Multiple select (bound to array):
```
<div>Selected: {{ selected }}</div>

<select v-model="selected" multiple>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
```

Select options can be dynamically rendered with `v-for`:
```
const selected = ref('A')

const options = ref([
  { text: 'One', value: 'A' },
  { text: 'Two', value: 'B' },
  { text: 'Three', value: 'C' }
])
```
```
<select v-model="selected">
  <option v-for="option in options" :value="option.value">
    {{ option.text }}
  </option>
</select>

<div>Selected: {{ selected }}</div>
```

<h3>Value Bindings</h3>

For radio,checkbox and select options, the `v-model` binding values are usually static strings (or booleans for checkbox):
```
<!-- `picked` is a string "a" when checked -->
<input type="radio" v-model="picked" value="a" />

<!-- `toggle` is either true or false -->
<input type="checkbox" v-model="toggle" />

<!-- `selected` is a string "abc" when the first option is selected -->
<select v-model="selected">
  <option value="abc">ABC</option>
</select>
```

But sometimes we may want to bind the value to a dynamic property on the current active instance. We can use `v-bind` to achieve that. In addition, using `v-bind` allows us to bind the input value to non-string values.

<h4>Checkbox</h4>

```
<input
  type="checkbox"
  v-model="toggle"
  true-value="yes"
  false-value="no" />
```

`true-value` and `false-value` are Vue-specific attributes that only work with `v-model`. Here the `toggle` property's value will be set to `'yes'` when the box is checked, and set to `'no'` when unchecked. You can also bind them to dynamic values using `v-bind`:
```
<input
  type="checkbox"
  v-model="toggle"
  :true-value="dynamicTrueValue"
  :false-value="dynamicFalseValue" />
```
Tip: The `true-value` and `false-value` attributes don't affect the input's `value` attribute, because browsers don't include unchecked boxes in form submissions. To guarantee that one of two values is submitted in a form (e.g "yes" or "no"), use radio inputs instead.


<h4>Radio</h4>

```
<input type="radio" v-model="pick" :value="first" />
<input type="radio" v-model="pick" :value="second" />
```
`pick` will be set to the value of `first` when the first radio input is checked, and set to the value `second` when the second one is checked.

<h4>Select Options</h4>

```
<select v-model="selected">
  <!-- inline object literal -->
  <option :value="{ number: 123 }">123</option>
</select>
```
`v-model` supports value bindings of non-string values as well! In the above example, when option is selected, `selected` will be set to the object literal value of `{ number: 123 }`.

<h3>Modifiers</h3>

<h4>

`.lazy`

</h4>

By default, `v-model` syncs the input with the data after each `input` event (with the exception of IME composition as stated above). You can add the `lazy` modifier to instead sync after `change` events:
```
<!-- synced after "change" instead of "input" -->
<input v-model.lazy="msg" />
```

<h4>

`.number`

</h4>

If you want input to be automatically typecast as number, you can add the `number` modifier to your `v-model` managed inputs:
```
<input v-model.number="age" />
```

If the value cannot be parsed with `parseFloat()`, then the original value is used instead.
The `number` modifier is applied automatically if the input has the `type="number"`.

<h4>

`.trim`

</h4>

If you want whitespace from user input to be trimmed automatically, you can add the `trim` modifier to your `v-model`-managed inputs:
```
<input v-model.trim="msg" />
```

<h3>

`v-model` with Compontents

</h4>

HTML's built-in input types won't always meet your needs. Fortunately, Vue components allow you to build reusable inputs with completely customized behavior. These inputs even work with `v-model`!