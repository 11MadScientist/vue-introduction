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