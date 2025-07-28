<script setup>
import {onMounted, ref} from "vue";

  const emit = defineEmits(['form-submitted'])
  const formRef = ref(null);
  const subject = ref('');
  function handleForm(event) {
    event.preventDefault();
    emit('form-submitted', subject.value);
  }

  onMounted(() => {
    showFlashCard.value = false;
    if (formRef.value) {
      formRef.value.addEventListener('submit', handleForm);
    }
  });

</script>

<template>
  <form ref="formRef" id="FlashcardForm" @submit="handleForm">
    <label for="subject">Enter subject:</label><br>
    <input v-model="subject" type="text" id="subject" name="subject">
    <br>
    <input type="submit" value="Generate Flashcard" class="submit">
  </form>
</template>

<style scoped>
  label {
    font-size: 2rem;
    font-weight: bold;
  }

  input {
    width: 42%;
    margin-bottom: 0.75rem;
  }

  .submit {
    width: 30%;
    padding: 0.5rem;
    border-radius: 0.25rem;
  }
</style>
