<template>
  <div>
    <h2>Task List</h2>
    <button @click="openTaskForm">Create New Task</button>
    <table>
      <thead>
      <tr>
        <th>Title</th>
        <th>Description</th>
        <th>Due Date</th>
        <th>Tags</th>
        <th>Actions</th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="task in tasks" :key="task._id">
        <td>{{ task.title }}</td>
        <td>{{ task.description }}</td>
        <td>{{ task.dueDate }}</td>
        <td>{{ task.tags.join(', ') }}</td>
        <td>
          <button @click="editTask(task)">Edit</button>
          <button @click="deleteTask(task._id)">Delete</button>
        </td>
      </tr>
      </tbody>
    </table>
    <TaskForm v-if="showForm" :task="selectedTask" :isEditing="isEditing" @formSubmitted="handleFormSubmitted" />
  </div>
</template>

<script lang="ts">
  import { defineComponent, PropType } from 'vue';
  import TaskForm from './TaskForm.vue';
  import axios from 'axios';

  interface Task {
    _id?: string;
    title: string;
    description: string;
    dueDate: string;
    tags: string[];
  }

  export default defineComponent({
    components: {
      TaskForm
    },
    props: {
      tasks: {
        type: Array as PropType<Task[]>,
        required: true
      }
    },
    data() {
      return {
        showForm: false,
        selectedTask: null as Task | null,
        isEditing: false
      };
    },
    methods: {
      openTaskForm() {
        this.selectedTask = { title: '', description: '', dueDate: '', tags: [] };
        this.isEditing = false;
        this.showForm = true;
      },
      editTask(task: Task) {
        this.selectedTask = { ...task };
        this.isEditing = true;
        this.showForm = true;
      },
      async deleteTask(taskId: string) {
        await axios.delete(`http://localhost:5000/api/tasks/${taskId}`);
        this.$emit('taskDeleted');
      },
      handleFormSubmitted() {
        this.showForm = false;
        this.$emit('taskUpdated');
      }
    }
  });
</script>
