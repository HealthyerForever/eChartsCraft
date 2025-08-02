<script setup>
import { reactive, watch } from 'vue'
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
  tooltip: false,
  axisPointer: false,
  toolbox: false,
})

// 代码弹窗相关
const showDialog = reactive({
  textStyle: false,
  subtextStyle: false,

  legendTextStyle: false,
  legnedLineStyle: false,
  legendItemStyle: false,

  xAxisLabelStyle: false,
  xAxisNameTextStyle: false,
  xAxisLineStyle: false,
  xAxisTickStyle: false,
  xAxisSplitLineStyle: false,

  yAxisLabelStyle: false,
  yAxisNameTextStyle: false,
  yAxisLineStyle: false,
  yAxisTickStyle: false,
  yAxisSplitLineStyle: false,

  tooltipTextStyle: false,

  axisPointerLabelStyle: false,
  axisPointerLineStyle: false,
  axisPointerShadowStyle: false,
  axisPointerHandleStyle: false,

  toolboxFeatures: false,
  toolboxIconStyle: false,
})

const code = reactive({
  textStyle: 'return { color: "#000", fontSize: 18 }',
  subtextStyle: 'return { color: "#000", fontSize: 16 }',

  legendTextStyle: 'return { color: "#000", fontSize: 12 }',
  legendLineStyle: '',
  legendItemStyle: '',

  xAxisLabelStyle: 'return { color: "#333", fontSize: 12 }',
  xAxisNameTextStyle: 'return { color: "#333", fontSize: 14 }',
  xAxisLineStyle: 'return { show: false }',
  xAxisTickStyle: 'return { show: false }',
  xAxisSplitLineStyle: 'return { show: false }',

  yAxisLabelStyle: 'return { color: "#333", fontSize: 12 }',
  yAxisNameTextStyle: 'return { color: "#333", fontSize: 14 }',
  yAxisLineStyle: 'return { show: false }',
  yAxisTickStyle: 'return { show: false }',
  yAxisSplitLineStyle: 'return { show: true }',

  tooltipTextStyle: '',

  axisPointerLabelStyle: 'return { color: "#fff", fontSize: 12 }',
  axisPointerLineStyle: 'return { color: "#ccc", width: 1, type: "solid" }',
  axisPointerShadowStyle: 'return { color: "rgba(150,150,150,0.3)" }',
  axisPointerHandleStyle: 'return { show: false }',

  toolboxFeatures: 'return { \n\tsaveAsImage: {}, \n\tdataView: {}, \n\trestore: {}, \n\tmagicType: { type: ["line", "bar"] } \n}',
  toolboxIconStyle: 'return { color: "", borderColor: "#666", borderWidth: 1 }',
})

