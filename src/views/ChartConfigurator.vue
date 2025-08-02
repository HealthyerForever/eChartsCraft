<script setup>
import { ref, reactive } from 'vue'
import BaseChart from '@/components/BaseChart.vue'
import DataConfig from '@/components/DataConfig.vue'
import CommonConfig from '@/components/CommonConfig.vue'
import AdvancedConfig from '@/components/AdvancedConfig.vue'

const activeTab = ref('type')
const chartType = ref('line')

const chartTypes = [
  { value: 'line', label: '折线图' },
  { value: 'bar', label: '柱状图' },
  { value: 'pie', label: '饼图' },
  { value: 'scatter', label: '散点图' }
]

const chartData = reactive({
  categories: [],
  series: []
})

const commonConfig = reactive({

})

const advancedConfig = reactive({
  // #region 通用配置
  stack: false,
  showLabel: false,
  labelPosition: 'top',
  // #endregion

  // #region 折线图配置
  smooth: false,
  areaStyleShow: false,
  areaOpacity: 0.4,
  areaColor: '#fff',
  areaStyleOrigin: 'start',

  lineStyle: {
    color: '#409EFF',
    width: 2,
    type: 'solid'
  },
  itemSymbol: 'circle',
  itemSymbolSize: 4,
  itemStyle: {
    color: '#409EFF',
    borderWidth: 2,
    borderType: 'solid'
  },
  xAxisIndex: 0,
  yAxisIndex: 0,
  step: false,
  cursor: 'pointer',
  markPointShow: false,
  markPoint: {
    data: []
  },
  markPointSymbol: 'pin',
  markPointSymbolSize: 50,
  markLineShow: false,
  markLine: {
    data: []
  },  
  markAreaShow: false,
  markArea: {
    data: []
  },
  // #endregion

  // #region 柱状图配置
  barWidth: 'auto',
  barBorderRadius: 0,
  // #endregion

  // #region 饼图配置
  radius: '75%',
  roseType: '',
  showPercent: true,
  // #endregion

  // #region 散点图配置
  symbolSize: 10,
  rippleEffect: false
  // #endregion
})

const currentOption = ref({})

// 处理各配置组件的更新
const handleDataUpdate = (data) => {
  Object.assign(chartData, data)
  generateOption()
}

const handleCommonUpdate = (common) => {
  Object.assign(commonConfig, common)
  generateOption()
}

const handleAdvancedUpdate = (advanced) => {
  Object.assign(advancedConfig, advanced)
  generateOption()
}

