<script setup>
import {ref} from 'vue';
import Input from './components/Input.vue';
import LLM from './components/LLM.vue';
import FlashCard from "./components/FlashCard.vue";

const subject = ref('');
const flashcard = ref({question: '', answer: ''});
const isLoading = ref(false);

function handleSubmit(newSubject) {
  subject.value = newSubject;
}
</script>

<template>
  <div class="outerBorder">
    <div class="pageBox">
      <img src="https://vuejs.org/images/logo.png" alt="Vue.js Logo" style="height: 6rem; vertical-align: middle;">
      <h1>Vue.js Flashcard Generator</h1>
      <Input @form-submitted="handleSubmit"/>
      <div v-if="isLoading">Generating flashcard...</div>
      <LLM
          :subject="subject"
          v-model:flashcard="flashcard"
          @update:loading="isLoading = $event"
      />
      <FlashCard :flashcard="flashcard"/>
    </div>
  </div>
</template>

<style>
html {
  background-color: #212327;
}

.outerBorder {
  border: #35495e solid 4px;
  border-radius: 2.5rem;
  padding: 0.25rem;
}

.pageBox {
  border: #42b983 solid 4px;
  border-radius: 2rem;
  padding: 10rem;
}
</style>
