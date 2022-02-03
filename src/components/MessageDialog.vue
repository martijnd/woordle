<script setup lang="ts">
defineProps({
  message: {
    message: String,
    required: true
  }
})
</script>

<style scoped>
</style>

<template>
  <div
    class="top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 p-4 bg-black shadow absolute text-center rounded-lg space-y-2"
  >
    <h3 class="text-lg">{{ message }}</h3>
  </div>
</template>
