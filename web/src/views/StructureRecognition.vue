<template>
  <!-- 背景遮罩 -->
  <div class="fixed inset-0 bg-black/50 flex items-center justify-center z-50">
    <!-- 模态框容器 -->
    <div class="bg-white rounded-lg shadow-2xl w-full max-w-6xl max-h-[90vh] overflow-hidden">
      <!-- 标题栏 -->
      <div class="flex justify-between items-center px-5 py-4 border-b border-gray-200 bg-gray-50">
        <h2 class="text-lg font-semibold text-gray-800">结构面智能识别</h2>
        <button 
          class="text-gray-400 hover:text-gray-600 hover:bg-gray-100 w-8 h-8 flex items-center justify-center rounded transition-colors"
          @click="closeWindow"
        >
          ×
        </button>
      </div>

      <!-- 主要内容区域 -->
      <div class="flex h-[calc(90vh-80px)]">
        <!-- 左侧：输入参数和功能按键 -->
        <div class="w-1/2 p-6 border-r border-gray-200 overflow-y-auto">
          <div class="space-y-8">
            <!-- 聚类算法选择 -->
            <div class="bg-gray-50 rounded-lg p-6">
              <h3 class="text-lg font-semibold text-gray-800 mb-4">聚类算法选择</h3>
              <div class="space-y-3">
                <label class="block text-base font-medium text-gray-700">选择聚类算法</label>
                <select 
                  class="w-full bg-white border border-gray-300 text-gray-700 px-4 py-3 rounded-lg text-base focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                  v-model="selectedAlgorithm"
                >
                  <option value="watershed">改进分水岭聚类算法</option>
                  <option value="kmeans">K均值聚类算法</option>
                  <option value="dbscan">DBSCAN聚类算法</option>
                </select>
              </div>
            </div>

            <!-- 阈值参数设置 -->
            <div class="bg-gray-50 rounded-lg p-6">
              <h3 class="text-lg font-semibold text-gray-800 mb-4">阈值参数设置</h3>
              <div class="grid grid-cols-2 gap-6">
                <div class="space-y-2">
                  <label class="block text-base font-medium text-gray-700">倾向差阈值 α_th</label>
                  <input 
                    type="number" 
                    class="w-full bg-white border border-gray-300 text-gray-700 px-4 py-3 rounded-lg text-base focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                    v-model="alphaThreshold"
                    min="0"
                    max="90"
                  />
                </div>
                <div class="space-y-2">
                  <label class="block text-base font-medium text-gray-700">倾向差阈值 β_th</label>
                  <input 
                    type="number" 
                    class="w-full bg-white border border-gray-300 text-gray-700 px-4 py-3 rounded-lg text-base focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                    v-model="betaThreshold"
                    min="0"
                    max="90"
                  />
                </div>
                <div class="space-y-2">
                  <label class="block text-base font-medium text-gray-700">倾角阈值 Dip_th</label>
                  <input 
                    type="number" 
                    class="w-full bg-white border border-gray-300 text-gray-700 px-4 py-3 rounded-lg text-base focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                    v-model="dipThreshold"
                    min="0"
                    max="90"
                  />
                </div>
                <div class="space-y-2">
                  <label class="block text-base font-medium text-gray-700">对角线偏转角阈值 θ_th</label>
                  <input 
                    type="number" 
                    class="w-full bg-white border border-gray-300 text-gray-700 px-4 py-3 rounded-lg text-base focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                    v-model="thetaThreshold"
                    min="0"
                    max="90"
                  />
                </div>
              </div>
            </div>

            <!-- 功能按键 -->
            <div class="bg-gray-50 rounded-lg p-6">
              <h3 class="text-lg font-semibold text-gray-800 mb-4">功能操作</h3>
              <div class="space-y-4">
                <button 
                  class="w-full bg-blue-100 border border-blue-300 text-blue-700 px-6 py-4 rounded-lg cursor-pointer text-base font-medium transition-colors hover:bg-blue-200 hover:border-blue-400"
                  @click="mergeSimilarAngles"
                >
                  合并相似角度簇
                </button>
                <button 
                  class="w-full bg-green-100 border border-green-300 text-green-700 px-6 py-4 rounded-lg cursor-pointer text-base font-medium transition-colors hover:bg-green-200 hover:border-green-400"
                  @click="mergeDiagonalClusters"
                >
                  合并对角相似簇
                </button>
                <div class="flex items-center gap-3 pt-2">
                  <input 
                    type="checkbox" 
                    id="auxiliaryMerge" 
                    v-model="auxiliaryMerge"
                    class="w-5 h-5 accent-blue-500"
                  />
                  <label for="auxiliaryMerge" class="text-base text-gray-700 cursor-pointer">启用辅助合并</label>
                </div>
                <button 
                  class="w-full bg-blue-500 border-none text-white px-6 py-4 rounded-lg cursor-pointer text-lg font-semibold transition-colors hover:bg-blue-600"
                  @click="startRecognition"
                >
                  开始结构面识别
                </button>
              </div>
            </div>
          </div>
        </div>

        <!-- 右侧：结构面识别和稳定性分析效果 -->
        <div class="w-1/2 p-6 overflow-y-auto">
          <div class="space-y-6">
            <!-- 识别结果 -->
            <div class="bg-white rounded-lg border border-gray-200 overflow-hidden">
              <div class="bg-blue-50 px-6 py-4 border-b border-gray-200">
                <h3 class="text-lg font-semibold text-gray-800">结构面识别结果</h3>
              </div>
              <div class="p-6">
                <div v-if="recognitionResults.length === 0" class="text-center py-8">
                  <div class="text-gray-400 text-lg">暂无识别结果</div>
                  <div class="text-gray-500 text-base mt-2">请点击"开始结构面识别"按钮</div>
                </div>
                <div v-else class="space-y-4">
                  <div 
                    v-for="(result, index) in recognitionResults" 
                    :key="index" 
                    class="bg-gray-50 rounded-lg p-4 border border-gray-200"
                  >
                    <div class="flex justify-between items-center">
                      <span class="text-lg font-medium text-gray-800">{{ result.cluster }}</span>
                      <span class="text-base text-gray-600">{{ result.attitude }}</span>
                    </div>
                  </div>
                </div>
              </div>
            </div>

            <!-- 可视化图表区域 -->
            <div class="bg-white rounded-lg border border-gray-200 overflow-hidden">
              <div class="bg-green-50 px-6 py-4 border-b border-gray-200">
                <h3 class="text-lg font-semibold text-gray-800">结构面分布可视化</h3>
              </div>
              <div class="p-6">
                <div class="bg-gray-100 rounded-lg h-64 flex items-center justify-center">
                  <div class="text-center">
                    <div class="text-gray-500 text-lg mb-2">📊</div>
                    <div class="text-gray-600 text-base">结构面分布图</div>
                    <div class="text-gray-500 text-sm mt-1">识别完成后将显示可视化结果</div>
                  </div>
                </div>
              </div>
            </div>

            <!-- 稳定性分析 -->
            <div class="bg-white rounded-lg border border-gray-200 overflow-hidden">
              <div class="bg-orange-50 px-6 py-4 border-b border-gray-200">
                <h3 class="text-lg font-semibold text-gray-800">稳定性分析结果</h3>
              </div>
              <div class="p-6">
                <div class="space-y-4">
                  <div class="grid grid-cols-2 gap-4">
                    <div class="bg-gray-50 rounded-lg p-4 text-center">
                      <div class="text-2xl font-bold text-blue-600">{{ stabilityAnalysis.overallStability }}%</div>
                      <div class="text-base text-gray-600 mt-1">整体稳定性</div>
                    </div>
                    <div class="bg-gray-50 rounded-lg p-4 text-center">
                      <div class="text-2xl font-bold text-green-600">{{ stabilityAnalysis.mainStructureGroups }}</div>
                      <div class="text-base text-gray-600 mt-1">主要结构面组</div>
                    </div>
                  </div>
                  <div v-if="stabilityAnalysis.riskAreas > 0" class="bg-yellow-50 border border-yellow-200 rounded-lg p-4">
                    <div class="text-base font-medium text-yellow-800 mb-2">⚠️ 风险提示</div>
                    <div class="text-sm text-yellow-700">{{ stabilityAnalysis.riskMessage }}</div>
                  </div>
                  <div v-else class="bg-green-50 border border-green-200 rounded-lg p-4">
                    <div class="text-base font-medium text-green-800 mb-2">✅ 安全状态</div>
                    <div class="text-sm text-green-700">未检测到明显的不稳定区域</div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue'
