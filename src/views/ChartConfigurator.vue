<script setup>
import { ref, reactive } from 'vue'
import BaseChart from '@/components/BaseChart.vue'
import DataConfig from '@/components/DataConfig.vue'
import CommonConfig from '@/components/CommonConfig.vue'
import AdvancedConfig from '@/components/AdvancedConfig.vue'
import { ElMessage } from 'element-plus'
import { DocumentCopy } from '@element-plus/icons-vue'
import { useClipboard } from '@vueuse/core'

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
  // #region 折线图配置
  line: {
    // 基础配置
    smooth: false,
    stack: false,
    xAxisIndex: 0,
    yAxisIndex: 0,
    step: false,
    cursor: 'pointer',

    // 面积样式
    areaStyleShow: false,
    areaOpacity: 0.4,
    areaColor: '#fff',
    areaStyleOrigin: 'start',

    // 标签样式
    showLabel: false,
    labelPosition: 'top',
    labelColor: '#000',
    labelFontSize: 12,
    label: {},

    // 线条样式
    lineStyleWidth: 2,
    lineStyleColor: '#409EFF',
    lineStyleType: 'solid',
    lineStyle: {
      color: '#409EFF',
      width: 2,
      type: 'solid'
    },

    // 图形样式
    itemSymbol: 'circle',
    itemSymbolSize: 8,
    itemStyleColor: '#409EFF',
    itemStyle: {
      color: '#409EFF',
      borderWidth: 2,
      borderType: 'solid'
    },

    // 高亮样式
    emphasisDisabled: false,
    emphasisFocus: 'none',
    emphasis: {},

    // 淡出样式
    blur: {},

    // 辅助标记
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
  },
  // #endregion

  // #region 柱状图配置
  bar: {
    // 基础配置
    xAxisIndex: 0,
    yAxisIndex: 0,
    cursor: 'pointer',
    stack: false,
    barWidth: 'auto',
    barBorderRadius: 0,

    // 标签样式
    showLabel: false,
    labelPosition: 'top',
    labelColor: '#000',
    labelFontSize: 12,
    label: {},

    // 背景样式
    showBackground: false,
    backgroundColor: 'rgba(0, 0, 0, 0.1)',
    backgroundOpacity: 1,
    backgroundStyle: {},

    // 图形样式
    itemStyleColor: '#409EFF',
    itemStyleOpacity: 1,
    itemStyle: {
      color: '#409EFF',
      borderWidth: 2,
      borderType: 'solid'
    },

    // 高亮样式
    emphasisDisabled: false,
    emphasisFocus: 'none',
    emphasis: {},

    // 淡出样式
    blur: {},

    // 辅助标记
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
  },
  // #endregion

  // #region 饼图配置
  pie: {
    // 基础配置
    innerRadius: 0,
    outerRadius: 75,
    startAngle: 0,
    endAngle: 360,
    clockwise: true,
    padAngle: 0,
    percentPrecision: 2,
    roseType: '',
    centerX: 50,
    centerY: 50,
    cursor: 'pointer',

    // 标签样式
    showLabel: true,
    labelPosition: 'outside',
    labelColor: '#000',
    labelFontSize: 12,
    label: {},

    // 标签线样式
    showLabelLine: true,
    labelLineLength: 10,
    labelLineLength2: 20,
    labelLineSmooth: 0,
    labelLine: {},

    // 图形样式
    itemStyleColor: '',
    itemStyleOpacity: 1,
    itemStyle: {
      borderWidth: 2,
      borderType: 'solid'
    },

    // 高亮样式
    emphasisDisabled: false,
    emphasisFocus: 'none',
    emphasis: {},

    // 淡出样式
    blur: {},

    // 辅助标记
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
  },
  // #endregion

  // #region 散点图配置
  scatter: {
    // 基础配置
    stack: false,
    xAxisIndex: 0,
    yAxisIndex: 0,
    step: false,
    cursor: 'pointer',

    // 标签样式
    showLabel: false,
    labelPosition: 'top',
    labelColor: '#000',
    labelFontSize: 12,
    label: {},

    // 图形样式
    itemSymbol: 'circle',
    itemSymbolSize: 8,
    itemStyleColor: '#409EFF',
    itemStyle: {
      color: '#409EFF',
      borderWidth: 2,
      borderType: 'solid'
    },

    // 高亮样式
    emphasisDisabled: false,
    emphasisFocus: 'none',
    emphasis: {},

    // 淡出样式
    blur: {},

    // 辅助标记
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
  },
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
  Object.assign(advancedConfig[chartType.value], advanced)
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
        smooth: advancedConfig.line.smooth,
        stack: advancedConfig.line.stack ? 'total' : undefined,
        xAxisIndex: advancedConfig.line.xAxisIndex,
        yAxisIndex: advancedConfig.line.yAxisIndex,
        step: advancedConfig.line.step,
        cursor: advancedConfig.line.cursor,
        symbol: advancedConfig.line.itemSymbol,
        symbolSize: advancedConfig.line.itemSymbolSize,
        label: {
          ...advancedConfig.line.label,
          show: advancedConfig.line.showLabel,
          position: advancedConfig.line.labelPosition || 'top',
          color: advancedConfig.line.labelColor,
          fontSize: advancedConfig.line.labelFontSize
        },
        areaStyle: advancedConfig.line.areaStyleShow ? {
          color: advancedConfig.line.areaColor,
          opacity: advancedConfig.line.areaOpacity,
          origin: advancedConfig.line.areaStyleOrigin,
          shadowBlur: advancedConfig.line.shadowBlur,
          shadowColor: advancedConfig.line.shadowColor,
          shadowOffsetX: advancedConfig.line.shadowOffsetX,
          shadowOffsetY: advancedConfig.line.shadowOffsetY
        } : undefined,
        lineStyle: {
          ...advancedConfig.line.lineStyle,
          color: advancedConfig.line.lineStyleColor,
          width: advancedConfig.line.lineStyleWidth,
          type: advancedConfig.line.lineStyleType
        },
        itemStyle: {
          ...advancedConfig.line.itemStyle,
          color: advancedConfig.line.itemStyleColor,
        },
        emphasis: {
          ...advancedConfig.line.emphasis,
          disabled: advancedConfig.line.emphasisDisabled,
          focus: advancedConfig.line.emphasisFocus
        },
        blur: advancedConfig.line.blur,
        markPoint: advancedConfig.line.markPointShow ? {
          ...advancedConfig.line.markPoint,
          symbol: advancedConfig.line.markPointSymbol,
          symbolSize: advancedConfig.line.markPointSymbolSize,
        } : undefined,
        markLine: advancedConfig.line.markLineShow ? advancedConfig.line.markLine : undefined,
        markArea: advancedConfig.line.markAreaShow ? advancedConfig.line.markArea : undefined
      }))
      break
    case 'bar':
      option.xAxis = { ...baseXAxis, type: 'category', data: chartData.categories }
      option.yAxis = { ...baseYAxis, type: 'value' }
      option.series = chartData.series.map(series => ({
        ...series,
        type: 'bar',
        stack: advancedConfig.stack ? 'total' : undefined,
        xAxisIndex: advancedConfig.bar.xAxisIndex,
        yAxisIndex: advancedConfig.bar.yAxisIndex,
        cursor: advancedConfig.bar.cursor,
        barWidth: advancedConfig.bar.barWidth,
        barGap: advancedConfig.bar.barGap,
        showBackground: advancedConfig.bar.showBackground,
        backgroundStyle: {
          ...advancedConfig.bar.backgroundStyle,
          color: advancedConfig.bar.backgroundColor,
          opacity: advancedConfig.bar.backgroundOpacity,
        },
        label: {
          ...advancedConfig.bar.label,
          show: advancedConfig.bar.showLabel,
          position: advancedConfig.bar.labelPosition || 'top',
          color: advancedConfig.bar.labelColor,
          fontSize: advancedConfig.bar.labelFontSize
        },
        itemStyle: {
          ...advancedConfig.bar.itemStyle,
          color: advancedConfig.bar.itemStyleColor,
          opacity: advancedConfig.bar.itemStyleOpacity,
        },
        emphasis: {
          ...advancedConfig.bar.emphasis,
          disabled: advancedConfig.bar.emphasisDisabled,
          focus: advancedConfig.bar.emphasisFocus
        },
        blur: advancedConfig.bar.blur,
        markPoint: advancedConfig.bar.markPointShow ? {
          ...advancedConfig.bar.markPoint,
          symbol: advancedConfig.bar.markPointSymbol,
          symbolSize: advancedConfig.bar.markPointSymbolSize,
        } : undefined,
        markLine: advancedConfig.bar.markLineShow ? advancedConfig.bar.markLine : undefined,
        markArea: advancedConfig.bar.markAreaShow ? advancedConfig.bar.markArea : undefined
      }))
      break
    case 'pie':
      option.series = [{
        type: 'pie',
        data: [
          { value: 335, name: '直接访问' },
          { value: 310, name: '邮件营销' },
          { value: 234, name: '联盟广告' },
          { value: 135, name: '视频广告' },
          { value: 1548, name: '搜索引擎' }
        ],
        data: chartData.series[0].data.map((m, index) => ({ value: m, name: chartData.categories[index] })),
        radius: [advancedConfig.pie.innerRadius + '%', advancedConfig.pie.outerRadius + '%'],
        roseType: advancedConfig.pie.roseType,
        startAngle: advancedConfig.pie.startAngle,
        endAngle: advancedConfig.pie.endAngle,
        clockwise: advancedConfig.pie.clockwise,
        padAngle: advancedConfig.pie.padAngle,
        percentPrecision: advancedConfig.pie.percentPrecision,
        cursor: advancedConfig.pie.cursor,
        center: [advancedConfig.pie.centerX + '%', advancedConfig.pie.centerY + '%'],
        label: {
          ...advancedConfig.pie.label,
          show: advancedConfig.pie.showLabel,
          position: advancedConfig.pie.labelPosition || 'top',
          color: advancedConfig.pie.labelColor,
          fontSize: advancedConfig.pie.labelFontSize
        },
        labelLine: {
          ...advancedConfig.pie.labelLine,
          show: advancedConfig.pie.showLabelLine,
          length: advancedConfig.pie.labelLineLength,
          length2: advancedConfig.pie.labelLineLength2,
          smooth: advancedConfig.pie.labelLineSmooth
        },
        itemStyle: {
          ...advancedConfig.pie.itemStyle,
          color: advancedConfig.pie.itemStyleColor,
          opacity: advancedConfig.pie.itemStyleOpacity,
        },
        emphasis: {
          ...advancedConfig.pie.emphasis,
          disabled: advancedConfig.pie.emphasisDisabled,
          focus: advancedConfig.pie.emphasisFocus
        },
        blur: advancedConfig.pie.blur,
        markPoint: advancedConfig.pie.markPointShow ? {
          ...advancedConfig.pie.markPoint,
          symbol: advancedConfig.pie.markPointSymbol,
          symbolSize: advancedConfig.pie.markPointSymbolSize,
        } : undefined,
        markLine: advancedConfig.pie.markLineShow ? advancedConfig.pie.markLine : undefined,
        markArea: advancedConfig.pie.markAreaShow ? advancedConfig.pie.markArea : undefined
      }]
      break
    case 'scatter':
      option.xAxis = { ...baseXAxis, type: 'category', data: chartData.categories }
      option.yAxis = { ...baseYAxis, type: 'value' }
      option.series = chartData.series.map(series => ({
        ...series,
        type: 'scatter',
        stack: advancedConfig.scatter.stack ? 'total' : undefined,
        xAxisIndex: advancedConfig.scatter.xAxisIndex,
        yAxisIndex: advancedConfig.scatter.yAxisIndex,
        cursor: advancedConfig.scatter.cursor,
        symbol: advancedConfig.scatter.itemSymbol,
        symbolSize: advancedConfig.scatter.itemSymbolSize,
        label: {
          ...advancedConfig.scatter.label,
          show: advancedConfig.scatter.showLabel,
          position: advancedConfig.scatter.labelPosition || 'top',
          color: advancedConfig.scatter.labelColor,
          fontSize: advancedConfig.scatter.labelFontSize
        },
        itemStyle: {
          ...advancedConfig.scatter.itemStyle,
          color: advancedConfig.scatter.itemStyleColor,
        },
        emphasis: {
          ...advancedConfig.scatter.emphasis,
          disabled: advancedConfig.scatter.emphasisDisabled,
          focus: advancedConfig.scatter.emphasisFocus
        },
        blur: advancedConfig.scatter.blur,
        markPoint: advancedConfig.scatter.markPointShow ? {
          ...advancedConfig.scatter.markPoint,
          symbol: advancedConfig.scatter.markPointSymbol,
          symbolSize: advancedConfig.scatter.markPointSymbolSize,
        } : undefined,
        markLine: advancedConfig.scatter.markLineShow ? advancedConfig.scatter.markLine : undefined,
        markArea: advancedConfig.scatter.markAreaShow ? advancedConfig.scatter.markArea : undefined
      }))
      break
  }
  //console.log(chartData)
  currentOption.value = option
}

const { copy } = useClipboard()
const copyConfig = () => {
  const configStr = JSON.stringify(currentOption.value, null, 2)
  copy(configStr).then(() => {
    ElMessage.success('配置已复制到剪贴板')
  })
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
        <AdvancedConfig :chart-type="chartType" :model-value="advancedConfig[chartType]"
          @update:modelValue="handleAdvancedUpdate" />
      </el-tab-pane>

      <el-tab-pane label="当前配置" name="currentConfig">
        <div class="config-viewer">
          <pre>{{ currentOption }}</pre>
          <el-button type="text" :icon="DocumentCopy" @click="copyConfig" class="icon-only-btn" />
        </div>
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

.config-viewer {
  position: relative;
  background: #f5f7fa;
  padding: 12px;
  border-radius: 4px;
  max-height: 600px;
  overflow: auto;
}

.config-viewer pre {
  margin: 0;
  white-space: pre-wrap;
  word-wrap: break-word;
  font-family: cursive;
  font-size: 14px;
}

.icon-only-btn {
  position: absolute;
  font-size: 18px;
  top: 0px;
  right: 10px;
  border: none;
  background: transparent;
  color: inherit;
}

.icon-only-btn:hover {
  color: #409eff;
  background: transparent !important;
}
</style>