// 生成最终的echarts配置
const generateOption = () => {
  const baseXAxis = {
    show: commonConfig.xAxisShow,
    type: commonConfig.xAxisType,
    data: chartData.categories,
    name: commonConfig.xAxisTitle,
    position: commonConfig.xAxisPosition,
    min: commonConfig.xAxisMin,
    max: commonConfig.xAxisMax,
    axisLabel: commonConfig.xAxisLabelStyle,
    inverse: commonConfig.xAxisReverse,
    nameTextStyle: commonConfig.xAxisNameTextStyle,
    axisLine: commonConfig.xAxisLineStyle,
    axisTick: commonConfig.xAxisTickStyle,
    splitLine: commonConfig.xAxisSplitLineStyle
  }

  const baseYAxis = {
    show: commonConfig.yAxisShow,
    type: commonConfig.yAxisType,
    name: commonConfig.yAxisTitle,
    position: commonConfig.yAxisPosition,
    min: commonConfig.yAxisMin,
    max: commonConfig.yAxisMax,
    axisLabel: commonConfig.yAxisLabelStyle,
    inverse: commonConfig.yAxisReverse,
    nameTextStyle: commonConfig.yAxisNameTextStyle,
    axisLine: commonConfig.yAxisLineStyle,
    axisTick: commonConfig.yAxisTickStyle,
    splitLine: commonConfig.yAxisSplitLineStyle
  }

  const option = {
    title: {
      show: commonConfig.titleShow,
      text: commonConfig.title,
      subtext: commonConfig.subtext,
      subtextStyle: commonConfig.subtextStyle,
      textStyle: commonConfig.textStyle,
      left: commonConfig.titleAlign,
      top: commonConfig.titleTop,
      bottom: commonConfig.titleBottom,
      padding: commonConfig.titlePadding,
      backgroundColor: commonConfig.titleBackgroundColor || 'transparent',
      borderColor: commonConfig.titleBorderColor || 'transparent',
      borderWidth: commonConfig.titleBorderWidth,
      borderRadius: commonConfig.titleBorderRadius
    },
    tooltip: {
      show: commonConfig.tooltipShow,
      trigger: commonConfig.tooltipTrigger,
      triggerOn: commonConfig.tooltipTriggerOn,
      backgroundColor: commonConfig.tooltipBackgroundColor,
      borderColor: commonConfig.tooltipBorderColor,
      borderWidth: commonConfig.tooltipBorderWidth,
      padding: commonConfig.tooltipPadding,
      textStyle: commonConfig.tooltipTextStyle,
      formatter: commonConfig.tooltipFormatter,
    },
    legend: {
      show: commonConfig.legendShow,
      data: chartData.series.map(s => s.name),
      orient: commonConfig.legendPosition === 'left' || commonConfig.legendPosition === 'right' ? 'vertical' : 'horizontal',
      [commonConfig.legendPosition]: 0,
      backgroundColor: commonConfig.legendBackgroundColor,
      borderColor: commonConfig.legendBorderColor,
      borderWidth: commonConfig.legendBorderWidth,
      itemWidth: commonConfig.legendWidth,
      itemHeight: commonConfig.legendHeight,
      align: commonConfig.legendAlign,
      padding: commonConfig.legendPadding,
      itemGap: commonConfig.legendItemGap,
      textStyle: commonConfig.legendTextStyle,
      lineStyle: commonConfig.legendLineStyle,
      itemStyle: commonConfig.legendItemStyle
    },
    grid: {
      show: commonConfig.gridShow,
      width: commonConfig.gridWidth,
      height: commonConfig.gridHeight,
      borderColor: commonConfig.gridBorderColor,
      borderWidth: commonConfig.gridBorderWidth,
      backgroundColor: commonConfig.gridBackgroundColor
    },
    axisPointer: {
      show: commonConfig.axisPointerShow,
      type: commonConfig.axisPointerType,
      triggerOn: commonConfig.axisPointerTriggerOn,
      label: commonConfig.axisPointerLabelStyle,
      lineStyle: commonConfig.axisPointerLineStyle,
      shadowStyle: commonConfig.axisPointerShadowStyle,
      handle: commonConfig.axisPointerHandleStyle
    },
    toolbox: {
      show: commonConfig.toolboxShow,
      feature: commonConfig.toolboxFeatures,
      orient: commonConfig.toolboxOrient,
      left: commonConfig.toolboxAlign,
      top: commonConfig.toolboxTop,
      bottom: commonConfig.toolboxBottom,
      iconStyle: commonConfig.toolboxIconStyle,
      itemGap: commonConfig.toolboxItemGap,
      itemSize: commonConfig.toolboxItemSize,
    },
    backgroundColor: commonConfig.backgroundColor,
  }

  switch (chartType.value) {
    case 'line':
      option.xAxis = { ...baseXAxis, type: 'category', data: chartData.categories }
      option.yAxis = { ...baseYAxis, type: 'value' }
      option.series = chartData.series.map(series => ({
        ...series,
        type: 'line',
        smooth: advancedConfig.smooth,
        stack: advancedConfig.stack ? 'total' : undefined,
        xAxisIndex: advancedConfig.xAxisIndex,
        yAxisIndex: advancedConfig.yAxisIndex,
        step: advancedConfig.step,
        cursor: advancedConfig.cursor,
        symbol: advancedConfig.itemSymbol,
        symbolSize: advancedConfig.itemSymbolSize,
        label: {
          show: advancedConfig.showLabel,
          position: advancedConfig.labelPosition || 'top'
        },
        areaStyle: advancedConfig.areaStyleShow ? {
          color: advancedConfig.areaColor,
          opacity: advancedConfig.areaOpacity,
          origin: advancedConfig.areaStyleOrigin,
          shadowBlur: advancedConfig.shadowBlur,
          shadowColor: advancedConfig.shadowColor,
          shadowOffsetX: advancedConfig.shadowOffsetX,
          shadowOffsetY: advancedConfig.shadowOffsetY
        } : undefined,
        lineStyle: {
          ...advancedConfig.lineStyle,
          color: advancedConfig.lineStyleColor,
          width: advancedConfig.lineStyleWidth,
          type: advancedConfig.lineStyleType
        },
        itemStyle: {
          ...advancedConfig.itemStyle,
          color: advancedConfig.itemStyleColor,
        },
        markPoint: advancedConfig.markPointShow ? {
          ...advancedConfig.markPoint,
          symbol: advancedConfig.markPointSymbol,
          symbolSize: advancedConfig.markPointSymbolSize,
        } : undefined,
        markLine: advancedConfig.markLineShow ? advancedConfig.markLine : undefined,
        markArea: advancedConfig.markAreaShow ? advancedConfig.markArea : undefined
      }))
      break
    case 'bar':
      option.xAxis = { ...baseXAxis, type: 'category', data: chartData.categories }
      option.yAxis = { ...baseYAxis, type: 'value' }
      option.series = chartData.series.map(series => ({
        ...series,
        type: 'bar',
        stack: advancedConfig.stack ? 'total' : undefined,
        barWidth: advancedConfig.barWidth,
        barBorderRadius: advancedConfig.barBorderRadius,
        label: {
          show: advancedConfig.showLabel,
          position: advancedConfig.labelPosition || 'top'
        }
      }))
      break
    case 'pie':
      option.series = [{
        type: 'pie',
        data: chartData.series[0]?.data || [],
        radius: advancedConfig.radius,
        roseType: advancedConfig.roseType,
        label: {
          show: advancedConfig.showLabel,
          position: advancedConfig.labelPosition || 'top',
          formatter: advancedConfig.showPercent ? '{b}: {c} ({d}%)' : '{b}: {c}'
        }
      }]
      break
    case 'scatter':
      option.xAxis = { ...baseXAxis, type: 'category', data: chartData.categories }
      option.yAxis = { ...baseYAxis, type: 'value' }
      option.series = chartData.series.map(series => ({
        ...series,
        type: 'scatter',
        symbolSize: advancedConfig.symbolSize,
        rippleEffect: advancedConfig.rippleEffect,
        label: {
          show: advancedConfig.showLabel,
          position: advancedConfig.labelPosition || 'top'
        }
      }))
      break
  }

  currentOption.value = option
}

