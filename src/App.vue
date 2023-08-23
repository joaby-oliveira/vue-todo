<script setup lang="ts">
import { ref, onMounted, computed, watch, type Ref } from 'vue'

type Todo = {
  content: string
  category: string
  createdAt: number,
  done: boolean
}
const todos: Ref<Todo[]> = ref([])
const name = ref('')
const input_content = ref('')
const input_category = ref('')

// eslint-disable-next-line vue/no-side-effects-in-computed-properties
const todos_asc = computed(() => todos.value.sort((a, b) => {
  console.log(a)
  return a.createdAt - b.createdAt
}))

watch(name, (newValue: string) => {
  localStorage.setItem('name', newValue)
})

watch(todos, (newTodos) => {
  localStorage.setItem('todos', JSON.stringify(newTodos))
}, { deep: true })

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos') ?? '');
})

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    createdAt: (new Date).getTime(),
    done: false
  })
}

const removeTodo = (removeTodo: Todo) => {
  todos.value = todos.value.filter(t => t !== removeTodo)
}
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Olá, <input type="text" placeholhder="Name here" v-model="name">
      </h2>
    </section>
    <section class="create-todo">
      <h3>CRIE SUAS TAREFAS</h3>
      <form @submit.prevent="addTodo">
        <h4>O que precisa ser feito?</h4>
        <input type="text" placeholder="e.g make a video" v-model="input_content">
        <h4>Escolha uma categoria</h4>
        <div class="options">
          <label>
            <input type="radio" name="category" id="category" value="business" v-model="input_category" />
            <span class="bubble business"></span>
            <div>Trabalho</div>
          </label>
          <label>
            <input type="radio" name="category" id="category" value="personal" v-model="input_category" />
            <span class="bubble personal"></span>
            <div>Pessoal</div>
          </label>
        </div>
        <input type="submit" value="Add todo">
      </form>
    </section>
    <section class="todo-list">
      <h3>
        Tarefas
      </h3>
      <ul class="list">
        <li v-for="todo in todos_asc" v-bind:key="todo.createdAt" :class="`todo-item ${todo.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="todo.done">
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content">
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Excluir</button>
          </div>
        </li>
      </ul>
    </section>
  </main>
</template>