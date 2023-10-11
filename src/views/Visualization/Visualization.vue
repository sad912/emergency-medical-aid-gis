<script setup lang="ts">
import { Viewer } from 'cesium'
import { onMounted } from 'vue'
onMounted(() => {
  new Viewer('cesiumContainer')
})
</script>

<template>
  <div id="cesiumContainer" ref="cesiumContainer"></div>
</template>
