<template>
  <div style="font-family: Verdana;">
    <div class="custom-background">
    <h1 class="title"> {{ title }} </h1>

    <div class="main-container">
      <div class="tasks-container">
        <h2>Add a new task</h2>
        <span>You have {{ allTasks }} {{ allTasks > 1 ? 'tasks' : 'task' }} </span>

        <div>
          <input type="text"
              v-model="newTask"
              @keyup.enter="addTask"
              placeholder="Add a new task"
          >

          <button
            @click="addTask"
            :disabled="newTask.length < 1"
          >
            Add task
          </button>
        </div>

        <div v-if="newTask.length > 0">
          <h3>New task preview</h3>
          <p>{{ newTask }}</p>
        </div>

        <ol>
          <li v-if="tasks.length === 0">No tasks added yet</li>
          <li 
            v-for="(task, index) in tasks" 
            :key="task.id"
            @click="finishTask(task)"
            :class="{ strikeout: task.finished }"
          >
            {{ task.name }}

            <div v-if="task.finished">
                <button @click="removeTask(task.id)">Delete task</button>
            </div>
          </li>
        </ol>

        <button
            @click="resetTaskList"
            :disabled="tasks.length < 1"
          >
            Reset Tasks
          </button>
      </div>

      <div class="counts-container">
        <div class="count-box">
          <h2>Task Stats</h2>
          <p>Completed tasks: {{ completedTasksCount }}</p>
          <p>Uncompleted tasks: {{ uncompletedTasksCount }}</p>
        </div>
      </div>
    </div>
  </div>
  </div>
</template>

<script setup>
  import { ref, reactive, computed, onMounted } from 'vue'

  const title = 'My To Do App'
  const newTask = ref('')
  const tasks = reactive([])

  onMounted (async() => {
    const response = await
    fetch("https://hkaasx7l4f.execute-api.us-east-2.amazonaws.com/todos")
    tasks = await
    response.json()
  })
  // methods from object api
  const addTask = () => {
    if (newTask.value.length < 1) return
      if (tasks.length === 0) {
        // If no tasks, add the new task with index 1
        tasks.push({
          id: 1,
          name: newTask.value,
          finished: false
        });
    } else {
      // Find the index of the last task and add the new task after it
      const lastTaskIndex = tasks[tasks.length - 1].id;
      tasks.push({
        id: lastTaskIndex + 1,
        name: newTask.value,
        finished: false
      });
    }
    newTask.value = ''
  };

  const removeTask = (taskId) => {
    const taskIndex = tasks.findIndex(task => task.id === taskId);
    if (taskIndex !== -1) {
      tasks[taskIndex].finished = true;
      tasks.splice(taskIndex, 1);
    }
  };

  const finishTask = (task) => {
    task.finished = !task.finished;
  };

  const resetTaskList = () => {
    tasks.splice(0, tasks.length);
  };

  //computed
  const allTasks = computed(() => tasks.length);
  const completedTasksCount = computed(() => tasks.filter(task => task.finished).length);
  const uncompletedTasksCount = computed(() => tasks.filter(task => !task.finished).length);

</script>

<style>
  .strikeout {
    text-decoration: line-through;
    padding: 20px;
    border-radius: 5px;
  }

  .custom-background {
    background-image: url("https://images.unsplash.com/photo-1521571086300-579bd981bbb6?q=80&w=2070&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D");
    background-size: cover; 
    background-position: center;
    padding: 20px;
    border-radius: 5px;
    height: 100vh;
  }

  .body-font {
    font-family: Verdana;
  }

  .title {
    background-color: rgb(186, 207, 192);
    color: rgb(45, 91, 63);
    font-weight: bold;
    text-align: center;
    margin-bottom: 20px;
    padding: 10px; 
    border-radius: 5px;
  }

  .main-container {
    display: flex;
    justify-content: space-between;
  }

  .tasks-container {
    background-color: rgb(148, 196, 169);
    color: rgb(26, 25, 25);
    padding: 20px;
    border-radius: 5px;
    width: 50%;
  }

  .counts-container {
    width: 25%;
  }

  .count-box {
    background-color: rgb(148, 196, 169);
    color: rgb(26, 25, 25);
    padding: 20px;
    border-radius: 5px;
  }
</style>
