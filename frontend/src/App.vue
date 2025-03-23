<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

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

</script>

<template>
  <div>
    <h1>Lista de Tareas</h1>
    <div v-for="todo in todos" :key="todo.id">
      <input type="checkbox" v-model="todo.completed" @change="updateTodo(todo)" />
      <span>{{ todo.title }}</span>
      <button @click="deleteTodo(todo.id)">Eliminar</button>
    </div>
  </div>
</template>

<style scoped>
button {
  margin-left: 10px;
}
</style>
