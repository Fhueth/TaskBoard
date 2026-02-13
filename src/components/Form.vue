<script setup lang="ts">
import Button from './form/Button.vue';
import Input from './form/Input.vue';


</script>

<template>
    <form @submit.prevent="">
        <Input/>
        <Button/>
    </form>
</template>