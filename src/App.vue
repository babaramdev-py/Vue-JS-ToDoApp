<script setup>
import { ref, onMounted, computed, watch } from 'vue';
/*
  ref is basically how we deal with state
  onMounted = lifeCycleHook
  computed = order by date
  watch = watch for any changes in ref
 */

const todos = ref([])
const name = ref('')
const input_content = ref('')
const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt;
}))

watch(name, (newVal) => {
  localStorage.setItem('name_of_person', newVal) // add this value to local
  // storage of the browser as name_of_person
}) // Watch the ref name, if the name changes, set the name to newVal

onMounted(() => {
  name.value = localStorage.getItem('name_of_person') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
  console.log(todos.value);
})

function addToDo() {
  if (input_content.value.trim() === '' || input_category.value === null) {
    console.log("Empty Input");
    return
  }
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime()
  })
  console.log(todos.value)
}

watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })


const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
}
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Hello! <input type="text" placeholder="Enter Name Here" v-model="name">
      </h2>
    </section>
    <section class="create-todo">
      <h3>Welcome to ToDoList!</h3>
      <form @submit="addToDo">
        <h4>What's on the list?</h4>
        <input type="text" name="" id="" placeholder="Add Item Here" v-model="input_content" />
        <h4>Pick a Category</h4>

        <div class="options">
          <label>
            <input type="radio" name="category" value="business" v-model="input_category" />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>

          <label>
            <input type="radio" name="category" value="personal" v-model="input_category" />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>

        <input type="submit" value="Add To Do">
      </form>

    </section>
    <section class="todo-list">
      <h3>To Do List</h3>
      <div class="list">
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
          <label >
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          
          <div class="todo-content">
            <input type="text" v-model="todo.content">
          </div>

          <div class="actions">
            <button class = "delete" @click = "removeTodo(todo)">Delete</button>
          </div>

        </div>


      </div>
    </section>
  </main>
</template>
