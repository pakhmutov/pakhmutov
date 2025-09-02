<template>
  <main class="container">
    <InfoBlock />
  </main>
  <ProjectBoard />
</template>

<script setup lang="ts">
import { onMounted } from 'vue';
import InfoBlock from '../components/InfoBlock.vue'
import ProjectBoard from '../components/ProjectBoard.vue'
import { useThemeStore } from '../store'

const themeStore = useThemeStore()

onMounted(() => {
  themeStore.setTheme(localStorage.getItem('theme') || 'light')
})
</script>