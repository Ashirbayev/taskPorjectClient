<template>
  <div>
    <h1>Task Manager</h1>
    <TaskForm @formSubmitted="fetchTasks" />
    <TaskList :tasks="tasks" @taskUpdated="fetchTasks" @taskDeleted="fetchTasks" />
  </div>
</template>

<script lang="ts">
  import { defineComponent } from 'vue';
  import axios from 'axios';
  import TaskForm from '../components/TaskForm.vue';
  import TaskList from '../components/TaskList.vue';

  export default defineComponent({
    name: 'HomeView',
    components: {
      TaskForm,
      TaskList
    },
    data() {
      return {
        tasks: []
      };
    },
    methods: {
      async fetchTasks() {
        try {
          const response = await axios.get(`http://localhost:5000/api/tasks`);
          this.tasks = response.data;
        } catch (error) {
          console.error('Error fetching tasks:', error);
        }
      }
    },
    mounted() {
      this.fetchTasks();
    }
  });
</script>

<style scoped>
  /* Добавьте стили по необходимости */
</style>
