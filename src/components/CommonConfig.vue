<script setup>
import { tr } from 'element-plus/es/locales.mjs'
import { ref, reactive, watchEffect } from 'vue'

const emit = defineEmits(['update'])

const showTitleAreaConfig = ref(false)
const showSubtitleAreaConfig = ref(false)
const showLegendAreaConfig = ref(false)
const showLegendFontAreaConfig = ref(false)
const showGridAreaConfig = ref(false)

const config = reactive({
  titleShow: true,
  title: '标题',
  titleFontSize: 20,
  titleColor: '#333',
  titleFontWeight: 'normal',
  titleAlign: 'left',
  titlePadding: 10,

  subtext: '',
  subtextColor: '#666',
  subtextFontSize: 14,
  subtextFontWeight: 'normal',

  legendShow: true,
  legendPosition: 'right',
  legendPadding: 5,
  legendItemGap: 10,
  legendWidth: '20',
  legendHeight: '10',

  backgroundColor: '#fff'
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
  padding: 10px;
}
</style>