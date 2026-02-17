<script setup lang="ts">
import { reactive } from 'vue';
import Button from './form/Button.vue';
import Input from './form/Input.vue';   
import type { Field } from '../types/Form';

const form = reactive({
    name: { value: '', valid: false },
});

const emit = defineEmits<{
    (e: 'title', title: string): void
}>();

const check = (field: Field, valid: boolean, inputValue: string) => {
    form[field].valid = valid
    form[field].value = inputValue
}
const submit = ()=> {
    if(!Object.values(form).every((f) => f.valid)) {
        return;
    }
    emit('title', form.name.value);
}

</script>

<template>
    <form @submit.prevent="submit" novalidate
    class="flex gap-2 items-center size-fit backdrop-blur-2xl fixed top-10 left-1/2 -translate-x-1/2 w-fit z-50"
    >
        <Input @validity="check" type="text" name="name" required>Nombre</Input>
        <Button/>
    </form>
</template>