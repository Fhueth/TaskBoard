<script setup lang="ts">
import { computed, onMounted, ref } from 'vue';
import type { Task } from '../types/Task';
import ToDoTask from './ToDoTask.vue';

const taskList = ref<Map<number, Task>>(new Map());
const save = () => {
    localStorage.setItem('tasks', JSON.stringify(arrayTasks.value));
}
const get = () => {
    return new Map(
        (JSON.parse(localStorage.getItem('tasks') || '[]') as Task[]).map(task => [task.id, task])
    );
}
const addTask = (title: string)=> {
    const id = Date.now();
    const tasksList: Task = {
        id: id,
        title: title,
        done: false,
        createdAt: new Date().toISOString().split('T')[0] ?? '',
    };
    taskList.value.set(Date.now(), tasksList);
    save();
}
const arrayTasks = computed(()=>Array.from(taskList.value.values()))
defineExpose({
    addTask
})
const saveTask = (id: number, task: Task)=> {
    taskList.value.set(id, task);
}
const deleteTaskFromList = (id: number)=> {
    taskList.value.delete(id);
    save();
}
onMounted(()=>{
    taskList.value = get();
})

</script>

<template>
    <section class="flex flex-wrap gap-2.5 overflow justify-center items-center p-10">
        <ToDoTask @editTask="saveTask" @deleteTask="deleteTaskFromList" v-for="(task, index) in arrayTasks" :key="index+1" :task="task"/>
    </section>
</template>