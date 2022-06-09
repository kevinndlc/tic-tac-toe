<script setup lang="ts">
import { computed, ref } from 'vue';
import XIcon from './Icons/XIcon.vue';
import OIcon from './Icons/OIcon.vue';
import RestartIcon from './Icons/RestartIcon.vue';
import BoardBox from '@/components/BoardBox.vue';
import Modal from './Modal.vue';

const props = defineProps<{
  playerOneMark: string;
  opponent: string;
}>();

const emit = defineEmits<{
  (e: 'back'): void;
}>();

const CPU_TIME_TO_PLAY_IN_MS = 600;
const WINNING_ROWS = [
  [0, 1, 2],
  [3, 4, 5],
  [6, 7, 8],
  [0, 3, 6],
  [1, 4, 7],
  [2, 5, 8],
  [0, 4, 8],
  [2, 4, 6],
];

const board = ref(['', '', '', '', '', '', '', '', '']);
const turn = ref('X');
const nbWinsX = ref(0);
const nbWinsO = ref(0);
const nbTies = ref(0);
const hasWon = ref(false);
const hasTied = ref(false);
const wannaRestart = ref(false);
const isCpuPlaying = ref(false);
const isModalOpen = ref(false);

(async () => {
  if (props.opponent === 'cpu' && props.playerOneMark !== turn.value) {
    await cpuTurn();
  }
})();

async function updateBoard(index: number) {
  if (!isCpuPlaying.value) {
    board.value[index] = turn.value;
    checkIfWin();
    if (!hasWon.value && !hasTied.value) {
      checkIfRemainingBoxes();
      changeTurn();
      await cpuTurn();
    }
  }
}

function changeTurn() {
  if (turn.value === 'X') {
    turn.value = 'O';
  } else {
    turn.value = 'X';
  }
}

async function cpuTurn() {
  if (props.playerOneMark !== turn.value && props.opponent === 'cpu') {
    isCpuPlaying.value = true;

    await new Promise((res) => {
      setTimeout(() => {
        res(true);
      }, CPU_TIME_TO_PLAY_IN_MS);
    });

    const choices = [];

    for (const ROW of WINNING_ROWS) {
      if (
        (board.value[ROW[0]] === '' && board.value[ROW[1]] === '') ||
        (board.value[ROW[1]] === '' && board.value[ROW[2]] === '') ||
        (board.value[ROW[0]] === '' && board.value[ROW[2]] === '')
      ) {
        continue;
      } else if (
        board.value[ROW[0]] === board.value[ROW[1]] &&
        board.value[ROW[2]] === ''
      ) {
        choices.push({ index: ROW[2], player: board.value[ROW[0]]});
      } else if (
        board.value[ROW[0]] === board.value[ROW[2]] &&
        board.value[ROW[1]] === ''
      ) {
        choices.push({index: ROW[1], player: board.value[ROW[0]]});
      } else if (
        board.value[ROW[1]] === board.value[ROW[2]] &&
        board.value[ROW[0]] === ''
      ) {
        choices.push({ index: ROW[0], player: board.value[ROW[1]]});
      }
    }

    isCpuPlaying.value = false;

    if (choices.length === 0) {
      const remainingBoxesIndex = board.value.reduce((acc, curr, index) => {
        if (curr === '') {
          return [...acc, index];
        }
        return acc;
      }, [] as number[]);
      const randomIndex = Math.floor(Math.random() * remainingBoxesIndex.length);
      updateBoard(remainingBoxesIndex[randomIndex]);
    } else if (choices.length === 1) {
      updateBoard(choices[0].index);
    } else {
      const winMoves = choices.filter(choice => choice.player !== props.playerOneMark);

      if (winMoves.length > 0) {
        updateBoard(winMoves[0].index);
      } else {
        updateBoard(choices[0].index);
      }
    }
  }
}

function checkIfWin() {
  for (const ROW of WINNING_ROWS) {
    if (
      board.value[ROW[0]] === '' ||
      board.value[ROW[1]] === '' ||
      board.value[ROW[2]] === ''
    ) {
      continue;
    } else if (
      board.value[ROW[0]] === board.value[ROW[1]] &&
      board.value[ROW[1]] === board.value[ROW[2]]
    ) {
      isModalOpen.value = true;
      if (turn.value === 'X') {
        nbWinsX.value++;
      } else {
        nbWinsO.value++;
      }
      hasWon.value = true;
    }
  }
}

function checkIfRemainingBoxes() {
  if (board.value.findIndex((box) => box === '') === -1) {
    isModalOpen.value = true;
    hasTied.value = true;
    nbTies.value++;
  }
}

