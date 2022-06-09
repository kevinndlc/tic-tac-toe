<script setup lang="ts">
import XIcon from './Icons/XIcon.vue';
import OIcon from './Icons/OIcon.vue';

defineProps<{
  mark: string;
  index: number;
  isCpuPlaying: boolean
}>()

const emit = defineEmits<{
  (e: 'updateBoard', index: number): void
}>()
</script>

<template>
  <button class="card aspect-square flex items-center justify-center" @click="emit('updateBoard', index)" :disabled="mark.length > 0 || isCpuPlaying" :class="isCpuPlaying ? 'cursor-wait' : ''">
    <XIcon v-if="mark === 'X'" class="w-10 text-primary-default" />
    <OIcon v-if="mark === 'O'" class="w-10 text-secondary-default" />
  </button>
</template>