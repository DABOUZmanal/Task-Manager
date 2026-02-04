<script setup>
const props = defineProps(['task']);

const emit = defineEmits(['togglecompleted', 'delete']);
</script>

<template>
  <div class="task" :class="{ 'completed': task.completed }">
    <div class="task-header">
      <h3>
        {{ task.name }}
      </h3>
      <button class="delete-btn" @click="emit('delete', task.id)" title="Delete task">
        <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <path d="M3 6h18M19 6v14a2 2 0 01-2 2H7a2 2 0 01-2-2V6m3 0V4a2 2 0 012-2h4a2 2 0 012 2v2M10 11v6M14 11v6"/>
        </svg>
      </button>
    </div>
    
    <p class="task-description">
      {{ task.description }}
    </p>
    
    <div class="task-footer">
      <div class="task-check">
        <input 
          type="checkbox" 
          :id="'task-' + task.id" 
          :checked="task.completed"
          @change="emit('togglecompleted', task.id)"
        />
        <label :for="'task-' + task.id">
          {{ task.completed ? 'Done' : 'To-Do' }}
        </label>
      </div>
      
      <div class="task-id">
        #{{ task.id }}
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.task {
  display: flex;
  flex-direction: column;
  background-color: white;
  color: #333;
  padding: 20px;
  border-radius: 12px;
  border-left: 4px solid #4a6cf7;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.08);
  transition: all 0.3s;
  height: 100%;
  position: relative;
  
  &:hover {
    box-shadow: 0 4px 16px rgba(0, 0, 0, 0.12);
    transform: translateY(-2px);
  }
  
  &.completed {
    border-left-color: #28a745;
    opacity: 0.9;
    
    .task-header h3 {
      text-decoration: line-through;
      color: #666;
    }
  }
  
  .task-header {
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    margin-bottom: 16px;
    
    h3 {
      font-size: 20px;
      font-weight: 600;
      line-height: 1.4;
      margin: 0;
      flex: 1;
      margin-right: 12px;
    }
    
    .delete-btn {
      background: none;
      border: none;
      color: #e74c3c;
      cursor: pointer;
      padding: 4px;
      border-radius: 4px;
      display: flex;
      align-items: center;
      justify-content: center;
      transition: all 0.2s;
      
      &:hover {
        background-color: #ffeaea;
        color: #c0392b;
      }
      
      svg {
        pointer-events: none;
      }
    }
  }
  
  .task-description {
    margin: 0 0 20px 0;
    font-size: 16px;
    line-height: 1.6;
    color: #555;
    flex: 1;
  }
  
  .task-footer {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-top: auto;
    padding-top: 16px;
    border-top: 1px solid #eee;
    
    .task-check {
      display: flex;
      align-items: center;
      
      label {
        font-size: 14px;
        font-weight: 500;
        line-height: 16px;
        margin-left: 8px;
        cursor: pointer;
        user-select: none;
      }
      
      input {
        width: 20px;
        height: 20px;
        border-radius: 50%;
        border: 2px solid #ddd;
        appearance: none;
        cursor: pointer;
        position: relative;
        transition: all 0.2s;
        
        &:checked {
          background-color: #28a745;
          border-color: #28a745;
          
          &::before {
            content: '';
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%) rotate(45deg);
            width: 5px;
            height: 10px;
            border: solid white;
            border-width: 0 2px 2px 0;
          }
        }
        
        &:hover {
          border-color: #aaa;
        }
      }
    }
    
    .task-id {
      font-size: 12px;
      color: #999;
      font-weight: 500;
    }
  }
}
</style>