<h1>Computed Property (sample on App.vue)</h1>

Is a reactive property that automatically recompute their values when their dependencies change.
Computed properties are useful for performing extensive operations only when necessary and for deriving state from other state in a way that is declarative and reactive.

Example Syntax:
```
import { ref, computed } from 'vue'

const hideCompleted = ref(false)
const todos = ref([
  /* ... */
])

const filteredTodos = computed(() => {
  // return filtered todos based on
  // `todos.value` & `hideCompleted.value`
})
```