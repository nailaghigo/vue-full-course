<script setup lang="ts">
import { onMounted, ref } from 'vue';

const numbers = [1, 2, 3];
const newTodoInput = ref('');
const selectedPriority = ref(1);
const input = ref<HTMLElement | null>();

const emits = defineEmits<{
  (
    event: 'onSaveDataToAdd',
    { description, priority }: { description: string; priority: number }
  ): void;
}>();

const handleOnSave = (description: string, priority: number) => {
  emits('onSaveDataToAdd', { description, priority });
  newTodoInput.value = '';
  selectedPriority.value = 1;
};

onMounted(() => {
  input.value?.focus();
});
</script>

<template>
  <div class="flex justify-between p-4 max-w-2xl">
    <input
      ref="input"
      type="text"
      v-model.trim="newTodoInput"
      class="text-black outline-0 rounded-md pl-2 w-72"
    />
    <div class="flex justify-between items-center">
      <label for="priority-select" class="max-w-xs"> Select a priority: </label>
      <select
        v-model="selectedPriority"
        id="number-select"
        class="form-select appearance-none block w-25 max-w-xs px-3 py-1.5 text-base font-normal text-gray-700 bg-white bg-clip-padding bg-no-repeat border border-solid border-gray-300 rounded transition ease-in-out m-0 focus:text-gray-700 focus:bg-white focus:border-blue-600 focus:outline-none"
      >
        <option v-for="number in numbers" :key="number">{{ number }}</option>
      </select>
    </div>
    <button
      @click="handleOnSave(newTodoInput, selectedPriority)"
      class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-1 px-4 rounded"
    >
      Add
    </button>
  </div>
</template>

<style scoped></style>
