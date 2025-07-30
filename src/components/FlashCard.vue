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
    <div class="question">Q: {{ flashcard.question }}</div>
    <div class="answer">A: {{ flashcard.answer }}</div>
  </div>
</template>

<style scoped>

</style>
