<script setup lang="ts">
import { ref } from 'vue';
import Form from './components/Form.vue';
import ToDoTaskList from './components/ToDoTaskList.vue';

const taskListRef = ref<InstanceType<typeof ToDoTaskList> | null>(null);
const setTask = (title: string)=> {
    taskListRef.value?.addTask(title)
}
</script>


<template>
    <Form @title="setTask"/>
    <ToDoTaskList ref="taskListRef" class="mt-30"/>
</template>