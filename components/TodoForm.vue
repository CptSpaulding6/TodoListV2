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
            li(v-for="(task, index) in filteredTasks" :key="index")
                span(
                :class="{ completed: task.completed }"
                @click="toggleTaskStatus(task)"
                ) {{ task.content }}
                button(@click="removeTask(index)") x
                button(@click="checkedTask(index)") v
    h4(v-if="filteredTasks.length === 0") Liste vide.
    .taskCheck
    label(v-for='type in allTask' :key='type')
      input(type='radio' :value='type' v-model='selectedTask')
      |           {{ type }}

</template>

<script setup lang="ts">
import { ref, onMounted, computed } from 'vue'

const newTask = ref('')
const tasks = ref<{ id: number, completed: boolean }[]>([])

const allTask = ["Toute", "Complète", "Incomplète"]
const selectedTask = ref("Toute")

watch(selectedTask, (newVal) => {
  console.log('Task selection changed:', newVal)
})


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
        completed: false,
        content: newTask.value
    })
    newTask.value = ''
    saveTasks()
    }
}

function toggleTaskStatus(task) {
    task.completed = !task.completed
    saveTasks()
}

function removeTask(index) {
    tasks.value.splice(index, 1)
    saveTasks()
}

function checkedTask(index) {
    tasks.value[index].completed = !tasks.value[index].completed
}

const filteredTasks = computed(() => {
    if (selectedTask.value === "Complète") {
        return tasks.value.filter(task => task.completed)
    } else if (selectedTask.value === "Incomplète") {
        return tasks.value.filter(task => !task.completed)
    } else {
        return tasks.value
    }
  })

onMounted(loadTasks)

</script>

<style scoped>
.completed {
    text-decoration: line-through;
    color: grey;
}
</style>
