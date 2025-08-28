<template>
  <!-- 背景遮罩 -->
  <div class="fixed inset-0 bg-black/50 flex items-center justify-center z-50">
    <!-- 模态框容器 -->
    <div class="bg-white rounded-lg shadow-2xl w-full max-w-4xl max-h-[90vh] overflow-hidden">
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
      <div class="p-5 overflow-y-auto max-h-[calc(90vh-80px)]">
        <!-- 聚类算法选择 -->
        <div class="mb-6">
          <div class="flex items-center mb-4">
            <label class="text-sm font-medium text-gray-700 mr-4 min-w-20">聚类算法</label>
            <select 
              class="bg-white border border-gray-300 text-gray-700 px-3 py-2 rounded focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent min-w-48"
              v-model="selectedAlgorithm"
            >
              <option value="watershed">改进分水岭聚类算法</option>
              <option value="kmeans">K均值聚类算法</option>
              <option value="dbscan">DBSCAN聚类算法</option>
            </select>
          </div>
        </div>

        <!-- 合并和阈值设置 - 上半部分 -->
        <div class="mb-6">
          <button 
            class="bg-gray-100 border border-gray-300 text-gray-700 px-4 py-2.5 rounded cursor-pointer text-sm mb-4 transition-colors hover:bg-gray-200 hover:border-gray-400"
            @click="mergeSimilarAngles"
          >
            合并相似角度簇
          </button>
          
          <div class="grid grid-cols-2 gap-5 mb-4">
            <div class="flex flex-col">
              <label class="text-xs text-gray-500 mb-1.5">倾向差阈值α_th</label>
              <input 
                type="number" 
                class="bg-white border border-gray-300 text-gray-700 px-3 py-2 rounded focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                v-model="alphaThreshold"
                min="0"
                max="90"
              />
            </div>
            <div class="flex flex-col">
              <label class="text-xs text-gray-500 mb-1.5">倾向差阈值β_th</label>
              <input 
                type="number" 
                class="bg-white border border-gray-300 text-gray-700 px-3 py-2 rounded focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                v-model="betaThreshold"
                min="0"
                max="90"
              />
            </div>
          </div>


        </div>

        <!-- 合并和阈值设置 - 下半部分 -->
        <div class="mb-6">
          <button 
            class="bg-gray-100 border border-gray-300 text-gray-700 px-4 py-2.5 rounded cursor-pointer text-sm mb-4 transition-colors hover:bg-gray-200 hover:border-gray-400"
            @click="mergeDiagonalClusters"
          >
            合并对角相似簇
          </button>
          
          <div class="grid grid-cols-2 gap-5">
            <div class="flex flex-col">
              <label class="text-xs text-gray-500 mb-1.5">倾角阈值Dip_th</label>
              <input 
                type="number" 
                class="bg-white border border-gray-300 text-gray-700 px-3 py-2 rounded focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                v-model="dipThreshold"
                min="0"
                max="90"
              />
            </div>
            <div class="flex flex-col">
              <label class="text-xs text-gray-500 mb-1.5">对角线偏转角阈值θ_th</label>
              <input 
                type="number" 
                class="bg-white border border-gray-300 text-gray-700 px-3 py-2 rounded focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
                v-model="thetaThreshold"
                min="0"
                max="90"
              />
            </div>
          </div>
        </div>
          <div class="flex items-center gap-5 pb-5">
            <button 
              class="bg-blue-500 border-none text-white px-6 py-3 rounded cursor-pointer text-sm font-medium transition-colors hover:bg-blue-600"
              @click="startRecognition"
            >
              开始识别
            </button>
            <div class="flex items-center gap-2">
              <input 
                type="checkbox" 
                id="auxiliaryMerge" 
                v-model="auxiliaryMerge"
                class="w-4 h-4 accent-blue-500"
              />
              <label for="auxiliaryMerge" class="text-sm text-gray-500 cursor-pointer">辅助合并</label>
            </div>
          </div>
        <!-- 结果表格 -->
        <div class="mb-6">
          <div class="bg-gray-50 rounded-lg overflow-hidden border border-gray-200">
            <table class="w-full border-collapse">
              <thead>
                <tr>
                  <th class="bg-gray-100 text-gray-700 px-4 py-3 text-left text-sm font-medium border-b border-gray-200">簇</th>
                  <th class="bg-gray-100 text-gray-700 px-4 py-3 text-left text-sm font-medium border-b border-gray-200">产状</th>
                </tr>
              </thead>
              <tbody>
                <tr 
                  v-for="(result, index) in recognitionResults" 
                  :key="index" 
                  class="border-b border-gray-200 hover:bg-gray-50"
                >
                  <td class="px-4 py-3 text-sm text-gray-600">{{ result.cluster }}</td>
                  <td class="px-4 py-3 text-sm text-gray-600">{{ result.attitude }}</td>
                </tr>
                <tr v-if="recognitionResults.length === 0" class="hover:bg-gray-50">
                  <td class="px-4 py-3 text-sm text-gray-400 italic text-center" colspan="2">暂无识别结果</td>
                </tr>
              </tbody>
            </table>
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
  }, 1000)
}

const mergeDiagonalClusters = () => {
  console.log('合并对角相似簇', {
    dip: dipThreshold.value,
    theta: thetaThreshold.value
  })
}
</script>
