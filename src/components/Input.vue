<script setup>
import { ref } from "vue";

const emit = defineEmits(['form-submitted']);
const subject = ref('');

function handleForm(event) {
  event.preventDefault();
  if (subject.value.trim()) {
    emit('form-submitted', subject.value);
  }
}
</script>

<template>
  <div class="form-container">
    <form @submit="handleForm" class="input-group">
      <input
          v-model="subject"
          type="text"
          class="input-field"
          placeholder="Enter a topic..."
          required
      >
      <button type="submit" class="submit-button">
        Generate Flashcard
      </button>
    </form>
  </div>
</template>

<style scoped>
.form-container {
  max-width: 600px;
  margin: 0 auto;
  padding: 1.5rem;
}

.input-group {
  display: flex;
  gap: 0.5rem;
  width: 100%;
}

.input-field {
  flex: 1;
  padding: 0.75rem 1rem;
  border: 2px solid #e2e8f0;
  border-radius: 0.5rem;
  font-size: 1rem;
  transition: all 0.2s ease;
  outline: none;
}

.input-field:focus {
  border-color: #42b983;
  box-shadow: 0 0 0 3px rgba(66, 185, 131, 0.1);
}

.submit-button {
  padding: 0.75rem 1.5rem;
  background-color: #42b983;
  color: white;
  border: none;
  border-radius: 0.5rem;
  font-size: 1rem;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s ease;
}

.submit-button:hover {
  background-color: #3aa876;
  transform: translateY(-1px);
}

.submit-button:active {
  transform: translateY(0);
}

@media (max-width: 640px) {
  .input-group {
    flex-direction: column;
  }
}
</style>
