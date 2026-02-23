<script setup lang="ts">
import { computed, onMounted, ref } from 'vue';
import type { Task } from '../types/Task';
import ToDoTask from './ToDoTask.vue';

const taskList = ref<Map<number, Task>>(new Map());
const save = () => {
    localStorage.setItem('tasks', JSON.stringify(Array.from(taskList.value.values())));
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
    taskList.value.set(id, tasksList);
    save();
}
const arrayTasks = computed(()=>Array.from(taskList.value.values()).sort((a, b) => Number(b.done) - Number(a.done)))
defineExpose({
    addTask
})
const saveTask = (id: number, task: Task)=> {
    taskList.value.set(task.id, task);
    save()
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
    <section class="flex flex-col w-[min(80%,1000px)] gap-5">
        <ToDoTask @editTask="saveTask" @deleteTask="deleteTaskFromList" v-for="task in arrayTasks" :key="task.id" :task="task"/>
    </section>
</template>