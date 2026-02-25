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

const isDragging = ref(false)
const onDragOver = () => {
    isDragging.value = true
}
const onDragLeave = () => {
    isDragging.value = false
}
const onDrop = (event: DragEvent) => {
    isDragging.value = false
    const file = event.dataTransfer?.files[0]
    if (file) {
        handleFileUpload({ target: { files: [file] } } as unknown as Event)
    }
}

</script>
<template>
    <div v-if="taskList.size >= 1" class="flex flex-col w-[min(80%,1000px)] gap-12">
        <div class="grid grid-cols-[1fr_auto_auto_auto] px-5 gap-3 items-center">
            <label class="w-50 border-2 relative rounded-3xl flex">
                <Icon color="#000" width="24" height="24" type="search" class="absolute left-2 top-1/2 -translate-y-1/2" />
                <input v-model="search" name="search" id="search" type="text" class="size-full py-3 pl-10 px-4 focus:outline-none">
                <button @click="search = ''" v-show="search !== ''" class="size-fit absolute right-2 top-1/2 -translate-y-1/2 cursor-pointer">
                    <Icon color="#000" width="16" height="16" type="delete" class="in-hover:rotate-90" />
                </button>
            </label>
            <label class="px-5 py-2 bg-amber-200 flex rounded-3xl gap-2 items-center">
                <Icon color="#000" width="24" height="24" type="upload"/>
                <p>Importa tus tareas aquí</p>
                <input type="file" accept=".csv" @change="handleFileUpload" class="size-0"/>
            </label>
            <button @click="exportCSV" class="size-10 cursor-pointer">
                <Icon color="#000" width="24" height="24" type="export" />
            </button>
            <button @click="deleteAll" class="size-10 cursor-pointer">
                <Icon color="#000" width="24" height="24" type="deleteAll" />
            </button>
        </div>
        <section v-if="arrayTasks.length >= 1">
            <ToDoTask @editTask="saveTask" @deleteTask="deleteTaskFromList" v-for="task in arrayTasks" :key="task.id"
            :task="task" />
        </section>
        <div class="flex px-6 py-4 gap-3 justify-center items-center bg-red-300 border border-red-400 rounded-2xl size-fit self-center" v-else>
            <Icon color="#000" width="16" height="16" type="info" />
            <p class="">No hemos encontrado ninguna tarea llamada "{{ search }}"</p>
        </div>
    </div>
    <label
    v-else 
    @dragover.prevent="onDragOver"
    @dragleave="onDragLeave"
    @drop.prevent="onDrop"
    class=" hover:bg-neutral-200 transition-colors duration-100 cursor-pointer outline-2 outline-neutral-300 outline-dashed px-26 py-16 bg-neutral-100 rounded-2xl flex flex-col items-center">
        <Icon width="24" height="24" color="#000" type="upload"/>
        <p>Crea tu primera tarea</p>
        <p>O</p>
        <p><strong>Clica aquí para importar</strong> / arrastra y suelta</p>
        <p>CSV</p>
        <input type="file" accept=".csv" @change="handleFileUpload" class="size-0" />
    </label>
</template>