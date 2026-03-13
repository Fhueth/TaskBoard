<script setup lang="ts">
const props = defineProps<{
    type: string,
    color: string,
    width: string,
    height: string,
}>()

</script>

<template>
    <svg :width="`${width}`" :height="`${height}`" :color="`${color ? color : 'currentColor'}`" aria-hidden="true">
        <use :href="`/icons/sprite.svg#${type}`" :xlink:href="`/icons/sprite.svg#${type}`" />
    </svg>
</template>