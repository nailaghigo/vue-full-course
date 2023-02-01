<script setup lang="ts">
import { v4 as uuidv4 } from "uuid";
import { Switch } from "@headlessui/vue";
import { computed, onMounted, ref, watch, watchEffect } from "vue";
import axios from "axios";

interface Todos {
  id: string;
  description: string;
  priority: number;
  done: boolean;
}

const todos = ref<Todos[] | null>(null);
const shownTodos = computed(() => {
  if (todos.value) return todos.value?.filter((t) => !t.done);
  return todos.value;
});

let newTodo = ref('');
const handleAdd = () => {
  if (newTodo.value !== '') {
    todos.value?.push({
          id: uuidv4(),
          description: newTodo.value,
          priority: 3,
          done: false,
    })
    newTodo.value = '';
  }
}

onMounted(async () => {
  try {
    const { data, status } = await axios.get("http://localhost:4000/todos");
    if (status !== 200) throw new Error("Error");
    todos.value = data;
  } catch (err) {
    todos.value = [
      {
        id: uuidv4(),
        description: "Mocked data, request failed",
        priority: 3,
        done: false,
      },
      {
        id: uuidv4(),
        description: "Call mom",
        priority: 3,
        done: false,
      },
      {
        id: uuidv4(),
        description: "Get Haircut",
        priority: 1,
        done: true,
      },
      {
        id: uuidv4(),
        description: "Call mom again",
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
      <div>
        <input type="text" v-model.trim="newTodo" />
        <button @click="handleAdd">Add</button>
      </div>
    </div>
    <div>
      <ul>
        <li
          v-for="todo in todos"
        >
          <div class="flex justify-between">
            <span>
              {{ todo.description }}
            </span>
            <span>
              <button @click="todo.done = !todo.done">
                {{ todo.done ? 'Undone' : 'Done' }}
              </button>
            </span>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<style scoped>

</style>

