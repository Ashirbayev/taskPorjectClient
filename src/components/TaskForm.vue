<template>
  <div>
    <h2>{{ isEditing ? 'Edit Task' : 'Create Task' }}</h2>
    <form @submit.prevent="submitForm">
      <div>
        <label for="title">Title:</label>
        <input type="text" v-model="task.title" required />
      </div>
      <div>
        <label for="description">Description:</label>
        <textarea v-model="task.description" required></textarea>
      </div>
      <div>
        <label for="dueDate">Due Date:</label>
        <input type="date" v-model="task.dueDate" />
      </div>
      <div>
        <label for="tags">Tags:</label>
        <input type="text" v-model="tagInput" @keyup.enter="addTag" />
        <ul>
          <li v-for="(tag, index) in task.tags" :key="index">
            {{ tag }} <button @click="removeTag(index)">x</button>
          </li>
        </ul>
      </div>
      <button type="submit">{{ isEditing ? 'Update' : 'Create' }}</button>
    </form>
  </div>
</template>

<script lang="ts">
  import { defineComponent, PropType } from 'vue';
  import axios from 'axios';

  interface Task {
    _id?: string;
    title: string;
    description: string;
    dueDate: string;
    tags: string[];
  }

  export default defineComponent({
    props: {
      task: {
        type: Object as PropType<Task>,
        required: false,
        default: () => ({ title: '', description: '', dueDate: '', tags: [] })
      },
      isEditing: {
        type: Boolean,
        default: false
      }
    },
    data() {
      return {
        tagInput: ''
      };
    },
    methods: {
      async submitForm() {
        const apiUrl = 'http://localhost:5000/api';
        if (this.isEditing) {
          await axios.put(`${apiUrl}/tasks/${this.task._id}`, this.task);
        } else {
          await axios.post(`${apiUrl}/tasks`, this.task);
        }
        this.$emit('formSubmitted');
      }
      ,
      addTag() {
        if (this.tagInput && !this.task.tags.includes(this.tagInput)) {
          this.task.tags.push(this.tagInput);
          this.tagInput = '';
        }
      },
      removeTag(index: number) {
        this.task.tags.splice(index, 1);
      }
    }
  });
</script>
