<script setup lang="ts">
import ActionButtons from './ActionButtons.vue'
import { computed, ref, watch, onMounted, onUnmounted } from 'vue'

let audioContext: AudioContext | null = null

const props = defineProps<{
    workDuration: number
    breakDuration: number
}>()

const emit = defineEmits(['addHistoryLog'])

const isWorking = ref(true)
const seconds = ref(props.workDuration)
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
        playSound()
        switchTimingMode()
    }
})

function switchTimingMode() {
    isWorking.value = !isWorking.value
    if (isWorking.value) {
        seconds.value = props.workDuration
    } else {
        seconds.value = props.breakDuration
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

    audioContext?.resume()
}

function resetTiming() {
    stopTiming()
    if (isWorking.value) {
        seconds.value = props.workDuration
    } else {
        seconds.value = props.breakDuration
    }
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

watch(() => props.workDuration, (newWorkDuration) => {
    if (isWorking.value) {
        seconds.value = newWorkDuration
    }
})
watch(() => props.breakDuration, (newBreakDuration) => {
    if (!isWorking.value) {
        seconds.value = newBreakDuration
    }
})

onMounted(() => {
    audioContext = new AudioContext()
})
onUnmounted(() => {
    stopTiming()
    if (audioContext) {
        audioContext.close()
    }
})

function playSound() {
    if (!audioContext) return

    const oscillator = audioContext.createOscillator()
    const gainNode = audioContext.createGain()

    oscillator.connect(gainNode)
    gainNode.connect(audioContext.destination)

    oscillator.type = 'sine'
    oscillator.frequency.setValueAtTime(1000, audioContext.currentTime)
    gainNode.gain.setValueAtTime(0.1, audioContext.currentTime)

    oscillator.start()
    setTimeout(() => {
        oscillator.stop()
    }, 2000)
}
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