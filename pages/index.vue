<script setup lang="ts">
const colorMode = useColorMode()

function toggleTheme() {
    colorMode.preference = colorMode.preference === 'light' ? 'dark' : 'light'
}
</script>

<template>
    <main class="container mx-auto my-6">
        <h1 class="py-6 text-3xl font-bold text-ink-950 dark:text-ink-50">
            Vue Component Library
        </h1>
        <div>
            <NuxtLink to="/docs/button">Components</NuxtLink>
        </div>
    </main>
</template>

<style scoped></style>
