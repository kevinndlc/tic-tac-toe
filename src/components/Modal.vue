<script setup lang="ts">
import {
  Dialog,
  DialogPanel,
  DialogTitle,
  DialogDescription,
} from '@headlessui/vue';

defineProps<{
  isOpen: boolean;
}>();
</script>

<template>
  <Dialog :open="isOpen" class="fixed inset-0 bg-[rgba(0,0,0,0.5)]">
    <DialogPanel
      class="absolute top-1/2 -translate-y-1/2 bg-semi-dark-navy w-full text-center p-[45px]"
    >
      <DialogTitle class="uppercase font-bold text-sm">
        <slot name="title"></slot>
      </DialogTitle>
      <DialogDescription class="mt-4 mb-6 text-2xl uppercase font-bold">
        <slot name="description"></slot>
      </DialogDescription>
      <slot></slot>
    </DialogPanel>
  </Dialog>
</template>
