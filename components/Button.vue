<script setup lang="ts"></script>

<template>
    <button
        type="button"
        class="focus:ring-offset-surface-900t flex items-center justify-around gap-2 rounded bg-primary px-3 py-1.5 text-ink-50 hover:bg-primary-600 focus:outline-none focus:ring-1 focus:ring-primary-500 focus:ring-opacity-50 focus:ring-offset-2 focus:ring-offset-surface-50 active:bg-primary-700 active:text-ink-50 disabled:cursor-not-allowed disabled:bg-surface-300 disabled:text-ink-600 dark:focus:ring-offset-surface-950 dark:disabled:bg-surface-800 dark:disabled:text-ink-500"
    >
        <slot name="icon" />
        <slot />
    </button>
</template>

<style scoped></style>
