<script setup lang="ts">
import { onMounted, ref, watch } from 'vue';
import type { Task } from '../types/Task';
import ToDoTask from './ToDoTask.vue';

const id = ref<number>(1);
const taskList = ref<Task[]>([]);

const props = defineProps<{
    title: string,
}>();

watch(()=>props.title, (title: string)=> {
    if (title != '') {
        const tasksList: Task = {
            id: id.value++,
            title: title,
            done: false,
            createdAt: new Date().toISOString().split('T')[0] ?? '',
        };
        taskList.value.push(tasksList);
        localStorage.setItem('tasks', JSON.stringify(taskList.value));
    }
});

onMounted(()=>{
    taskList.value = JSON.parse(localStorage.getItem('tasks') || '[]');
})
</script>

<template>
    <section class="flex flex-wrap gap-2.5 overflow">
        <ToDoTask v-for="task in taskList" :key="task.id" :task="task"/>
    </section>
</template>