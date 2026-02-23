<script setup lang="ts">
const props = defineProps<{
    type: string,
    color: string,
}>()

</script>

<template>
    <svg class="size-8" :color="`${color ? color : 'currentColor'}`" aria-hidden="true">
        <use :href="`/icons/sprite.svg#${type}`" :xlink:href="`/icons/sprite.svg#${type}`" />
    </svg>
</template>