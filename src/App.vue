<script setup>
import {reactive,ref,computed} from 'vue';
import Task from './components/Task.vue';
import Filter from './components/Filter.vue';
import ModalWindow from './components/modal/ModalWindow.vue';   

const appName="Tasks Manager";

let tasks= reactive([
   
]);

let newTask=reactive({
  name: '',
  description: '',
  completed: false
});

let filterBy=ref("");
let modelIsActive=ref(false);
let formErrors = reactive({});

const filteredTasks=computed(()=>{
  switch(filterBy.value){
    case 'done':
      return tasks.filter(task=>task.completed);
    case 'todo':
      return tasks.filter(task=>!task.completed);  
    default:
      return tasks;
  }
});

const taskCount = computed(() => {
  const total = tasks.length;
  const completed = tasks.filter(task => task.completed).length;
  const pending = total - completed;
  return { total, completed, pending };
});

function validateForm() {
  formErrors = {};
  
  if (!newTask.name.trim()) {
    formErrors.name = 'Title is required';
  }
  
  if (!newTask.description.trim()) {
    formErrors.description = 'Description is required';
  }
  
  return Object.keys(formErrors).length === 0;
}

function addTask() {
  if (validateForm()) {
    newTask.id = Math.max(...tasks.map(task=>task.id)) + 1;  
    tasks.push({...newTask});
    resetForm();
    modelIsActive.value = false;
  }
}

function resetForm() {
  newTask.name = '';
  newTask.description = '';
  newTask.completed = false;
  formErrors = {};
}

function toggleTaskCompleted(id) {
  const task = tasks.find(task => task.id === id);
  if (task) {
    task.completed = !task.completed;
  }
}

function deleteTask(id) {
  if (confirm('Are you sure you want to delete this task?')) {
    const index = tasks.findIndex(task => task.id === id);
    if (index !== -1) {
      tasks.splice(index, 1);
    }
  }
}

function setFilter(value){
  filterBy.value = value;
}

function openAddTaskModal() {
  resetForm();
  modelIsActive.value = true;
}
</script>

<template>
  <main class="container">
    <div class="header">
      <div class="header-side">
        <h1>
          Tasks Manager
        </h1>
        <div class="task-stats">
          <span class="stat total">Total: {{ taskCount.total }}</span>
          <span class="stat completed">Completed: {{ taskCount.completed }}</span>
          <span class="stat pending">Pending: {{ taskCount.pending }}</span>
        </div>
      </div> 
      <div class="header-side">
        <button class="btn primary" @click="openAddTaskModal">
          <span class="btn-icon">+</span> Add Task
        </button>
      </div>
    </div>
    
    <Filter :filter-by="filterBy" @setFilter="setFilter"/>
    
    <div v-if="filteredTasks.length === 0" class="empty-state">
      <div class="empty-icon">📝</div>
      <h3>No tasks found</h3>
      <p v-if="filterBy === 'done'">No completed tasks. Try completing some tasks first!</p>
      <p v-else-if="filterBy === 'todo'">No pending tasks. Great job! </p>
      <p v-else>No tasks available. Add your first task to get started!</p>
      <button class="btn primary" @click="openAddTaskModal">Add Your First Task</button>
    </div>
    
    <div v-else class="tasks">
      <Task 
        @togglecompleted="toggleTaskCompleted" 
        @delete="deleteTask" 
        v-for="task in filteredTasks" 
        :task="task" 
        :key="task.id"
      />
    </div>
    
    <!-- Modal Window for Adding Tasks -->
    <ModalWindow v-if="modelIsActive" @closePopup="modelIsActive = false">
      <div class="form">
        <h3>Add a new task</h3>
        
        <div class="form-group">
          <label for="title">Title *</label>
          <input 
            v-model="newTask.name" 
            type="text" 
            id="title" 
            name="title" 
            placeholder="Enter a title..."
            :class="{ 'error': formErrors.name }"
            @keyup.enter="addTask"
          >
          <span v-if="formErrors.name" class="error-message">{{ formErrors.name }}</span>
        </div>
        
        <div class="form-group">
          <label for="description">Description *</label>
          <textarea 
            v-model="newTask.description" 
            id="description" 
            name="description" 
            rows="4" 
            placeholder="Enter a description..."
            :class="{ 'error': formErrors.description }"
          ></textarea>
          <span v-if="formErrors.description" class="error-message">{{ formErrors.description }}</span>
        </div>
        
        <div class="form-group checkbox-group">
          <input 
            v-model="newTask.completed" 
            type="checkbox" 
            id="completed" 
            name="completed"
          >
          <label for="completed">Mark as completed</label>
        </div>
        
        <div class="form-actions">
          <button type="button" class="btn secondary" @click="modelIsActive = false">Cancel</button>
          <button type="button" class="btn primary" @click="addTask">Add Task</button>
        </div>
      </div>
    </ModalWindow>
  </main>
