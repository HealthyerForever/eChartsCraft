<script setup>
import { ref, reactive } from 'vue'

const emit = defineEmits(['update'])

const categoriesStr = ref('Mon,Tue,Wed,Thu,Fri,Sat,Sun')
const seriesData = reactive([{
  name: '销量',
  dataStr: '120,200,150,80,70,110,130'
}])

const updateData = () => {
  const data = {
    categories: categoriesStr.value.split(',').map(item => item.trim()),
    series: seriesData.map(item => ({
      name: item.name,
      data: item.dataStr.split(',').map(Number)
    }))
  }
  emit('update', data)
}

const addSeries = () => {
  seriesData.push({
    name: '',
    dataStr: ''
  })
}

const removeSeries = (index) => {
  seriesData.splice(index, 1)
  updateData()
}

// 初始化时触发一次更新
updateData()
</script>

<template>
  <div class="data-config">
    <el-form label-width="80px">
      <el-form-item label="X轴数据">
        <el-input v-model="categoriesStr" @change="updateData" />
      </el-form-item>
      
      <el-form-item 
        v-for="(series, index) in seriesData" 
        :key="index" 
        :label="`系列${index + 1}`"
      >
        <el-input v-model="series.name" placeholder="系列名称" @input="updateData" />
        <el-input v-model="series.dataStr" @change="updateData" />
        <el-button @click="removeSeries(index)">删除</el-button>
      </el-form-item>
      
      <el-button @click="addSeries">添加系列</el-button>
    </el-form>
  </div>
</template>