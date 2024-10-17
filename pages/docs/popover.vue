<script setup lang="ts">
definePageMeta({
    layout: 'documentation',
})

const value = ref(true)
const valueDate = ref(new Date())
</script>

<template>
    <h1>Popover</h1>
    <Popover>
        <!-- Activator Slot -->
        <template v-slot:activator>
            <button
                type="button"
                class="mt-4 rounded border border-surface-300 bg-form-light px-3 py-1.5 text-center focus:outline-none focus:ring-1 focus:ring-primary-500 focus:ring-opacity-50 focus:ring-offset-2 focus:ring-offset-surface-50 dark:border-surface-800 dark:bg-form-dark dark:focus:ring-offset-surface-950"
            >
                Open Popover
            </button>
        </template>

        <!-- Popover Content -->
        <div class="p-4">
            <p>This is the content inside the popover.</p>
            <p>You can place any Vue components or HTML content here.</p>
            <DatePicker v-model="valueDate" />
        </div>
    </Popover>
</template>

<style scoped></style>