// 初始化默认数据
chartData.categories = ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']
chartData.series = [{
  name: '销量',
  data: [120, 200, 150, 80, 70, 110, 130]
}]
generateOption()
</script>

<template>
  <div class="chart-configurator">
    <el-tabs v-model="activeTab">
      <el-tab-pane label="图表类型" name="type">
        <el-select v-model="chartType" @change="generateOption">
          <el-option v-for="type in chartTypes" :key="type.value" :label="type.label" :value="type.value" />
        </el-select>
      </el-tab-pane>

      <el-tab-pane label="数据" name="data">
        <DataConfig :chart-type="chartType" @update="handleDataUpdate" />
      </el-tab-pane>

      <el-tab-pane label="基础" name="common">
        <CommonConfig @update="handleCommonUpdate" />
      </el-tab-pane>

      <el-tab-pane label="高级" name="advanced">
        <AdvancedConfig :chart-type="chartType" :model-value="advancedConfig"
          @update:modelValue="handleAdvancedUpdate" />
      </el-tab-pane>
    </el-tabs>

    <div class="chart-preview">
      <BaseChart :option="currentOption" :theme="commonConfig.darkMode ? 'dark' : 'light'" />
    </div>
  </div>
</template>

<style scoped>
.chart-configurator {
  padding: 20px;
  display: flex;
  gap: 40px;
}

.chart-preview {
  display: flex;
  align-items: center;
  justify-content: center;
  flex: 1;
  min-height: 600px;
}

:deep(.el-tabs) {
  width: 300px;
  height: 700px;
}
</style>