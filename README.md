<h1>Emits (sample on App.vue)</h1>

`emits` are used to create custom events that a child component can emit to notify its parent component about certain actions or changes. This allows the parent component to react to these events accordingly.

The first arguement of `emit()` is the event name. Any additional arguements are passed on th event listener.

The parent can listen to chile emitted events using `v-on`, here the handler receives the extra agruement from the child emit call and assigns it to local state

Child side:
```
<script setup>
// declare emitted events
const emit = defineEmits(['response'])

// emit with argument
emit('response', 'hello from child')
</script>
```
Parent side:
```
<ChildComp @response="(msg) => childMsg = msg" />
```