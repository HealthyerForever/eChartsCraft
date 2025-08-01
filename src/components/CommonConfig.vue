<script setup>
import { ref, reactive, watchEffect } from 'vue'
import CodeDialog from '@/components/dialogs/CodeDialog.vue'

const emit = defineEmits(['update'])

// 折叠控制
const showGroup = reactive({
  title: false,
  subtitle: false,
  legend: false,
  legendFont: false,
  grid: false,
  xAxis: false,
  yAxis: false,
})

// 代码弹窗相关
const showAxisLabelDialog = ref(false)
const axisLabelCode = ref('return { color: "#333", fontSize: 12 }')

// 配置对象
const config = reactive({
  // 标题配置
  titleShow: true,
  title: '标题',
  titleColor: '#333',
  titleFontSize: 20,
  titleFontWeight: 'normal',
  titleAlign: 'left',
  titlePadding: 10,
  subtext: '',
  subtitleColor: '#666',
  subtitleFontSize: 14,
  subtitleFontWeight: 'normal',

  // 图例配置
  legendShow: true,
  legendPosition: 'right',
  legendPadding: 5,
  legendItemGap: 10,
  legendWidth: 20,
  legendHeight: 10,
  legendAlign: 'auto',
  legendItemWidth: 25,
  legendBackgroundColor: '#fff',
  legendBorderColor: '#ccc',
  legendBorderWidth: 0,
  legendFontColor: '#333',
  legendFontSize: 12,
  legendFontWeight: 'normal',

  // 坐标系配置
  gridShow: false,
  gridWidth: null,
  gridHeight: null,
  gridBorderColor: '#ccc',
  gridBorderWidth: 1,
  gridBackgroundColor: 'transparent',

  // x坐标轴配置
  xAxisShow: true,
  xAxisTitle: '',
  xAxisPosition: 'bottom',
  xAxisType: 'category',
  xAxisMin: null,
  xAxisMax: null,
  xAxisReverse: false,
  xAxisLabelStyle: {},

  // y坐标轴配置
  yAxisShow: true,
  yAxisTitle: '',
  yAxisPosition: 'left',
  yAxisType: 'value',
  yAxisMin: null,
  yAxisMax: null,
  yAxisReverse: false,

  // 全局配置
  backgroundColor: '',
  darkMode: false
})

