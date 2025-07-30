<script setup>
import FlashCard from "./components/FlashCard.vue";
import HomePage from "./components/HomePage.vue";
import Input from "./components/Input.vue";
import {ref} from "vue";
import ExampleLlm from "./components/ExampleLLM.vue";

const showFlashCard = ref(false)
const subject = ref('');

function handleFlashcardSubmit(value) {
  subject.value = value;
  showFlashCard.value = true
}
</script>

<template>
<!--  <ExampleLlm/>-->
</template>

<style scoped>

</style>
