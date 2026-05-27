<script setup lang="ts">
import { ref } from 'vue'
import type { HistoryLog } from './types/HistoryLog'
import HistoryList from './components/HistoryList.vue'
import TimingCard from './components/TimingCard.vue'
import InputTiming from './components/InputTiming.vue'

const workDuration = ref(0)
const breakDuration = ref(0)

const historyLogs = ref<HistoryLog[]>([])

function addHistoryLog(log: HistoryLog) {
  historyLogs.value.push(log)
}

function setTiming(timing: { workSeconds: number, breakSeconds: number }) {
  workDuration.value = timing.workSeconds
  breakDuration.value = timing.breakSeconds
}

</script>

<template>
  <header>
    <h1 class="text-3xl font-bold underline">
      Pomodoro Timer
    </h1>
  </header>
  <main>
    <InputTiming @setTiming="setTiming" />
    <TimingCard :workDuration="workDuration" :breakDuration="breakDuration" @addHistoryLog="addHistoryLog" />
    <HistoryList :historyLogs="historyLogs" />
  </main>
</template>