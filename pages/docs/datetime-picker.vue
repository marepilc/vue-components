<script setup lang="ts">
definePageMeta({
    layout: 'documentation',
})

const value = ref(new Date())
const numericValue = ref(null)
const timeValue = ref('00:00')
const localDate = computed(() => {
    const adjustedDate = new Date(
        value.value.getTime() - value.value.getTimezoneOffset() * 60000
    )
    return adjustedDate.toISOString().slice(0, 19)
})
</script>

<template>
    <h1>Datetime picker</h1>
    <DatePicker v-model="value" format="YYYY-MM-DD HH:mm" />

    <div class="my-4">
        {{ localDate }}
    </div>
    <div>
        <AppNumericInput class="w-16" v-model="numericValue" />
    </div>
    <div class="my-4">
        {{ numericValue }}
    </div>
    <div>
        <AppTimeInput class="w-24" v-model="timeValue" />
    </div>
    <div>
        {{ timeValue }}
    </div>
</template>

<style scoped></style>
