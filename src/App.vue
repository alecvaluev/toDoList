<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref("")

const input_content = ref("")
const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a,b) => b.createdAt - a.createdAt))

const addTodo = () => {
  if(input_content.value.trim() === "" || 
  input_category.value === null){
    return 
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    createdAt: new Date().getTime(),
    done: false
  })
}

const removeTodo = (todo) => {
  todos.value = todos.value.filter(t => t !== todo)
}

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

watch(todos, (newTodos) => {
  localStorage.setItem("todos", JSON.stringify(newTodos)) 
}, { deep: true})

onMounted(() => {
  name.value = localStorage.getItem('name') || ""
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})

</script>

<template>
  <main class="app">

    <section class="greeting">
      <h2 class="title">
        What's up, <input type="text" placeholder="Name here" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE TO DO</h3>

      <form @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input 
          type="text" 
          placeholder="e.g. make a video" 
          v-model="input_content"/>
      
        <h4>Pick a category</h4>
        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              value="chores"
              v-model="input_category"
              />
              <span class="chores bubble"></span>
              <div>Regular</div>
          </label>

          <label>
            <input
              type="radio"
              name="category"
              value="shopping"
              v-model="input_category"
              />
              <span class="shopping bubble"></span>
              <div>Shopping</div>
          </label>

          <label>
            <input
              type="radio"
              name="category"
              value="personal"
              v-model="input_category"
              />
              <span class="personal bubble"></span>
              <div>Important</div>
          </label>
        </div>

        <input type="submit" value="Add todo"/>
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">
        <div 
          v-for="todo in todos_asc" 
          :class="`todo-item ${todo.done && 'done'}`">
            <lable>
              <input 
                type="checkbox"
                v-model="todo.done"
                />
              <span :class="`bubble ${todo.category}`"></span>  
            </lable>

            <div class="todo-content">
              <input
                type="text"
                v-model="todo.content"/>
            </div>

            <div class="actions">
              <button class="delete" @click="removeTodo(todo)">Delete</button>
            </div>
        </div>
      </div>
    </section>
  </main>  
</template>