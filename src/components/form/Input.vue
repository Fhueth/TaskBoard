<script setup lang="ts">

import { ref, watch } from 'vue';
import type { Field } from '../../types/Form';
const props = defineProps<{
    type: string
    name: Field
    required?: boolean
}>()

const emit = defineEmits<{
    validity: [name: Field, valid: boolean, inputValue: string]
}>();

const resetValue = ()=> {
    value.value = '';
}

defineExpose({
    resetValue
})

const value = ref<string>("");
const valid = ref<boolean>(false);

watch(value, ()=>{
    valid.value = value.value.length > 3;
    emit('validity', props.name, valid.value, value.value);
});

</script>

<template>
    <div class="relative w-fit h-fit px-3 py-2">
        <input v-model="value" :type="`${type}`" :required="required" minlength="3"  id="name" :name="`${name}`" :class="`w-2xs border-2 rounded-4xl p-4 text-[16px] ${(!valid && value != '') ? ` border-orange-600` : `border-neutral-300`} appearance-none truncate text-neutral-500 font-bold shadow-xl outline-none focus:border-neutral-700`"/>
        <label for="name" class="absolute px-2 left-4 -top-0.5 z-10 rounded-2xl font-medium text-neutral-700 bg-white"><slot/></label>
    </div>
</template>