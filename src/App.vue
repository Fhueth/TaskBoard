<script setup lang="ts">
import { ref } from 'vue';
import Form from './components/Form.vue';
import ToDoTaskList from './components/ToDoTaskList.vue';

const data = ref<string>('');
const setTask = (title: string)=> {
    data.value = title;
}
</script>


<template>
    <Form @title="setTask"/>
    <ToDoTaskList class="mt-20" :title="data"/>
</template>