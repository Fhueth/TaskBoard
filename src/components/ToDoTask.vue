<script setup lang="ts">
import type { Task } from '../types/Task';


const props = defineProps<{
    task: Task,
}>();

</script>

<template>
    <article class="bg-fuchsia-200">
        <p>ID: {{ task.id }}</p>
        <p>Título: {{ task.title }}</p>
        <p>Estado: {{ task.done }}</p>
        <p>Fecha: {{ task.createdAt }}</p>
    </article>
</template>9