<template>
  <div class="icon-gallery">
    <h1>图标库</h1>
    <div class="icon-grid">
      <div class="icon-item" v-for="icon in icons" :key="icon.name">
        <component :is="icon.component" class="icon" />
        <span class="icon-name">{{ icon.name }}</span>
      </div>
    </div>
  </div>
</template>

<script setup>
import {
  IconNewFile,
  IconOpenFolder,
  IconSave,
  IconPrint,
  IconSearch,
  IconZoomIn,
  IconZoomOut,
  IconFitToScreen,
  IconDrag,
  IconArrowUp,
  IconArrowRight,
  IconNorthPole,
  IconCompass,
  IconBreak,
  IconDiscrete,
  IconHeatmapEarth,
  IconLatLonEarth,
  IconGridEarth,
  IconAngleMeasurement,
  IconSlopeMeasurement,
  IconCone,
  IconExclude,
  IconAddText,
  IconSitArrow,
  IconOvalBox,
  IconSlash,
  IconIncompleteHexagon,
  IconRegularHexagon
} from '@/components/icons'

const icons = [
  { name: '新建文件', component: IconNewFile },
  { name: '打开文件夹', component: IconOpenFolder },
  { name: '保存', component: IconSave },
  { name: '打印', component: IconPrint },
  { name: '查找', component: IconSearch },
  { name: '放大', component: IconZoomIn },
  { name: '缩小', component: IconZoomOut },
  { name: '视角调整到合适大小', component: IconFitToScreen },
  { name: '拖动', component: IconDrag },
  { name: '朝上', component: IconArrowUp },
  { name: '朝右', component: IconArrowRight },
  { name: '北极', component: IconNorthPole },
  { name: '指南针', component: IconCompass },
  { name: '断裂', component: IconBreak },
  { name: '离散', component: IconDiscrete },
  { name: '热力图地球', component: IconHeatmapEarth },
  { name: '经纬度地球', component: IconLatLonEarth },
  { name: '网格地球', component: IconGridEarth },
  { name: '测量两点与地心之间的夹角', component: IconAngleMeasurement },
  { name: '测量坡度', component: IconSlopeMeasurement },
  { name: '圆锥', component: IconCone },
  { name: '不包括', component: IconExclude },
  { name: '添加文本', component: IconAddText },
  { name: '坐上箭头', component: IconSitArrow },
  { name: '椭圆方框', component: IconOvalBox },
  { name: '斜杠', component: IconSlash },
  { name: '六边形有六个点但左下的边缺失', component: IconIncompleteHexagon },
  { name: '普通六边形', component: IconRegularHexagon }
]
</script>

<style scoped>
.icon-gallery {
  padding: 20px;
  max-width: 1200px;
  margin: 0 auto;
}

.icon-gallery h1 {
  text-align: center;
  margin-bottom: 30px;
  color: #2c3e50;
}

.icon-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
  gap: 20px;
}

.icon-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  background: white;
  transition: all 0.3s ease;
}

.icon-item:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  border-color: #42b883;
}

.icon {
  width: 48px;
  height: 48px;
  margin-bottom: 10px;
  color: #42b883;
}

.icon-name {
  text-align: center;
  font-size: 12px;
  color: #666;
  line-height: 1.4;
  word-break: break-word;
}
</style>
