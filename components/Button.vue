<script setup lang="ts"></script>

<template>
    <button
        type="button"
        class="flex items-center justify-around gap-2 rounded bg-primary px-3 py-2 font-semibold text-ink-50 hover:bg-primary-600 focus:outline-none focus:ring-0"
    >
        <slot name="icon" />
        <slot />
    </button>
</template>

<style scoped></style>
