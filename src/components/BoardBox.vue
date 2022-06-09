<script setup lang="ts">
import XIcon from './Icons/XIcon.vue';
import OIcon from './Icons/OIcon.vue';
import XIconOutline from './Icons/XIconOutline.vue';
import OIconOutline from './Icons/OIconOutline.vue';

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
  <button class="card aspect-square flex items-center justify-center md:card-lg" @click="emit('updateBoard', index)" :disabled="mark.length > 0 || isCpuPlaying" :class="isCpuPlaying ? 'cursor-wait' : ''">
    <XIcon v-if="mark === 'X'" class="plain-icon w-10 md:w-16 text-primary-default" />
    <XIconOutline v-if="mark === 'X'" class="outline-icon w-10 md:w-16 text-primary-default" />
    <OIcon v-if="mark === 'O'" class="plain-icon w-10 md:w-16 text-secondary-default" />
    <OIconOutline v-if="mark === 'O'" class="outline-icon w-10 md:w-16 text-secondary-default" />
  </button>
</template>

<style scoped>
  .outline-icon {
    display: none;
  }
  button:hover .plain-icon {
    display: none;
  }

  button:hover .outline-icon {
    display: revert;
  }
</style>