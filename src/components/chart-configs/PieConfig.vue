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

})

// 代码弹窗相关
const showDialog = reactive({
  
})

const code = reactive({

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
      
    ]
  },
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