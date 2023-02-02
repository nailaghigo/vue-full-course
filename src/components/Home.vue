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
const pendingTasks = ref(false)
const newTodoInput = ref('');
const numbers = [1, 2, 3];
const selectedPriority = ref(1);
const sortBy = ref(false)

const shownTodos = computed(() => {
  if (todos.value) {
    if (pendingTasks.value) {
      return todos.value?.filter((t) => !t.done);
    }

    return todos.value;
  }

  return [];
});

const sort = () => {
  if(sortBy.value) {
    shownTodos.value?.sort((a, b) => a.priority - b.priority)
  } else {
    shownTodos.value?.sort((a, b) => {
      return b.done === a.done
        ? 0
        : b.done
          ? -1
          : 1
    })
  }
}

watch(sortBy, () => {
  sort()
})

const undoneQty = computed(() => {
  return shownTodos.value?.filter(todo => !todo.done).length || 0
})

const handleAdd = (description: string, priority: number) => {
  if (selectedPriority.value < 1 || selectedPriority.value > 3) return;
  if (description && newTodoInput.value !== '') {
    todos.value?.push({
      id: uuidv4(),
      description,
      priority,
      done: false,
    })
    newTodoInput.value = '';
    selectedPriority.value = 1

    sort()
  }
}

const handleDelete = (id: string) => {
 if(todos.value){
  todos.value = todos.value.filter((item) => {
    return item.id !== id
  })

  sort()
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

  sort()
});


</script>

<template>
  <div class="max-w-5xl m-auto mt-4">
    <div class="flex justify-between">
      <h1>To Do List With Vue</h1>
      <div>
        You have {{ undoneQty }} pending ToDo´s
      </div>
      <div>
        <input
          type="text"
          v-model.trim="newTodoInput"
          class="text-black outline-0 rounded-md pl-2"
        />
        <div>
        <label for="priority-select">Select a priority:</label>
          <select
            v-model="selectedPriority"
            id="number-select"
            class="text-black outline-0 rounded-md pl-2"
          >
            <option v-for="number in numbers" :key="number">{{ number }}</option>
          </select>
        </div>
        <button @click="() => handleAdd(newTodoInput, selectedPriority)">Add</button>
      </div>
    </div>
    <div class="flex justify-between">
      <div>
        <Switch
          v-model="pendingTasks"
          :class="pendingTasks ? 'bg-blue-600' : 'bg-gray-400'"
          class="relative inline-flex h-6 w-11 items-center rounded-full"
        >
          <span class="sr-only">Show Pending Tasks</span>
          <span
            :class="pendingTasks ? 'translate-x-6' : 'translate-x-1'"
            class="inline-block h-4 w-4 transform rounded-full bg-white transition"
          />
        </Switch>
        Show Only Pending Tasks
      </div>
      <div>
        Status
        <Switch
          v-model="sortBy"
          class="relative inline-flex h-6 w-11 items-center rounded-full bg-blue-600"
        >
          <span
            :class="sortBy ? 'translate-x-6' : 'translate-x-1'"
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
                <button @click="() => handleDelete(todo.id)">Remove</button>
              </span>
            </div>
          </div>
        </li>
      </ul>
    </div>
    <div>
      You have {{ undoneQty }} pending ToDo´s
    </div>
  </div>
</template>

<style scoped>

</style>
