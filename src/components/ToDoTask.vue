<script setup lang="ts">
import { ref } from 'vue';
import { useEventListener } from '@vueuse/core';
import type { Task } from '../types/Task';
import Icon from './form/Icon.vue';

const isEditing = ref(false);
const props = defineProps<{
    task: Task,
}>();
const newValue = ref<string>(props.task.title);

const emit = defineEmits<{
    deleteTask: [id: number]
    editTask: [id: number, task: Task]
}>();
const deleteTask = ()=> {
    emit('deleteTask', props.task.id)
}
const editTask = ()=> {
    isEditing.value = true;
}

useEventListener('keydown', (e: KeyboardEvent) => {
    if(e.key === 'Enter') {
        emit('editTask', props.task.id, props.task)
        isEditing.value = false;
    }
    else if(e.key === 'Escape') {
        isEditing.value = false;
    }
})
</script>

<template>
    <article v-if="!isEditing" @dblclick="editTask" :class="`font-mono ${task.done ? `bg-emerald-500` : `bg-red-400`} min-w-2xs min-h-2xs py-10 px-6 rounded-xl flex flex-col gap-2 justify-center items-center shadow-2xl relative`">
        <button @click="deleteTask" class="size-6 absolute top-1 right-1 transition-transform duration-150 hover:scale-110 cursor-pointer">
            <Icon class="size-full" type="delete"/>
        </button>
        <h1 class="text-2xl">{{ task.title }}</h1>
        <p>{{ task.createdAt }}</p>
    </article>
    <article v-else :class="`font-mono ${task.done ? `bg-emerald-500` : `bg-red-400`} min-w-2xs min-h-2xs py-10 px-6 rounded-xl flex flex-col gap-2 justify-center items-center shadow-2xl relative`">
        <input v-model="newValue" class="text-2xl">
        <p>{{ task.createdAt }}</p>
    </article>
</template>9