async function nextRound() {
  isModalOpen.value = false;
  hasWon.value = false;
  hasTied.value = false;
  if (wannaRestart.value) {
    nbWinsX.value = 0;
    nbWinsO.value = 0;
    nbTies.value = 0;
    wannaRestart.value = false;
  }
  await resetBoard();
}

async function resetBoard() {
  board.value = board.value.map((box) => (box = ''));
  turn.value = 'X';
  await cpuTurn();
}

function handleRestart() {
  isModalOpen.value = true;
  wannaRestart.value = true;
}

function backHome() {
  if (!wannaRestart.value) {
    emit('back');
  } else {
    isModalOpen.value = false;
    wannaRestart.value = false;
  }
}

const modalTitle = computed(() => {
  if (hasWon.value) {
    if (props.opponent === 'cpu') {
      if (props.playerOneMark === turn.value) {
        return 'You won';
      }
      return 'Oh no, you lost...';
    } else {
      if (props.playerOneMark === turn.value) {
        return 'Player 1 wins!';
      }
      return 'Player 2 wins!';
    }
  }
  return '';
});

const whoIsX = computed(() => {
  if (props.opponent === 'cpu') {
    if (props.playerOneMark === 'X') {
      return 'You';
    }
    return 'CPU';
  } else {
    if (props.playerOneMark === 'X') {
      return 'Player 1';
    }
    return 'Player 2';
  }
});

const whoIsO = computed(() => {
  if (props.opponent === 'cpu') {
    if (props.playerOneMark === 'O') {
      return 'You';
    }
    return 'CPU';
  } else {
    if (props.playerOneMark === 'O') {
      return 'Player 1';
    }
    return 'Player 2';
  }
});
</script>

<template>
  <div>
    <Modal :isOpen="isModalOpen">
      <template #title>{{ modalTitle }}</template>
      <template #description>
        <div
          v-if="hasWon"
          class="flex gap-[9px] items-center justify-center"
          :class="
            turn === 'X' ? 'text-primary-default' : 'text-secondary-default'
          "
        >
          <XIcon v-if="turn === 'X'" class="w-7" /><OIcon
            v-else
            class="w-7"
          /><span>takes the round</span>
        </div>
        <div v-else-if="hasTied">Round Tied</div>
        <div v-else>Restart Game?</div>
      </template>
      <button class="btn-sm px-4 pt-[15px] pb-[17px] mr-4" @click="backHome">
        {{ wannaRestart ? 'No, cancel' : 'Quit' }}
      </button>
      <button
        class="btn-sm-secondary px-4 pt-[15px] pb-[17px]"
        @click="nextRound"
      >
        {{ wannaRestart ? 'Yes, restart' : 'Next round' }}
      </button>
    </Modal>
    <header class="relative grid grid-cols-3 items-center gap-5 mb-16">
      <img
        src="@/assets/logo.svg"
        alt="Logo"
        class="cursor-pointer"
        @click="emit('back')"
      />
      <div
        class="card-sm p-[9px] pb-[13px] flex items-center justify-center gap-[9px] uppercase font-bold"
      >
        <XIcon v-if="turn === 'X'" class="w-4" />
        <OIcon v-else class="w-4" />
        Turn
      </div>
      <button
        class="btn-sm w-10 aspect-square self-start ml-auto"
        @click="handleRestart"
      >
        <RestartIcon class="w-4 mx-auto" />
      </button>
      <p
        v-if="isCpuPlaying"
        class="absolute font-semibold left-1/2 -bottom-7 -translate-x-1/2 translate-y-1/2"
        :class="
          playerOneMark === 'X'
            ? 'text-secondary-default'
            : 'text-primary-default'
        "
      >
        CPU is currenly playing...
      </p>
    </header>
    <main class="grid grid-cols-3 gap-5">
      <BoardBox
        v-for="(box, index) in board"
        :mark="box"
        :index="index"
        :is-cpu-playing="isCpuPlaying"
        @update-board="updateBoard"
      />
    </main>
    <footer class="grid grid-cols-3 gap-5 mt-5">
      <div
        class="text-center bg-primary-default text-dark-navy rounded-[10px] p-3"
      >
        <h2 class="uppercase text-sm font-medium">X ({{ whoIsX }})</h2>
        <span class="font-bold text-xl">{{ nbWinsX }}</span>
      </div>
      <div
        class="text-center bg-silver-default text-dark-navy rounded-[10px] p-3"
      >
        <h2 class="uppercase text-sm font-medium">Ties</h2>
        <span class="font-bold text-xl">{{ nbTies }}</span>
      </div>
      <div
        class="text-center bg-secondary-default text-dark-navy rounded-[10px] p-3"
      >
        <h2 class="uppercase text-sm font-medium">O ({{ whoIsO }})</h2>
        <span class="font-bold text-xl">{{ nbWinsO }}</span>
      </div>
    </footer>
  </div>
</template>
