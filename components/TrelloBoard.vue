<script setup lang="ts">
import type { Column, Task } from '~/types';
import draggable from 'vuedraggable';
import { nanoid } from 'nanoid';
const columns = useLocalStorage<Column[]>('trelloBoard', [
  {
    id: nanoid(),
    title: 'Backlog',
    tasks: [
      {
        id: nanoid(),
        title: 'Create marketing landing page',
        createdAt: new Date(),
      },
      {
        id: nanoid(),
        title: 'Develop cool new feature',
        createdAt: new Date(),
      },
      {
        id: nanoid(),
        title: 'Fix page nav bug',
        createdAt: new Date(),
      },
    ],
  },
  {
    id: nanoid(),
    title: 'Selected for Dev',
    tasks: [],
  },
  {
    id: nanoid(),
    title: 'In Progress',
    tasks: [],
  },
  {
    id: nanoid(),
    title: 'QA',
    tasks: [],
  },
  {
    id: nanoid(),
    title: 'Complete',
    tasks: [],
  },
]);
const alt = useKeyModifier("Alt");

function createColumn() {
  const column: Column = {
    id: nanoid(),
    title: '',
    tasks: [],
  }

  columns.value.push(column);
  nextTick(() => {
    (document.querySelector('.column:last-of-type .title-input') as HTMLInputElement).focus();
  });
}
</script>

<template>
  <div class="board flex gap-4 items-start overflow-x-auto">
    <draggable
      v-model="columns"
      group="columns"
      :animation="150"
      handle=".drag-handle"
      item-key="id"
      class="flex gap-4 items-start"
    >
      <template #item="{element: column}: { element: Column }">
        <div class="column bg-gray-200 p-5 rounded min-w-[250px]">
          <header class="font-bold mb-4">
            <DragHandle />
            <input
              class="title-input bg-transparent focus:bg-white rounded px-1 w-4/5"
              @keyup.enter="($event.target as HTMLInputElement).blur()"
              @keydown.backspace="
                column.title === ''
                  ? columns = columns.filter((columnToDelete) => columnToDelete.id !== column.id)
                  : null"
              type="text"
              v-model="column.title"
            />
          </header>
          <draggable
            v-model="column.tasks"
            :group="{ name: 'tasks', pull: alt ? 'clone' : true }"
            :animation="150"
            handle=".drag-handle"
            item-key="id"
          >
            <template #item="{element: task}: { element: Task }">
              <div>
                <TrelloBoardTask
                  :task="task"
                  @delete="column.tasks = column.tasks.filter((task) => task.id !== $event)"
                />
              </div>
            </template>
          </draggable>
          <footer>
            <NewTask @add="column.tasks.push($event)" />
          </footer>
        </div>
      </template>
    </draggable>
    <button
      @click="createColumn"
      class="bg-gray-200 whitespace-nowrap p-2 rounded opacity-50"
    >
      + Add Another Column
    </button>
  </div>
</template>

<style>
  .board {
    padding-bottom: 2.5rem;
    scrollbar-width: thin;
    scrollbar-color: rgba(203 213 225/0.2);
  }

  .board::-webkit-scrollbar {
    height: 0.5rem;
    border-radius: 0.25rem;
    background-color: rgba(203 213 225/0.2);
  }

  .board::-webkit-scrollbar-thumb {
    border-radius: 0.25rem;
    background-color: rgba(226 232 240/0.2);
  }
</style>