<script setup lang="ts">
import XIcon from './Icons/XIcon.vue';
import OIcon from './Icons/OIcon.vue';
import XIconOutline from './Icons/XIconOutline.vue';
import OIconOutline from './Icons/OIconOutline.vue';

defineProps<{
  mark: string;
  index: number;
  isCpuPlaying: boolean;
  highlighted: boolean;
  currentTurn: string;
}>();

const emit = defineEmits<{
  (e: 'updateBoard', index: number): void;
}>();
</script>

<template>
  <button
    class="card aspect-square flex items-center justify-center md:card-lg"
    @click="emit('updateBoard', index)"
    :disabled="mark.length > 0 || isCpuPlaying"
    :class="[
      isCpuPlaying ? 'cursor-wait' : '',
      (highlighted && mark === 'X') ? '!bg-primary-default !text-dark-navy' : '',
      (highlighted && mark === 'O') ? 'bg-secondary-default text-dark-cyan' : '',
    ]"
  >
    <XIcon
      v-if="mark === 'X'"
      class="plain-icon w-10 md:w-16 text-primary-default"
      :class="highlighted ? '!text-dark-navy' : ''"
    />
    <XIconOutline
      v-if="mark === '' && !isCpuPlaying && currentTurn === 'X'"
      class="outline-icon w-10 md:w-16 text-primary-default"
      :class="highlighted ? '!text-dark-navy' : ''"
    />
    <OIcon
      v-if="mark === 'O'"
      class="plain-icon w-10 md:w-16 text-secondary-default"
      :class="highlighted ? '!text-dark-navy' : ''"
    />
    <OIconOutline
      v-if="mark === '' && !isCpuPlaying && currentTurn === 'O'"
      class="outline-icon w-10 md:w-16 text-secondary-default"
      :class="highlighted ? '!text-dark-navy' : ''"
    />
  </button>
</template>

<style scoped>
.outline-icon {
  display: none;
}

button:hover .outline-icon {
  display: revert;
}
</style>
