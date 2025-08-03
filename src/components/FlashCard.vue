<script setup>
defineProps({
  flashcard: {
    type: Object,
    default: () => ({ question: '', answer: '' })
  }
});
</script>

<template>
  <div v-if="flashcard && flashcard.question" class="flashcard">
    <div class="question">{{ flashcard.question }}</div>
    <div class="answer">{{ flashcard.answer }}.</div>
  </div>
</template>

<style scoped>
    .flashcard {
      background-color: #292a2d;
      padding: 2rem;
      margin: 2rem;
      border-radius: 1rem;
    }
    .question {
      font-size: 2rem;
      margin-top: 1rem;
      font-weight: bold;
      color: #42b983;
    }
    .answer {
      font-size: 1.2rem;
      margin-bottom: 1rem;
    }
</style>
