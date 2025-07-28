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
  modelValue: {  // 使用v-model接收配置
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
      <!-- 通用配置 -->
      <template v-if="chartType !== 'pie'">
        <el-form-item label="堆叠显示">
          <el-switch v-model="modelValue.stack" @change="updateConfig('stack', $event)" />
        </el-form-item>

        <el-form-item label="显示数据标签">
          <el-switch v-model="modelValue.showLabel" @change="updateConfig('showLabel', $event)" />
        </el-form-item>

        <el-form-item label="标签位置" v-if="modelValue.showLabel">
          <el-select v-model="modelValue.labelPosition" @change="updateConfig('labelPosition', $event)">
            <el-option label="内部" value="inside" />
            <el-option label="顶部" value="top" />
            <el-option label="右侧" value="right" />
            <el-option label="左侧" value="left" />
            <el-option label="底部" value="bottom" />
          </el-select>
        </el-form-item>
      </template>

      <!-- 动态加载图表特有配置 -->
      <component :is="currentConfigComponent" :model-value="modelValue"
        @update:modelValue="$emit('update:modelValue', $event)" />
    </el-form>
  </div>
</template>