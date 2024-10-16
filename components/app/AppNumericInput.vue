<script setup lang="ts">
const props = defineProps<{
    modelValue: number | null
}>()

const emit = defineEmits(['update:modelValue'])

// Use a ref to hold the input value and initialize it from props
const inputValue = ref(props.modelValue ?? '')

// Watch for changes in props.modelValue and update inputValue
watch(
    () => props.modelValue,
    (newValue) => {
        inputValue.value = newValue ?? ''
    }
)

// Emit the updated value whenever inputValue changes
function updateValue(event: Event) {
    const value = (event.target as HTMLInputElement).value
    const numericValue = value === '' ? null : parseFloat(value)

    // Emit the new value to the parent component
    emit('update:modelValue', numericValue)
}
</script>

<template>
    <input
        class="rounded border border-surface-300 bg-form-light px-3 py-1.5 focus:outline-none focus:ring-1 focus:ring-primary-500 focus:ring-opacity-50 focus:ring-offset-2 focus:ring-offset-surface-50 dark:border-surface-800 dark:bg-form-dark dark:focus:ring-offset-surface-950"
        type="number"
        :value="inputValue"
        @input="updateValue"
    />
</template>
