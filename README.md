<h1>Template Syntax<h1>

Vue uses an HTML-based template syntax that allows you to declaratively bind the rendered DOM to the underlying component instance's data. All Vue templates are syntactically valid HTML that can be parased by spec-compliant browsers and HTML parasers.

Under the hood, Vue compiles the templates into highly-optimized Javascript code. Combined with the reactivity system, Vue can intelligently figure out the minimal number of components to re-render and apply the minimal amount of DOM manipulations when the app state changes.

<h3>Text Interpolation</h3>

The most basic form of data binding is text interpolation using the "Mustache" syntax

`<span>Message: {{msg}}</span>`
The mustache tag will be replaced with the value of the msg proeprty from the corresponding component instance. It will also be updated whenever the msg property changes

<h3>Raw HTML</h3>

The double mustaches interpret the data as plain text, not HTML. In order to output real HTML, you will need to use the `v-html` directive:
`<p>Using v-html directive: <span v-html="rawHtml"></span></p>`
where value of rawHtml is:
`const rawHtml = ref("<span style='color:red'>John Doe</span>");`

the contents of `span` will be replaced with the value of the `rawHtml` property, interpreted as plain HTML - data bindings are ignored. Note that you cannot use v-html to compose template partials, because vue is not a string based templating engine. Instead, components are preferred as the fundamental unit for UI reuse and composition.

<h3>Using Javascript Expressions</h3>

Vue supports the full power of Javascript expressions inside all data bindings:

```
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div :id="`list-${id}`"></div>
```
These expressions will be evaluated as Javascript in the data scope of the current component instance.

In Vue templates, Javascript expressions can be used in the following positions:
- Inside text Interpolation (mustaches)
- In the attribute value of any Vue directives (special attributes that starts with `v-`)

<h3>Expressions Only</h3>
Each binding can only contain one single expression. An expression is a piece of code that can be evaluated to a value. A simple check is whether it can be used after return.

Therefore, the following will NOT work:
```
<!-- this is a statement, not an expression: -->
{{ var a = 1 }}

<!-- flow control won't work either, use ternary expressions -->
{{ if (ok) { return message } }}
```

<h3> Directives </h3>

Directives are special attributes with the `v-` prefix. Vue provides a number of built-in directives, including `v-html` and `v-bind`.

Directive attribute values are expected to be single Javascript Expressions (with exception of `v-for`, `v-on`, and `v-slot`). A directive's job is to reactively apply updates to the DOM when the value of its expression changes. Take `v-if` as an example:
`<p v-if="seen">Now you see me</p>`
Here, the v-if directive would remove / insert the <p> element based on the truthiness of the value of the expression seen.

<h3>Arguments</h3>

Some directives can take an "argument", denoted by a colon after the directive name. For example, the `v-bind` directive is used to reactively update an HTML attribute:
```
<a v-bind:href="url"> ... </a>

<!-- shorthand -->
<a :href="url"> ... </a>
```
Here, href is the argument, which tells the v-bind directive to bind the element's href attribute to the value of the expression url. In the shorthand, everything before the argument (i.e., v-bind:) is condensed into a single character, :.

Another example is the `v-on` directive, which listens to DOM events:
```
<a v-on:click="doSomething"> ... </a>

<!-- shorthand -->
<a @click="doSomething"> ... </a>
```

<h3> Dynamic Arguments </h3>
It is also possible to use a Javascript expresssion in a directive argument by wrapping it with a square brackets:
```
<!--
Note that there are some constraints to the argument expression,
as explained in the "Dynamic Argument Value Constraints" and "Dynamic Argument Syntax Constraints" sections below.
-->
<a v-bind:[attributeName]="url"> ... </a>

<!-- shorthand -->
<a :[attributeName]="url"> ... </a>
```

Here, attributeName will be dynamically evaluated as a Javascript expression, and its evaluated value will be used as the final value for the argument. For example, if your component instance has a data property, attributeName, whose value is "href", then this binding will be equivalent to `v-bind:href`.

Similarly, you can use dynamic arguments to bind a handler to a dynamic event name:
```
<a v-on:[eventName]="doSomething"> ... </a>

<!-- shorthand -->
<a @[eventName]="doSomething"> ... </a>
```

In this example, when `eventName`'s value is `"focus"`, `v-on:[eventName]` will be equivalet to `v-on:focus`.

<h3>Dynamic Argument Value Constraints</h3>

Dynamic arguments are expected to evaluate a string, with the exception of `null`. the special value `null` can be used to explicitly remove the binding. Any other non-string value will trigger a warning.

<h3>Dynamic Argument Syntax Constraints</h3>
Dynamic argumet expressions have some syntax constraints because certain characters, such as spaces and quotes, are invalid inside HTML attribute names. For example, the following is invalid:

```
<!-- This will trigger a compiler warning. -->
<a :['foo' + bar]="value"> ... </a>
```
If you need to pass a complex dynamic argument, it's probably better to use a computed property.

When using in-DOM templates (templates directly written in an HTML file), you should also avoid naming keys with uppercase characters, as browsers will coerce attribute names into lowercase:

```
<a :[someAttr]="value"> ... </a>
```

The above will be converted to :[someattr] in in-DOM templates. If your comopnent has a `someAttr` property instead of `someattr`, your code won't work. Templates inside Single-File Components are not subject to this constraint.

<h3>Modifiers</h3>

Modifiers are special postfixes denoted by dot, which indicate that a directive should be bound  in some special way. For example, the `.prevent` modifier tells the `v-on` directive to call `event.preventDefault()` on the triggered event:

`<form @submit.prevent="onSubmit">...</form>`