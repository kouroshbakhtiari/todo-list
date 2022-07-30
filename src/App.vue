<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt
}))

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime()
  })


  input_content.value = ''
  input_category.value = null
}




const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
}


watch(todos, newVal => {
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
        What's up, <input type="text" placeholder="name here" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      
      <form @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input type="text" placeholder="Add your new todo ..." v-model="input_content" />

        <div class="options">
          <label>
            <input type="radio" name="category" id="category" value="business" v-model="input_category" />
            <span class="bubble business"></span>
          </label>


          <label>
            <input type="radio" name="category" id="category" value="personal" v-model="input_category" />
            <span class="bubble personal"></span>
          </label>

          <input type="submit" value="Add todo" />
        </div>



      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">
        <div v-for="todo in todos_asc" :key="todo" :class="`todo-item ${todo.done && 'done'}`">

          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content">
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)"><span class="material-symbols-outlined">
                delete
              </span></button>
          </div>

        </div>

      </div>

    </section>

  </main>
</template>

