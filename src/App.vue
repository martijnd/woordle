<script setup lang="ts">
import { ref } from 'vue';

const currentGuess = ref(['B', 'F']);
const guesses = ref<{ letter: string, color: string }[][]>([]);
const solution = ref('taart');

window.addEventListener('keydown', function (e) {
  // Backspace
  if (currentGuess.value.length > 0 && e.code === 'Backspace') {
    currentGuess.value = currentGuess.value.filter((_, idx) => idx !== currentGuess.value.length - 1);
  }

  // Enter
  if (currentGuess.value.length === 5 && e.code === 'Enter') {
    guesses.value = [...guesses.value, currentGuess.value.map((letter, index) =>
      ({ color: getColor(letter, index, solution.value), letter })
    )];

    currentGuess.value = [];
  }

  if (currentGuess.value.length >= 5 || !e.code.startsWith('Key')) {
    return;
  }

  currentGuess.value = [...currentGuess.value, e.key.toUpperCase()];
});

function getColor(key: string, index: number, solution: string) {
  if (solution[index].toUpperCase() === key) {
    return 'bg-green-600';
  }

  if (solution.toUpperCase().split('').includes(key)) {
    return 'bg-orange-400';
  }

  return 'bg-gray-600';
}
</script>

<template>
  <div class="mx-auto font-bold">
    <div class="flex" v-for="row in 6">
      <!-- Past guesses -->
      <div
        v-if="guesses.length >= row"
        class="h-12 w-12 border grid place-items-center"
        :class="guesses[row - 1][letter - 1].color"
        v-for="letter in 5"
      >
        <span v-if="guesses[row - 1]">{{ guesses[row - 1][letter - 1].letter }}</span>
      </div>
      <div
        v-else
        class="h-12 w-12 border grid place-items-center"
        v-for="letter in 5"
      >
        <span
          v-if="row === guesses.length + 1 && currentGuess[letter - 1]"
        >{{ currentGuess[letter - 1] }}</span>
      </div>
    </div>
  </div>
</template>
