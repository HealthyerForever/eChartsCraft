<script setup>
const props = defineProps({
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
</script>

<template>
  <el-form-item label="饼图半径">
    <el-input 
      v-model="modelValue.radius" 
      @change="updateConfig('radius', $event)"
      placeholder="例如: 50% 或 ['40%', '70%']" 
    />
  </el-form-item>

  <el-form-item label="玫瑰图模式">
    <el-select 
      v-model="modelValue.roseType" 
      @change="updateConfig('roseType', $event)"
    >
      <el-option label="无" value="" />
      <el-option label="半径玫瑰图" value="radius" />
      <el-option label="面积玫瑰图" value="area" />
    </el-select>
  </el-form-item>

  <el-form-item label="显示百分比">
    <el-switch 
      v-model="modelValue.showPercent" 
      @change="updateConfig('showPercent', $event)"
    />
  </el-form-item>

  <el-form-item label="标签位置" v-if="modelValue.showPercent">
    <el-select 
      v-model="modelValue.labelPosition" 
      @change="updateConfig('labelPosition', $event)"
    >
      <el-option label="内部" value="inside" />
      <el-option label="外部" value="outside" />
      <el-option label="中心" value="center" />
    </el-select>
  </el-form-item>
</template>

<style scoped>
/* 可以添加饼图特有的样式 */
</style>