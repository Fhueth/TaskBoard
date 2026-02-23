<script setup lang="ts">
import { reactive, ref, watchEffect } from 'vue';
import { useEventListener } from '@vueuse/core';
import type { Task } from '../types/Task';
import Icon from './form/Icon.vue';

const emit = defineEmits<{
    deleteTask: [id: number]
    editTask: [id: number, task: Task]
}>();
const deleteTask = ()=> {
    emit('deleteTask', props.task.id)
}
const props = defineProps<{
    task: Task,
}>();
const isEditing = ref(false);
const editableTask = reactive<Task>(props.task);

useEventListener('keydown', (e: KeyboardEvent) => {
    if(e.key === 'Enter') {
        emit('editTask', props.task.id, editableTask)
        isEditing.value = false;
    }
    else if(e.key === 'Escape') {
        isEditing.value = false;
    }
})
watchEffect(()=> {
    emit('editTask', props.task.id, editableTask)
})
</script>

<template>
    <ul class="font-mono grid grid-cols-[auto_1fr_auto] items-center w-full gap-5 rounded-3xl px-5 py-2">
        <div class="relative size-5">
            <input v-model="editableTask.done" type="checkbox" name="done" id="done" class="top-2 left-2 absolute opacity-0 size-0 peer">
            <label for="done" class="cursor-pointer size-full border border-gray-400 after:absolute absolute rounded-full after:w-1/4 after:h-1/2 after:border-b-2 after:border-r-2 after:left-1/2 after:border-white after:-translate-x-1/2 after:-translate-y-1/4 after:top-1/4 after:rotate-45 peer-checked:border-transparent peer-checked:bg-emerald-700 peer-checked:after:visible after:invisible"></label>
        </div>
        <h1 v-if="!isEditing" @dblclick="isEditing = true" :class="`text-2xl ${editableTask.done ? 'line-through decoration-1 decoration-gray-800 text-black/60' : ''}`">{{ task.title }}</h1>
        <input v-else type="text" name="title" id="title" v-model="editableTask.title" class="text-2xl">
        <button @click="deleteTask" class="size-6 top-1 right-1 transition-transform duration-150 hover:scale-110 cursor-pointer">
            <Icon class="size-full" color="black" type="delete"/>
        </button>
    </ul>
</template>