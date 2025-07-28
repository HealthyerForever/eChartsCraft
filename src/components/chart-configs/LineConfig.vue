<script setup>
import { ref } from 'vue'
import { ArrowDown } from '@element-plus/icons-vue'

const props = defineProps({
  modelValue: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['update:modelValue'])

const showAreaConfig = ref(false)

// 处理配置更新
const updateConfig = (key, value) => {
  emit('update:modelValue', { 
    ...props.modelValue,
    [key]: value
  })
}

</script>

<template>
  <el-form-item label="平滑曲线">
    <el-switch 
      v-model="modelValue.smooth" 
      @change="updateConfig('smooth', $event)" 
    />
  </el-form-item>

  <div class="config-group">
    <div class="group-header" @click="showAreaConfig = !showAreaConfig">
      <el-form-item label="面积图">
        <el-switch 
          v-model="modelValue.areaStyle" 
          @click.stop 
          @click="showAreaConfig = modelValue.areaStyle"
          @change="updateConfig('areaStyle', $event)"
        />
      </el-form-item>
      <el-icon :class="['expand-icon', { 'rotate-180': showAreaConfig }]">
        <ArrowDown />
      </el-icon>
    </div>

    <el-collapse-transition>
      <div v-show="showAreaConfig && modelValue.areaStyle" class="group-content">
        <el-form-item label="面积颜色">
          <el-color-picker 
            v-model="modelValue.areaColor" 
            @change="updateConfig('areaColor', $event)"
            show-alpha 
          />
        </el-form-item>
        <el-form-item label="透明度">
          <el-slider 
            v-model="modelValue.areaOpacity" 
            :min="0" 
            :max="1" 
            :step="0.1" 
            @change="updateConfig('areaOpacity', $event)"
          />
        </el-form-item>
      </div>
    </el-collapse-transition>
  </div>
</template>

<style scoped>
/* 样式与之前保持一致 */
</style>