<script setup lang="ts">
import type { Task, ID } from '@/types'
const props = defineProps<{
  task: Task;
}>();

const emit = defineEmits<{
  (event: 'delete', payload: ID): void;
}>();

const focused = ref(false);
onKeyStroke('Backspace', (event) => {
  if (focused.value) emit('delete', props.task.id)
})
</script>

<template>
  <div
    :title="task.createdAt.toLocaleDateString()"
    class="task bg-white p-2 mb-2 rounded shadow-sm max-w-[250px] flex"
    @focus="focused = true"
    @blur="focused = false"
    tabindex="0"
  >
    <DragHandle class="pr-2" />
    <span>
      {{ task.title }}
    </span>
  </div>
</template>

<style>
  .sortable-drag .task {
    transform: rotate(5deg);
  }

  .sortable-ghost .task {
    position: relative;
  }

  .sortable-ghost .task::after {
    content: '';
    /*@apply absolute top-0 right-0 bottom-0 left-0 bg-slate-300 rounded*/
    position: absolute;
    inset: 0;
    border-radius: 0.25rem;
    background-color: rgb(203 213 225);
  }

  .task:focus,
  .task:focus-visible {
    /*@apply outline-gray-400 !important*/
    outline: gray auto 1px;
  }
</style>