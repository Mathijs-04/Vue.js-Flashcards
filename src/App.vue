<script setup>
import {ref} from 'vue';
import Input from './components/Input.vue';
import LLM from './components/LLM.vue';
import FlashCard from "./components/FlashCard.vue";

const subject = ref('');
const flashcard = ref({question: '', answer: ''});
const isLoading = ref(false);
const engineReady = ref(false);

function handleSubmit(newSubject) {
  subject.value = newSubject;
}
</script>

<template>
  <div class="outerBorder">
    <div class="pageBox">
      <img src="https://vuejs.org/images/logo.png" alt="Vue.js Logo" style="height: 6rem; vertical-align: middle;">
      <h1>Vue.js Flashcard Generator</h1>
      <Input @form-submitted="handleSubmit" :disabled="isLoading || !engineReady"
      />
      <div v-if="isLoading">
        <span class="loader"/>
      </div>
      <LLM
          :subject="subject"
          v-model:flashcard="flashcard"
          @update:loading="isLoading = $event"
          @update:engine-ready="engineReady = $event"
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
  border: #42b983 solid 4px;
  border-radius: 2.5rem;
  padding: 0.25rem;
}

.pageBox {
  border: #35495e solid 4px;
  border-radius: 2rem;
  padding: 2rem 10rem 2rem 10rem;
}

.loader {
  display: inline-block;
  width: 1.5rem;
  height: 1.5rem;
  border: 3px solid #42b983;
  border-top: 3px solid #35495e;
  border-radius: 50%;
  animation: spin 0.8s linear infinite;
  vertical-align: middle;
  margin-right: 0.5rem;
}

@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}
</style>
