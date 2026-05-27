<script setup lang="ts">
const props = defineProps<{
    isRunning: boolean
    startTime: Date | null
}>()
const emit = defineEmits(['start', 'stop', 'reset', 'resume'])
</script>
<template>
    <button v-if="startTime === null" @click="emit('start')">Start</button>
    <button v-else-if="isRunning" @click="emit('stop')">Pause</button>
    <div v-else>
        <button @click="emit('reset')">Reset</button>
        <button @click="emit('resume')">Resume</button>
    </div>
</template>