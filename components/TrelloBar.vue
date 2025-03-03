<script setup lang="ts">
import { ref } from 'vue'
import type { Column, Task } from '~/types'
import { nanoid } from 'nanoid';
import draggable from "vuedraggable"


const columns = ref<Column[]>([
    {
        id: nanoid(),
        title: "Backlog",
        tasks: [
            {
                id: nanoid(), 
                title: "Create marketing landing page", 
                createdAt: new Date(),
            },
            {
                id: nanoid(), 
                title: "Add new section", 
                createdAt: new Date(),
            },
            {
                id: nanoid(), 
                title: "Create new page", 
                createdAt: new Date(),
            },
        ],
    },
    { title: "Create marketing landing page", id: nanoid(),tasks: [] },
    { title: "Create new page", id: nanoid(),tasks: [] },
    { title: "To Do", id: nanoid(),tasks: [] },
    { title: "Add new", id: nanoid(),tasks: [] },
]);
const alt = useKeyModifier("Alt")

function createColumn(){
    const column: Column = {
        id: nanoid(),
        title: "",
        tasks:[]
    }
    columns.value.push(column);
    document.querySelector(".column:last-of-type .title-input");
}
</script>

<template>
    <div class="flex items-start overflow-x-auto gap-4">
        <draggable
            v-model="columns"
            group="columns"
            :animation="150"
            handle=".drag-handle"
            item-key="id"
            class="flex gap-4 items-start"
        >
        <template #item="{element: column}: {element: Column}">
            <div class="column bg-gray-200 p-5 rounded min-w-[250px]">
                <header class="font-bold mb-4">
                    <DragHandle />
                    <input
                        class="bg-transparent focus:bg-white rounded px-1 w-4/5" 
                        @keyup.enter=" ($event. target as HTMLInputElement).blur()" 
                        type="text" 
                        v-model="column.title"
                    />
                </header>
                <draggable
                    v-model="column.tasks"
                    :group="{name: 'tasks', pull: alt ? 'clone' : true}"
                    handle=".drag-handle"
                    :animation="150"
                    item-key="id"
                >
                <template #item="{element: task}: {element: Task}">
                    <div>
                        <TrelloBoardTask :task="task" @delete="column.tasks = column.tasks.filter((t) => t.id !== $event)"/>
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
            + Add Column
        </button>
    </div>
</template>