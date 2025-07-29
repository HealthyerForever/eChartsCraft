<script setup>
import { ref, reactive, watchEffect } from 'vue'
import CodeDialog from '@/components/dialogs/CodeDialog.vue'

const emit = defineEmits(['update'])

// 显示/隐藏各个配置区域
const showTitleAreaConfig = ref(false)
const showSubtitleAreaConfig = ref(false)
const showLegendAreaConfig = ref(false)
const showLegendFontAreaConfig = ref(false)
const showGridAreaConfig = ref(false)
const showXAxisAreaConfig = ref(false)
const showYAxisAreaConfig = ref(false)

// 显示对话框
const showAxisLabelDialog = ref(false)

// 自定义代码
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
  subtitle: '',
  subtitleColor: '#666',
  subtitleFontSize: 14,
  subtitleFontWeight: 'normal',

  // 图例配置
  legendShow: true,
  legendPosition: 'right',
  legendPadding: 5,
  legendItemGap: 10,
  legendWidth: '20',
  legendHeight: '10',
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
  gridWidth: 'auto',
  gridHeight: 'auto',
  gridBorderColor: '#ccc',
  gridBorderWidth: 1,
  gridBackgroundColor: 'transparent',

  // 坐标轴配置
  xAxisShow: true,
  xAxisTitle: '',
  xAxisPosition: 'bottom',
  xAxisMin: null,
  xAxisMax: null,
  yAxisShow: true,
  yAxisTitle: '',
  yAxisPosition: 'left',
  yAxisMin: null,
  yAxisMax: null,

  backgroundColor: '',
  darkMode: false
})

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

useCodeToConfig(axisLabelCode, config, 'xAxisLabelStyle', updateCommon)
</script>

