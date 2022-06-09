<script setup lang="ts">
import XIcon from "./components/Icons/XIcon.vue";
import OIcon from "./components/Icons/OIcon.vue";
import { ref } from "vue";
import GameBoard from "./components/GameBoard.vue";

const opponent = ref('');
const mark = ref('O')
</script>

<template>
  <template v-if="!opponent">
    <img src="./assets/logo.svg" alt="Logo" class="h-8 mb-8 mx-auto">
    <div class="card-lg p-6 pb-8 text-center">
      <h2 class="uppercase font-bold tracking-wide mb-6">Pick Player 1's mark</h2>
      <div class="flex gap-2 rounded-[10px] bg-dark-navy h-[72px] mb-4 p-2">
        <input type="radio" name="mark" id="X" value="X" class="hidden" v-model="mark">
        <label for="X" class="flex-1 flex items-center justify-center rounded-[10px] cursor-pointer transition-colors duration-300">
          <XIcon class="w-8" />
        </label>
        <input type="radio" name="mark" id="O" value="O" class="hidden" v-model="mark">
        <label for="O" class="flex-1 flex items-center justify-center rounded-[10px] cursor-pointer transition-colors duration-300">
          <OIcon class="w-8" />
        </label>
      </div>
      <p class="uppercase font-medium text-sm opacity-50">Remember: X goes first</p>
    </div>
    <button class="btn-secondary w-full mt-8 p-[14px] pb-[22px]" @click="opponent = 'cpu'" >
      New game (vs CPU)
    </button>
    <button class="btn-primary w-full mt-4 p-[14px] pb-[22px]" @click="opponent = 'player'">
      New game (vs Player)
    </button>
  </template>
  <GameBoard v-else :player-one-mark="mark" :opponent="opponent"/>
</template>

<style>
  input:checked + label {
    @apply bg-silver-default text-dark-navy;
  }
</style>