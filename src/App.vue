<script setup lang="ts">
import { ref } from 'vue';

const WORD_LENGTH = 5;
const AVAILABLE_GUESSES = 6;

const currentGuess = ref<string[]>([]);
const guesses = ref<{ letter: string, color: string }[][]>([]);
const solution = ref('taart');
const container = ref<HTMLDivElement | null>(null);


window.addEventListener('keydown', function (e) {
  handleLetterPress(e.code);
});

function handleLetterPress(code: string) {
// Backspace
  if (currentGuess.value.length > 0 && code === 'Backspace') {
    currentGuess.value = currentGuess.value.filter((_, idx) => idx !== currentGuess.value.length - 1);
  }

  // Enter
  if (currentGuess.value.length === WORD_LENGTH && code === 'Enter') {
    guesses.value = [...guesses.value, currentGuess.value.map((letter, index) =>
      ({ color: getColor(letter, index, solution.value), letter })
    )];

    currentGuess.value = [];
  }

  if (currentGuess.value.length >= WORD_LENGTH || !code.startsWith('Key')) {
    return;
  }

  currentGuess.value = [...currentGuess.value, code[3].toUpperCase()];
}

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
  <div class="relative">
    <div
      class="mx-auto min-h-screen max-w-sm font-bold flex flex-col justify-center items-center"
      ref="container"
      @click="container?.focus()"
    >
      <div class="flex" v-for="row in AVAILABLE_GUESSES">
        <!-- Past guesses -->
        <div
          v-if="guesses.length >= row"
          class="h-12 w-12 border grid place-items-center"
          :class="guesses[row - 1][letter - 1].color"
          v-for="letter in WORD_LENGTH"
        >
          <span
            v-if="guesses[row - 1]"
            v-text="guesses[row - 1][letter - 1].letter"
          />
        </div>
        <div
          v-else
          class="h-12 w-12 border grid place-items-center"
          v-for="letter in WORD_LENGTH"
        >
          <span
            v-if="row === guesses.length + 1 && currentGuess[letter - 1]"
            v-text="currentGuess[letter - 1]"
          />
        </div>
      </div>
    </div>
    <div class="absolute bottom-0 w-full text-center max-w-lg font-bold">
      <div class="flex justify-between m-2">
        <div class="px-4 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('KeyQ')">Q</div>
        <div class="px-4 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('KeyW')">W</div>
        <div class="px-4 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('KeyE')">E</div>
        <div class="px-4 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('KeyR')">R</div>
        <div class="px-4 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('KeyT')">T</div>
        <div class="px-4 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('KeyY')">Y</div>
        <div class="px-4 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('KeyU')">U</div>
        <div class="px-4 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('Keyi')">i</div>
        <div class="px-4 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('KeyO')">O</div>
        <div class="px-4 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('KeyP')">P</div>
      </div>
      <div class="flex justify-between m-2">
        <div class="px-5 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('KeyA')">A</div>
        <div class="px-5 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('KeyS')">S</div>
        <div class="px-5 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('KeyD')">D</div>
        <div class="px-5 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('KeyF')">F</div>
        <div class="px-5 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('KeyG')">G</div>
        <div class="px-5 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('KeyH')">H</div>
        <div class="px-5 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('KeyJ')">J</div>
        <div class="px-5 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('KeyK')">K</div>
        <div class="px-5 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('KeyL')">L</div>
      </div>
      <div class="flex justify-between m-2">
        <div class="px-3 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('Enter')">ENTER</div>
        <div class="px-3 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('KeyZ')">Z</div>
        <div class="px-3 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('KeyX')">X</div>
        <div class="px-3 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('KeyC')">C</div>
        <div class="px-3 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('KeyV')">V</div>
        <div class="px-3 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('KeyB')">B</div>
        <div class="px-3 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('KeyN')">N</div>
        <div class="px-3 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('KeyM')">M</div>
        <div class="px-3 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('Keyi')">i</div>
        <div class="px-3 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('KeyO')">O</div>
        <div class="px-3 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('KeyP')">P</div>
        <div class="px-3 py-1 bg-gray-600 grid place-items-center cursor-pointer rounded-sm" @click="handleLetterPress('Backspace')">&lt;</div>
      </div>
    </div>
  </div>
</template>