// 配置对象
const config = reactive({
  // #region 标题配置
  titleShow: true,
  title: '标题',
  textStyle: {},
  subtext: '',
  subtextStyle: {},
  titleAlign: 'left',
  titlePadding: 10,
  titleTop: 0,
  titleBottom: 0,
  titleBackgroundColor: '',
  titleBorderColor: '',
  titleBorderWidth: 0,
  titleBorderRadius: 0,
  // #endregion

  // #region 图例配置
  legendShow: true,
  legendPosition: 'right',
  legendPadding: 5,
  legendItemGap: 10,
  legendWidth: 20,
  legendHeight: 10,
  legendAlign: 'right',
  legendWidth: 20,
  legendHeight: 10,
  legendAlign: 'auto',
  legendItemWidth: 25,
  legendBackgroundColor: '#fff',
  legendBorderColor: '#ccc',
  legendBorderWidth: 0,
  legendTextStyle: {},
  legendLineStyle: {},
  legendItemStyle: {},
  // #endregion

  // #region 坐标系配置
  gridShow: false,
  gridWidth: null,
  gridHeight: null,
  gridBorderColor: '#ccc',
  gridBorderWidth: 1,
  gridBackgroundColor: 'transparent',
  // #endregion

  // #region x坐标轴配置
  xAxisShow: true,
  xAxisTitle: '',
  xAxisPosition: 'bottom',
  xAxisType: 'category',
  xAxisMin: null,
  xAxisMax: null,
  xAxisReverse: false,
  xAxisLabelStyle: {},
  xAxisReverse: false,
  xAxisLabelStyle: {},
  xAxisNameTextStyle: {},
  xAxisLineStyle: {},
  xAxisTickStyle: {},
  xAxisSplitLineStyle: {},
  // #endregion

  // #region y坐标轴配置
  yAxisShow: true,
  yAxisTitle: '',
  yAxisPosition: 'left',
  yAxisType: 'value',
  yAxisMin: null,
  yAxisMax: null,
  yAxisReverse: false,
  yAxisLabelStyle: {},
  yAxisNameTextStyle: {},
  yAxisLineStyle: {},
  yAxisTickStyle: {},
  yAxisSplitLineStyle: {},
  // #endregion

  // #region 提示框配置
  tooltipShow: true,
  tooltipTrigger: 'item',
  tooltipTriggerOn: 'mousemove|click',
  tooltipBackgroundColor: '#fff',
  tooltipBorderColor: '#333',
  tooltipBorderWidth: 0,
  tooltipPadding: 5,
  tooltipTextStyle: {},
  tooltipFormatter: '',
  // #endregion

  // #region 坐标轴指示器配置
  axisPointerShow: false,
  axisPointerType: 'line',
  axisPointerTriggerOn: 'mousemove|click',
  axisPointerLabelStyle: {},
  axisPointerLineStyle: {},
  axisPointerShadowStyle: {},
  axisPointerHandleStyle: {},
  // #endregion

  // #region 工具栏配置
  toolboxShow: false,
  toolboxFeatures: {},
  toolboxAlign: 'right',
  toolboxOrient: 'horizontal',
  toolboxTop: null,
  toolboxBottom: null,
  toolboxItemGap: 8,
  toolboxItemSize: 15,
  toolboxIconStyle: {},
  // #endregion

  // #region 全局配置
  backgroundColor: '',
  darkMode: false
  // #endregion
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
      { type: 'input', label: '主标题', prop: 'title' },
      {
        type: 'custom',
        label: '主标题样式',
        key: 'textStyle',
        prop: 'textStyle',
        dialogTitle: '主标题样式',
        placeholder: '如：return { color: "#f00", fontSize: 16 }',
      },
      { type: 'input', label: '副标题', prop: 'subtext' },
      {
        type: 'custom',
        label: '副标题样式',
        key: 'subtextStyle',
        prop: 'subtextStyle',
        dialogTitle: '副标题样式',
        placeholder: '如：return { color: "#f00", fontSize: 16 }',
      },
      {
        type: 'select', label: '标题水平对齐', prop: 'titleAlign',
        options: [
          { label: '左对齐', value: 'left' },
          { label: '居中', value: 'center' },
          { label: '右对齐', value: 'right' }
        ]
      },
      { type: 'input', label: '上边距', prop: 'titleTop' },
      { type: 'input', label: '下边距', prop: 'titleBottom' },
      { type: 'input-number', label: '内边距', prop: 'titlePadding' },
      { type: 'color-picker', label: '背景颜色', prop: 'titleBackgroundColor' },
      { type: 'color-picker', label: '边框颜色', prop: 'titleBorderColor' },
      { type: 'input-number', label: '边框宽度', prop: 'titleBorderWidth' },
      { type: 'input-number', label: '边框圆角', prop: 'titleBorderRadius' }
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
      {
        type: 'custom',
        label: '图例文本样式',
        key: 'legendTextStyle',
        prop: 'legendTextStyle',
        dialogTitle: '图例文本样式',
        placeholder: '如：return { color: "#f00", fontSize: 16 }',
      },
      {
        type: 'custom',
        label: '图例线条样式',
        key: 'legendLineStyle',
        prop: 'legendLineStyle',
        dialogTitle: '图例线条样式',
        placeholder: '如：return { color: "#ccc", width: 1, type: "solid" }',
      },
      {
        type: 'custom',
        label: '图例图形样式',
        key: 'legendItemStyle',
        prop: 'legendItemStyle',
        dialogTitle: '图例图形样式',
        placeholder: '如：return { color: "#000", borderColor: "#000", borderWidth: 1 }',
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
      {
        type: 'custom',
        label: '名称样式',
        key: 'xAxisNameTextStyle',
        prop: 'xAxisNameTextStyle',
        dialogTitle: 'x轴名称样式',
        placeholder: '如：return { color: "#f00", fontSize: 16 }',
      },
      {
        type: 'custom',
        label: '刻度标签样式',
        key: 'xAxisLabelStyle',
        prop: 'xAxisLabelStyle',
        dialogTitle: 'x轴刻度标签样式',
        placeholder: '如：return { color: "#f00", fontSize: 16 }',
      },
      {
        type: 'custom',
        label: '轴线样式',
        key: 'xAxisLineStyle',
        prop: 'xAxisLineStyle',
        dialogTitle: 'x轴轴线样式',
        placeholder: '如：return { show: false }',
      },
      {
        type: 'custom',
        label: '刻度线样式',
        key: 'xAxisTickStyle',
        prop: 'xAxisTickStyle',
        dialogTitle: 'x轴刻度线样式',
        placeholder: '如：return { show: false }',
      },
      {
        type: 'custom',
        label: '分割线样式',
        key: 'xAxisSplitLineStyle',
        prop: 'xAxisSplitLineStyle',
        dialogTitle: 'x轴分割线样式',
        placeholder: '如：return { show: false }',
      }
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
      { type: 'switch', label: '反转y轴', prop: 'yAxisReverse' },
      {
        type: 'custom',
        label: '名称样式',
        key: 'yAxisNameTextStyle',
        prop: 'yAxisNameTextStyle',
        dialogTitle: 'y轴名称样式',
        placeholder: '如：return { color: "#f00", fontSize: 16 }',
      },
      {
        type: 'custom',
        label: '刻度标签样式',
        key: 'yAxisLabelStyle',
        prop: 'yAxisLabelStyle',
        dialogTitle: 'y轴刻度标签样式',
        placeholder: '如：return { color: "#f00", fontSize: 16 }',
      },
      {
        type: 'custom',
        label: '轴线样式',
        key: 'yAxisLineStyle',
        prop: 'yAxisLineStyle',
        dialogTitle: 'y轴轴线样式',
        placeholder: '如：return { show: false }',
      },
      {
        type: 'custom',
        label: '刻度线样式',
        key: 'yAxisTickStyle',
        prop: 'yAxisTickStyle',
        dialogTitle: 'y轴刻度线样式',
        placeholder: '如：return { show: false }',
      },
      {
        type: 'custom',
        label: '分割线样式',
        key: 'yAxisSplitLineStyle',
        prop: 'yAxisSplitLineStyle',
        dialogTitle: 'y轴分割线样式',
        placeholder: '如：return { show: true }',
      }
    ]
  },
  {
    key: 'tooltip',
    label: '提示框',
    show: () => config.tooltipShow,
    toggle: () => showGroup.tooltip = !showGroup.tooltip,
    sync: () => showGroup.tooltip = config.tooltipShow,
    expanded: () => showGroup.tooltip,
    items: [
      { type: 'switch', label: '提示框', prop: 'tooltipShow' },
      {
        type: 'select', label: '触发方式', prop: 'tooltipTrigger',
        options: [
          { label: '鼠标悬停', value: 'item' },
          { label: '坐标轴触发', value: 'axis' },
        ]
      },
      {
        type: 'select', label: '触发时机', prop: 'tooltipTriggerOn',
        options: [
          { label: '鼠标移动或点击', value: 'mousemove|click' },
          { label: '鼠标点击', value: 'click' },
          { label: '鼠标移动', value: 'mousemove' }
        ]
      },
      { type: 'color-picker', label: '背景颜色', prop: 'tooltipBackgroundColor' },
      { type: 'color-picker', label: '边框颜色', prop: 'tooltipBorderColor' },
      { type: 'input-number', label: '边框宽度', prop: 'tooltipBorderWidth' },
      { type: 'input-number', label: '内边距', prop: 'tooltipPadding' },
      { type: 'input', label: '格式字符串', prop: 'tooltipFormatter' },
      {
        type: 'custom',
        label: '文本样式',
        key: 'tooltipTextStyle',
        prop: 'tooltipTextStyle',
        dialogTitle: '提示框文本样式',
        placeholder: '如：return { color: "#fff", fontSize: 12 }',
      }
    ]
  },
  {
    key: 'axisPointer',
    label: '指示器',
    show: () => config.axisPointerShow,
    toggle: () => showGroup.axisPointer = !showGroup.axisPointer,
    sync: () => showGroup.axisPointer = config.axisPointerShow,
    expanded: () => showGroup.axisPointer,
    items: [
      { type: 'switch', label: '显示指示器', prop: 'axisPointerShow' },
      {
        type: 'select', label: '指示器类型', prop: 'axisPointerType',
        options: [
          { label: '线型', value: 'line' },
          { label: '阴影型', value: 'shadow' }
        ]
      },
      {
        type: 'select', label: '触发方式', prop: 'axisPointerTriggerOn',
        options: [
          { label: '鼠标移动或点击', value: 'mousemove|click' },
          { label: '鼠标点击', value: 'click' },
          { label: '鼠标移动', value: 'mousemove' }
        ]
      },
      {
        type: 'custom',
        label: '标签样式',
        key: 'axisPointerLabelStyle',
        prop: 'axisPointerLabelStyle',
        dialogTitle: '指示器标签样式',
        placeholder: '如：return { color: "#fff", fontSize: 12 }',
      },
      {
        type: 'custom',
        label: '线条样式',
        key: 'axisPointerLineStyle',
        prop: 'axisPointerLineStyle',
        dialogTitle: '指示器线条样式',
        placeholder: '如：return { color: "#333", width: 1, type: "solid" }',
      },
      {
        type: 'custom',
        label: '阴影样式',
        key: 'axisPointerShadowStyle',
        prop: 'axisPointerShadowStyle',
        dialogTitle: '指示器阴影样式',
        placeholder: '如：return { color: "rgba(150,150,150,0.3)" }',
      },
      {
        type: 'custom',
        label: '手柄样式',
        key: 'axisPointerHandleStyle',
        prop: 'axisPointerHandleStyle',
        dialogTitle: '指示器手柄样式',
        placeholder: '如：return { show: false }',
      }
    ]
  },
  {
    key: 'toolbox',
    label: '工具栏',
    show: () => config.toolboxShow,
    toggle: () => showGroup.toolbox = !showGroup.toolbox,
    sync: () => showGroup.toolbox = config.toolboxShow,
    expanded: () => showGroup.toolbox,
    items: [
      { type: 'switch', label: '显示工具栏', prop: 'toolboxShow' },
      {
        type: 'custom',
        label: '工具栏功能',
        key: 'toolboxFeatures',
        prop: 'toolboxFeatures',
        dialogTitle: '工具栏功能配置',
        placeholder: '如：return { saveAsImage: {}, dataView: {}, restore: {}, magicType: { type: ["line", "bar"] } }',
      },
      {
        type: 'select',
        label: '工具栏对齐',
        prop: 'toolboxAlign',
        options: [
          { label: '左对齐', value: 'left' },
          { label: '右对齐', value: 'right' },
          { label: '居中对齐', value: 'center' }
        ]
      },
      {
        type: 'select',
        label: '工具栏方向',
        prop: 'toolboxOrient',
        options: [
          { label: '垂直', value: 'vertical' },
          { label: '水平', value: 'horizontal' }
        ]
      },
      { type: 'input-number', label: '上边距', prop: 'toolboxTop' },
      { type: 'input-number', label: '下边距', prop: 'toolboxBottom' },
      { type: 'input-number', label: '图标间距', prop: 'toolboxItemGap' },
      { type: 'input-number', label: '图标大小', prop: 'toolboxItemSize' },
      {
        type: 'custom',
        label: '图标样式',
        key: 'toolboxIconStyle',
        prop: 'toolboxIconStyle',
        dialogTitle: '工具栏图标样式',
        placeholder: '如：return { color: "#000", borderColor: "#000", borderWidth: 1 }',
      }
    ]
  },
]

const updateCommon = () => {
  emit('update', { ...config })
}

// 监听代码变化，自动更新配置
function useCodeToConfig(codeKey, targetObj, prop, updateFn) {
  watch(
    () => code[codeKey],
    (newValue) => {
      if (newValue) {
        try {
          const fn = new Function(newValue);
          targetObj[prop] = fn();
        } catch (e) {
          targetObj[prop] = {};
        }
        updateFn?.();
      }
    },
    { immediate: true }
  );
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

//自动为所有自定义项设置监听
formGroups.forEach(group => {
  group.items.forEach(item => {
    if (item.type === 'custom') {
      useCodeToConfig(item.key, config, item.prop, updateCommon);
    }
  });
});

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
              <template v-for="(item, idx) in group.items" :key="item.prop || item.label">
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
                  <!-- 自定义项 -->
                  <template v-else-if="item.type === 'custom'">
                    <el-form-item :label="item.label">
                      <el-button type="primary" @click="showDialog[item.key] = true">自定义</el-button>
                      <CodeDialog v-model="code[item.key]" :visible="showDialog[item.key]" :title="item.dialogTitle"
                        :placeholder="item.placeholder" @update:visible="v => showDialog[item.key] = v" />
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