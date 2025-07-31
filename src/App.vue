<script setup>
import { ref } from 'vue';
import Input from './components/Input.vue';
import LLM from './components/LLM.vue';
import FlashCard from "./components/FlashCard.vue";

const subject = ref('');
const flashcard = ref({ question: '', answer: '' });
const isLoading = ref(false);

function handleSubmit(newSubject) {
  subject.value = newSubject;
}
</script>

<template>
  <h1>Vue.js Flashcard Generator</h1>
  <Input @form-submitted="handleSubmit" />
  <div v-if="isLoading">Generating flashcard...</div>
  <LLM
      :subject="subject"
      v-model:flashcard="flashcard"
      @update:loading="isLoading = $event"
  />
  <FlashCard :flashcard="flashcard" />
</template>

<style >
  html {
    background-color: #212327;
  }
</style>
