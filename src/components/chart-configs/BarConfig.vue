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
  <el-form-item label="柱条宽度">
    <el-input 
      v-model="modelValue.barWidth" 
      @change="updateConfig('barWidth', $event)"
      placeholder="例如: 20 或 30%" 
    />
  </el-form-item>

  <el-form-item label="柱条圆角">
    <el-slider 
      v-model="modelValue.barBorderRadius" 
      :min="0" 
      :max="20" 
      @change="updateConfig('barBorderRadius', $event)"
    />
  </el-form-item>
</template>