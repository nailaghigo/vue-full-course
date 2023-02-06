<script setup lang="ts">
import { v4 as uuidv4 } from 'uuid';
import { computed, onMounted, ref, watchEffect } from 'vue';
import axios from 'axios';
import Filters from './Filters.vue';
import AddForm from './AddForm.vue';
import TodosList from './TodosList.vue';
import { Todos } from '../types';

const todos = ref<Todos[] | null>(null);
const hidePendingTasks = ref(false);
const sortByPriority = ref(false);

const shownTodos = computed(() => {
  if (hidePendingTasks.value) return todos.value?.filter((t) => !t.done);
  return todos.value ?? [];
});

watchEffect(() => {
  shownTodos.value?.sort((a, b) =>
    sortByPriority.value ? a.priority - b.priority : a.done ? 1 : -1
  );
});

const undoneQty = computed(() => {
  return shownTodos.value?.filter((todo) => !todo.done).length || 0;
});

const onSaveDataToAdd = ({
  description,
  priority,
}: {
  description: string;
  priority: number;
}) => {
  if (description !== '') {
    todos.value?.push({
      id: uuidv4(),
      description,
      priority,
      done: false,
    });
  }
};

onMounted(async () => {
  try {
    const { data, status } = await axios.get('http://localhost:4000/todos');
    if (status !== 200) throw new Error('Error');
    todos.value = data;
  } catch (err) {
    todos.value = [
      {
        id: uuidv4(),
        description: 'Mocked data, request failed',
        priority: 3,
        done: false,
      },
      {
        id: uuidv4(),
        description: 'Call mom',
        priority: 3,
        done: false,
      },
      {
        id: uuidv4(),
        description: 'Get Haircut',
        priority: 1,
        done: true,
      },
      {
        id: uuidv4(),
        description: 'Call mom again',
        priority: 3,
        done: false,
      },
    ];
    console.error(err);
  }
});
</script>

<template>
  <div class="max-w-5xl m-auto mt-4">
    <div class="w-full flex justify-center">
      <h1 class="text-3xl font-bold">To Do List With Vue</h1>
    </div>
    <div>You have {{ undoneQty }} pending ToDo´s</div>
    <Filters
      :hide-pending-tasks="hidePendingTasks"
      :sort-by-priority="sortByPriority"
      @on-hide-pending-tasks="hidePendingTasks = !hidePendingTasks"
      @on-sort-by-priority="sortByPriority = !sortByPriority"
    />

    <AddForm @onSaveDataToAdd="onSaveDataToAdd($event)" />

    <TodosList :todo-list="shownTodos || []" />
  </div>
</template>

<style scoped></style>
