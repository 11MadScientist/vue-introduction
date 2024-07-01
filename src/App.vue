<template>
  <form @submit.prevent="">
    <div>
      <label for="todoTitle">Add New Todo:</label>
      <input id="todoTitle" v-model="todoTitle" type="text">
      <button @click="addTodo">Add</button>
    </div>

    <div>
      <ol>
        <li v-for="todo in filteredTodos" :key = todo.id>
          <input type="checkbox" v-model="todo.done">
          <!--{done: todo.done} => object, where key is 'done', and value is expression 'todo.done'
                 If value is true, key will be applied as class.-->
          <span :class="{done: todo.done}">{{ todo.title }}</span>
          <button @click="removeTodo(todo.id)">Remove</button>
        </li>
      </ol>
      <br>
      <button @click="showTodo = !showTodo">{{ showTodo ? "Hide Completed" : "Show All"  }}</button>
    </div>

  </form>
</template>

<script setup>
import {ref, reactive, computed} from 'vue'

let todoId = 0;

const todoTitle = ref("");

const todoList = ref([]);

const showTodo = ref(true);

const addTodo = () => {
  todoList.value.push({id: todoId++, title: todoTitle.value, done: false});
  todoTitle.value = "";
}

const removeTodo = (todoId) => {
  todoList.value = todoList.value.filter(todo => todo.id !== todoId);  
}

const filteredTodos = computed(() => {
  return showTodo.value ? 
         todoList.value:
         todoList.value.filter(todo => !todo.done);
});


</script>

<style scoped>
.done {
  text-decoration: line-through;
}
</style>