<template>
  <div class="style-config">
    <el-form label-width="100px">
      <div class="config-group">
        <div class="group-header" @click="showTitleAreaConfig = !showTitleAreaConfig">
          <el-form-item label="标题">
            <el-switch v-model="config.titleShow" @click.stop @click="showTitleAreaConfig = config.titleShow"
              @change="updateCommon" />
          </el-form-item>
          <el-icon :class="['expand-icon', { 'rotate-180': showTitleAreaConfig }]">
            <ArrowDown />
          </el-icon>
        </div>

        <el-collapse-transition>
          <div v-show="config.titleShow && showTitleAreaConfig" class="group-content">
            <el-form-item label="图表标题">
              <el-input v-model="config.title" @input="updateCommon" />
            </el-form-item>
            <el-form-item label="字体大小">
              <el-input-number v-model="config.titleFontSize" @change="updateCommon" />
            </el-form-item>
            <el-form-item label="标题颜色">
              <el-color-picker v-model="config.titleColor" @change="updateCommon" />
            </el-form-item>
            <el-form-item label="标题粗细">
              <el-select v-model="config.titleFontWeight" @change="updateCommon">
                <el-option label="正常" value="normal" />
                <el-option label="粗体" value="bold" />
                <el-option label="细体" value="lighter" />
              </el-select>
            </el-form-item>
            <el-form-item label="标题水平对齐">
              <el-select v-model="config.titleAlign" @change="updateCommon">
                <el-option label="左对齐" value="left" />
                <el-option label="居中" value="center" />
                <el-option label="右对齐" value="right" />
              </el-select>
            </el-form-item>
            <el-form-item label="内边距">
              <el-input-number v-model="config.titlePadding" @change="updateCommon" />
            </el-form-item>

            <div class="config-group">
              <div class="group-header" @click="showSubtitleAreaConfig = !showSubtitleAreaConfig">
                <el-form-item label="副标题">
                  <el-input v-model="config.subtext" @click.stop @input="updateCommon" />
                </el-form-item>
                <el-icon :class="['expand-icon', { 'rotate-180': showSubtitleAreaConfig }]">
                  <ArrowDown />
                </el-icon>
              </div>

              <el-collapse-transition>
                <div v-show="showSubtitleAreaConfig" class="group-content">
                  <el-form-item label="字体颜色">
                    <el-color-picker v-model="config.subtitleColor" @change="updateCommon" />
                  </el-form-item>
                  <el-form-item label="字体大小">
                    <el-input-number v-model="config.subtitleFontSize" @change="updateCommon" />
                  </el-form-item>
                  <el-form-item label="字体粗细">
                    <el-select v-model="config.subtitleFontWeight" @change="updateCommon">
                      <el-option label="正常" value="normal" />
                      <el-option label="粗体" value="bold" />
                      <el-option label="细体" value="lighter" />
                    </el-select>
                  </el-form-item>
                </div>
              </el-collapse-transition>
            </div>
          </div>

        </el-collapse-transition>
      </div>

      <div class="config-group">
        <div class="group-header" @click="showLegendAreaConfig = !showLegendAreaConfig">
          <el-form-item label="图例">
            <el-switch v-model="config.legendShow" @click.stop @click="showLegendAreaConfig = config.legendShow"
              @change="updateCommon" />
          </el-form-item>
          <el-icon :class="['expand-icon', { 'rotate-180': showLegendAreaConfig }]">
            <ArrowDown />
          </el-icon>
        </div>

        <el-collapse-transition>
          <div v-show="config.legendShow && showLegendAreaConfig" class="group-content">
            <el-form-item label="图例位置">
              <el-select v-model="config.legendPosition" @change="updateCommon">
                <el-option label="顶部" value="top" />
                <el-option label="底部" value="bottom" />
                <el-option label="左侧" value="left" />
                <el-option label="右侧" value="right" />
              </el-select>
            </el-form-item>
            <el-form-item label="图例内边距">
              <el-input-number v-model="config.legendPadding" @change="updateCommon" />
            </el-form-item>
            <el-form-item label="图例间距">
              <el-input-number v-model="config.legendItemGap" @change="updateCommon" />
            </el-form-item>
            <el-form-item label="图例对齐">
              <el-select v-model="config.legendAlign" @change="updateCommon">
                <el-option label="左对齐" value="left" />
                <el-option label="右对齐" value="right" />
              </el-select>
            </el-form-item>
            <el-form-item label="图例宽度">
              <el-input-number v-model="config.legendWidth" @change="updateCommon" />
            </el-form-item>
            <el-form-item label="图例高度">
              <el-input-number v-model="config.legendHeight" @change="updateCommon" />
            </el-form-item>
            <el-form-item label="背景颜色">
              <el-color-picker v-model="config.legendBackgroundColor" @change="updateCommon" show-alpha />
            </el-form-item>
            <el-form-item label="边框颜色">
              <el-color-picker v-model="config.legendBorderColor" @change="updateCommon" show-alpha />
            </el-form-item>
            <el-form-item label="边框宽度">
              <el-input-number v-model="config.legendBorderWidth" @change="updateCommon" />
            </el-form-item>

            <div class="config-group">
              <div class="group-header" @click="showLegendFontAreaConfig = !showLegendFontAreaConfig">
                <el-form-item label="字体样式">
                </el-form-item>
                <el-icon :class="['expand-icon', { 'rotate-180': showLegendFontAreaConfig }]">
                  <ArrowDown />
                </el-icon>
              </div>

              <el-collapse-transition>
                <div v-show="showLegendFontAreaConfig" class="group-content">
                  <el-form-item label="字体颜色">
                    <el-color-picker v-model="config.legendFontColor" @change="updateCommon" />
                  </el-form-item>
                  <el-form-item label="字体大小">
                    <el-input-number v-model="config.legendFontSize" @change="updateCommon" />
                  </el-form-item>
                  <el-form-item label="字体粗细">
                    <el-select v-model="config.legendFontWeight" @change="updateCommon">
                      <el-option label="正常" value="normal" />
                      <el-option label="粗体" value="bold" />
                      <el-option label="细体" value="lighter" />
                    </el-select>
                  </el-form-item>
                </div>
              </el-collapse-transition>
            </div>
          </div>
        </el-collapse-transition>
      </div>

      <div class="config-group">
        <div class="group-header" @click="showGridAreaConfig = !showGridAreaConfig">
          <el-form-item label="坐标系">
            <el-switch v-model="config.gridShow" @click.stop @click="showGridAreaConfig = config.gridShow"
              @change="updateCommon" />
          </el-form-item>
          <el-icon :class="['expand-icon', { 'rotate-180': showGridAreaConfig }]">
            <ArrowDown />
          </el-icon>
        </div>

        <el-collapse-transition>
          <div v-show="config.gridShow && showGridAreaConfig" class="group-content">
            <el-form-item label="坐标系宽度">
              <el-input-number v-model="config.gridWidth" @change="updateCommon" />
            </el-form-item>
            <el-form-item label="坐标系高度">
              <el-input-number v-model="config.gridHeight" @change="updateCommon" />
            </el-form-item>
            <el-form-item label="背景颜色">
              <el-color-picker v-model="config.gridBackgroundColor" @change="updateCommon" show-alpha />
            </el-form-item>
            <el-form-item label="边框颜色">
              <el-color-picker v-model="config.gridBorderColor" @change="updateCommon" show-alpha />
            </el-form-item>
            <el-form-item label="边框宽度">
              <el-input-number v-model="config.gridBorderWidth" @change="updateCommon" />
            </el-form-item>
          </div>
        </el-collapse-transition>
      </div>

      <div class="config-group">
        <div class="group-header" @click="showXAxisAreaConfig = !showXAxisAreaConfig">
          <el-form-item label="x轴">
            <el-switch v-model="config.xAxisShow" @click.stop @click="showXAxisAreaConfig = config.xAxisShow"
              @change="updateCommon" />
          </el-form-item>
          <el-icon :class="['expand-icon', { 'rotate-180': showXAxisAreaConfig }]">
            <ArrowDown />
          </el-icon>
        </div>

        <el-collapse-transition>
          <div v-show="showXAxisAreaConfig" class="group-content">
            <el-form-item label="x轴标题">
              <el-input v-model="config.xAxisTitle" @input="updateCommon" />
            </el-form-item>
            <el-form-item label="x轴位置">
              <el-select v-model="config.xAxisPosition" @change="updateCommon">
                <el-option label="底部" value="bottom" />
                <el-option label="顶部" value="top" />
              </el-select>
            </el-form-item>
            <el-form-item label="x轴类型">
              <el-select v-model="config.xAxisType" @change="updateCommon">
                <el-option label="类目轴" value="category" />
                <el-option label="数值轴" value="value" />
              </el-select>
            </el-form-item>
            <el-form-item label="刻度最小值">
              <el-input-number v-model="config.xAxisMin" @change="updateCommon" />
            </el-form-item>
            <el-form-item label="刻度最大值">
              <el-input-number v-model="config.xAxisMax" @change="updateCommon" />
            </el-form-item>
            <el-form-item label="反转x轴">
              <el-switch v-model="config.xAxisReverse" @change="updateCommon" />
            </el-form-item>
            <el-form-item label="刻度标签配置">
              <el-button type="primary" @click="showAxisLabelDialog = true">自定义</el-button>
            </el-form-item>

            <CodeDialog v-model="axisLabelCode" :visible="showAxisLabelDialog"
              @update:visible="v => showAxisLabelDialog = v" title="自定义x轴刻度标签样式"
              placeholder="如：return { color: '#f00', fontSize: 16 }" />

          </div>
        </el-collapse-transition>
      </div>

      <div class="config-group">
        <div class="group-header" @click="showYAxisAreaConfig = !showYAxisAreaConfig">
          <el-form-item label="y轴">
            <el-switch v-model="config.yAxisShow" @click.stop @click="showYAxisAreaConfig = config.yAxisShow"
              @change="updateCommon" />
          </el-form-item>
          <el-icon :class="['expand-icon', { 'rotate-180': showYAxisAreaConfig }]">
            <ArrowDown />
          </el-icon>
        </div>

        <el-collapse-transition>
          <div v-show="showYAxisAreaConfig" class="group-content">
            <el-form-item label="y轴标题">
              <el-input v-model="config.yAxisTitle" @input="updateCommon" />
            </el-form-item>
            <el-form-item label="y轴位置">
              <el-select v-model="config.yAxisPosition" @change="updateCommon">
                <el-option label="左边" value="left" />
                <el-option label="右边" value="right" />
              </el-select>
            </el-form-item>
            <el-form-item label="y轴类型">
              <el-select v-model="config.yAxisType" @change="updateCommon">
                <el-option label="类目轴" value="category" />
                <el-option label="数值轴" value="value" />
              </el-select>
            </el-form-item>
            <el-form-item label="刻度最小值">
              <el-input-number v-model="config.yAxisMin" @change="updateCommon" />
            </el-form-item>
            <el-form-item label="刻度最大值">
              <el-input-number v-model="config.yAxisMax" @change="updateCommon" />
            </el-form-item>
            <el-form-item label="反转y轴">
              <el-switch v-model="config.yAxisReverse" @change="updateCommon" />
            </el-form-item>

          </div>
        </el-collapse-transition>
      </div>

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