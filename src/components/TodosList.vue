<script setup lang="ts">
import { computed, ref, watchEffect } from 'vue';
import { Todos } from '../types';
import TodoItem from './TodoItem.vue';

const todos = ref<Todos[] | null>(null);

const props = defineProps<{
  todoList: Todos[];
}>();

const handleDelete = (id: string) => {
  if (todos.value) {
    todos.value = todos.value.filter((item) => {
      return item.id !== id;
    });
  }
};
</script>

<template>
  <div>
    <ul>
      <li v-for="todo in props.todoList">
        <TodoItem :todo="todo" @handle-delete="handleDelete" />
      </li>
    </ul>
  </div>
</template>

<style scoped />
