<script setup lang="ts">
import { computed, ref, type Ref } from 'vue';
import XIcon from './Icons/XIcon.vue';
import OIcon from './Icons/OIcon.vue';
import RestartIcon from './Icons/RestartIcon.vue';
import BoardBox from '@/components/BoardBox.vue';
import Modal from './Modal.vue';

const props = defineProps<{
  playerOneMark: string;
  opponent: string;
}>();

const board = ref(['', '', '', '', '', '', '', '', '']);
const turn = ref('X');
const isModalOpen = ref(false);

function updateBoard(index: number) {
  board.value[index] = turn.value;
  checkIfWin();
  changeTurn();
}

function changeTurn() {
  if (turn.value === 'X') {
    turn.value = 'O';
  } else {
    turn.value = 'X';
  }
}

function checkIfWin() {
  if (
    (board.value[0] !== '' && board.value[1] !== '' && board.value[2] !== '' && board.value[0] === board.value[1] && board.value[1] === board.value[2]) ||
    (board.value[3] !== '' && board.value[4] !== '' && board.value[5] !== '' && board.value[3] === board.value[4] && board.value[4] === board.value[5]) ||
    (board.value[6] !== '' && board.value[7] !== '' && board.value[8] !== '' && board.value[6] === board.value[7] && board.value[7] === board.value[8]) ||
    (board.value[0] !== '' && board.value[3] !== '' && board.value[6] !== '' && board.value[0] === board.value[3] && board.value[3] === board.value[6]) ||
    (board.value[1] !== '' && board.value[4] !== '' && board.value[7] !== '' && board.value[1] === board.value[4] && board.value[4] === board.value[7]) ||
    (board.value[2] !== '' && board.value[5] !== '' && board.value[8] !== '' && board.value[2] === board.value[5] && board.value[5] === board.value[8]) ||
    (board.value[0] !== '' && board.value[4] !== '' && board.value[8] !== '' && board.value[0] === board.value[4] && board.value[4] === board.value[8]) ||
    (board.value[2] !== '' && board.value[4] !== '' && board.value[6] !== '' && board.value[2] === board.value[4] && board.value[4] === board.value[6])
  ) {
    isModalOpen.value = true;
  }
}

function resetBoard() {
  board.value = board.value.map((box) => (box = ''));
  turn.value = 'X';
}

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
    <Modal :isOpen="isModalOpen" cancelButtonText="No, cancel" continueButtonText = "Yes, restart">
      <template #title>
        You won!
      </template>
      <template #description>
        <XIcon class="inline w-7"/> takes the round
      </template>
      <button class="btn-sm px-4 pt-[15px] pb-[17px] mr-4" @click="isModalOpen = false">Quit</button>
      <button class="btn-sm-secondary px-4 pt-[15px] pb-[17px]" @click="isModalOpen = false">Next round</button>
    </Modal>
    <header class="grid grid-cols-3 items-center gap-5 mb-16">
      <img src="@/assets/logo.svg" alt="Logo" />
      <div
        class="card-sm p-[9px] pb-[13px] flex items-center justify-center gap-[9px] uppercase font-bold"
      >
        <XIcon v-if="turn === 'X'" class="w-4" />
        <OIcon v-else class="w-4" />
        Turn
      </div>
      <button
        class="btn-sm w-10 aspect-square self-start ml-auto"
        @click="resetBoard"
      >
        <RestartIcon class="w-4 mx-auto" />
      </button>
    </header>
    <main class="grid grid-cols-3 gap-5">
      <BoardBox
        v-for="(box, index) in board"
        :mark="box"
        :index="index"
        @update-board="updateBoard"
      />
    </main>
    <footer class="grid grid-cols-3 gap-5 mt-5">
      <div
        class="text-center bg-primary-default text-dark-navy rounded-[10px] p-3"
      >
        <h2 class="uppercase text-sm font-medium">X ({{ whoIsX }})</h2>
        <span class="font-bold text-xl">14</span>
      </div>
      <div
        class="text-center bg-silver-default text-dark-navy rounded-[10px] p-3"
      >
        <h2 class="uppercase text-sm font-medium">Ties</h2>
        <span class="font-bold text-xl">32</span>
      </div>
      <div
        class="text-center bg-secondary-default text-dark-navy rounded-[10px] p-3"
      >
        <h2 class="uppercase text-sm font-medium">O ({{ whoIsO }})</h2>
        <span class="font-bold text-xl">11</span>
      </div>
    </footer>
  </div>
</template>
