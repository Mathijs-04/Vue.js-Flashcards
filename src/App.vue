<script setup>
import FlashCard from "./components/FlashCard.vue";
import HomePage from "./components/HomePage.vue";
import Input from "./components/Input.vue";
import {ref} from "vue";

const showFlashCard = ref(false)
const subject = ref('');

function handleFlashcardSubmit(value) {
  subject.value = value;
  showFlashCard.value = true
}
</script>

<template>
  <HomePage/>
  <Input @form-submitted="handleFlashcardSubmit"/>
  <FlashCard v-if="showFlashCard" v-bind:subject="subject"/>
</template>

<style scoped>

</style>
