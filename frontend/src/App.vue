<script setup lang="ts">
import { onMounted, ref } from 'vue'

const statusMessage = ref('API와 데이터베이스 연결을 확인하는 중입니다.')

onMounted(async () => {
  try {
    const response = await fetch('/api/health', { signal: AbortSignal.timeout(5000) })
    const health = await response.json()
    statusMessage.value = response.ok && health.status === 'UP'
      ? 'API와 데이터베이스가 연결됐습니다.'
      : 'API 또는 데이터베이스가 아직 준비되지 않았습니다.'
  } catch {
    statusMessage.value = 'API에 연결하지 못했습니다. 잠시 후 새로고침해 주세요.'
  }
})
</script>

<template>
  <main>
    <h1>GateShift</h1>
    <p role="status">{{ statusMessage }}</p>
  </main>
</template>

<style>
body {
  margin: 0;
  background: #f4f7fb;
  color: #10213b;
  font-family: system-ui, sans-serif;
}

main {
  max-width: 42rem;
  margin: 4rem auto;
  padding: 0 1.5rem;
}
</style>
