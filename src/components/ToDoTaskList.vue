<script setup lang="ts">
import { onMounted, ref, watch } from 'vue';
import type { Task } from '../types/Task';
import ToDoTask from './ToDoTask.vue';

const taskList = ref<Task[]>([]);
const id = ref<number>(taskList.value.length === 0 ? 0 : taskList.value.length-1);
const props = defineProps<{
    title: string,
}>();

const save = () => {
    localStorage.setItem('tasks', JSON.stringify(taskList.value));
}
watch(()=>props.title, (title: string)=> {
    if (title != '') {
        const tasksList: Task = {
            id: id.value++,
            title: title,
            done: false,
            createdAt: new Date().toISOString().split('T')[0] ?? '',
        };
        taskList.value.push(tasksList);
        save();
    }
});
const deleteTaskFromList = (id: number)=> {
    taskList.value.splice(taskList.value.findIndex(task => task.id === id), 1);
    save();
}
onMounted(()=>{
    taskList.value = JSON.parse(localStorage.getItem('tasks') || '[]');
})
</script>

<template>
    <section class="flex flex-wrap gap-2.5 overflow justify-center items-center p-10">
        <ToDoTask @deleteTask="deleteTaskFromList" v-for="task in taskList" :key="task.id" :task="task"/>
    </section>
</template>