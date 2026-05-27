<script setup lang="ts">
import { ref } from 'vue'
const workMinutes = ref(0)
const breakMinutes = ref(0)
const emit = defineEmits(['setTiming'])

function setTiming() {
    emit('setTiming', {
        workSeconds: workMinutes.value * 60,
        breakSeconds: breakMinutes.value * 60
    })
}
</script>

<template>
    <form @submit.prevent="setTiming">
        <input type="number" v-model.number="workMinutes" min="1" placeholder="Work (minutes)" required />
        <input type="number" v-model.number="breakMinutes" min="1" placeholder="Break (minutes)" required />
        <button type="submit">Set</button>
    </form>
</template>