<script setup lang="ts">
import { ref } from 'vue';
import Icon from './form/Icon.vue';

type Decision = 'replace' | 'merge' | 'cancel'

const isOpen = ref<boolean>(false);
const tryRequest = ()=> {
    isOpen.value = true;
    return new Promise<Decision>((resolve)=>{
        resolver = resolve;
    })
}
let resolver: ((v: Decision) => void) | null = null
const choose = (value: Decision)=> {
    resolver?.(value);
    resolver = null;
    isOpen.value = false;
}
defineExpose({
    tryRequest
})
</script>

<template>
    <div v-if="isOpen" class="absolute bg-black/65 inset-0 z-50 grid">
        <div class="relative bg-white rounded-2xl size-fit px-12 py-16 top-1/2 -translate-y-1/2 -right-1/2 -translate-x-1/2 flex flex-col gap-10 items-center">
            <h2>Ya existen tareas. ¿Qué quieres hacer?</h2>
            <button class="cursor-pointer absolute top-1 right-1" @click="choose('cancel')">
                <Icon class="" width="16" height="16" color="black" type="delete"/>
            </button>
            <div class="flex gap-4">
                <button class="cursor-pointer px-4 py-2 bg-emerald-600 rounded-2xl" @click="choose('replace')">Reemplazar</button>
                <button class="cursor-pointer px-4 py-2 bg-blue-500 rounded-2xl" @click="choose('merge')">Añadir</button>
            </div>
            
        </div>
    </div>
</template>