<script setup>
import { computed, reactive } from 'vue'

const props = defineProps({
  chartType: {
    type: String,
    default: 'line'
  }
})

const emit = defineEmits(['update'])

const isPieChart = computed(() => props.chartType === 'pie')

const config = reactive({
  // 通用配置
  stack: false,
  showLabel: false,
  labelPosition: 'top',
  
  // 折线图配置
  smooth: false,
  areaStyle: false,
  areaOpacity: 0.4,
  
  // 饼图配置
  radius: '75%',
  roseType: '',
  showPercent: true,
  
  // 柱状图配置
  barWidth: 'auto',
  barBorderRadius: 0,
  
  // 散点图配置
  symbolSize: 10,
  rippleEffect: false
})

const updateConfig = () => {
  emit('update', { ...config })
}

// 初始化时触发一次更新
updateConfig()
</script>

<template>
  <div class="advanced-config">
    <el-form label-width="120px">
      <!-- 通用高级配置 -->
      <template v-if="!isPieChart">
        <el-form-item label="堆叠显示">
          <el-switch v-model="config.stack" @change="updateConfig" />
        </el-form-item>
        
        <el-form-item label="显示数据标签">
          <el-switch v-model="config.showLabel" @change="updateConfig" />
        </el-form-item>
        
        <el-form-item label="标签位置">
          <el-select 
            v-model="config.labelPosition" 
            :disabled="!config.showLabel"
            @change="updateConfig"
          >
            <el-option label="内部" value="inside" />
            <el-option label="顶部" value="top" />
            <el-option label="右侧" value="right" />
            <el-option label="左侧" value="left" />
            <el-option label="底部" value="bottom" />
          </el-select>
        </el-form-item>
      </template>
      
      <!-- 折线图特有配置 -->
      <template v-if="chartType === 'line'">
        <el-form-item label="平滑曲线">
          <el-switch v-model="config.smooth" @change="updateConfig" />
        </el-form-item>
        
        <el-form-item label="面积图">
          <el-switch v-model="config.areaStyle" @change="updateConfig" />
        </el-form-item>
        
        <el-form-item label="面积图透明度" v-if="config.areaStyle">
          <el-slider 
            v-model="config.areaOpacity" 
            :min="0" 
            :max="1" 
            :step="0.1"
            @change="updateConfig"
          />
        </el-form-item>
      </template>
      
      <!-- 饼图特有配置 -->
      <template v-if="isPieChart">
        <el-form-item label="饼图半径">
          <el-input 
            v-model="config.radius" 
            placeholder="例如: 50% 或 ['40%', '70%']"
            @change="updateConfig"
          />
        </el-form-item>
        
        <el-form-item label="玫瑰图模式">
          <el-select v-model="config.roseType" @change="updateConfig">
            <el-option label="无" value="" />
            <el-option label="半径玫瑰图" value="radius" />
            <el-option label="面积玫瑰图" value="area" />
          </el-select>
        </el-form-item>
        
        <el-form-item label="显示百分比">
          <el-switch v-model="config.showPercent" @change="updateConfig" />
        </el-form-item>
      </template>
      
      <!-- 柱状图特有配置 -->
      <template v-if="chartType === 'bar'">
        <el-form-item label="柱条宽度">
          <el-input 
            v-model="config.barWidth" 
            placeholder="例如: 20 或 30%"
            @change="updateConfig"
          />
        </el-form-item>
        
        <el-form-item label="柱条圆角">
          <el-slider 
            v-model="config.barBorderRadius" 
            :min="0" 
            :max="20"
            @change="updateConfig"
          />
        </el-form-item>
      </template>
      
      <!-- 散点图特有配置 -->
      <template v-if="chartType === 'scatter'">
        <el-form-item label="点大小">
          <el-slider 
            v-model="config.symbolSize" 
            :min="5" 
            :max="50"
            @change="updateConfig"
          />
        </el-form-item>
        
        <el-form-item label="显示涟漪效果">
          <el-switch v-model="config.rippleEffect" @change="updateConfig" />
        </el-form-item>
      </template>
    </el-form>
  </div>
</template>

<style scoped>
.advanced-config {
  padding: 10px;
}

.el-form-item {
  margin-bottom: 18px;
}

.el-slider {
  width: 80%;
}
</style>