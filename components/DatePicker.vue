<script setup lang="ts">
import { ref, computed, defineProps, nextTick } from 'vue'

const props = defineProps({
    format: {
        type: String,
        default: 'YYYY-MM-DD',
    },
})

const pickerOpen = ref(false)
const pickerRef = ref<HTMLElement | null>(null)
const buttonRef = ref<HTMLElement | null>(null)
const pickerView = ref<'days' | 'months' | 'years'>('months')

const weekdays = ['S', 'M', 'T', 'W', 'T', 'F', 'S']
const monthNames = [
    'January',
    'February',
    'March',
    'April',
    'May',
    'June',
    'July',
    'August',
    'September',
    'October',
    'November',
    'December',
]
const monthAbbreviations = [
    'Jan',
    'Feb',
    'Mar',
    'Apr',
    'May',
    'Jun',
    'Jul',
    'Aug',
    'Sep',
    'Oct',
    'Nov',
    'Dec',
]

const selectedDate = ref(new Date())

// Compute an array of days in the selected month/year
const daysInMonth = computed(() => {
    const days = []
    const year = selectedDate.value.getFullYear()
    const month = selectedDate.value.getMonth()

    const lastDayOfCurrentMonth = new Date(year, month + 1, 0).getDate()
    const firstDayOfWeek = new Date(year, month, 1).getDay()
    const lastDayOfPreviousMonth = new Date(year, month, 0).getDate()

    // Fill in the days from the previous month to align the first day of the current month
    for (let i = firstDayOfWeek - 1; i >= 0; i--) {
        days.push({ day: lastDayOfPreviousMonth - i, currentMonth: false })
    }

    // Fill in the days of the current month
    for (let i = 1; i <= lastDayOfCurrentMonth; i++) {
        days.push({ day: i, currentMonth: true })
    }

    // Fill in the days from the next month to complete the grid (if necessary)
    while (days.length % 7 !== 0) {
        days.push({
            day: days.length - lastDayOfCurrentMonth - firstDayOfWeek + 1,
            currentMonth: false,
        })
    }

    return days
})

function toggleSelector() {
    pickerOpen.value = !pickerOpen.value
    pickerView.value = 'days'

    if (pickerOpen.value) {
        nextTick(() => {
            positionPicker()
        })
    }

    console.log(formatDate(selectedDate.value, props.format))
}

function positionPicker() {
    if (pickerRef.value && buttonRef.value) {
        const buttonRect = buttonRef.value.getBoundingClientRect()
        pickerRef.value.style.top = `${buttonRect.bottom + window.scrollY + 4}px` // Add some space below the button
        pickerRef.value.style.left = `${buttonRect.left + window.scrollX}px`
    }
}

function selectDate(day: number) {
    selectedDate.value = new Date(
        selectedDate.value.getFullYear(),
        selectedDate.value.getMonth(),
        day
    )
    pickerOpen.value = false
}

function isSelectedDay(day: { day: number }): boolean {
    return (
        selectedDate.value.getDate() === day.day &&
        selectedDate.value.getMonth() === selectedDate.value.getMonth() &&
        selectedDate.value.getFullYear() === selectedDate.value.getFullYear()
    )
}

function formatDate(date: Date, format: string): string {
    const year = date.getFullYear()
    const month = String(date.getMonth() + 1).padStart(2, '0') // getMonth() is zero-indexed
    const day = String(date.getDate()).padStart(2, '0')
    const hours = String(date.getHours()).padStart(2, '0')
    const minutes = String(date.getMinutes()).padStart(2, '0')
    const seconds = String(date.getSeconds()).padStart(2, '0')

    return format
        .replace(/YYYY/g, year.toString())
        .replace(/MMMM/g, monthNames[date.getMonth()])
        .replace(/MMM/g, monthAbbreviations[date.getMonth()])
        .replace(/MM/g, month)
        .replace(/DD/g, day)
        .replace(/HH/g, hours)
        .replace(/mm/g, minutes)
        .replace(/ss/g, seconds)
}

function offsetDate(offset: number) {
    const currentYear = selectedDate.value.getFullYear()
    const currentMonth = selectedDate.value.getMonth()
    const currentDay = selectedDate.value.getDate()

    if (pickerView.value === 'years') {
        const shift = offset < 0 ? -12 : 12
        changeDateYear(currentYear + shift, false)
        return
    }

    const newDate = new Date(currentYear, currentMonth + offset, 1)
    const newYear = newDate.getFullYear()
    const newMonth = newDate.getMonth()
    const lastDayOfNewMonth = new Date(newYear, newMonth + 1, 0).getDate()

    selectedDate.value = new Date(
        newYear,
        newMonth,
        Math.min(currentDay, lastDayOfNewMonth)
    )
}

function changeDateMonth(newMonth: number) {
    const currentYear = selectedDate.value.getFullYear()
    const currentDay = selectedDate.value.getDate()
    const lastDayOfNewMonth = new Date(currentYear, newMonth + 1, 0).getDate()

    selectedDate.value = new Date(
        currentYear,
        newMonth,
        Math.min(currentDay, lastDayOfNewMonth)
    )
    pickerView.value = 'days'
}

function getYearsArray() {
    const currentYear = selectedDate.value.getFullYear()
    const startYear = currentYear - 7
    return Array.from({ length: 12 }, (_, i) => startYear + i)
}

function changeDateYear(newYear: number, closeYearView = true) {
    const currentMonth = selectedDate.value.getMonth()
    const currentDay = selectedDate.value.getDate()
    const lastDayOfNewMonth = new Date(newYear, currentMonth + 1, 0).getDate()

    selectedDate.value = new Date(
        newYear,
        currentMonth,
        Math.min(currentDay, lastDayOfNewMonth)
    )

    if (closeYearView) {
        pickerView.value = 'days'
    }
}

