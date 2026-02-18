<script setup lang="ts">
const props = defineProps<{
    type: string
}>()

</script>

<template>
    <svg class="size-8 text-white" aria-hidden="true">
        <use :href="`/icons/sprite.svg#${type}`" :xlink:href="`/icons/sprite.svg#${type}`" />
    </svg>
</template>