<script setup lang="ts">
import { computed, ref, watchEffect } from 'vue';
import { Todos } from '../types';
import Modal from './Modal.vue';
import TodoItem from './TodoItem.vue';

const props = defineProps<{
  todoList: Todos[];
}>();

const emit = defineEmits<{
  (e: 'onDeleteTodo', id: string): void;
}>();

const showModal = ref(false);
const selectedTodo = ref<Todos>();

const handleOpenModal = (todo: Todos) => {
  showModal.value = true;
  selectedTodo.value = todo;
};

const handleDelete = () => {
  if (selectedTodo.value) {
    emit('onDeleteTodo', selectedTodo.value.id);
  }
  showModal.value = false;
};
</script>

<template>
  <Modal @close="showModal = false" :show="showModal">
    <div>
      <div class="p-8 text-black">
        <h3 class="mb-8 text-2xl font-bold text-center">
          Are you sure you want to delete this item?
        </h3>
        <p class="font-bold text-center">{{ selectedTodo?.description }}</p>
      </div>
      <div class="flex justify-center p-4 gap-8">
        <button
          class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-1 px-4 rounded"
          @click="showModal = false"
        >
          Close
        </button>
        <button
          v-if="selectedTodo"
          class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-1 px-4 rounded"
          @click="handleDelete"
        >
          Delete
        </button>
      </div>
    </div>
  </Modal>

  <div>
    <ul>
      <li v-for="todo in props.todoList">
        <TodoItem :todo="todo" @handle-open-delete-modal="handleOpenModal" />
      </li>
    </ul>
  </div>
</template>

<style scoped />
