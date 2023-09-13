<template>
  <div class="big__screen__page">123</div>
</template>

<script setup lang="ts">
import { getParkInfo } from '@/api/park'
import { ref, onMounted } from 'vue'
import type { ParkResponseType } from '@/types/park'
const parkInfo = ref<ParkResponseType>()
const initParkInfo = async () => {
  const parkRes = await getParkInfo()
  console.log('parkRes', parkRes)
  parkInfo.value = parkRes.data
}
onMounted(() => {
  initParkInfo()
})
</script>

<style lang="scss" scoped></style>
