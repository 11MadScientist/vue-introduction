<h1>Event Handling (sample in App.vue)</h1>

<h3>Listening to Events</h3>

We can use the `v-on` directive, which we typically shorten to `@` symbol, to listen to DOM events and run some JavaScript when they're triggered. The usage would be `v-on:click="handler"` or with the shortcut `@click="handler"`.

The handler value can be one of the following:
1. Inline handlers: Inline JavaScript to be executed when the event is triggered (similar to the native `onclick` attribute).

2. Method handlers: A property name or path that points to a method defined on the component.

<h3>Inline Handlers</h3>

Inline handlers are typically used in simple cases, for example:
```
const count = ref(0)
```
```
<button @click="count++">Add 1</button>
<p>Count is: {{ count }}</p>
```

<h3>Method Handlers</h3>

The logic for many event handlers will be more complex though, and likely isn't feasable with inline handlers. That's why `v-on` can also accept the name or path of a component method you'd like to call.

For example:

```
const name = ref('Vue.js')

function greet(event) {
  alert(`Hello ${name.value}!`)
  // `event` is the native DOM event
  if (event) {
    alert(event.target.tagName)
  }
}
```
```
<!-- `greet` is the name of the method defined above -->
<button @click="greet">Greet</button>
```
A method handler automatically recieves the native DOM Event object that triggers it - in the example above, we are able to access the element dispatching via `event.target`.

<h4>Method vs. Inline Detection</h4>

The template compiler detects method handlers by checking whether the `v-on` value string is a valid JavaScript identifier or property access path. For example, `foo`, `foo.bar` and `foo[bar]` are treated as method handlers, while `foo()` and `count++` are treated as inline handlers.

<h3>Calling Methods in Inline Handlers</h3>

Instead of binding directly to a method name, we can also call methods in an inline handler. This allows us to pass the method custom arguments instead of the native event:

```
function say(message) {
  alert(message)
}
```
```
<button @click="say('hello')">Say hello</button>
<button @click="say('bye')">Say bye</button>
```

<h3>Accessing Event Argument in Inline Handlers</h3>

Sometimes we also need to access the original DOM event in an inline handler. You can pass it into a method using the special `$event` variable, or use an inline arrow function:

```
<!-- using $event special variable -->
<button @click="warn('Form cannot be submitted yet.', $event)">
  Submit
</button>

<!-- using inline arrow function -->
<button @click="(event) => warn('Form cannot be submitted yet.', event)">
  Submit
</button>
```
```
function warn(message, event) {
  // now we have access to the native event
  if (event) {
    event.preventDefault()
  }
  alert(message)
}
```

<h3>Event Modifiers</h3>

It is a very common need to call `event.preventDefault()` or `event.stopPropagation()` inside event handlers. Although we can do this easily inside methods, it would be better if the methods can be purely about data logic rather than having to deal with DOM event details.

To address this problem, Vue provides event modifiers for `v-on`. Recall that modifiers are directive postfixes denoted by a dot.
- `.stop`
- `.prevent`
- `.self`
- `.capture`
- `.once`
- `.passive`

```
<!-- the click event's propagation will be stopped -->
<a @click.stop="doThis"></a>

<!-- the submit event will no longer reload the page -->
<form @submit.prevent="onSubmit"></form>

<!-- modifiers can be chained -->
<a @click.stop.prevent="doThat"></a>

<!-- just the modifier -->
<form @submit.prevent></form>

<!-- only trigger handler if event.target is the element itself -->
<!-- i.e. not from a child element -->
<div @click.self="doThat">...</div>
```

Tip: Order matters when using modifiers because the relevant code is generated in the same order. Therefore using `@click.prevent.self` will prevent click's default action on the element itself and its children, while `@click.self.prevent` will only prevent click's default action on the element itself.

The `.capture`, `.once`, and `.passive` modifiers mirror the options of the native `addEventListener` method:
```
<!-- use capture mode when adding the event listener     -->
<!-- i.e. an event targeting an inner element is handled -->
<!-- here before being handled by that element           -->
<div @click.capture="doThis">...</div>

<!-- the click event will be triggered at most once -->
<a @click.once="doThis"></a>

<!-- the scroll event's default behavior (scrolling) will happen -->
<!-- immediately, instead of waiting for `onScroll` to complete  -->
<!-- in case it contains `event.preventDefault()`                -->
<div @scroll.passive="onScroll">...</div>
```

The `.passive` modifier is typically used with touch event listeners for improving performance on mobile devices.

Tip: Do not use `.passive` and `.prevent` together, because `.passive` already indicates to the browser that you do not intend to prevent the event's default behavior, and you will likely see a warning from the browser if you do so.

<h3>Key Modifiers</h3>

When listening for keybaord events, we often need to check for specific keys. Vue allows adding key modifiers for `v-on` or `@` when listening for key events:

```
<!-- only call `submit` when the `key` is `Enter` -->
<input @keyup.enter="submit" />
```

You can directly use any valid key names exposed via `KeyboardEvent.key` as modifiers by converting them to kebab-case.
```
<input @keyup.page-down="onPageDown" />
```
In the above example, the handler will only be called if `$event.key` is equal to `'pageDown'`.

<h4>Key Aliases</h4>

Vue provides aliases for the most commonly used keys:

- `.enter`
- `.tab`
- `.delete` (captures both "Delete" and "Backspace" keys)
- `.esc`
- `.space`
- `.up`
- `.down`
- `.left`
- `.right`

<h4>System Modifier Keys</h4>

You can use the following modifiers to trigger mouse of keyboard event listeners only when the corresponding modifier is pressed:
- `.ctrl`
- `.alt`
- `.shift`
- `.meta`

Note: On Macintosh keyboards, meta is the command key (⌘). On Windows keyboards, meta is the Windows key (⊞). On Sun Microsystems keyboards, meta is marked as a solid diamond (◆). On certain keyboards, specifically MIT and Lisp machine keyboards and successors, such as the Knight keyboard, space-cadet keyboard, meta is labeled “META”. On Symbolics keyboards, meta is labeled “META” or “Meta”.

For example:
```
<!-- Alt + Enter -->
<input @keyup.alt.enter="clear" />

<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">Do something</div>
```

Tip: Note that modifier keys are different from regular keys and when used with `keyup` events, they have to be pressed when the event is emitted. In other words, `keyup.ctrl` will only trigger if you release a key while holding down `ctrl`. It won't trigger if you release the `ctrl` key alone.

<h4>

`.exact` Modifier

</h4>

The `.exact` modifier allows control of the exact combination of system modifiers needed to trigger an event.

```
<!-- this will fire even if Alt or Shift is also pressed -->
<button @click.ctrl="onClick">A</button>

<!-- this will only fire when Ctrl and no other keys are pressed -->
<button @click.ctrl.exact="onCtrlClick">A</button>

<!-- this will only fire when no system modifiers are pressed -->
<button @click.exact="onClick">A</button>
```

<h3>Mouse Button Modifiers</h3>

- `.left`
- `.right`
- `.middle`

These modifiers restrict the handler to events triggered by specific mouse button.
