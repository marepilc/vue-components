<script setup lang="ts">
const props = defineProps<{
    label: string
    modelValue: boolean
}>()

const emit = defineEmits(['update:modelValue'])

const isChecked = ref(props.modelValue)

watch(isChecked, (value) => {
    emit('update:modelValue', value)
})
</script>

<template>
    <div class="flex items-center">
        <label
            class="relative flex h-6 w-12 cursor-pointer items-center rounded-full border border-surface-300 bg-form-light dark:border-surface-800 dark:bg-form-dark"
        >
            <input type="checkbox" class="peer sr-only" v-model="isChecked" />
            <span
                class="mx-0.5 h-4/5 w-2/5 rounded-full bg-surface-500 transition-colors duration-500 peer-checked:ml-auto peer-checked:bg-primary-500 dark:bg-surface-500 dark:peer-checked:bg-primary-500"
            ></span>
        </label>
        <span class="ml-2">{{ label }}</span>
    </div>
</template>

<style scoped></style>
