<script setup lang="ts"></script>

<template>
    <div class="flex h-full flex-col">
        <header
            class="border-b border-surface-200 bg-surface-100 p-2 dark:border-surface-800 dark:bg-surface-900"
        >
            <AppNavbar />
        </header>
        <!-- Make the main content flex and allow it to grow -->
        <main class="container mx-auto flex flex-1 overflow-hidden">
            <!-- Sidebar -->
            <div class="scrollbar-custom w-72 overflow-auto">
                <AppSidebar />
            </div>
            <!-- Main Content -->
            <div class="flex-1 overflow-auto p-4">
                <slot />
            </div>
        </main>
    </div>
</template>

<style scoped></style>
