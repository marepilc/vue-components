<script setup lang="ts">
import { ref, watch, onBeforeUnmount, nextTick } from 'vue'

const popoverOpen = ref(false)
const popoverRef = ref<HTMLElement | null>(null)
const activatorRef = ref<HTMLElement | null>(null)

function togglePopover() {
    popoverOpen.value = !popoverOpen.value
    if (popoverOpen.value) {
        nextTick(() => {
            positionPopover()
        })
    }
}

function positionPopover() {
    if (popoverRef.value && activatorRef.value) {
        const activatorRect = activatorRef.value.getBoundingClientRect()
        const popoverEl = popoverRef.value

        const popoverHeight = popoverEl.offsetHeight
        const popoverWidth = popoverEl.offsetWidth

        const viewportHeight = window.innerHeight
        const viewportWidth = window.innerWidth

        // Calculate positions
        let top = activatorRect.bottom + window.scrollY + 4
        let left =
            activatorRect.left +
            window.scrollX +
            activatorRect.width / 2 -
            popoverWidth / 2

        // Adjust positioning if not enough space
        if (left < 4) {
            left = 4 // Add some padding from the left edge
        } else if (left + popoverWidth > viewportWidth) {
            left = viewportWidth - popoverWidth - 4 // Add some padding from the right edge
        }

        if (top + popoverHeight > viewportHeight + window.scrollY) {
            // Not enough space below, position above the activator
            top = activatorRect.top + window.scrollY - popoverHeight - 4
        }

        // Apply positioning styles
        popoverEl.style.top = `${top}px`
        popoverEl.style.left = `${left}px`
    }
}

function handleClickOutside(event: MouseEvent) {
    if (
        popoverRef.value &&
        activatorRef.value &&
        !popoverRef.value.contains(event.target as Node) &&
        !activatorRef.value.contains(event.target as Node)
    ) {
        popoverOpen.value = false
    }
}

watch(popoverOpen, (isOpen) => {
    if (isOpen) {
        document.addEventListener('click', handleClickOutside)
    } else {
        document.removeEventListener('click', handleClickOutside)
    }
})

onBeforeUnmount(() => {
    document.removeEventListener('click', handleClickOutside)
})
</script>

<template>
    <div class="inline-flex flex-col items-center">
        <!-- Activator Slot -->
        <div @click="togglePopover" ref="activatorRef">
            <slot name="activator"></slot>
        </div>

        <!-- Popover Content -->
        <div
            v-if="popoverOpen"
            ref="popoverRef"
            class="scale-95 transform opacity-0 transition-all duration-300 ease-out"
            :class="popoverOpen ? 'scale-100 opacity-100' : ''"
            style="position: absolute; z-index: 10"
        >
            <div
                class="min-w-60 rounded-md border border-surface-300 bg-form-light shadow-md dark:border-surface-800 dark:bg-form-dark"
            >
                <slot></slot>
            </div>
        </div>
    </div>
</template>
