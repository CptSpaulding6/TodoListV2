<template lang="pug">
    div
      h1 Mes tâches :
      form(@submit.prevent="addTask")
        label Nouvelles tâches
        input(
          v-model="newTask"
          name="newTask"
          autocomplete="off"
        )
        button(type="submit") +
      h2 Liste des tâches :
      ul
        li(v-for="(task, index) in tasks" :key="index")
          span(
            :class="{ done: task.done }"
            @click="toggleTaskStatus(task)"
          ) {{ task.content }}
          button(@click="removeTask(index)") x
      h4(v-if="tasks.length === 0") Liste vide.
    </template>
    
    <script setup lang="ts">
    import { ref, onMounted } from 'vue'
    
    const newTask = ref('')
    const tasks = ref([])
    
    function loadTasks() {
      if (process.client) {
        const savedTasks = localStorage.getItem('tasks')
        tasks.value = savedTasks ? JSON.parse(savedTasks) : []
      }
    }
    
    function saveTasks() {
      if (process.client) {
        localStorage.setItem('tasks', JSON.stringify(tasks.value))
      }
    }
    
    function addTask() {
      if (newTask.value.trim()) {
        tasks.value.push({
          done: false,
          content: newTask.value
        })
        newTask.value = ''
        saveTasks()
      }
    }
    
    function toggleTaskStatus(task) {
      task.done = !task.done
      saveTasks()
    }
    
    function removeTask(index) {
      tasks.value.splice(index, 1)
      saveTasks()
    }
    
    onMounted(loadTasks)
    
    </script>
    
    <style scoped>
    .done {
      text-decoration: line-through;
      color: grey;
    }
    </style>
    