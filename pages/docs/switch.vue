<script setup lang="ts">
definePageMeta({
    layout: 'documentation',
})

const value = ref(true)
</script>

<template>
    <h1>Switch</h1>
    <AppSwitch v-model="value" label="Toggle me" />
</template>

<style scoped></style>
