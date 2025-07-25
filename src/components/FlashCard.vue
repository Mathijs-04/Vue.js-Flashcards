<script setup>
import {ref} from 'vue'

const count = ref(0);
</script>

<template>
  <button :class="count % 2 === 0 ? 'button1' : 'button2'" @click="count++">
    Flashcard
  </button>
</template>

<style scoped>
.button1 {
  color: #35495e;
  background-color: #42b883;
  padding: 1rem;
  border-radius: 1rem;
  border: #35495e solid 0.2rem;
}

.button2 {
  color: #42b883;
  background-color: #35495e;
  padding: 1rem;
  border-radius: 1rem;
  border: #42b883 solid 0.2rem;
}
</style>
