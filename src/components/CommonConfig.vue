<script setup>
import { reactive, watchEffect } from 'vue'

const emit = defineEmits(['update'])

const config = reactive({
  title: '我的图表',
  titleColor: '#333',
  backgroundColor: '',
  legendPosition: 'top'
})

const updateCommon = () => {
  emit('update', { ...config })
}

// 自动监听变化
watchEffect(updateCommon)
</script>

<template>
  <div class="style-config">
    <el-form label-width="100px">
      <el-form-item label="图表标题">
        <el-input v-model="config.title" @input="updateCommon" />
      </el-form-item>
      
      <el-form-item label="标题颜色">
        <el-color-picker v-model="config.titleColor" @change="updateCommon" />
      </el-form-item>
      
      <el-form-item label="背景颜色">
        <el-color-picker v-model="config.backgroundColor" @change="updateCommon" show-alpha />
      </el-form-item>
      
      <el-form-item label="图例位置">
        <el-select v-model="config.legendPosition" @change="updateCommon">
          <el-option label="顶部" value="top" />
          <el-option label="底部" value="bottom" />
          <el-option label="左侧" value="left" />
          <el-option label="右侧" value="right" />
        </el-select>
      </el-form-item>
    </el-form>
  </div>
</template>

<style scoped>
.style-config {
  padding: 10px;
}
</style>