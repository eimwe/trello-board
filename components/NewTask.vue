<script setup lang="ts">
import type { Task } from '@/types';
import { nanoid } from 'nanoid';

const emit = defineEmits<{
  (event: 'add', payload: Task): void;
}>();

const focused = ref(false);
const title = ref('');

function createTask(event: Event) {
  if (title.value.trim()) {
    event.preventDefault();
    emit('add', {
      id: nanoid(),
      title: title.value.trim(),
      createdAt: new Date(),
    } as Task);
  }

  title.value = '';
}
</script>

<template>
  <div>
    <textarea
      v-model="title"
      @keydown.tab="createTask"
      @keyup.enter="createTask"
      class="focus:bg-white focus:shadow resize-none rounded w-full border-none bg-transparent"
      :class="{
        'h-7': !focused,
        'h-20': focused,
      }"
      style="outline: none !important"
      @focus="focused = true"
      @blur="focused = false"
      :placeholder="!focused ? '+ Add A Card' : 'Enter a title for this card'"
    ></textarea>
  </div>
</template>