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
const onlyDone = ref(false)

// const shownTodos = computed(() => {
//   if (todos.value) {
//     if (onlyDone) {
//       return todos.value?.filter((t) => t.done);
//     }

//     return todos.value?.filter((t) => !t.done);
//   }

//   return [];
// });

const newTodo = ref('');
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
            <div class="flex gap-5">
              <span>
                <button @click="todo.done = !todo.done">
                  {{ todo.done ? 'Undone' : 'Done' }}
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
    <span>
      <Switch
        v-model="onlyDone"
        :class="onlyDone ? 'bg-blue-600' : 'bg-gray-400'"
        class="relative inline-flex h-6 w-11 items-center rounded-full"
      >
        <span class="sr-only">Only Done</span>
        <span
          :class="onlyDone ? 'translate-x-6' : 'translate-x-1'"
          class="inline-block h-4 w-4 transform rounded-full bg-white transition"
        />
      </Switch>
      Only Done
    </span>
  </div>
</template>

<style scoped>

</style>

