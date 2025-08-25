<script setup>
import { ref, onMounted, watch } from 'vue'
import { useRoute, useRouter } from 'vue-router'

const route = useRoute()
const router = useRouter()

const history = ref([])
const transitionName = ref('slide-right')

// 初始化历史记录
onMounted(() => {
  history.value.push(route.fullPath)
})

// 监听路由变化
watch(
  () => route.fullPath,
  (toPath, fromPath) => {
    const toIndex = history.value.indexOf(toPath)
    const fromIndex = history.value.indexOf(fromPath)

    if (toIndex > -1 && toIndex < fromIndex) {
      // 后退操作
      transitionName.value = 'slide-right'
    } else {
      // 前进操作
      transitionName.value = 'slide-left'
      if (fromIndex === history.value.length - 1) {
        history.value.push(toPath)
      }
    }

    // 限制历史记录长度
    if (history.value.length > 10) {
      history.value.shift()
    }
  },
)

// 清除历史记录的方法（可选）
const clearHistory = () => {
  history.value = [route.fullPath]
}

// 控制菜单展开状态
const openSections = ref({
  dataManagement: false,
  structureRecognition: false,
  kinematicAnalysis: true, // 默认展开运动学分析
  sourceEstimation: true   // 默认展开物源估算
})

// 切换菜单段落的展开/收起状态
const toggleSection = (sectionName) => {
  openSections.value[sectionName] = !openSections.value[sectionName]
}

// 导航到具体功能页面
const navigateToPage = (routeName) => {
  router.push({ name: routeName })
}

// 获取当前页面标题
const getCurrentPageTitle = () => {
  const pageMap = {
    'home': '主页',

    'StructureRecognition': '结构面智能识别'
  }
  return pageMap[route.name] || '未知页面'
}
</script>

