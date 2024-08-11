<h1>Conditional Rendering (Sample in App.vue)</h1>

conditional rendering allows you to display elements based on certain conditions. The primary directives used for this are `v-if`, `v-else-if`, `v-else`, and `v-show`.

1. `v-if`: Renders the element if the expression evaluates to true.
`<div v-if="isVisible">Visible Content</div>`

2. `v-else-if`: Specifies a new condition to check if the previous v-if was false.
`<div v-if="type === 'A'">Type A</div>`
`<div v-else-if="type === 'B'">Type B</div>`

3. `v-else`: Renders the element if none of the preceding conditions are true.
`<div v-if="type === 'A'">Type A</div>`
`<div v-else-if="type === 'B'">Type B</div>`
`<div v-else>Other Types</div>`

4. `v-show`: Toggles the element's visibility based on the expression.
`<div v-show="isVisible">Visible Content</div>`

<h3>Difference between `v-if` and `v-show`</h3>

`v-if`
- Adds / removes the element from DOM completely, it is enifficient to use if element can appear and disappear in one session, best to use on large sections of the DOM, where you know
the condition won't be toggled (page won't rerender).

`v-show`
- Toggles the visibility of the element based on the condition. It will just add `display: none`
style to hide the element without removing it from the DOM, this way it can appear and disappear without needing large amount of computing.

- does not work with v-else, does not support usage in `<template>`

<h3>v-if with v-for</h3>

When `v-if` and `v-for` are both used on the same element, `v-if` will be evaluated first.