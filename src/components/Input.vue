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
  <form @submit="handleForm">
    <label for="subject">Enter subject:</label>
    <input v-model="subject" type="text" id="subject" required>
    <button type="submit">Generate</button>
  </form>
</template>

<style scoped>

</style>
