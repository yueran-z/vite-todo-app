<script setup>
import { ref, onMounted, computed, watch } from 'vue'
const todos = ref([])
const name = ref('')
const input_content = ref('')
const input_category = ref(null)

const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt
  })
)

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return
  }
  console.log('add')
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    createdAt: new Date().getTime(),
    done: false,
  })
  input_content.value = ''
  input_category.value = null
}

// const removeTodo = (index) => {
//   todos.value.splice(index, 1)
// }
const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo) //return false
}

watch(
  todos,
  (newVal) => {
    localStorage.setItem('todos', JSON.stringify(newVal))
  },
  { deep: true }
)

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})
onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
  <div>
    <main class="app">
      <section class="greeting">
        <h2 class="title">
          hello,<input type="text" v-model="name" placeholder="name here" />
        </h2>
      </section>

      <section class="create-todo">
        <h3>create todo</h3>
        <form @submit.prevent="addTodo">
          <h4>what's on your todo list?</h4>
          <input
            type="text"
            name=""
            id=""
            placeholder="eg:make a video"
            v-model="input_content"
          />
          <h4>pick a category</h4>

          <div class="options">
            <label>
              <input
                type="radio"
                name="category"
                value="business"
                v-model="input_category"
              />
              <span class="bubble business"></span>
              <div>business</div>
            </label>

            <label>
              <input
                type="radio"
                name="category"
                value="personal"
                v-model="input_category"
              />
              <span class="bubble personal"></span>
              <div>personal</div>
            </label>
          </div>
          <!-- {{ input_category }} -->
          <input type="submit" value="add" />
        </form>
      </section>
      <!-- {{ todos_asc }} -->
      <section class="todo-list">
        <h3>TODO LIST</h3>
        <div class="list">
          <div
            v-for="todo in todos_asc"
            :key="todo"
            :class="`todo-item ${todo.done && 'done'}}`"
          >
            <label>
              <input type="checkbox" name="" id="" v-model="todo.done" />
              <span :class="`bubble ${todo.category}`"></span>
            </label>

            <div class="todo-content">
              <input type="text" v-model="todo.content" />
            </div>

            <div class="actions">
              <button class="delete" @click="removeTodo(todo)">Delete</button>
            </div>
          </div>
        </div>
      </section>
    </main>
  </div>
</template>

<style scoped></style>