import { useRouter } from 'vue-router'

// 获取路由实例
const router = useRouter()

// 响应式数据
const selectedAlgorithm = ref('watershed')
const alphaThreshold = ref(15)
const betaThreshold = ref(15)
const dipThreshold = ref(70)
const thetaThreshold = ref(10)
const auxiliaryMerge = ref(true)
const recognitionResults = ref([])
const stabilityAnalysis = ref({
  overallStability: 85,
  mainStructureGroups: 3,
  riskAreas: 1,
  riskMessage: '检测到1个潜在不稳定区域，建议进一步分析'
})

// 方法
const closeWindow = () => {
  // 返回主页
  router.push({ name: 'home' })
}

const mergeSimilarAngles = () => {
  console.log('合并相似角度簇', {
    alpha: alphaThreshold.value,
    beta: betaThreshold.value
  })
}

const startRecognition = () => {
  console.log('开始识别', {
    algorithm: selectedAlgorithm.value,
    alpha: alphaThreshold.value,
    beta: betaThreshold.value,
    auxiliary: auxiliaryMerge.value
  })
  
  // 模拟识别结果
  setTimeout(() => {
    recognitionResults.value = [
      { cluster: '簇1', attitude: '倾向: 45°, 倾角: 30°' },
      { cluster: '簇2', attitude: '倾向: 135°, 倾角: 60°' },
      { cluster: '簇3', attitude: '倾向: 225°, 倾角: 45°' }
    ]
    
    // 更新稳定性分析数据
    stabilityAnalysis.value = {
      overallStability: Math.floor(Math.random() * 20) + 80, // 80-100%
      mainStructureGroups: recognitionResults.value.length,
      riskAreas: Math.random() > 0.5 ? 1 : 0,
      riskMessage: '检测到1个潜在不稳定区域，建议进一步分析'
    }
  }, 1000)
}

const mergeDiagonalClusters = () => {
  console.log('合并对角相似簇', {
    dip: dipThreshold.value,
    theta: thetaThreshold.value
  })
}
</script>
