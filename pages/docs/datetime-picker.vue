<script setup lang="ts">
definePageMeta({
    layout: 'documentation',
})

const ddd = ref(new Date())
</script>

<template>
    <h1>Datetime picker</h1>
    <DatePicker v-model="ddd" />
    <div class="my-4">
        {{ ddd.toISOString().slice(0, 19) }}
    </div>
</template>

<style scoped></style>
