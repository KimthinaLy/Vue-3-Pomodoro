<script setup lang="ts">
import { ref, onUnmounted, watch, computed } from 'vue'
import type { HistoryLog } from './types/HistoryLog'

const isWorking = ref(true)
const seconds = ref(12)
let intervalId = 0
const historyLogs = ref<HistoryLog[]>([])
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
    if (isWorking.value) {
      const endTime = new Date()
      historyLogs.value.push({
        id: crypto.randomUUID(),
        startTime: startTime.value?.toISOString() || '',
        endTime: endTime.toISOString(),
        duration: (endTime.getTime() - (startTime.value?.getTime() || 0)) / 1000
      })
    }

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
  <header>
    <h1 class="text-3xl font-bold underline">
      Pomodoro Timer
    </h1>
  </header>

  <main>
    <p>Time remaining: {{ formattedTime }}</p>
    <button v-if="startTime === null" @click="startTiming">Start</button>
    <button v-else-if="isRunning" @click="stopTiming">Pause</button>
    <div v-else>
      <button @click="resetTiming">Reset</button>
      <button @click="resumeTiming">Resume</button>
    </div>
    <div v-if='isWorking'>
      <h3>Working</h3>
      <div> Focus .. </div>
    </div>
    <div v-else>
      <h3>Congratulation for Completing this task</h3>
      <div>Let's take a break</div>
    </div>
    <p v-for='history in historyLogs' :key='history.id'>
      {{ history.duration }}
      {{ history.startTime }}
      {{ history.endTime }}
    </p>
  </main>
</template>
