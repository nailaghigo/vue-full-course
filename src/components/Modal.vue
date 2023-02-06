<script setup lang="ts">
import { onUpdated, ref, watch } from 'vue';

const props = withDefaults(
  defineProps<{
    show?: boolean;
    title?: string;
    message?: string;
  }>(),
  {
    show: false,
    title: 'Title',
    message: 'Some action',
  }
);
const emit = defineEmits<{
  (e: 'close'): void;
}>();

const modal = ref<HTMLElement | null>(null);

onUpdated(() => {
  modal.value?.focus();
});
</script>
<template>
  <teleport to="#modal">
    <div
      v-if="show"
      ref="modal"
      tabindex="1"
      class="fixed z-30 inset-0 flex items-center justify-center"
      @keydown.esc="emit('close')"
    >
      <div
        @click="emit('close')"
        class="z-20 absolute bg-white opacity-30 w-full h-full"
      ></div>
      <div
        class="z-30 min-w-min bg-white flex flex-col justify-around rounded-xl min-h-[275px]"
      >
        <slot></slot>
      </div>
    </div>
  </teleport>
</template>

<style scoped>
.modalButton {
  padding: 1rem 3rem;
  color: black;
}
</style>
