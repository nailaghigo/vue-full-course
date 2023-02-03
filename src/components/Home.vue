<script setup lang="ts">
import { v4 as uuidv4 } from "uuid";
import { Switch } from "@headlessui/vue";
import { computed, onMounted, ref, watchEffect } from "vue";
import axios from "axios";

interface Todos {
  id: string;
  description: string;
  priority: number;
  done: boolean;
}

const todos = ref<Todos[] | null>(null);
const hidePendingTasks = ref(false)
const newTodoInput = ref('');
const numbers = [1, 2, 3];
const selectedPriority = ref(1);
const sortByPriority = ref(false)

const shownTodos = computed(() => {
    if (hidePendingTasks.value) return todos.value?.filter((t) => !t.done);
    return todos.value ?? [];
  }
);

watchEffect(() => {
  shownTodos.value?.sort((a, b) =>
    sortByPriority.value
      ? a.priority - b.priority
      : a.done ? 1 : -1
  )
})

const undoneQty = computed(() => {
  return shownTodos.value?.filter(todo => !todo.done).length || 0
})

const handleAdd = (description: string, priority: number) => {
  if (newTodoInput.value !== '') {
    todos.value?.push({
      id: uuidv4(),
      description,
      priority,
      done: false,
    })
    newTodoInput.value = '';
    selectedPriority.value = 1
  }
}

const handleDelete = (id: string) => {
 if(todos.value){
  todos.value = todos.value.filter((item) => {
    return item.id !== id
  })
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
        You have {{ undoneQty }} pending ToDo´s
      </div>
    </div>
    <div class="flex justify-between p-4 max-w-2xl">
      <input
        type="text"
        v-model.trim="newTodoInput"
        class="text-black outline-0 rounded-md pl-2 w-72"
      />
      <div class="flex justify-between items-center">
        <label
          for="priority-select"
          class="max-w-xs"
        >
          Select a priority:
        </label>
        <select
          v-model="selectedPriority"
          id="number-select"
          class="form-select appearance-none block w-25 max-w-xs px-3 py-1.5 text-base font-normal text-gray-700 bg-white bg-clip-padding bg-no-repeat border border-solid border-gray-300 rounded transition ease-in-out m-0 focus:text-gray-700 focus:bg-white focus:border-blue-600 focus:outline-none"
        >
          <option v-for="number in numbers" :key="number">{{ number }}</option>
        </select>
      </div>
      <button
        @click="handleAdd(newTodoInput, selectedPriority)"
        class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-1 px-4 rounded"
      >
        Add
      </button>
    </div>
    <div class="flex justify-between">
      <div>
        <Switch
          v-model="hidePendingTasks"
          :class="hidePendingTasks ? 'bg-blue-600' : 'bg-gray-400'"
          class="relative inline-flex h-6 w-11 items-center rounded-full"
        >
          <span class="sr-only">Show Pending Tasks</span>
          <span
            :class="hidePendingTasks ? 'translate-x-6' : 'translate-x-1'"
            class="inline-block h-4 w-4 transform rounded-full bg-white transition"
          />
        </Switch>
        Show Only Pending Tasks
      </div>
      <div>
        Status
        <Switch
          v-model="sortByPriority"
          class="relative inline-flex h-6 w-11 items-center rounded-full bg-blue-600"
        >
          <span
            :class="sortByPriority ? 'translate-x-6' : 'translate-x-1'"
            class="inline-block h-4 w-4 transform rounded-full bg-white transition"
          />
        </Switch>
        Priority
      </div>
    </div>
    <div>
      <ul>
        <li
          v-for="todo in shownTodos"
        >
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

<style scoped>

</style>