</template>

<style lang="scss" scoped>
.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
  min-height: 100vh;
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 30px;
  flex-wrap: wrap;

  .header-side {
    display: flex;
    flex-direction: column;

    h1 {
      text-transform: capitalize;
      font-size: 42px;
      font-weight: 700;
      line-height: 47px;
      margin: 0 0 10px 0;
      color: #2c3e50;
    }
    
    .task-stats {
      display: flex;
      gap: 15px;
      flex-wrap: wrap;
      
      .stat {
        font-size: 14px;
        font-weight: 500;
        padding: 4px 12px;
        border-radius: 20px;
        background: #f8f9fa;
        
        &.total {
          color: #6c757d;
        }
        
        &.completed {
          color: #28a745;
          background: #d4edda;
        }
        
        &.pending {
          color: #ffc107;
          background: #fff3cd;
        }
      }
    }
  }
}

.tasks {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(350px, 1fr));
  gap: 24px;
  margin-top: 24px;

  @media (max-width: 768px) {
    grid-template-columns: 1fr;
  }
}

.empty-state {
  text-align: center;
  padding: 60px 20px;
  color: #666;
  margin-top: 40px;
  background: #f8f9fa;
  border-radius: 12px;
  border: 2px dashed #dee2e6;
  
  .empty-icon {
    font-size: 64px;
    margin-bottom: 20px;
  }
  
  h3 {
    font-size: 24px;
    margin: 0 0 10px 0;
    color: #495057;
  }
  
  p {
    font-size: 16px;
    margin-bottom: 20px;
    max-width: 400px;
    margin-left: auto;
    margin-right: auto;
  }
  
  .btn {
    margin-top: 15px;
  }
}

.form {
  width: 100%;
  
  h3 {
    margin-top: 0;
    margin-bottom: 24px;
    font-size: 24px;
    color: #2c3e50;
  }
  
  .form-group {
    margin-bottom: 20px;
    
    label {
      display: block;
      margin-bottom: 8px;
      font-weight: 500;
      color: #495057;
      font-size: 14px;
    }
    
    input[type="text"],
    textarea {
      width: 100%;
      padding: 10px 12px;
      border: 1px solid #ced4da;
      border-radius: 6px;
      font-size: 14px;
      transition: border-color 0.3s;
      font-family: inherit;
      
      &:focus {
        outline: none;
        border-color: #4a6cf7;
        box-shadow: 0 0 0 3px rgba(74, 108, 247, 0.1);
      }
      
      &.error {
        border-color: #e74c3c;
      }
    }
    
    textarea {
      resize: vertical;
      min-height: 100px;
    }
    
    .error-message {
      display: block;
      color: #e74c3c;
      font-size: 12px;
      margin-top: 5px;
    }
    
    &.checkbox-group {
      display: flex;
      align-items: center;
      
      input[type="checkbox"] {
        margin-right: 10px;
        width: 18px;
        height: 18px;
        cursor: pointer;
      }
      
      label {
        margin-bottom: 0;
        cursor: pointer;
        font-size: 14px;
      }
    }
  }
  
  .form-actions {
    display: flex;
    justify-content: flex-end;
    gap: 12px;
    margin-top: 30px;
    padding-top: 20px;
    border-top: 1px solid #eee;
  }
}

.btn {
  padding: 12px 24px;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-weight: 500;
  font-size: 14px;
  transition: all 0.3s;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  
  &:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  }
  
  &:active {
    transform: translateY(0);
  }
  
  .btn-icon {
    margin-right: 8px;
    font-size: 16px;
    font-weight: bold;
  }
}

.primary {
  background-color: #4a6cf7;
  color: white;
  
  &:hover {
    background-color: #3a5ce5;
  }
}

.secondary {
  background-color: #6c757d;
  color: white;
  
  &:hover {
    background-color: #5a6268;
  }
}

@media (max-width: 768px) {
  .header {
    flex-direction: column;
    gap: 15px;
    
    .header-side {
      width: 100%;
    }
  }
  
  .form-actions {
    flex-direction: column;
    
    .btn {
      width: 100%;
    }
  }
}
</style>