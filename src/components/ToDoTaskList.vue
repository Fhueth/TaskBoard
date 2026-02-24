<script setup lang="ts">
import { computed, onMounted, ref } from 'vue';
import type { Task } from '../types/Task';
import Papa from 'papaparse';
import ToDoTask from './ToDoTask.vue';
import Icon from './form/Icon.vue';

const taskList = ref<Map<number, Task>>(new Map());
const search = ref<string>('');
const save = () => {
    localStorage.setItem('tasks', JSON.stringify(Array.from(taskList.value.values())));
}
const get = () => {
    return new Map(
        (JSON.parse(localStorage.getItem('tasks') || '[]') as Task[]).map(task => [task.id, task])
    );
}
const addTask = (title: string) => {
    const id = Date.now();
    const tasksList: Task = {
        id: id,
        title: title,
        done: false,
    };
    taskList.value.set(id, tasksList);
    save();
}
const arrayTasks = computed(() => {
    const tasks = Array.from(taskList.value.values())
    const filtered = search.value !== ''
        ? tasks.filter(task =>
            task.title.toLowerCase().includes(search.value.toLowerCase())
        )
        : tasks
    return filtered.sort((a, b) => Number(a.done) - Number(b.done))
})
defineExpose({
    addTask
})
const saveTask = (task: Task) => {
    taskList.value.set(task.id, task);
    save()
}
const deleteTaskFromList = (id: number) => {
    taskList.value.delete(id);
    save();
}
onMounted(() => {
    taskList.value = get();
})
const exportCSV = () => {
    const csv = Papa.unparse(Array.from(taskList.value.values()))
    const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' })
    const url = URL.createObjectURL(blob)
    const link = document.createElement('a')
    link.href = url
    link.download = 'tasks.csv'
    link.click()
}
const deleteAll = () => {
    taskList.value.clear();
    save()
}

const handleFileUpload = (event: Event) => {
    const input = event.target as HTMLInputElement
    const file = input.files?.[0]
    if (!file) return

    Papa.parse(file, {
        header: true,
        skipEmptyLines: true,
        dynamicTyping: true,
        complete: (results) => {
            const rows = results.data as Task[]
            if (!rows.length) {
                alert('CSV vacío o inválido.')
                return
            }
            if (taskList.value.size > 0) {
                const replace = window.confirm(
                    'Ya existen tareas. ¿Quieres reemplazarlas? (Cancelar = añadir)'
                )
                if (replace) taskList.value.clear()
            }
            rows.forEach((task) => {
                let id = task.id
                while (taskList.value.has(id)) {
                    id = Date.now() + Math.floor(Math.random() * 100000)
                }
                taskList.value.set(id, { ...task, id })
            })
            save()
            input.value = ''
        },

        error: (error) => {
            console.error('Error al importar CSV:', error)
            alert('Error al importar CSV.')
        }
    })
}

</script>
<template>
    <section class="flex flex-col w-[min(80%,1000px)] gap-5">
        <div class="grid grid-cols-[1fr_auto_auto_auto] px-5">
            <input v-model="search" name="search" id="search" type="text" class="w-50 border-2 rounded-3xl">
            <input type="file" accept=".csv" @change="handleFileUpload" />
            <button @click="exportCSV" class="size-10 cursor-pointer">
                <Icon color="#000" type="export" />
            </button>
            <button @click="deleteAll" class="size-10 cursor-pointer">
                <Icon color="#000" type="deleteAll" />
            </button>
        </div>
        <ToDoTask @editTask="saveTask" @deleteTask="deleteTaskFromList" v-for="task in arrayTasks" :key="task.id"
            :task="task" />
    </section>
</template>