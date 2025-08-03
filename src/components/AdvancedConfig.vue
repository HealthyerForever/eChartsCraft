<script setup>
import { computed } from 'vue'
import LineConfig from '@/components/chart-configs/LineConfig.vue'
import BarConfig from '@/components/chart-configs/BarConfig.vue'
import PieConfig from '@/components/chart-configs/PieConfig.vue'
import ScatterConfig from '@/components/chart-configs/ScatterConfig.vue'

const props = defineProps({
  chartType: {
    type: String,
    default: 'line'
  },
  modelValue: {  
    type: Object,
    required: true
  }
})

const emit = defineEmits(['update:modelValue'])

const updateConfig = (key, value) => {
  emit('update:modelValue', {
    ...props.modelValue,
    [key]: value
  })
}
// 动态组件映射
const chartComponents = {
  line: LineConfig,
  bar: BarConfig,
  pie: PieConfig,
  scatter: ScatterConfig
}

// 当前图表配置组件
const currentConfigComponent = computed(() => chartComponents[props.chartType] || null)
</script>

<template>
  <div class="advanced-config">
    <el-form label-width="120px">
      <!-- 动态加载图表特有配置 -->
      <component :is="currentConfigComponent" :model-value="modelValue"
        @update:modelValue="$emit('update:modelValue', $event)" />
    </el-form>
  </div>
</template>