<template>
  <div class="flex h-screen w-screen text-gray-800 bg-gradient-to-br from-white via-gray-50 to-gray-100 select-none">
    <!-- 左侧菜单栏 -->
    <div class="w-80 bg-white/95 backdrop-blur-sm border-r border-gray-200 shadow-lg flex flex-col">
      <!-- 顶部标题 -->
      <div class="p-6 border-b border-gray-200">
        <h1 class="text-xl font-bold text-center text-blue-600">
          泥石流物源自动识别与体积估算软件
        </h1>
      </div>
      
      <!-- 菜单内容 -->
      <div class="flex-1 overflow-y-auto p-4">
        <!-- 数据管理 -->
        <div class="mb-4">
          <div 
            @click="toggleSection('dataManagement')"
            class="flex items-center p-3 rounded-lg cursor-pointer hover:bg-gray-100 transition-all"
          >
            <div class="w-0 h-0 border-l-6 border-l-gray-600 border-t-3 border-t-transparent border-b-3 border-b-transparent mr-3 transition-transform"
                 :class="{ 'rotate-90': openSections.dataManagement }"></div>
            <span class="font-medium">数据管理</span>
          </div>
          <div v-show="openSections.dataManagement" class="ml-6 mt-2 space-y-1">
            <div class="flex items-center p-2 rounded cursor-pointer hover:bg-gray-50 transition-all">
              <div class="w-0 h-0 border-l-4 border-l-gray-400 border-t-2 border-t-transparent border-b-2 border-b-transparent mr-2"></div>
              <span class="text-sm">数据载入</span>
            </div>
            <div class="flex items-center p-2 rounded cursor-pointer hover:bg-gray-50 transition-all">
              <div class="w-0 h-0 border-l-4 border-l-gray-400 border-t-2 border-t-transparent border-b-2 border-b-transparent mr-2"></div>
              <span class="text-sm">绘图选项</span>
            </div>
            <div class="flex items-center p-2 rounded cursor-pointer hover:bg-gray-50 transition-all">
              <div class="w-0 h-0 border-l-4 border-l-gray-400 border-t-2 border-t-transparent border-b-2 border-b-transparent mr-2"></div>
              <span class="text-sm">成果输出</span>
            </div>
          </div>
        </div>

        <!-- 结构面智能识别 -->
        <div class="mb-4">
          <div 
            @click="toggleSection('structureRecognition')"
            class="flex items-center p-3 rounded-lg cursor-pointer hover:bg-gray-100 transition-all"
          >
            <div class="w-0 h-0 border-l-6 border-l-gray-600 border-t-3 border-t-transparent border-b-3 border-b-transparent mr-3 transition-transform"
                 :class="{ 'rotate-90': openSections.structureRecognition }"></div>
            <span class="font-medium">结构面智能识别</span>
          </div>
          <div v-show="openSections.structureRecognition" class="ml-6 mt-2 space-y-1">
            <div class="flex items-center p-2 rounded cursor-pointer hover:bg-gray-50 transition-all">
              <div class="w-0 h-0 border-l-4 border-l-gray-400 border-t-2 border-t-transparent border-b-2 border-b-transparent mr-2"></div>
              <span class="text-sm">法向量估计</span>
            </div>
            <div class="flex items-center p-2 rounded cursor-pointer hover:bg-gray-50 transition-all">
              <div class="w-0 h-0 border-l-4 border-l-gray-400 border-t-2 border-t-transparent border-b-2 border-b-transparent mr-2"></div>
              <span class="text-sm">核密度估计</span>
            </div>
            <div 
              @click="navigateToPage('StructureRecognition')"
              class="flex items-center p-2 rounded cursor-pointer hover:bg-gray-50 transition-all"
            >
              <div class="w-0 h-0 border-l-4 border-l-gray-400 border-t-2 border-t-transparent border-b-2 border-b-transparent mr-2"></div>
              <span class="text-sm">智能识别</span>
            </div>
          </div>
        </div>

        <!-- 运动学分析 -->
        <div class="mb-4">
          <div 
            @click="toggleSection('kinematicAnalysis')"
            class="flex items-center p-3 rounded-lg cursor-pointer hover:bg-gray-100 transition-all"
          >
            <div class="w-0 h-0 border-l-6 border-l-gray-600 border-t-3 border-t-transparent border-b-3 border-b-transparent mr-3 transition-transform"
                 :class="{ 'rotate-90': openSections.kinematicAnalysis }"></div>
            <span class="font-medium">运动学分析</span>
          </div>
          <div v-show="openSections.kinematicAnalysis" class="ml-6 mt-2">
            <div class="bg-gray-50 border border-gray-200 rounded-lg p-3">
              <div class="grid grid-cols-2 gap-2 text-xs">
                <div class="space-y-1">
                  <div class="text-gray-600">Direct Toppling: ✓</div>
                  <div class="text-gray-600">Slope Dip: 42</div>
                  <div class="text-gray-600">Slope Dip Direction: 150</div>
                  <div class="text-gray-600">Friction Angle: 30</div>
                </div>
                <div class="space-y-1">
                  <div class="text-gray-600">Lateral Limit: 20</div>
                  <div class="flex items-center space-x-2">
                    <input type="checkbox" checked class="w-3 h-3">
                    <span class="text-gray-600">Show Construction Lines</span>
                  </div>
                  <div class="flex items-center space-x-2">
                    <input type="checkbox" checked class="w-3 h-3">
                    <span class="text-gray-600">Show Height</span>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- 物源估算功能 -->

      </div>
    </div>

    <!-- 右侧主内容区 -->
    <div class="flex-1 flex flex-col">
      <!-- 顶部工具栏 -->
      <div class="h-16 bg-white border-b border-gray-200 shadow-sm flex items-center px-4">
        <div class="flex items-center space-x-4 text-sm">
          <span class="text-gray-600 cursor-pointer hover:text-blue-600">文件</span>
          <span class="text-gray-600 cursor-pointer hover:text-blue-600">编辑</span>
          <span class="text-gray-600 cursor-pointer hover:text-blue-600">分析</span>
          <span class="text-gray-600 cursor-pointer hover:text-blue-600">视图</span>
          <span class="text-gray-600 cursor-pointer hover:text-blue-600">工具</span>
          <span class="text-gray-600 cursor-pointer hover:text-blue-600">帮助</span>
        </div>
        
        <!-- 当前页面显示 -->
        <div class="ml-auto flex items-center space-x-4">
          <span class="text-sm text-gray-500">当前页面: {{ getCurrentPageTitle() }}</span>
 
        </div>
      </div>
      
      <!-- 工具图标栏 -->
      <div class="h-12 bg-gray-50 border-b border-gray-200 flex items-center px-4 space-x-2">
        <!-- 文件操作工具 -->
        <div class="flex items-center space-x-1">
          <button class="p-2 hover:bg-gray-200 rounded" title="新建文件">
            <svg class="w-4 h-4 text-gray-600" fill="currentColor" viewBox="0 0 20 20">
              <path d="M9 12h6v-2H9v2zm0-4h6V6H9v2zm-2 4V6a2 2 0 012-2h6a2 2 0 012 2v8a2 2 0 01-2 2H9a2 2 0 01-2-2z"/>
            </svg>
          </button>
          <button class="p-2 hover:bg-gray-200 rounded" title="打开文件夹">
            <svg class="w-4 h-4 text-yellow-500" fill="currentColor" viewBox="0 0 20 20">
              <path d="M2 6a2 2 0 012-2h5l2 2h5a2 2 0 012 2v6a2 2 0 01-2 2H4a2 2 0 01-2-2V6z"/>
            </svg>
          </button>
          <button class="p-2 hover:bg-gray-200 rounded" title="保存">
            <svg class="w-4 h-4 text-blue-500" fill="currentColor" viewBox="0 0 20 20">
              <path d="M7.707 10.293a1 1 0 10-1.414 1.414l3 3a1 1 0 001.414 0l3-3a1 1 0 00-1.414-1.414L11 11.586V6h-1v5.586l-2.293-2.293z"/>
            </svg>
          </button>
        </div>
        
        <div class="w-px h-6 bg-gray-300"></div>
        
        <!-- 视图控制工具 -->
        <div class="flex items-center space-x-1">
          <button class="p-2 hover:bg-gray-200 rounded" title="放大">
            <svg class="w-4 h-4 text-gray-600" fill="currentColor" viewBox="0 0 20 20">
              <path d="M10 5a1 1 0 011 1v3h3a1 1 0 110 2h-3v3a1 1 0 11-2 0v-3H6a1 1 0 110-2h3V6a1 1 0 011-1z"/>
            </svg>
          </button>
          <button class="p-2 hover:bg-gray-200 rounded" title="缩小">
            <svg class="w-4 h-4 text-gray-600" fill="currentColor" viewBox="0 0 20 20">
              <path d="M3 10a1 1 0 011-1h12a1 1 0 110 2H4a1 1 0 01-1-1z"/>
            </svg>
          </button>
          <button class="p-2 hover:bg-gray-200 rounded" title="适应窗口">
            <svg class="w-4 h-4 text-gray-600" fill="currentColor" viewBox="0 0 20 20">
              <path d="M3 4a1 1 0 011-1h12a1 1 0 011 1v2a1 1 0 01-1 1H4a1 1 0 01-1-1V4zM3 10a1 1 0 011-1h6a1 1 0 011 1v6a1 1 0 01-1 1H4a1 1 0 01-1-1v-6zM14 9a1 1 0 00-1 1v6a1 1 0 001 1h2a1 1 0 001-1v-6a1 1 0 00-1-1h-2z"/>
            </svg>
          </button>
          <button class="p-2 hover:bg-gray-200 rounded" title="平移工具">
            <svg class="w-4 h-4 text-gray-600" fill="currentColor" viewBox="0 0 20 20">
              <path d="M7.707 10.293a1 1 0 10-1.414 1.414l3 3a1 1 0 001.414 0l3-3a1 1 0 00-1.414-1.414L11 11.586V6h-1v5.586l-2.293-2.293z"/>
            </svg>
          </button>
        </div>
        
        <div class="w-px h-6 bg-gray-300"></div>
        
        <!-- 移动和旋转工具 -->
        <div class="flex items-center space-x-1">
          <button class="p-2 hover:bg-gray-200 rounded bg-blue-100" title="向上移动">
            <svg class="w-4 h-4 text-red-500" fill="currentColor" viewBox="0 0 20 20">
              <path d="M3 4a1 1 0 011-1h12a1 1 0 011 1v2a1 1 0 01-1 1H4a1 1 0 01-1-1V4zM3 10a1 1 0 011-1h6a1 1 0 011 1v6a1 1 0 01-1 1H4a1 1 0 01-1-1v-6zM14 9a1 1 0 00-1 1v6a1 1 0 001 1h2a1 1 0 001-1v-6a1 1 0 00-1-1h-2z"/>
            </svg>
          </button>
          <button class="p-2 hover:bg-gray-200 rounded" title="向右移动">
            <svg class="w-4 h-4 text-gray-600" fill="currentColor" viewBox="0 0 20 20">
              <path d="M3 4a1 1 0 011-1h12a1 1 0 011 1v2a1 1 0 01-1 1H4a1 1 0 01-1-1V4zM3 10a1 1 0 011-1h6a1 1 0 011 1v6a1 1 0 01-1 1H4a1 1 0 01-1-1v-6zM14 9a1 1 0 00-1 1v6a1 1 0 001 1h2a1 1 0 001-1v-6a1 1 0 00-1-1h-2z"/>
            </svg>
          </button>
        </div>
        
        <div class="w-px h-6 bg-gray-300"></div>
        
        <!-- 方向和测量工具 -->
        <div class="flex items-center space-x-1">
          <button class="p-2 hover:bg-gray-200 rounded bg-blue-100" title="指北针">
            <svg class="w-4 h-4 text-blue-500" fill="currentColor" viewBox="0 0 20 20">
              <circle cx="10" cy="10" r="8" stroke="currentColor" stroke-width="2" fill="none"/>
              <path d="M10 2L10 18M2 10L18 10" stroke="currentColor" stroke-width="1"/>
              <path d="M10 2L12 8L10 6L8 8L10 2" fill="currentColor"/>
            </svg>
          </button>
          <button class="p-2 hover:bg-gray-200 rounded" title="分割">
            <svg class="w-4 h-4 text-gray-600" fill="currentColor" viewBox="0 0 20 20">
              <path d="M3 4a1 1 0 011-1h12a1 1 0 011 1v2a1 1 0 01-1 1H4a1 1 0 01-1-1V4z"/>
            </svg>
          </button>
          <button class="p-2 hover:bg-gray-200 rounded" title="旋转">
            <svg class="w-4 h-4 text-gray-600" fill="currentColor" viewBox="0 0 20 20">
              <path d="M4 2a1 1 0 011 1v2.101a7.002 7.002 0 0111.601 2.566 1 1 0 11-1.885.666A5.002 5.002 0 005.999 7H9a1 1 0 010 2H4a1 1 0 01-1-1V3a1 1 0 011-1zm.008 9.057a1 1 0 011.276.61A5.002 5.002 0 0014.001 13H11a1 1 0 110-2h5a1 1 0 011 1v5a1 1 0 01-1 1h-2.101a7.002 7.002 0 01-11.601-2.566 1 1 0 01-.61-1.276z"/>
            </svg>
          </button>
          <button class="p-2 hover:bg-gray-200 rounded" title="测量">
            <svg class="w-4 h-4 text-gray-600" fill="currentColor" viewBox="0 0 20 20">
              <path d="M3 4a1 1 0 011-1h12a1 1 0 011 1v2a1 1 0 01-1 1H4a1 1 0 01-1-1V4zM3 10a1 1 0 011-1h6a1 1 0 011 1v6a1 1 0 01-1 1H4a1 1 0 01-1-1v-6z"/>
            </svg>
          </button>
        </div>
        
        <div class="w-px h-6 bg-gray-300"></div>
        
        <!-- 绘图工具 -->
        <div class="flex items-center space-x-1">
          <button class="p-2 hover:bg-gray-200 rounded" title="点">
            <svg class="w-4 h-4 text-blue-500" fill="currentColor" viewBox="0 0 20 20">
              <circle cx="10" cy="10" r="3" fill="currentColor"/>
            </svg>
          </button>
          <button class="p-2 hover:bg-gray-200 rounded" title="线">
            <svg class="w-4 h-4 text-blue-500" fill="currentColor" viewBox="0 0 20 20">
              <path d="M3 10h14" stroke="currentColor" stroke-width="2" stroke-linecap="round"/>
            </svg>
          </button>
          <button class="p-2 hover:bg-gray-200 rounded" title="面">
            <svg class="w-4 h-4 text-blue-500" fill="currentColor" viewBox="0 0 20 20">
              <path d="M3 4a1 1 0 011-1h12a1 1 0 011 1v2a1 1 0 01-1 1H4a1 1 0 01-1-1V4z"/>
            </svg>
          </button>
        </div>
        
        <div class="w-px h-6 bg-gray-300"></div>
        
        <!-- 分析工具 -->
        <div class="flex items-center space-x-1">
          <button class="p-2 hover:bg-gray-200 rounded" title="坡度分析">
            <svg class="w-4 h-4 text-gray-600" fill="currentColor" viewBox="0 0 20 20">
              <path d="M3 4a1 1 0 011-1h12a1 1 0 011 1v2a1 1 0 01-1 1H4a1 1 0 01-1-1V4z"/>
            </svg>
          </button>
          <button class="p-2 hover:bg-gray-200 rounded bg-blue-100" title="高程分析">
            <svg class="w-4 h-4 text-red-500" fill="currentColor" viewBox="0 0 20 20">
              <path d="M3 4a1 1 0 011-1h12a1 1 0 011 1v2a1 1 0 01-1 1H4a1 1 0 01-1-1V4z"/>
            </svg>
          </button>
        </div>
        
        <div class="w-px h-6 bg-gray-300"></div>
        
        <!-- 标注工具 -->
        <div class="flex items-center space-x-1">
          <button class="p-2 hover:bg-gray-200 rounded" title="文本标注">
            <svg class="w-4 h-4 text-gray-600" fill="currentColor" viewBox="0 0 20 20">
              <path d="M3 4a1 1 0 011-1h12a1 1 0 011 1v2a1 1 0 01-1 1H4a1 1 0 01-1-1V4z"/>
            </svg>
          </button>
          <button class="p-2 hover:bg-gray-200 rounded" title="箭头">
            <svg class="w-4 h-4 text-gray-600" fill="currentColor" viewBox="0 0 20 20">
              <path d="M3 4a1 1 0 011-1h12a1 1 0 011 1v2a1 1 0 01-1 1H4a1 1 0 01-1-1V4z"/>
            </svg>
          </button>
        </div>
      </div>
      
      <!-- 主工作区 - 带路由过渡动画 -->
      <div class="flex-1 bg-gray-50/80 relative overflow-hidden">
    <Transition :name="transitionName" mode="out-in">
      <RouterView />
    </Transition>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* 路由左滑动画（前进） */
.slide-left-enter-active,
.slide-left-leave-active {
  transition: all 0.5s ease;
  position: absolute;
  width: 100%;
  height: 100%;
}

.slide-left-enter-from {
  transform: translateX(100%);
  opacity: 0;
}

.slide-left-leave-to {
  transform: translateX(-100%);
  opacity: 0;
}

/* 路由右滑动画（后退） */
.slide-right-enter-active,
.slide-right-leave-active {
  transition: all 0.5s ease;
  position: absolute;
  width: 100%;
  height: 100%;
}

.slide-right-enter-from {
  transform: translateX(-100%);
  opacity: 0;
}

.slide-right-leave-to {
  transform: translateX(100%);
  opacity: 0;
}
</style>
