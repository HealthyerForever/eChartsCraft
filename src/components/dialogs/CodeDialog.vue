<script setup>
import { ref, watch, computed } from 'vue'
import { ElMessage } from 'element-plus'

const props = defineProps({
  modelValue: String, // 代码内容
  title: { type: String, default: '自定义代码' },
  placeholder: { type: String, default: 'return {...}' },
  visible: Boolean
})
const emit = defineEmits(['update:modelValue', 'update:visible', 'valid'])

const code = ref(props.modelValue)

watch(() => props.modelValue, val => { code.value = val })
watch(code, val => emit('update:modelValue', val))

const dialogVisible = computed({
  get: () => props.visible,
  set: v => emit('update:visible', v)
})

const handleOk = () => {
  try {
    new Function(code.value)()
    emit('valid', code.value)
    dialogVisible.value = false
  } catch (e) {
    ElMessage.error('代码有误，请检查！')
  }
}
</script>

<template>
  <el-dialog v-model="dialogVisible" :title="title" width="500px" draggable="true" center="true">
    <el-input
      type="textarea"
      v-model="code"
      :rows="6"
      :placeholder="placeholder"
    />
    <template #footer>
      <el-button type="primary" @click="handleOk">确定</el-button>
    </template>
  </el-dialog>
</template>