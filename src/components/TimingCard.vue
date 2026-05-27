<script setup lang="ts">
import ActionButtons from './ActionButtons.vue'
import { computed, ref, watch, onUnmounted } from 'vue'

const emit = defineEmits(['addHistoryLog'])

const isWorking = ref(true)
const seconds = ref(12)
let intervalId = 0
const startTime = ref<Date | null>(null)
const isRunning = ref(false)

const formattedTime = computed(() => {
    const mn = Math.floor(seconds.value / 60)
    const s = seconds.value % 60
    const minute = mn.toString().padStart(2, "0")
    const second = s.toString().padStart(2, "0")
    return minute + ':' + second
})

watch(seconds, (newSeconds) => {
    if (newSeconds === 0) {
        stopTiming()
        emit('addHistoryLog', {
            id: crypto.randomUUID(),
            startTime: startTime.value?.toISOString() || '',
            endTime: new Date().toISOString(),
            duration: (new Date().getTime() - (startTime.value ? new Date(startTime.value).getTime() : 0)) / 1000
        })
        switchTimingMode()
    }
})

function switchTimingMode() {
    isWorking.value = !isWorking.value
    if (isWorking.value) {
        seconds.value = 12
    } else {
        seconds.value = 3
    }
    startTime.value = null
}

function startTiming() {
    isRunning.value = true
    startTime.value = new Date()
    intervalId = setInterval(
        () => {
            seconds.value -= 1
        }, 1000
    )
}

function resetTiming() {
    stopTiming()
    seconds.value = 12
    startTime.value = null
}

function resumeTiming() {
    isRunning.value = true
    intervalId = setInterval(
        () => {
            seconds.value -= 1
        }, 1000
    )
}

function stopTiming() {
    clearInterval(intervalId)
    isRunning.value = false
}

onUnmounted(stopTiming)
</script>

<template>
    <p>Time remaining: {{ formattedTime }}</p>
    <ActionButtons :isRunning="isRunning" :startTime="startTime" @start="startTiming" @stop="stopTiming"
        @reset="resetTiming" @resume="resumeTiming"></ActionButtons>

    <div v-if='isWorking'>
        <h3>Working</h3>
        <div> Focus .. </div>
    </div>
    <div v-else>
        <h3>Congratulation for Completing this task</h3>
        <div>Let's take a break</div>
    </div>
</template>