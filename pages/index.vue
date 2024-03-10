<template>
  <div>
    <div class="container">
      <div v-for="(section, index) in sections" :key="index">
        <h4>
          <span :style="{ backgroundColor: section.titleColor, padding: '5px', borderRadius: '5px' }">
            {{ section.title }}
          </span>
          <span class="task-count">{{ section.tasks.length }}</span>
          <span class="remove-section-icon" @click="removeSection(section.id)">❌</span>
        </h4>

        <div class="progress-section" :id="section.id">
          <div v-for="task in section.tasks" :key="task.id" class="task-card">
            <nuxt-link :to="{ name: 'TaskDetails', params: { sectionId: section.id, taskId: task.id } }">
              <div class="task-info" @click="openTaskDetails(task)">
                {{ task.title }}
              </div>
            </nuxt-link>
            <div class="delete-icon" @click="deleteTask(section.id, task.id)">🗑️</div>
          </div>
        </div>

        <div class="add-task-icon" @click="addNewTask(section.id)">➕ New</div>
      </div>
      <div class="new-status-container">
        <div class="dropdown">
          <button class="dropbtn">Add New Status</button>
          <div class="dropdown-content">
            <input v-model="newStatusTitle" placeholder="New Status Title">
            <button @click="addNewStatus">Add</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      sections: [
        { id: "not-started", title: "Not Started", tasks: [], titleColor: "rgb(235, 156, 156)" },
        { id: "in-progress", title: "In Progress", tasks: [], titleColor: "rgb(255, 253, 132)" },
        { id: "completed", title: "Completed", tasks: [], titleColor: "rgb(155, 236, 133)" },
      ],
      totalTaskCount: 0,
      newStatusTitle: "",
    };
  },

  methods: {
    openTaskDetails(task) {
      console.log("Open details for task:", task);
    },
    addNewTask(sectionId) {
      const section = this.sections.find((s) => s.id === sectionId);
      const existingNumbers = this.sections
        .reduce((numbers, s) => numbers.concat(s.tasks.map((task) => parseInt(task.title.split(" ")[1]))), [])
        .filter((number) => !isNaN(number));
      const newTaskNumber = Math.max(...existingNumbers, 0) + 1;
      const newTaskTitle = `Card ${newTaskNumber}`;
      const newTask = { id: Date.now(), title: newTaskTitle };
      section.tasks.push(newTask);
      this.totalTaskCount++;
    },

    deleteTask(sectionId, taskId) {
      const section = this.sections.find((s) => s.id === sectionId);
      section.tasks = section.tasks.filter((task) => task.id !== taskId);
      this.totalTaskCount--;
      this.saveTasksToLocalStorage();
    },
    addNewStatus() {
      if (this.newStatusTitle.trim() !== "") {
        const newStatus = {
          id: `status-${this.sections.length + 1}`,
          title: this.newStatusTitle,
          tasks: [],
          titleColor: "rgb(120, 207, 207)",
        };
        this.sections.push(newStatus);
        this.newStatusTitle = "";
        this.saveTasksToLocalStorage();
      }
    },
    removeSection(sectionId) {
      const index = this.sections.findIndex((s) => s.id === sectionId);
      if (index !== -1) {
        this.sections.splice(index, 1);
        this.saveTasksToLocalStorage();
      }
    },
    saveTasksToLocalStorage() {
      localStorage.setItem("taskSections", JSON.stringify(this.sections));
    },
  },
};
</script>

<style>
body {
  width: 90%;
  max-width: 900px;
  margin: 2em auto;
  font: 0.9em/1.2 Arial, Helvetica, sans-serif;
}

.container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 20px;
}

.new-status-container {
  position: fixed;
  top: 10px;
  right: 10px;
  display: flex;
  flex-direction: column;
}

.new-status-container input,
.new-status-container button {
  margin-bottom: 10px;
}

.task-card {
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-radius: 5px;
  padding: 10px;
  background-color: rgb(255, 255, 255);
  border: 2px solid rgb(79, 185, 227);
  box-shadow: 0 10px 10px rgba(0, 0, 0, 0.1);
  margin-top: 10px;
  cursor: pointer;
}

.task-info {
  cursor: pointer;
}

.delete-icon {
  cursor: pointer;
  color: #e74c3c;
}

.remove-section-icon {
  cursor: pointer;
  margin-left: 10px;
  color: #e74c3c;
}

.add-task-icon {
  margin: 9px;
  cursor: pointer;
  color: grey;
}

.progress-section {
  border-radius: 5px;
}

.task-count {
  margin-left: 10px;
  font-size: 1em;
  color: grey;
}

/* Dropdown styles */
.dropdown {
  position: relative;
  display: inline-block;
}

.dropbtn {
  background-color: #3498db;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
}

.dropdown-content {
  display: none;
  position: absolute;
  background-color: #f9f9f9;
  min-width: 160px;
  box-shadow: 0 8px 8px rgba(3, 3, 3, 0.1);
  border: 1px solid #ddd;
  border-radius: 4px;
  z-index: 1;
}

.dropdown-content input,
.dropdown-content button {
  width: 100%;
  padding: 8px;
  margin-bottom: 16px;
  border: 1px solid #ddd;
  border-radius: 4px;
  box-sizing: border-box;
}

.dropdown-content button {
  background-color: #3498db;
  color: white;
}

.dropdown-content button:hover {
  background-color: #2980b9;
}

.dropdown:hover .dropdown-content {
  display: block;
}

.dropdown:hover .dropbtn {
  background-color: #2980b9;
}
</style>
