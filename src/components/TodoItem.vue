<script setup lang="ts">
import { computed, ref, watchEffect } from 'vue';
import { Todos } from '../types';

const props = defineProps<{
  todo: Todos;
}>();

const emit = defineEmits<{
  (e: 'handleOpenDeleteModal', todo: Todos): void;
}>();
</script>

<template>
  <div class="flex justify-between">
    <span>
      {{ todo.description }}
    </span>
    <div class="flex gap-5">
      <span>Priority: {{ todo.priority }}</span>
      <span>
        <button @click="todo.done = !todo.done">
          {{ todo.done ? 'Done' : 'Undone' }}
        </button>
      </span>
      <span>
        <button @click="emit('handleOpenDeleteModal', todo)">Remove</button>
      </span>
    </div>
  </div>
</template>

<style scoped />
