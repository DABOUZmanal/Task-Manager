<script setup>
import ModalCloseButton from './ModalCloseButton.vue';
import { onMounted, onUnmounted } from 'vue';

const emit = defineEmits(['closePopup']);

function handleEscapeKey(event) {
  if (event.key === 'Escape') {
    emit('closePopup');
  }
}

function handleClickOutside(event) {
  if (event.target.classList.contains('modal-wrapper')) {
    emit('closePopup');
  }
}

onMounted(() => {
  document.addEventListener('keydown', handleEscapeKey);
  document.body.style.overflow = 'hidden'; 
});

onUnmounted(() => {
  document.removeEventListener('keydown', handleEscapeKey);
  document.body.style.overflow = ''; 
});
</script>

<template>
  <div 
    class="modal-wrapper" 
    aria-modal="true"
    role="dialog" 
    tabindex="-1"
    @click="handleClickOutside"
  >
    <div class="inner">
      <ModalCloseButton @click="$emit('closePopup')"/>
      <slot></slot>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.modal-wrapper {
  position: fixed;
  left: 0;
  top: 0;
  z-index: 500;
  width: 100vw;
  height: 100vh;
  background: rgba(0, 0, 0, 0.5); 
  display: grid;
  place-items: center;
  color: #000;
  animation: fadeIn 0.3s ease-out;
  
  .inner {
    background-color: white;
    padding: 40px 30px 30px 30px; 
    border-radius: 12px;
    display: flex;
    flex-direction: column;
    position: relative;
    max-width: 600px;
    width: 90%;
    max-height: 80vh;
    overflow-y: auto;
    animation: slideUp 0.3s ease-out;
    box-shadow: 0 10px 40px rgba(0, 0, 0, 0.2);

    h3 {
      font-size: 20px; 
      font-weight: 700;
      line-height: 21px;
      margin-bottom: 20px;
      margin-top: 0;
    }

    .form {
      display: flex;
      flex-direction: column;
      max-width: 100%;

      label {
        font-size: 14px;
        font-weight: 500;
        line-height: 16px;
        letter-spacing: 0em;
        text-align: left;
        margin-bottom: 5px;
      }

      input,
      select,
      textarea {
        font-size: 14px; 
        font-weight: 400;
        line-height: 16px;
        letter-spacing: 0em;
        text-align: left;
        border: 1px solid #C2C2C2;
        border-radius: 6px; 
        padding: 10px 12px; 
        margin-top: 0;
        margin-bottom: 15px;
        transition: border-color 0.3s;

        &:focus {
          outline: none;
          border-color: #4a6cf7;
          box-shadow: 0 0 0 3px rgba(74, 108, 247, 0.1);
        }

        &::placeholder {
          color: #A6A6A6;
        }
      }

      .btn {
        width: fit-content;
        padding-inline: 23px;
      }
    }
  }
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

@keyframes slideUp {
  from {
    transform: translateY(20px);
    opacity: 0;
  }
  to {
    transform: translateY(0);
    opacity: 1;
  }
}
</style>