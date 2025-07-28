<script setup>
  defineProps(['subject'])
</script>

<template>
  <div>
    {{ subject }}
  </div>
</template>

<style scoped>
div {
  height: 10rem;
  margin-top: 3rem;
  padding-top: 2rem;
  font-size: 4rem;
  font-weight: bold;
  color: #42b883;
  border: #42b883 solid 0.2rem;
  border-radius: 1rem;
}
</style>
