<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const newTodoTitle = ref('')
const todos = ref([])
const error = ref(null)
const status = ref('idle')

const fetchTodos = async () => {
  status.value = 'pending'
  try {
    const response = await axios.get('http://localhost:8000/api/todos/')
    todos.value = response.data
    status.value = 'idle'
  } catch (err) {
    error.value = 'Error al cargar las tareas'
    status.value = 'idle'
  }
}

onMounted(() => {
  fetchTodos()
})

const addTodo = async () => {
  if (!newTodoTitle.value) return

  try {
    const response = await axios.post('http://localhost:8000/api/todos/', {
      title: newTodoTitle.value,
      completed: false,
    })
    todos.value.push(response.data)
    newTodoTitle.value = ''
  } catch (err) {
    console.error('Error al agregar tarea:', err)
  }
}

const updateTodo = async (todo) => {
  try {
    await axios.put(`http://localhost:8000/api/todos/${todo.id}/`, {
      title: todo.title,
      completed: todo.completed,
    })
  } catch (err) {
    console.error('Error al actualizar tarea:', err)
  }
}

const deleteTodo = async (id) => {
  try {
    await axios.delete(`http://localhost:8000/api/todos/${id}/`)
    todos.value = todos.value.filter((todo) => todo.id !== id)
  } catch (err) {
    console.error('Error al eliminar tarea:', err)
  }
}
</script>

<template>
  <div>
    <h1>Lista de Tareas</h1>
    <p v-if="error">Error al cargar las tareas</p>
    <div v-if="status === 'pending'">Cargando tareas...</div>

    <div v-for="todo in todos" :key="todo.id">
      <input type="checkbox" v-model="todo.completed" @change="updateTodo(todo)" />
      <span>{{ todo.title }}</span>
      <button @click="deleteTodo(todo.id)">Eliminar</button>
    </div>

    <form @submit.prevent="addTodo">
      <input type="text" v-model="newTodoTitle" placeholder="Nueva tarea" />
      <button type="submit">Agregar</button>
    </form>
  </div>
</template>

<style scoped>
button {
  margin-left: 10px;
}
</style>
