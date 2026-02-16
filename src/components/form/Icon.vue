<script setup lang="ts">
const props = defineProps<{
    type: string
}>()

</script>

<template>
    <svg class="icon size-8">
        <use :href="`/icons/sprite.svg#${type}`"></use>
    </svg>
</template>