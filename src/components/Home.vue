<script setup lang="ts">
import { v4 as uuidv4 } from 'uuid';
import { computed, onMounted, ref, watchEffect } from 'vue';
import axios from 'axios';
import Filters from './Filters.vue';
import AddForm from './AddForm.vue';

interface Todos {
  id: string;
  description: string;
  priority: number;
  done: boolean;
}

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
  newTodoInput,
  selectedPriority,
}: {
  newTodoInput: string;
  selectedPriority: number;
}) => {
  if (newTodoInput !== '') {
    todos.value?.push({
      id: uuidv4(),
      description: newTodoInput,
      priority: selectedPriority,
      done: false,
    });
  }
};

const handleDelete = (id: string) => {
  if (todos.value) {
    todos.value = todos.value.filter((item) => {
      return item.id !== id;
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
    <div class="flex justify-between">
      <h1>To Do List With Vue</h1>
      <div>You have {{ undoneQty }} pending ToDo´s</div>
    </div>
    <Filters
      :hide-pending-tasks="hidePendingTasks"
      :sort-by-priority="sortByPriority"
      @on-hide-pending-tasks="hidePendingTasks = !hidePendingTasks"
      @on-sort-by-priority="sortByPriority = !sortByPriority"
    />

    <AddForm @onSaveDataToAdd="onSaveDataToAdd($event)" />

    <div>
      <ul>
        <li v-for="todo in shownTodos">
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
                <button @click="handleDelete(todo.id)">Remove</button>
              </span>
            </div>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<style scoped></style>
