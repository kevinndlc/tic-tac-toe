<script setup lang="ts">
import {
  Dialog,
  DialogPanel,
  DialogTitle,
  DialogDescription,
} from '@headlessui/vue';

defineProps<{
  isOpen: boolean;
  title: string;
}>();
</script>

<template>
  <Dialog :open="isOpen" class="fixed inset-0 bg-[rgba(0,0,0,0.5)]">
    <DialogPanel
      class="absolute top-1/2 -translate-y-1/2 bg-semi-dark-navy w-full text-center h-[228px] flex flex-col justify-center gap-4 md:h-[380px]"
    >
      <DialogTitle v-if="title" class="uppercase font-bold text-sm md:text-base">
        {{ title }}
      </DialogTitle>
      <DialogDescription class="text-2xl md:text-[40px] uppercase font-bold">
        <slot name="description"></slot>
      </DialogDescription>
      <div class="mt-2">
        <slot></slot>
      </div>
    </DialogPanel>
  </Dialog>
</template>
