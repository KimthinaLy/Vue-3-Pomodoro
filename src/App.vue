<script setup lang="ts">
import { ref } from 'vue'
import type { HistoryLog } from './types/HistoryLog'
import HistoryList from './components/HistoryList.vue'
import TimingCard from './components/TimingCard.vue'
import InputTiming from './components/InputTiming.vue'

const seconds = ref(12)

const historyLogs = ref<HistoryLog[]>([])

function addHistoryLog(log: HistoryLog) {
  historyLogs.value.push(log)
}

function setSeconds(s: number) {
  seconds.value = s
}

</script>

<template>
  <header>
    <h1 class="text-3xl font-bold underline">
      Pomodoro Timer
    </h1>
  </header>

  <main>
    <InputTiming @setSeconds="setSeconds" />
    <TimingCard @addHistoryLog="addHistoryLog" />
    <HistoryList :historyLogs="historyLogs" />
  </main>
</template>