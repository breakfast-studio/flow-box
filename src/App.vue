<template>
    <Lunchbox
        background="indigo"
        :cameraPosition="[5, 5, 5]"
        :cameraLook="[0, 0, 0]"
        orthographic
    >
        <CurvedBox :points="points" :length="8" closed />
    </Lunchbox>
</template>

<script setup lang="ts">
import CurvedBox from './CurvedBox.vue'

const scale = 3
const points = [
    [1, 0, -1],
    [1, 0, 1],
    [-1, 0, 1],
    [-1, 0, -1],
].map((v) => v.map((p) => p * scale))
</script>