// 配置驱动表单项
const formGroups = [
  {
    key: 'title',
    label: '标题',
    show: () => config.titleShow,
    toggle: () => showGroup.title = !showGroup.title,
    sync: () => showGroup.title = config.titleShow,
    expanded: () => showGroup.title,
    items: [
      { type: 'switch', label: '标题', prop: 'titleShow' },
      { type: 'input', label: '图表标题', prop: 'title' },
      { type: 'input-number', label: '字体大小', prop: 'titleFontSize' },
      { type: 'color-picker', label: '标题颜色', prop: 'titleColor' },
      {
        type: 'select', label: '标题粗细', prop: 'titleFontWeight',
        options: [
          { label: '正常', value: 'normal' },
          { label: '粗体', value: 'bold' },
          { label: '细体', value: 'lighter' }
        ]
      },
      {
        type: 'select', label: '标题水平对齐', prop: 'titleAlign',
        options: [
          { label: '左对齐', value: 'left' },
          { label: '居中', value: 'center' },
          { label: '右对齐', value: 'right' }
        ]
      },
      { type: 'input-number', label: '内边距', prop: 'titlePadding' },
      // 副标题分组
      {
        type: 'group',
        label: '副标题',
        key: 'subtitle',
        show: () => true,
        toggle: () => showGroup.subtitle = !showGroup.subtitle,
        expanded: () => showGroup.subtitle,
        items: [
          { type: 'input', label: '副标题', prop: 'subtext' },
          { type: 'color-picker', label: '字体颜色', prop: 'subtitleColor' },
          { type: 'input-number', label: '字体大小', prop: 'subtitleFontSize' },
          {
            type: 'select', label: '字体粗细', prop: 'subtitleFontWeight',
            options: [
              { label: '正常', value: 'normal' },
              { label: '粗体', value: 'bold' },
              { label: '细体', value: 'lighter' }
            ]
          }
        ]
      }
    ]
  },
  {
    key: 'legend',
    label: '图例',
    show: () => config.legendShow,
    toggle: () => showGroup.legend = !showGroup.legend,
    sync: () => showGroup.legend = config.legendShow,
    expanded: () => showGroup.legend,
    items: [
      { type: 'switch', label: '图例', prop: 'legendShow' },
      {
        type: 'select', label: '图例位置', prop: 'legendPosition',
        options: [
          { label: '顶部', value: 'top' },
          { label: '底部', value: 'bottom' },
          { label: '左侧', value: 'left' },
          { label: '右侧', value: 'right' }
        ]
      },
      { type: 'input-number', label: '图例内边距', prop: 'legendPadding' },
      { type: 'input-number', label: '图例间距', prop: 'legendItemGap' },
      {
        type: 'select', label: '图例对齐', prop: 'legendAlign',
        options: [
          { label: '左对齐', value: 'left' },
          { label: '右对齐', value: 'right' }
        ]
      },
      { type: 'input-number', label: '图例宽度', prop: 'legendWidth' },
      { type: 'input-number', label: '图例高度', prop: 'legendHeight' },
      { type: 'color-picker', label: '背景颜色', prop: 'legendBackgroundColor' },
      { type: 'color-picker', label: '边框颜色', prop: 'legendBorderColor' },
      { type: 'input-number', label: '边框宽度', prop: 'legendBorderWidth' },
      // 字体样式分组
      {
        type: 'group',
        label: '字体样式',
        key: 'legendFont',
        show: () => true,
        toggle: () => showGroup.legendFont = !showGroup.legendFont,
        expanded: () => showGroup.legendFont,
        items: [
          { type: 'color-picker', label: '字体颜色', prop: 'legendFontColor' },
          { type: 'input-number', label: '字体大小', prop: 'legendFontSize' },
          {
            type: 'select', label: '字体粗细', prop: 'legendFontWeight',
            options: [
              { label: '正常', value: 'normal' },
              { label: '粗体', value: 'bold' },
              { label: '细体', value: 'lighter' }
            ]
          }
        ]
      }
    ]
  },
  {
    key: 'grid',
    label: '坐标系',
    show: () => config.gridShow,
    toggle: () => showGroup.grid = !showGroup.grid,
    sync: () => showGroup.grid = config.gridShow,
    expanded: () => showGroup.grid,
    items: [
      { type: 'switch', label: '坐标系', prop: 'gridShow' },
      { type: 'input-number', label: '坐标系宽度', prop: 'gridWidth' },
      { type: 'input-number', label: '坐标系高度', prop: 'gridHeight' },
      { type: 'color-picker', label: '背景颜色', prop: 'gridBackgroundColor' },
      { type: 'color-picker', label: '边框颜色', prop: 'gridBorderColor' },
      { type: 'input-number', label: '边框宽度', prop: 'gridBorderWidth' }
    ]
  },
  {
    key: 'xAxis',
    label: 'x轴',
    show: () => config.xAxisShow,
    toggle: () => showGroup.xAxis = !showGroup.xAxis,
    sync: () => showGroup.xAxis = config.xAxisShow,
    expanded: () => showGroup.xAxis,
    items: [
      { type: 'switch', label: 'x轴', prop: 'xAxisShow' },
      { type: 'input', label: 'x轴标题', prop: 'xAxisTitle' },
      {
        type: 'select', label: 'x轴位置', prop: 'xAxisPosition',
        options: [
          { label: '底部', value: 'bottom' },
          { label: '顶部', value: 'top' }
        ]
      },
      {
        type: 'select', label: 'x轴类型', prop: 'xAxisType',
        options: [
          { label: '类目轴', value: 'category' },
          { label: '数值轴', value: 'value' }
        ]
      },
      { type: 'input-number', label: '刻度最小值', prop: 'xAxisMin' },
      { type: 'input-number', label: '刻度最大值', prop: 'xAxisMax' },
      { type: 'switch', label: '反转x轴', prop: 'xAxisReverse' },
      // 复杂项：自定义代码弹窗
      { type: 'custom', customKey: 'xAxisLabelCustom' }
    ]
  },
  {
    key: 'yAxis',
    label: 'y轴',
    show: () => config.yAxisShow,
    toggle: () => showGroup.yAxis = !showGroup.yAxis,
    sync: () => showGroup.yAxis = config.yAxisShow,
    expanded: () => showGroup.yAxis,
    items: [
      { type: 'switch', label: 'y轴', prop: 'yAxisShow' },
      { type: 'input', label: 'y轴标题', prop: 'yAxisTitle' },
      {
        type: 'select', label: 'y轴位置', prop: 'yAxisPosition',
        options: [
          { label: '左边', value: 'left' },
          { label: '右边', value: 'right' }
        ]
      },
      {
        type: 'select', label: 'y轴类型', prop: 'yAxisType',
        options: [
          { label: '类目轴', value: 'category' },
          { label: '数值轴', value: 'value' }
        ]
      },
      { type: 'input-number', label: '刻度最小值', prop: 'yAxisMin' },
      { type: 'input-number', label: '刻度最大值', prop: 'yAxisMax' },
      { type: 'switch', label: '反转y轴', prop: 'yAxisReverse' }
    ]
  }
]

const updateCommon = () => {
  emit('update', { ...config })
}

