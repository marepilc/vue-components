<script setup lang="ts">
definePageMeta({
    layout: 'documentation',
})

const timeValue = ref('00:00')
</script>

<template>
    <h1>Time picker</h1>

    <div>
        <TimePicker class="w-24" v-model="timeValue" />
    </div>
    <div>
        {{ timeValue }}
    </div>
</template>

<style scoped></style>
