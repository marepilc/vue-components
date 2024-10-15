<script setup lang="ts">
definePageMeta({
    layout: 'documentation',
})

const value = ref(new Date())
const localDate = computed(() => {
    const adjustedDate = new Date(
        value.value.getTime() - value.value.getTimezoneOffset() * 60000
    )
    return adjustedDate.toISOString().slice(0, 19)
})
</script>

<template>
    <h1>Datetime picker</h1>
    <DatePicker v-model="value" />
    <div class="my-4">
        {{ localDate }}
    </div>
</template>

<style scoped></style>
