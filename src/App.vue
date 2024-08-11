<template>
  <ul class="list-class">
    <li v-for="(value, key, index) in myObject">
      {{index}}. {{key}}: {{ value }}
    </li>
  </ul>

  <form @submit.prevent="">
    <div>
      <label for="todoTitle">Add New Todo:</label>
      <input id="todoTitle" v-model="todoTitle" type="text">
      <button @click="addTodo">Add</button>
    </div>

    <div>
      <ol>
        <li v-for="todo in todoList" :key=todo.id>
          {{ todo.title }}
          <button @click="removeTodo(todo.id)">Remove</button>
        </li>
      </ol>
    </div>

  </form>

</template>



<script setup>
import { ref, reactive } from 'vue'

let todoId = 0;

const todoTitle = ref("");

const todoList = ref([]);

const addTodo = () => {
  todoList.value.push({ id: todoId++, title: todoTitle.value });
  todoTitle.value = "";
}

const removeTodo = (todoId) => {
  todoList.value = todoList.value.filter(todo => todo.id !== todoId);
}

const myObject = reactive({
  title: 'How to do lists in Vue',
  author: 'Jane Doe',
  publishedAt: '2016-04-10'
})

</script>

<style scoped>
.list-class {
  color: white;
}
</style>
