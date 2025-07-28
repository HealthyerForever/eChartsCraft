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
  <el-form-item label="点大小">
    <el-slider 
      v-model="modelValue.symbolSize" 
      :min="5" 
      :max="50" 
      @change="updateConfig('symbolSize', $event)"
    />
  </el-form-item>

  <el-form-item label="显示涟漪效果">
    <el-switch 
      v-model="modelValue.rippleEffect" 
      @change="updateConfig('rippleEffect', $event)"
    />
  </el-form-item>

  <template v-if="modelValue.rippleEffect">
    <el-form-item label="涟漪大小">
      <el-slider 
        v-model="modelValue.rippleSize" 
        :min="5" 
        :max="30" 
        @change="updateConfig('rippleSize', $event)"
      />
    </el-form-item>
    
    <el-form-item label="涟漪颜色">
      <el-color-picker 
        v-model="modelValue.rippleColor" 
        @change="updateConfig('rippleColor', $event)"
        show-alpha
      />
    </el-form-item>
  </template>

  <el-form-item label="点形状">
    <el-select 
      v-model="modelValue.symbol" 
      @change="updateConfig('symbol', $event)"
    >
      <el-option label="圆形" value="circle" />
      <el-option label="矩形" value="rect" />
      <el-option label="三角形" value="triangle" />
      <el-option label="菱形" value="diamond" />
    </el-select>
  </el-form-item>
</template>

<style scoped>
/* 可以添加散点图特有的样式 */
</style>