function handleClickOutside(event: MouseEvent) {
    if (
        pickerRef.value &&
        buttonRef.value &&
        !pickerRef.value.contains(event.target as Node) &&
        !buttonRef.value.contains(event.target as Node)
    ) {
        pickerOpen.value = false
    }
}

watch(pickerOpen, (isOpen) => {
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
    <div class="inline-flex cursor-default flex-col items-center">
        <!-- Date Picker Popover -->
        <div
            v-if="pickerOpen"
            ref="pickerRef"
            class="bg-form-light dark:bg-form-dark min-w-60 scale-95 transform rounded-md border border-surface-300 opacity-0 shadow-md transition-all duration-300 ease-out dark:border-surface-800"
            :class="pickerOpen ? 'scale-100 opacity-100' : ''"
            style="position: absolute; z-index: 10"
        >
            <div class="flex flex-col">
                <div class="flex items-center justify-between">
                    <div
                        class="flex items-center justify-between gap-1 px-2 py-1 text-lg font-bold"
                    >
                        <div class="flex gap-1">
                            <span
                                class="cursor-pointer hover:text-primary-500"
                                @click="pickerView = 'months'"
                            >
                                {{ formatDate(selectedDate, 'MMMM') }}
                            </span>
                            <span
                                class="cursor-pointer hover:text-primary-500"
                                @click="pickerView = 'years'"
                            >
                                {{ formatDate(selectedDate, 'YYYY') }}
                            </span>
                        </div>
                    </div>
                    <div class="flex items-center justify-between px-2 py-1">
                        <button
                            class="p-1 hover:text-primary-500"
                            type="button"
                            @click="offsetDate(-1)"
                        >
                            <svg
                                class="h-5 w-5"
                                xmlns="http://www.w3.org/2000/svg"
                                viewBox="0 0 24 24"
                            >
                                <path
                                    fill="currentColor"
                                    d="M16 19L5 12l11-7zm-2-3.65v-6.7L8.75 12z"
                                />
                            </svg>
                        </button>
                        <button
                            class="p-1 hover:text-primary-500"
                            type="button"
                            @click="offsetDate(1)"
                        >
                            <svg
                                class="h-5 w-5"
                                xmlns="http://www.w3.org/2000/svg"
                                viewBox="0 0 24 24"
                            >
                                <path
                                    fill="currentColor"
                                    d="M8 19V5l11 7zm2-3.65L15.25 12L10 8.65z"
                                />
                            </svg>
                        </button>
                    </div>
                </div>
                <div
                    class="grid grid-cols-7 gap-1 p-2"
                    v-if="pickerView === 'days'"
                >
                    <div
                        v-for="(weekday, ix) in weekdays"
                        :key="'weekday-' + ix"
                        class="text-center font-bold"
                    >
                        {{ weekday }}
                    </div>

                    <!-- Days Grid -->
                    <div
                        v-for="(day, ix) in daysInMonth"
                        :key="'day-' + ix"
                        class="cursor-default rounded p-1 text-center"
                        :class="[
                            day.currentMonth
                                ? 'cursor-pointer text-ink-950 hover:bg-primary-100 hover:!text-ink-950 dark:text-ink-50'
                                : 'text-ink-400 dark:text-ink-600',
                            day.currentMonth && isSelectedDay(day)
                                ? 'bg-primary-500 !text-ink-50'
                                : '',
                        ]"
                        @click="day.currentMonth ? selectDate(day.day) : null"
                    >
                        {{ day.day }}
                    </div>
                </div>
                <div
                    class="grid grid-cols-3 gap-1 p-2"
                    v-if="pickerView === 'months'"
                >
                    <div
                        v-for="ix in monthNames.map((_, index) => index)"
                        :key="'month-' + ix"
                        class="cursor-pointer rounded p-1 text-center"
                        :class="[
                            'hover:bg-primary-100 hover:!text-ink-950 dark:text-ink-50',
                            selectedDate.getMonth() === ix
                                ? 'bg-primary-500 !text-ink-50'
                                : '',
                        ]"
                        @click.stop="changeDateMonth(ix)"
                    >
                        {{ monthAbbreviations[ix] }}
                    </div>
                </div>
                <div
                    class="grid grid-cols-3 gap-1 p-2"
                    v-if="pickerView === 'years'"
                >
                    <div
                        v-for="(year, ix) in getYearsArray()"
                        :key="'year-' + ix"
                        class="cursor-pointer rounded p-1 text-center"
                        :class="[
                            'hover:bg-primary-100 hover:!text-ink-950 dark:text-ink-50',
                            selectedDate.getFullYear() === year
                                ? 'bg-primary-500 !text-ink-50'
                                : '',
                        ]"
                        @click.stop="changeDateYear(year)"
                    >
                        {{ year }}
                    </div>
                </div>
            </div>
        </div>

        <button
            type="button"
            ref="buttonRef"
            @click="toggleSelector"
            class="bg-form-light dark:bg-form-dark mt-4 cursor-default rounded border border-surface-300 px-3 py-1.5 text-center focus:outline-none focus:ring-1 focus:ring-primary-500 focus:ring-opacity-50 focus:ring-offset-2 focus:ring-offset-surface-50 dark:border-surface-800 dark:focus:ring-offset-surface-950"
        >
            {{ formatDate(selectedDate, props.format) }}
        </button>
    </div>
</template>
