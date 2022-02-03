<script setup lang="ts">
defineProps({
  won: {
    type: Number,
    default: () => 0
  },
  lost: {
    type: Number,
    default: () => 0
  },
  solution: {
    type: String,
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
    <h3 class="font-bold text-lg">Woord</h3>
    <div>{{ solution }}</div>
    <div class="grid grid-cols-2">
      <span>Gewonnen</span>
      <span>{{ won }}</span>
      <span>Verloren</span>
      <span>{{ lost }}</span>
    </div>
    <button
      class="bg-blue-900 font-bold px-4 py-2 rounded shadow"
      @click="$emit('play-again')"
    >Opnieuw</button>
  </div>
</template>
