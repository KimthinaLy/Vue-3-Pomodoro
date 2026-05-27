<script setup lang="ts">
import type { HistoryLog } from '@/types/HistoryLog'
const props = defineProps<{
    historyLogs: HistoryLog[]
}>()

function formatDateTime(log: HistoryLog): string {
    const s = log.duration % 60
    const minute = Math.floor(log.duration / 60).toString().padStart(2, "0")
    const second = s.toString().padStart(2, "0")
    return minute + 'mins, ' + second + 'secs'
}
</script>
<template>
    <div class="history-list">
        <h2>History</h2>
        <ul>
            <li v-for="log in historyLogs" :key="log.id">
                {{ log.startTime }} - {{ log.endTime }} ({{ formatDateTime(log) }})
            </li>
        </ul>
    </div>
</template>
