<script setup lang="ts">
import { Switch } from "@headlessui/vue";

const props = defineProps<{
  hidePendingTasks: boolean,
  sortByPriority: boolean
}>()

const emit = defineEmits<{
  (e: 'onHidePendingTasks'): void,
  (e: 'onSortByPriority'): void
}>()

</script>

<template>
  <div class="flex justify-between">
    <div>
      <Switch
        v-model="props.hidePendingTasks"
        :class="props.hidePendingTasks ? 'bg-blue-600' : 'bg-gray-400'"
        class="relative inline-flex h-6 w-11 items-center rounded-full"
        @click="emit('onHidePendingTasks')"
      >
        <span class="sr-only">Show Pending Tasks</span>
        <span
          :class="props.hidePendingTasks ? 'translate-x-6' : 'translate-x-1'"
          class="inline-block h-4 w-4 transform rounded-full bg-white transition"
        />
      </Switch>
      Show Only Pending Tasks
    </div>
    <div>
      Status
      <Switch
        v-model="props.sortByPriority"
        class="relative inline-flex h-6 w-11 items-center rounded-full bg-blue-600"
        @click="emit('onSortByPriority')"
      >
        <span
          :class="props.sortByPriority ? 'translate-x-6' : 'translate-x-1'"
          class="inline-block h-4 w-4 transform rounded-full bg-white transition"
        />
      </Switch>
      Priority
    </div>
  </div>
</template>

<style scoped>

</style>

