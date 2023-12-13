
<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const todos_asc = computed(() => todos.value.sort((a,b) => {
  return b.createdAt - a.createdAt
}))

const input_content = ref('')


const addTodo = () => {
  if (input_content.value.trim() === '') {
    return 
  }

  todos.value.push({
    content: input_content.value,
    done: false,
    createdAt: new Date().getTime()
  })
  input_content.value = ''
}

const removeTodo = todo => {
  todos.value = todos.value.filter(todo_selected => todo_selected !== todo)
}

watch(() => todos.value, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || [] 
})
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Hello
        <input id="name" 
        type="text" 
        placeholder="Name here" 
        autocomplete="off" 
        v-model="name" /> 
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TODO</h3>

      <form @submit.prevent="addTodo">
        <input 
        id="w-code" 
        type="text" 
        placeholder="e.g. write a code" 
        v-model="input_content" />

        <div class="options">
          
        </div>

        <input type="submit" value="Add todo" />
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">
          <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`" >
          
              <label>
                <input type="checkbox" v-model="todo.done">
                <span class="bubble"></span>
              </label>

              <div class="todo-content">
                <input type="text" v-model="todo.content">
              </div>

              <div class="actions">
                <button class="delete" @click="removeTodo(todo)">Delete</button>
              </div>

          </div>
      </div>
    </section>

  </main>
</template>
