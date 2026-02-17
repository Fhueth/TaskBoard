<script setup lang="ts">
import { ref, watch } from 'vue';
import type { Task } from '../types/Task';
import ToDoTask from './ToDoTask.vue';

const id = ref<number>(1);
const taskList = ref<Task[]>([]);

const props = defineProps<{
    title: string,
}>();

watch(()=>props.title, (title: string)=> {
    if (title != '') {
        taskList.value.push({
            id: id.value++,
            title: title,
            done: false,
            createdAt: new Date().toISOString().split('T')[0] ?? '',
        });
    }
});

</script>

<template>
    <section class="flex flex-wrap gap-2.5">
        <ToDoTask v-for="task in taskList" :key="task.id" :task="task"/>
    </section>
</template>