// 监听代码变化，更新配置
function useCodeToConfig(codeRef, targetObj, key, updateFn) {
  watchEffect(() => {
    if (codeRef.value) {
      try {
        const fn = new Function(codeRef.value)
        targetObj[key] = fn()
      } catch (e) {
        targetObj[key] = {}
      }
      updateFn && updateFn()
    }
  })
}

// 辅助函数
function getComponentType(type) {
  switch (type) {
    case 'input': return 'el-input'
    case 'input-number': return 'el-input-number'
    case 'color-picker': return 'el-color-picker'
    case 'select': return 'el-select'
    case 'switch': return 'el-switch'
    default: return 'el-input'
  }
}

useCodeToConfig(axisLabelCode, config, 'xAxisLabelStyle', updateCommon)

</script>

<template>
  <div class="style-config">
    <el-form label-width="100px">
      <template v-for="group in formGroups" :key="group.key">
        <div class="config-group">
          <div class="group-header" @click="group.toggle()">
            <el-form-item :label="group.label">
              <el-switch v-if="group.items[0]?.type === 'switch'" v-model="config[group.items[0].prop]" @click.stop
                @click="group.sync()" @change="updateCommon" />
            </el-form-item>
            <el-icon :class="['expand-icon', { 'rotate-180': group.expanded() }]">
              <ArrowDown />
            </el-icon>
          </div>
          <el-collapse-transition>
            <div v-show="group.show() && group.expanded()" class="group-content">
              <template v-for="(item, idx) in group.items" :key="item.prop || item.label || item.customKey">
                <!-- 跳过第一个switch项（已在header渲染） -->
                <template v-if="!(idx === 0 && item.type === 'switch')">
                  <!-- 普通表单项 -->
                  <template v-if="item.type !== 'group' && item.type !== 'custom'">
                    <el-form-item :label="item.label">
                      <component :is="getComponentType(item.type)" v-model="config[item.prop]"
                        v-bind="item.options ? { options: item.options } : {}" @change="updateCommon"
                        @input="updateCommon">
                        <template v-if="item.type === 'select'">
                          <template v-for="opt in item.options" :key="opt.value">
                            <el-option :label="opt.label" :value="opt.value" />
                          </template>
                        </template>
                      </component>
                    </el-form-item>
                  </template>
                  <!-- 嵌套分组 -->
                  <template v-else-if="item.type === 'group'">
                    <div class="config-group">
                      <div class="group-header" @click="item.toggle()">
                        <el-form-item :label="item.label"></el-form-item>
                        <el-icon :class="['expand-icon', { 'rotate-180': item.expanded() }]">
                          <ArrowDown />
                        </el-icon>
                      </div>
                      <el-collapse-transition>
                        <div v-show="item.show() && item.expanded()" class="group-content">
                          <template v-for="sub in item.items" :key="sub.prop || sub.label">
                            <el-form-item :label="sub.label">
                              <component :is="getComponentType(sub.type)" v-model="config[sub.prop]"
                                v-bind="sub.options ? { options: sub.options } : {}" @change="updateCommon"
                                @input="updateCommon">
                                <template v-if="sub.type === 'select'">
                                  <template v-for="opt in sub.options" :key="opt.value">
                                    <el-option :label="opt.label" :value="opt.value" />
                                  </template>
                                </template>
                                <!-- <template v-if="sub.type === 'select'" v-for="opt in sub.options" :key="opt.value">
                                  <el-option :label="opt.label" :value="opt.value" />
                                </template> -->
                              </component>
                            </el-form-item>
                          </template>
                        </div>
                      </el-collapse-transition>
                    </div>
                  </template>
                  <!-- 复杂自定义项 -->
                  <template v-else-if="item.type === 'custom' && item.customKey === 'xAxisLabelCustom'">
                    <el-form-item label="刻度标签配置">
                      <el-button type="primary" @click="showAxisLabelDialog = true">自定义</el-button>
                      <CodeDialog v-model="axisLabelCode" :visible="showAxisLabelDialog" title="自定义x轴刻度标签样式"
                        placeholder="如：return { color: '#f00', fontSize: 16 }"
                        @update:visible="v => showAxisLabelDialog = v" />
                    </el-form-item>
                  </template>
                </template>
              </template>
            </div>
          </el-collapse-transition>
        </div>
      </template>
      <!-- 其它全局项 -->
      <el-form-item label="暗黑模式">
        <el-switch v-model="config.darkMode" @change="updateCommon" />
      </el-form-item>
      <el-form-item label="背景颜色">
        <el-color-picker v-model="config.backgroundColor" @change="updateCommon" show-alpha />
      </el-form-item>
    </el-form>
  </div>
</template>

<style scoped>
.style-config {
  padding: 10px 10px 10px 0;
}
</style>