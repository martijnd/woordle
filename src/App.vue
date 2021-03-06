<script setup lang="ts">
import { ref } from 'vue';
import words from './words';
import solutions from './solutions';
import SolutionDialog from './components/SolutionDialog.vue';
import MessageDialog from './components/MessageDialog.vue';

const WORD_LENGTH = 5;
const AVAILABLE_GUESSES = 6;

const currentGuess = ref<string[]>([]);
const guesses = ref<{ letter: string, color: string }[][]>([]);
const solution = ref(shuffleArray(solutions)[0].toUpperCase());
const container = ref<HTMLDivElement | null>(null);
const foundLetters = ref<string[]>([]);
const correctLetters = ref<string[]>([]);
const noneLetters = ref<string[]>([]);
const showSolutionDialog = ref(false);
const result = ref<string | null>(null);
function shuffleArray(array: string[]) {
  for (let i = array.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    const temp = array[i];
    array[i] = array[j];
    array[j] = temp;
  }

  return array;
}


window.addEventListener('keydown', function (e) {
  handleKeyPress(e.code);
});

const dialogMessage = ref<string | null>(null);

function showMessageDialog(message: string) {
  dialogMessage.value = message;
  setTimeout(() => {
    dialogMessage.value = null;
  }, 1000)
}

function handleKeyPress(code: string) {

  // Backspace
  if (currentGuess.value.length > 0 && code === 'Backspace') {
    currentGuess.value = currentGuess.value.filter((_, idx) => idx !== currentGuess.value.length - 1);
  }

  // Enter
  if (currentGuess.value.length === WORD_LENGTH && code === 'Enter') {
    // Check if it is a valid word
    if (!words.includes(currentGuess.value.join('').toLowerCase())) {
      showMessageDialog('Dat is geen woord...');
      return;
    }

    // Add to guesses list
    guesses.value = [...guesses.value, currentGuess.value.map((letter, index) =>
      ({ color: getColor(letter, index, solution.value), letter })
    )];

    // Won
    if (currentGuess.value.join('') === solution.value) {
      result.value = 'won';
      localStorage.won = localStorage.won ? parseInt(localStorage.won) + 1 : 1;
      won.value = localStorage.won || 0;
      showSolutionDialog.value = true;
    }

    currentGuess.value = [];
    // Lost
    if (guesses.value.length === AVAILABLE_GUESSES) {
      result.value = 'lost';
      localStorage.lost = localStorage.lost ? parseInt(localStorage.lost) + 1 : 1;
      lost.value = localStorage.lost || 0;
      showSolutionDialog.value = true;
      return;
    }
  }

  if (currentGuess.value.length >= WORD_LENGTH || !code.startsWith('Key')) {
    return;
  }

  // Letter press
  currentGuess.value = [...currentGuess.value, code[3].toUpperCase()];
}

function getColor(key: string, index: number, solution: string) {
  if (solution[index].toUpperCase() === key) {
    correctLetters.value = [...correctLetters.value, key];
    return 'bg-green-600';
  }

  if (solution.toUpperCase().split('').includes(key)) {
    foundLetters.value = [...foundLetters.value, key];
    return 'bg-orange-400';
  }

  noneLetters.value = [...noneLetters.value, key];
  return 'bg-gray-600';
}

const keyRows = ref([
  ['Q', 'W', 'E', 'R', 'T', 'Y', 'U', 'I', 'O', 'P',],
  ['A', 'S', 'D', 'F', 'G', 'H', 'J', 'K', 'L'],
  ['Enter', 'Z', 'X', 'C', 'V', 'B', 'N', 'M', 'Backspace']]);

function getKeyBackgroundClass(key: string) {
  if (correctLetters.value.includes(key)) {
    return 'bg-green-600';
  }

  if (foundLetters.value.includes(key)) {
    return 'bg-orange-400';
  }

  if (noneLetters.value.includes(key)) {
    return 'bg-gray-800';
  }

  return 'bg-gray-600';
}

function onClickPlayAgain() {
  currentGuess.value = [];
  guesses.value = [];
  showSolutionDialog.value = false;
  foundLetters.value = [];
  correctLetters.value = [];
  noneLetters.value = [];
  result.value = null;

  solution.value = shuffleArray(solutions)[0].toUpperCase();
}

const won = ref(localStorage.won || 0);
const lost = ref(localStorage.lost || 0);
</script>

<template>
  <SolutionDialog
    v-if="showSolutionDialog"
    @play-again="onClickPlayAgain"
    :won="parseInt(won)"
    :lost="parseInt(lost)"
    :solution="solution"
  />
  <!-- Message dialog -->
  
  <MessageDialog
    v-if="dialogMessage"
  :message="dialogMessage" />
  <div class="flex flex-col justify-between items-center min-h-screen">
    <header class="text-center mt-4">
      <h1 class="text-slate-300 font-bold text-xl">Woordle</h1>
      <h2 class="text-slate-500">Gemaakt door Martijn Dorsman</h2>
    </header>
    <div
      class="max-w-sm font-bold flex flex-grow flex-col gap-2 justify-center items-center"
      ref="container"
      @click="container?.focus()"
    >
      <div class="flex" v-for="row in AVAILABLE_GUESSES">
        <!-- Past guesses -->
        <div
          v-if="guesses.length >= row"
          class="h-12 w-12 border border-gray-600 grid place-items-center mx-1"
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
          class="h-12 w-12 border border-gray-600 grid place-items-center mx-1"
          v-for="letter in WORD_LENGTH"
        >
          <span
            v-if="row === guesses.length + 1 && currentGuess[letter - 1]"
            v-text="currentGuess[letter - 1]"
          />
        </div>
      </div>
    </div>
    <div class="w-full text-center max-w-lg font-bold text-xs">
      <div class="flex justify-center gap-1 m-1" v-for="row of keyRows">
        <div
          v-for="key of row"
          class="px-[11px] py-3 grid place-items-center cursor-pointer rounded-sm"
          :class="getKeyBackgroundClass(key)"
          @click="handleKeyPress(['Backspace', 'Enter'].includes(key) ? key : `Key${key}`)"
        >{{ key === 'Backspace' ? '&#9003;' : key }}</div>
      </div>
    </div>
  </div>
</template>
