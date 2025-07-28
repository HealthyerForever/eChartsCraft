<script setup>
import * as echarts from 'echarts'
import 'echarts/theme/dark.js'
import { onMounted, onBeforeUnmount, ref, watch } from 'vue'

const props = defineProps({
  option: {
    type: Object,
    required: true
  },
  width: {
    type: String,
    default: '100%'
  },
  height: {
    type: String,
    default: '400px'
  },
  theme: {
    type: String,
    default: ''
  }
})

const chartRef = ref(null)
let chartInstance = null

// 初始化图表
const initChart = () => {
  if (chartRef.value) {
    if (chartInstance) {
      chartInstance.dispose()
    }
    chartInstance = echarts.init(chartRef.value, props.theme)
    chartInstance.setOption(props.option)
  }
}

// 更新图表
const updateChart = () => {
  if (chartInstance) {
    chartInstance.setOption(props.option, true)
  }
}

// 响应式更新
watch(() => props.option, updateChart, { deep: true })
watch(
  () => props.theme,
  (newTheme) => {
    if (chartInstance) {
      chartInstance.dispose()
      chartInstance = null
    }
    initChart()
  }
)

// 窗口大小变化时重绘
const handleResize = () => {
  chartInstance?.resize()
}

onMounted(() => {
  initChart()
  window.addEventListener('resize', handleResize)
})

onBeforeUnmount(() => {
  window.removeEventListener('resize', handleResize)
  chartInstance?.dispose()
})
</script>

<template>
  <div ref="chartRef" :style="{ width: props.width, height: props.height }"></div>
</template>

<style scoped>

</style>