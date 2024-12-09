<template>
    <div class="todo-container">
      <div class="todo-box">
        <h1>Daily To Do list</h1>
        
        <!-- Input Section -->
        <div class="input-group">
          <input 
            v-model="newTask" 
            @keyup.enter="addTask" 
            placeholder="Add New Task..."
            class="task-input"
          />
          <button @click="addTask" class="add-btn">Add Task</button>
        </div>
  
        <!-- Pending Tasks -->
        <div class="task-section">
        <h3>Pending Tasks</h3>
        <div class="tasks-container">
          <!-- Show when no tasks -->
          <div v-if="pendingTasks.length === 0" class="no-tasks">
            <p>No Tasks Found!</p>
            <span>Add a new task to get started</span>
          </div>
    
   
    <div v-else v-for="task in pendingTasks" 
               :key="task.id"
               class="task-item">
            <div class="task-content">
              <input 
                type="checkbox" 
                v-model="task.completed"
                class="checkbox"
              />
              <span>{{ task.text }}</span>
            </div>
            <div class="task-actions">
              <button @click="removeTask(task.id)" class="delete-btn">
                Delete
              </button>
            </div>
          </div>
        </div>
      </div>

        <!-- Completed Tasks -->
        <div class="completed-section" v-show="completedTasks.length > 0">
          <h3>Complete Task</h3>
          <div class="completed-container">
            <div v-for="task in completedTasks" 
                 :key="task.id"
                 class="task-item completed">
              <div class="task-content">
                <input 
                  type="checkbox" 
                  v-model="task.completed"
                  class="checkbox"
                />
                <span>{{ task.text }}</span>
              </div>
              <div class="task-actions">
                <button @click="removeTask(task.id)" class="delete-btn">
                  Delete
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import { ref, computed, watch } from 'vue'
  
  export default {
    name: 'TodoList',
    setup() {
      const newTask = ref('')
      const tasks = ref([])
  
      // Load saved tasks
      if (localStorage.getItem('tasks')) {
        tasks.value = JSON.parse(localStorage.getItem('tasks'))
      }
  
      // Separate pending and completed tasks
      const pendingTasks = computed(() => {
        return tasks.value.filter(task => !task.completed)
      })
  
      const completedTasks = computed(() => {
        return tasks.value.filter(task => task.completed)
      })
  
      const addTask = () => {
        if (newTask.value.trim()) {
          tasks.value.push({
            id: Date.now(),
            text: newTask.value,
            completed: false
          })
          saveTasks()
          newTask.value = ''
        }
      }
  
      const removeTask = (taskId) => {
        tasks.value = tasks.value.filter(t => t.id !== taskId)
        saveTasks()
      }
  
      const saveTasks = () => {
        localStorage.setItem('tasks', JSON.stringify(tasks.value))
      }
  
      // Watch for changes in tasks
      watch(tasks, () => {
        saveTasks()
      }, { deep: true })
  
      return {
        newTask,
        tasks,
        pendingTasks,
        completedTasks,
        addTask,
        removeTask
      }
    }
  }
  </script>
  
  <style scoped>
  .todo-container {
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
    background-color: #f5f6fa;
  }
  
  .todo-box {
    background: white;
    padding: 2rem;
    border-radius: 15px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    width: 100%;
    max-width: 600px;
  }
  
  h1 {
    color: #2c3e50;
    margin-bottom: 1.5rem;
    text-align: center;
  }
  
  h3 {
    color: #2c3e50;
    margin: 1.5rem 0 1rem;
    font-size: 1.2rem;
  }
  
  .input-group {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
  }
  
  .task-input {
    flex: 1;
    padding: 10px;
    border: 2px solid #e2e8f0;
    border-radius: 8px;
    font-size: 1rem;
  }
  
  .add-btn {
    background-color: #4CAF50;
    color: white;
    border: none;
    padding: 10px 20px;
    border-radius: 8px;
    cursor: pointer;
  }
  
  .task-section, .completed-section {
    margin-bottom: 1.5rem;
  }
  
  .completed-section {
    border-top: 2px dashed #e2e8f0;
    padding-top: 1rem;
  }
  
  .task-item {
    background: #f8f9fa;
    margin-bottom: 8px;
    padding: 12px;
    border-radius: 8px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    transition: all 0.3s ease;
  }
  
  .task-content {
    display: flex;
    align-items: center;
    gap: 10px;
  }
  
  .checkbox {
    width: 18px;
    height: 18px;
    cursor: pointer;
  }
  
  .completed {
    background: #e8f5e9;
    opacity: 0.8;
  }
  
  .completed span {
    text-decoration: line-through;
    color: #666;
  }
  
  .delete-btn {
    background-color: #ff4757;
    color: white;
    border: none;
    padding: 6px 12px;
    border-radius: 6px;
    cursor: pointer;
  }
  
  .delete-btn:hover {
    background-color: #ff6b81;
  }
  
  /* Animation for tasks */
  .task-item {
    animation: slideIn 0.3s ease-out;
  }
  
  @keyframes slideIn {
    from {
      opacity: 0;
      transform: translateY(-10px);
    }
    to {
      opacity: 1;
      transform: translateY(0);
    }
  }
  .no-tasks {
  text-align: center;
  padding: 2rem;
  background: #f8f9fa;
  border-radius: 8px;
  color: #666;
}

.no-tasks p {
  font-size: 1.2rem;
  margin-bottom: 0.5rem;
  color: #2c3e50;
}

.no-tasks span {
  font-size: 0.9rem;
  color: #666;
}
  </style>