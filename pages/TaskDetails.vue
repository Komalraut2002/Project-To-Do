<template>
  <div>
    <h2>Edit Task</h2>
    <div>
      <label>Title:</label>
      <input v-model="editedTask.title" />
    </div>
    <div>
      <label>Status:</label>
      <input v-model="editedTask.status" />
    </div>
    <div>
      <label>Description:</label>
      <textarea v-model="editedTask.description"></textarea>
    </div>
    <button id="update" @click="updateTask">Update Task</button>
    <button id="delete" @click="deleteTask">Delete Task</button>
    <button id="back" @click="back"><nuxt-link to="/">Back</nuxt-link></button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      editedTask: {
        id: null,
        title: '',
        status: '',
        description: '',
      },
    };
  },
  mounted() {
    const taskId = this.$route.params.taskId;
    const storedTask = localStorage.getItem(`taskDetails-${taskId}`);
    if (storedTask) {
      this.editedTask = JSON.parse(storedTask);
    }
  },
  methods: {
    updateTask() {
      console.log('Task updated:', this.editedTask);
      const taskId = this.$route.params.taskId;
      localStorage.setItem(`taskDetails-${taskId}`, JSON.stringify(this.editedTask));

      this.$router.push('/');
    },
    deleteTask() {
      console.log('Task deleted:', this.editedTask);
      const taskId = this.$route.params.taskId;
      localStorage.removeItem(`taskDetails-${taskId}`);

      this.$router.push('/');
    },
    back() {
      this.$router.push('/');
    },
  },
};
</script>

<style scoped>

div {
  max-width: 400px;
  margin: 0 auto;
  padding: 20px;
  background-color: #f8f8f8; 
  border: 1px solid #ddd;
  border-radius: 8px;
  box-shadow: 0 8px 8px rgba(3, 3, 3, 0.1);
  text-align: center;
}

h2 {
  color: #333;
}

label {
  display: block;
  margin-bottom: 8px;
  font-weight: bold;
  color: #555;
}

input, textarea {
  width: 100%;
  padding: 8px;
  margin-bottom: 16px;
  border: 1px solid #ddd;
  border-radius: 4px;
  box-sizing: border-box;
}

button {
  background-color: #3498db;
  color: #fff;
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
}

button:hover {
  background-color: #2980b9; 
}

#delete {
  margin-top: 20px;
  margin-left: 30px;
}

#back {
  margin-top: 20px;
  margin-left: 30px;
}

#back a {
  text-decoration: none; 
  color: white; 
}
</style>
