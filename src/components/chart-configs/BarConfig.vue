<script setup>
import { reactive, watch } from 'vue'
import CodeDialog from '@/components/dialogs/CodeDialog.vue'
import { ArrowDown } from '@element-plus/icons-vue'

const props = defineProps({
  modelValue: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['update:modelValue'])

// 折叠控制
const showGroup = reactive({
  basic: false,
  label: false,
  backgroundStyle: false,
  itemStyle: false,
  markPoint: false,
  markLine: false,
  markArea: false
})

// 代码弹窗相关
const showDialog = reactive({
  label: false,
  backgroundStyle: false,
  itemStyle: false,
  markPoint: false,
  markLine: false,
  markArea: false
})

const code = reactive({
  label: '',
  backgroundStyle: 'return { backgroundColor: "#fff", border: "1px solid #eee" }',
  itemStyle: 'return { color: "#409EFF", borderWidth: 2, borderType: "solid" }',
  markPoint: 'return { data: [{ type: "max", name: "最高点" }] }',
  markLine: 'return { data: [{ type: "average", name: "平均值" }] }',
  markArea: 'return { data: {} }'
})

// 处理配置更新
const updateConfig = (key, value) => {
  emit('update:modelValue', {
    ...props.modelValue,
    [key]: value
  })
}

// 监听代码变化，自动更新配置
function useCodeToConfig(codeKey, prop) {
  watch(
    () => code[codeKey],
    (newValue) => {
      if (newValue) {
        try {
          const fn = new Function(newValue);
          updateConfig(prop, fn());
        } catch (e) {
          updateConfig(prop, {});
        }
      }
    },
    { immediate: true }
  );
}

// 配置驱动表单项
const formGroups = [
  {
    key: 'basic',
    label: '基础配置',
    toggle: () => showGroup.basic = !showGroup.basic,
    expanded: () => showGroup.basic,
    items: [
      {
        type: 'input-number',
        label: '柱宽',
        prop: 'barWidth',
        min: 1,
        max: 100,
        step: 1,
        onChange: (val) => updateConfig('barWidth', val)
      },
      {
        type: 'input-number',
        label: '柱间距',
        prop: 'barGap',
        min: 0,
        max: 100,
        step: 1,
        onChange: (val) => updateConfig('barGap', val)
      },
      {
        type: 'input-number',
        label: 'X轴索引',
        prop: 'xAxisIndex',
        min: 0,
        max: 10,
        step: 1,
        onChange: (val) => updateConfig('xAxisIndex', val)
      },
      {
        type: 'input-number',
        label: 'Y轴索引',
        prop: 'yAxisIndex',
        min: 0,
        max: 10,
        step: 1,
        onChange: (val) => updateConfig('yAxisIndex', val)
      },
      {
        type: 'select',
        label: '光标样式',
        prop: 'cursor',
        onChange: (val) => updateConfig('cursor', val),
        options: [
          { label: '默认', value: 'default' },
          { label: '十字线', value: 'crosshair' },
          { label: '手型', value: 'pointer' },
          { label: '文本', value: 'text' },
          { label: '移动', value: 'move' },
          { label: '等待', value: 'wait' }
        ]
      }
    ]
  },
  {
    key: 'label',
    label: '数据标签',
    toggle: () => showGroup.label = !showGroup.label,
    expanded: () => showGroup.label,
    items: [
      {
        type: 'switch',
        label: '显示数据标签',
        prop: 'showLabel',
        onChange: (val) => updateConfig('showLabel', val)
      },
      {
        type: 'select',
        label: '标签位置',
        prop: 'labelPosition',
        onChange: (val) => updateConfig('labelPosition', val),
        options: [
          { label: '内部', value: 'inside' },
          { label: '顶部', value: 'top' },
          { label: '右侧', value: 'right' },
          { label: '左侧', value: 'left' },
          { label: '底部', value: 'bottom' }
        ]
      },
      {
        type: 'color-picker',
        label: '标签颜色',
        prop: 'labelColor',
        showAlpha: true,
        onChange: (val) => updateConfig('labelColor', val)
      },
      {
        type: 'input-number',
        label: '字体大小',
        prop: 'labelFontSize',
        min: 10,
        max: 30,
        step: 1,
        onChange: (val) => updateConfig('labelFontSize', val)
      },
      {
        type: 'custom',
        label: '自定义样式',
        key: 'label',
        prop: 'label',
        dialogTitle: '数据标签样式配置',
        placeholder: '如：return { show: true, position: "top" }',
      }
    ]
  },
  {
    key: 'backgroundStyle',
    label: '背景',
    toggle: () => showGroup.backgroundStyle = !showGroup.backgroundStyle,
    expanded: () => showGroup.backgroundStyle,
    items: [
      {
        type: 'switch',
        label: '显示背景',
        prop: 'showBackground',
        onChange: (val) => updateConfig('showBackground', val)
      },
      {
        type: 'color-picker',
        label: '背景颜色',
        prop: 'backgroundColor',
        showAlpha: true,
        onChange: (val) => updateConfig('backgroundColor', val)
      },
      {
        type: 'slider',
        label: '背景透明度',
        prop: 'backgroundOpacity',
        min: 0,
        max: 1,
        step: 0.01,
        onChange: (val) => updateConfig('backgroundOpacity', val)
      },
    ]
  },
  {
    key: 'itemStyle',
    label: '柱条',
    toggle: () => showGroup.itemStyle = !showGroup.itemStyle,
    expanded: () => showGroup.itemStyle,
    items: [
      {
        type: 'color-picker',
        label: '图形颜色',
        prop: 'itemStyleColor',
        showAlpha: true,
        onChange: (val) => updateConfig('itemStyleColor', val)
      },
      {
        type: 'slider',
        label: '图形透明度',
        prop: 'itemStyleOpacity',
        min: 0,
        max: 1,
        step: 0.01,
        onChange: (val) => updateConfig('itemStyleOpacity', val)
      },
      {
        type: 'custom',
        label: '自定义样式',
        key: 'itemStyle',
        prop: 'itemStyle',
        dialogTitle: '数据点样式配置',
        placeholder: '如：return { color: "#409EFF", borderWidth: 2, borderType: "solid" }'
      }
    ]
  },
  {
    key: 'markPoint',
    label: '标记点',
    toggle: () => showGroup.markPoint = !showGroup.markPoint,
    expanded: () => showGroup.markPoint,
    items: [
      {
        type: 'switch',
        label: '显示标记点',
        prop: 'markPointShow',
        onChange: (val) => updateConfig('markPointShow', val)
      },
      {
        type: 'select',
        label: '标记点类型',
        prop: 'markPointSymbol',
        onChange: (val) => updateConfig('markPointSymbol', val),
        options: [
          { label: '圆形', value: 'circle' },
          { label: '方形', value: 'rect' },
          { label: '三角形', value: 'triangle' },
          { label: '菱形', value: 'diamond' },
          { label: '图钉', value: 'pin' },
          { label: '箭头', value: 'arrow' }
        ]
      },
      {
        type: 'input-number',
        label: '标记点大小',
        prop: 'markPointSymbolSize',
        step: 1,
        onChange: (val) => updateConfig('markPointSymbolSize', val)
      },
      {
        type: 'custom',
        label: '标记点配置',
        key: 'markPoint',
        prop: 'markPoint',
        dialogTitle: '标记点配置',
        placeholder: '如：return [{ name: "最高点", coord: [10, 20] }]'
      }
    ]
  },
  {
    key: 'markLine',
    label: '标记线',
    toggle: () => showGroup.markLine = !showGroup.markLine,
    expanded: () => showGroup.markLine,
    items: [
      {
        type: 'switch',
        label: '显示标记线',
        prop: 'markLineShow',
        onChange: (val) => updateConfig('markLineShow', val)
      },
      {
        type: 'custom',
        label: '标记线配置',
        key: 'markLine',
        prop: 'markLine',
        dialogTitle: '标记线配置',
        placeholder: '如：return [{ name: "平均值", yAxis: 10 }]'
      }
    ]
  },
  {
    key: 'markArea',
    label: '标记区域',
    toggle: () => showGroup.markArea = !showGroup.markArea,
    expanded: () => showGroup.markArea,
    items: [
      {
        type: 'switch',
        label: '显示标记区域',
        prop: 'markAreaShow',
        onChange: (val) => updateConfig('markAreaShow', val)
      },
      {
        type: 'custom',
        label: '标记区域配置',
        key: 'markArea',
        prop: 'markArea',
        dialogTitle: '标记区域配置',
        placeholder:
          '如：return [{ name:"高峰期", xAxis:"2023-01-01", yAxis:[10, 20] }]'
      }
    ]
  }
]

// 辅助函数
function getComponentType(type) {
  switch (type) {
    case 'input': return 'el-input'
    case 'input-number': return 'el-input-number'
    case 'color-picker': return 'el-color-picker'
    case 'select': return 'el-select'
    case 'switch': return 'el-switch'
    case 'slider': return 'el-slider'
    default: return 'el-input'
  }
}

// 自动为所有自定义项设置监听
formGroups.forEach(group => {
  group.items.forEach(item => {
    if (item.type === 'custom') {
      useCodeToConfig(item.key, item.prop);
    }
  });
});
</script>

<template>
  <div class="style-config">
    <el-form label-width="120px">
      <template v-for="group in formGroups" :key="group.key">
        <div class="config-group">
          <div class="group-header" @click="group.toggle()">
            <el-form-item :label="group.label">
            </el-form-item>
            <el-icon :class="['expand-icon', { 'rotate-180': group.expanded() }]">
              <ArrowDown />
            </el-icon>
          </div>

          <el-collapse-transition>
            <div v-show="group.expanded()" class="group-content">
              <template v-for="(item, idx) in group.items" :key="item.prop || item.label">
                <!-- 普通表单项 -->
                <template v-if="item.type !== 'custom'">
                  <el-form-item :label="item.label">
                    <component :is="getComponentType(item.type)" v-model="modelValue[item.prop]" v-bind="{
                      showAlpha: item.showAlpha,
                      min: item.min,
                      max: item.max,
                      step: item.step
                    }" @change="item.onChange">
                      <template v-if="item.type === 'select'">
                        <el-option v-for="opt in item.options" :key="opt.value" :label="opt.label" :value="opt.value" />
                      </template>
                    </component>
                  </el-form-item>
                </template>

                <!-- 自定义项 -->
                <template v-else-if="item.type === 'custom'">
                  <el-form-item :label="item.label">
                    <el-button type="primary" @click="showDialog[item.key] = true">
                      自定义
                    </el-button>
                    <CodeDialog v-model="code[item.key]" :visible="showDialog[item.key]" :title="item.dialogTitle"
                      :placeholder="item.placeholder" @update:visible="val => showDialog[item.key] = val" />
                  </el-form-item>
                </template>
              </template>
            </div>
          </el-collapse-transition>
        </div>
      </template>
    </el-form>
  </div>
</template>

<style scoped></style>