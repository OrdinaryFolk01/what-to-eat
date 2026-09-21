<template>
  <div class="min-h-screen bg-[#FAF8F5] text-slate-800 flex flex-col justify-between p-4 sm:p-6 md:p-8 font-sans selection:bg-orange-500 selection:text-white relative overflow-hidden">
    <!-- 顶部环境光晕 -->
    <div class="absolute -top-36 -left-36 w-96 h-96 bg-orange-200/40 rounded-full blur-3xl pointer-events-none"></div>
    <div class="absolute -bottom-36 -right-36 w-96 h-96 bg-amber-200/40 rounded-full blur-3xl pointer-events-none"></div>

    <!-- 顶部 Header -->
    <header class="text-center max-w-2xl mx-auto my-2 sm:my-4 relative z-10">
      <div class="inline-flex items-center gap-1.5 px-3 py-1 bg-orange-100/80 border border-orange-200/60 rounded-full text-orange-600 text-xs font-bold mb-3 shadow-xs">
        <span class="w-2 h-2 rounded-full bg-orange-500 animate-pulse"></span>
        <span>优雅解决午餐选择困难症</span>
      </div>
      <h1 class="text-3xl sm:text-4xl md:text-5xl font-black bg-gradient-to-r from-orange-600 via-amber-500 to-rose-500 bg-clip-text text-transparent tracking-tight flex items-center justify-center gap-3">
        <span>今天中午吃什么？</span>
        <span class="inline-flex items-center justify-center p-2 bg-orange-100 border border-orange-200 rounded-2xl shadow-sm text-orange-500 animate-bounce">
          <svg class="w-7 h-7 sm:w-8 sm:h-8" fill="none" stroke="currentColor" viewBox="0 0 24 24" stroke-width="2">
            <path stroke-linecap="round" stroke-linejoin="round" d="M12 4.5c-4.142 0-7.5 1.567-7.5 3.5h15c0-1.933-3.358-3.5-7.5-3.5zM4 11h16M4 15.5h16M5.5 19.5h13a1.5 1.5 0 001.5-1.5v-.5H4v.5a1.5 1.5 0 001.5 1.5z"></path>
          </svg>
        </span>
      </h1>
      <p class="text-xs sm:text-sm text-slate-500 mt-2.5 font-medium tracking-wide">
        转出美味，把决定权交给好运 🍀
      </p>
    </header>

    <!-- 主体区域：响应式双列布局 -->
    <main class="w-full max-w-5xl mx-auto grid grid-cols-1 lg:grid-cols-12 gap-6 my-auto items-start relative z-10">
      <!-- 左侧：抽奖舞台 -->
      <section class="lg:col-span-5 flex flex-col items-center bg-white/80 border border-orange-100/80 backdrop-blur-xl rounded-3xl p-6 shadow-xl shadow-orange-950/5 relative">
        <!-- 模式切换 TAB -->
        <div class="flex bg-slate-100/80 p-1.5 rounded-2xl border border-slate-200/60 mb-6 w-full max-w-xs shadow-inner">
          <button
            @click="mode = 'wheel'"
            :class="[
              'flex-1 py-2 text-xs font-bold rounded-xl transition-all duration-300 flex items-center justify-center gap-1.5 cursor-pointer',
              mode === 'wheel' ? 'bg-gradient-to-r from-orange-500 to-amber-500 text-white shadow-md shadow-orange-500/20' : 'text-slate-500 hover:text-slate-800'
            ]"
          >
            🎯 幸运大转盘
          </button>
          <button
            @click="mode = 'slot'"
            :class="[
              'flex-1 py-2 text-xs font-bold rounded-xl transition-all duration-300 flex items-center justify-center gap-1.5 cursor-pointer',
              mode === 'slot' ? 'bg-gradient-to-r from-orange-500 to-amber-500 text-white shadow-md shadow-orange-500/20' : 'text-slate-500 hover:text-slate-800'
            ]"
          >
            🎰 美食老虎机
          </button>
        </div>

        <!-- 抽奖展示区 -->
        <div class="relative flex items-center justify-center my-2">
          <!-- 1. 转盘模式 -->
          <div v-show="mode === 'wheel'" class="relative flex items-center justify-center">
            <div class="absolute -top-3.5 z-20 w-0 h-0 border-x-8 border-x-transparent border-t-[20px] border-t-rose-500 drop-shadow-[0_4px_8px_rgba(244,63,94,0.4)]"></div>
            <div class="relative w-64 h-64 sm:w-72 sm:h-72 lg:w-76 lg:h-76 flex items-center justify-center p-1.5 rounded-full bg-white border-4 border-orange-100/80 shadow-2xl shadow-orange-500/10">
              <canvas ref="wheelCanvas" class="w-full h-full rounded-full transition-transform duration-75"></canvas>
            </div>
            <button
              @click="startDraw"
              :disabled="isRolling || activeMenu.length === 0"
              class="absolute z-10 w-16 h-16 rounded-full bg-slate-900 border-2 border-amber-300 text-amber-300 font-black text-base flex items-center justify-center shadow-lg shadow-slate-950/20 hover:scale-105 active:scale-95 disabled:opacity-50 transition-all cursor-pointer"
            >
              GO!
            </button>
          </div>

          <!-- 2. 老虎机模式 -->
          <div v-show="mode === 'slot'" class="w-64 sm:w-72 lg:w-76 h-64 sm:h-72 lg:h-76 flex flex-col items-center justify-center bg-orange-50/50 border border-orange-100 rounded-3xl p-4 shadow-inner relative overflow-hidden">
            <div class="w-full bg-white border border-orange-200/60 rounded-2xl py-12 px-4 text-center my-auto shadow-lg shadow-orange-950/5 relative overflow-hidden">
              <span 
                class="text-2xl sm:text-3xl font-black transition-all duration-75 block text-orange-600 tracking-wide"
                :class="{ 'scale-110 text-orange-500': isRolling }"
              >
                {{ currentDisplay || '准备好了吗？' }}
              </span>
            </div>
          </div>
        </div>

        <!-- 主抽奖按钮 -->
        <button
          @click="startDraw"
          :disabled="isRolling || activeMenu.length === 0"
          class="w-full py-4 mt-6 bg-gradient-to-r from-orange-500 via-amber-500 to-orange-500 hover:opacity-95 disabled:from-slate-200 disabled:to-slate-200 disabled:text-slate-400 text-white font-black text-lg rounded-2xl transition-all shadow-lg shadow-orange-500/25 active:scale-[0.98] cursor-pointer flex items-center justify-center gap-2"
        >
          <span>🎲</span>
          <span>{{ isRolling ? '抽取中...' : '帮我决定！' }}</span>
        </button>
      </section>

      <!-- 右侧：场景预设与菜单管理 -->
      <section class="lg:col-span-7 flex flex-col gap-6">
        <!-- 快捷场景切换 -->
        <div class="bg-white/80 border border-orange-100/80 backdrop-blur-xl rounded-3xl p-5 shadow-xl shadow-orange-950/5">
          <div class="flex items-center justify-between gap-2 mb-3">
            <h2 class="text-xs sm:text-sm font-bold text-slate-700 flex items-center gap-2">
              <span class="text-orange-500">⚡</span> 快捷场景预设
            </h2>
            <span class="text-xs text-slate-400">{{ activeMenu.length }}/{{ menu.length }} 可选</span>
          </div>
          
          <div class="grid grid-cols-2 sm:grid-cols-4 gap-2.5">
            <button
              v-for="(preset, key) in presets"
              :key="key"
              @click="loadPreset(key)"
              class="px-3 py-3 bg-orange-50/40 hover:bg-orange-100/60 border border-orange-100/80 rounded-2xl text-xs font-bold text-slate-700 hover:text-orange-600 transition-all text-center flex flex-col items-center gap-1.5 active:scale-95 cursor-pointer"
            >
              <span class="text-xl">{{ preset.icon }}</span>
              <span>{{ preset.name }}</span>
            </button>
          </div>
        </div>

        <!-- 菜单管理区 -->
        <div class="bg-white/80 border border-orange-100/80 backdrop-blur-xl rounded-3xl p-5 shadow-xl shadow-orange-950/5 space-y-4">
          <div class="flex gap-2">
            <input
              v-model="newItem"
              @keyup.enter="addItem"
              type="text"
              placeholder="输入你想吃的 (如: 螺蛳粉、牛肉汤)..."
              class="flex-1 bg-slate-50/80 border border-slate-200/80 rounded-2xl px-4 py-3 text-sm text-slate-800 placeholder-slate-400 focus:outline-none focus:border-orange-400 focus:bg-white transition-all"
            />
            <button
              @click="addItem"
              class="px-5 py-3 bg-slate-900 hover:bg-slate-800 text-white font-bold rounded-2xl text-sm transition-all active:scale-95 cursor-pointer flex items-center gap-1 shadow-md shadow-slate-950/10"
            >
              <span>+</span> 添加
            </button>
          </div>

          <div class="flex items-center justify-between text-xs text-slate-400 px-1 pt-1 border-t border-slate-100">
            <span>点击标签切换【开启/禁用】</span>
            <div class="flex gap-4">
              <button @click="resetDefault" class="hover:text-orange-600 transition-colors cursor-pointer">恢复默认</button>
              <button @click="clearAll" class="hover:text-rose-500 transition-colors cursor-pointer">清空全部</button>
            </div>
          </div>

          <div class="flex flex-wrap gap-2.5 max-h-56 overflow-y-auto p-1 custom-scrollbar">
            <div
              v-for="(item, index) in menu"
              :key="index"
              @click="toggleItem(index)"
              :style="item.enabled ? { backgroundColor: item.bgColor, borderColor: item.borderColor, color: item.textColor } : {}"
              :class="[
                'px-3.5 py-2 rounded-2xl text-xs font-bold flex items-center gap-2 cursor-pointer transition-all border shadow-xs hover:scale-[1.02]',
                !item.enabled && 'bg-slate-100 border-slate-200 text-slate-400 line-through opacity-60'
              ]"
            >
              <span>{{ item.icon }} {{ item.text }}</span>
              <button
                @click.stop="removeItem(index)"
                class="hover:opacity-100 opacity-60 font-bold text-sm ml-1"
              >
                ×
              </button>
            </div>
          </div>
        </div>
      </section>
    </main>

    <!-- 📌 右下角常驻悬浮分享按钮 (FAB) -->
    <button
      @click="showShareMenuModal = true"
      class="fixed bottom-6 right-6 z-40 bg-gradient-to-r from-orange-500 to-amber-500 hover:from-orange-600 hover:to-amber-600 text-white font-black px-4 py-3 sm:px-5 sm:py-3.5 rounded-full shadow-2xl shadow-orange-500/40 hover:scale-105 active:scale-95 transition-all cursor-pointer flex items-center gap-2 border-2 border-white/80"
    >
      <span class="text-base sm:text-lg animate-pulse">✨</span>
      <span class="text-xs sm:text-sm tracking-wide">分享应用</span>
    </button>

    <!-- 聚合分享弹窗 Modal -->
    <div 
      v-if="showShareMenuModal" 
      class="fixed inset-0 z-50 bg-slate-900/40 backdrop-blur-md flex items-center justify-center p-4 animate-fade-in"
      @click="showShareMenuModal = false"
    >
      <div 
        class="bg-white border border-orange-100 rounded-3xl p-6 max-w-sm w-full shadow-2xl shadow-orange-950/20 relative overflow-hidden animate-scale-up"
        @click.stop
      >
        <div class="flex items-center justify-between mb-4 pb-2 border-b border-slate-100">
          <h3 class="text-base font-black text-slate-800 flex items-center gap-1.5">
            <span>✨</span> 分享今天吃什么
          </h3>
          <button @click="showShareMenuModal = false" class="text-slate-400 hover:text-slate-600 font-bold text-lg">×</button>
        </div>

        <div class="space-y-2.5">
          <!-- 方式 1: 复制菜单链接 -->
          <button 
            @click="copyMenuUrl"
            class="w-full p-3.5 bg-orange-50/80 hover:bg-orange-100/80 border border-orange-200/80 rounded-2xl text-left transition-all flex items-center justify-between group cursor-pointer"
          >
            <div class="flex items-center gap-3">
              <span class="p-2 bg-orange-500 text-white rounded-xl text-lg">🔗</span>
              <div>
                <p class="text-xs font-bold text-slate-800">复制菜单链接</p>
                <p class="text-[11px] text-slate-500 mt-0.5">同步当前你选中的菜谱给好友</p>
              </div>
            </div>
            <span class="text-xs font-bold text-orange-600 group-hover:translate-x-1 transition-transform">复制 →</span>
          </button>

          <!-- 方式 2: 生成拍立得海报 -->
          <button 
            @click="triggerPosterGenerate"
            class="w-full p-3.5 bg-amber-50/80 hover:bg-amber-100/80 border border-amber-200/80 rounded-2xl text-left transition-all flex items-center justify-between group cursor-pointer"
          >
            <div class="flex items-center gap-3">
              <span class="p-2 bg-amber-500 text-white rounded-xl text-lg">📸</span>
              <div>
                <p class="text-xs font-bold text-slate-800">生成精致海报</p>
                <p class="text-[11px] text-slate-500 mt-0.5">生成带专属网址与签名图片</p>
              </div>
            </div>
            <span class="text-xs font-bold text-amber-700 group-hover:translate-x-1 transition-transform">生成 →</span>
          </button>

          <!-- 方式 3: 系统/微信原生分享 -->
          <button 
            @click="triggerNativeShare"
            class="w-full p-3.5 bg-rose-50/80 hover:bg-rose-100/80 border border-rose-200/80 rounded-2xl text-left transition-all flex items-center justify-between group cursor-pointer"
          >
            <div class="flex items-center gap-3">
              <span class="p-2 bg-rose-500 text-white rounded-xl text-lg">📲</span>
              <div>
                <p class="text-xs font-bold text-slate-800">发送给微信/好友</p>
                <p class="text-[11px] text-slate-500 mt-0.5">拉起手机原生分享面板</p>
              </div>
            </div>
            <span class="text-xs font-bold text-rose-600 group-hover:translate-x-1 transition-transform">分享 →</span>
          </button>
        </div>
      </div>
    </div>

    <!-- 抽中结果弹窗 -->
    <div 
      v-if="showModal && winnerItem" 
      class="fixed inset-0 z-50 bg-slate-900/40 backdrop-blur-md flex items-center justify-center p-4 animate-fade-in"
    >
      <div class="bg-white border border-orange-100 rounded-3xl p-6 sm:p-8 max-w-sm w-full text-center shadow-2xl shadow-orange-950/20 relative overflow-hidden animate-scale-up">
        <div class="absolute -top-20 -left-20 w-40 h-40 bg-orange-200/50 rounded-full blur-2xl pointer-events-none"></div>
        <div class="absolute -bottom-20 -right-20 w-40 h-40 bg-amber-200/50 rounded-full blur-2xl pointer-events-none"></div>

        <div class="text-6xl my-3 animate-bounce">{{ winnerItem.icon }}</div>
        <h3 class="text-xs font-black text-orange-500 uppercase tracking-widest mt-2">大吉大利 今天就吃</h3>
        <p class="text-3xl font-black text-slate-800 mt-1 mb-6 bg-gradient-to-r from-orange-600 to-amber-600 bg-clip-text text-transparent">
          {{ winnerItem.text }}
        </p>

        <div class="flex flex-col gap-2.5">
          <button
            @click="showModal = false"
            class="w-full py-3.5 bg-gradient-to-r from-orange-500 to-amber-500 hover:opacity-95 text-white font-black rounded-2xl shadow-lg shadow-orange-500/25 transition-all active:scale-95 cursor-pointer"
          >
            好嘞，就吃这个！ 😋
          </button>
          
          <div class="grid grid-cols-2 gap-2">
            <button
              @click="generatePoster(winnerItem.text)"
              class="py-2.5 bg-orange-50 hover:bg-orange-100 border border-orange-200/80 text-orange-600 font-bold rounded-2xl text-xs transition-all cursor-pointer flex items-center justify-center gap-1"
            >
              <span>📸</span> 导出海报
            </button>
            <button
              @click="excludeAndReroll"
              class="py-2.5 bg-slate-100 hover:bg-slate-200 text-slate-500 hover:text-rose-500 font-bold rounded-2xl text-xs transition-all cursor-pointer"
            >
              不喜欢重抽 🔄
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- 海报预览弹窗 -->
    <div 
      v-if="posterImgUrl" 
      class="fixed inset-0 z-50 bg-slate-900/80 backdrop-blur-md flex flex-col items-center justify-center p-4 animate-fade-in"
      @click="posterImgUrl = ''"
    >
      <div class="relative max-w-xs sm:max-w-sm w-full bg-white rounded-3xl p-3 shadow-2xl animate-scale-up" @click.stop>
        <img :src="posterImgUrl" alt="美食决定海报" class="w-full h-auto rounded-2xl border" />
        <p class="text-center text-xs text-slate-500 mt-3 mb-1 font-bold">提示：在手机上可长按上方图片保存到相册 📱</p>
        <button 
          @click="posterImgUrl = ''" 
          class="w-full py-2 bg-slate-100 hover:bg-slate-200 text-slate-700 text-xs font-bold rounded-xl mt-2 cursor-pointer"
        >
          关闭预览
        </button>
      </div>
    </div>

    <!-- Toast 轻提示 -->
    <div 
      v-if="toastMessage" 
      class="fixed bottom-20 left-1/2 -translate-x-1/2 z-50 bg-slate-900/90 text-white px-5 py-2.5 rounded-full text-xs font-bold shadow-xl animate-fade-in flex items-center gap-2"
    >
      <span>✨</span> {{ toastMessage }}
    </div>

    <!-- 页脚 -->
    <footer class="mt-8 text-center text-xs text-slate-400 py-3 relative z-10">
      <p>
        <a 
          href="https://t.me/xiaoyuGeG" 
          target="_blank" 
          rel="noopener noreferrer"
          class="hover:text-orange-500 transition-colors underline decoration-slate-300 underline-offset-4 font-medium"
        >
          作者：小鱼哥哥
        </a>
      </p>
    </footer>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, watch, nextTick } from 'vue'
import confetti from 'canvas-confetti'

interface MenuItem {
  text: string
  icon: string
  enabled: boolean
  bgColor: string
  borderColor: string
  textColor: string
}

const foodEmojiLibrary = [
  '🍔', '🍕', '🍟', '🌭', '🍿', '🥓', '🍳', '🧇', '🥞', '🥐', 
  '🍞', '🥨', '🧀', '🥗', '🥣', '🍱', '🍘', '🍙', '🍚', '🍛', 
  '🍜', '🍝', '🍢', '🍣', '🍤', '🍥', '🥮', '🍡', '🥟', '🥡', 
  '🦪', '🥩', '🍗', '🍖', '🍲', '🌮', '🌯', '🥙', '🧆', '🍦'
]

const colorPalette = [
  { bg: 'rgba(255, 237, 213, 0.6)', border: 'rgba(253, 186, 116, 0.8)', text: '#c2410c' },
  { bg: 'rgba(254, 243, 199, 0.6)', border: 'rgba(252, 211, 77, 0.8)',  text: '#b45309' },
  { bg: 'rgba(207, 250, 254, 0.6)', border: 'rgba(103, 232, 249, 0.8)', text: '#0e7490' },
  { bg: 'rgba(209, 250, 229, 0.6)', border: 'rgba(110, 231, 183, 0.8)', text: '#047857' },
  { bg: 'rgba(252, 231, 243, 0.6)', border: 'rgba(249, 168, 212, 0.8)', text: '#be185d' },
  { bg: 'rgba(238, 242, 255, 0.6)', border: 'rgba(199, 210, 254, 0.8)', text: '#4338ca' }
]

const getGourmetMeta = (text: string) => {
  if (text.includes('麦当劳') || text.includes('汉堡')) return { icon: '🍔', ...colorPalette[0] }
  if (text.includes('肯德基') || text.includes('炸鸡')) return { icon: '🍗', ...colorPalette[0] }
  if (text.includes('火锅') || text.includes('麻辣烫') || text.includes('串串')) return { icon: '🍲', ...colorPalette[4] }
  if (text.includes('鱼')) return { icon: '🐟', ...colorPalette[2] }
  if (text.includes('面') || text.includes('粉') || text.includes('拉面')) return { icon: '🍜', ...colorPalette[1] }
  if (text.includes('日料') || text.includes('寿司') || text.includes('刺身')) return { icon: '🍣', ...colorPalette[2] }
  if (text.includes('烧烤') || text.includes('烤')) return { icon: '🍢', ...colorPalette[0] }
  if (text.includes('沙拉') || text.includes('轻食')) return { icon: '🥗', ...colorPalette[3] }
  if (text.includes('披萨')) return { icon: '🍕', ...colorPalette[1] }
  if (text.includes('饺') || text.includes('包子')) return { icon: '🥟', ...colorPalette[1] }

  let hash = 0
  for (let i = 0; i < text.length; i++) {
    hash = text.charCodeAt(i) + ((hash << 5) - hash)
  }
  const positiveHash = Math.abs(hash)
  return {
    icon: foodEmojiLibrary[positiveHash % foodEmojiLibrary.length],
    ...colorPalette[positiveHash % colorPalette.length]
  }
}

const presets: Record<string, { name: string; icon: string; items: string[] }> = {
  fast: { name: '工作日快餐', icon: '⚡', items: ['黄焖鸡米饭', '麻辣烫', '兰州拉面', '麦当劳', '便利店便当', '沙县小吃'] },
  treat: { name: '犒劳大餐', icon: '🍲', items: ['海底捞火锅', '酸菜鱼', '日料刺身', '韩式烤肉', '烤鸭', '牛排'] },
  light: { name: '轻食健康', icon: '🥗', items: ['沙拉碗', '海南鸡饭', '赛百味', '关东煮', '清蒸海鲜'] },
  night: { name: '夜宵小吃', icon: '🍢', items: ['烧烤', '炸鸡啤酒', '螺蛳粉', '小龙虾', '串串香'] }
}

const mode = ref<'wheel' | 'slot'>('wheel')
const menu = ref<MenuItem[]>([])
const newItem = ref('')
const currentDisplay = ref('')
const winnerItem = ref<MenuItem | null>(null)
const showModal = ref(false)
const showShareMenuModal = ref(false)
const isRolling = ref(false)
const toastMessage = ref('')
const posterImgUrl = ref('')

const wheelCanvas = ref<HTMLCanvasElement | null>(null)
const activeMenu = computed(() => menu.value.filter(item => item.enabled))
const wheelColors = ['#FF6B35', '#FFB800', '#2EC4B6', '#E71D36', '#FF9F1C', '#10B981', '#8B5CF6', '#F43F5E']

const showToast = (msg: string) => {
  toastMessage.value = msg
  setTimeout(() => { toastMessage.value = '' }, 2500)
}

const drawWheel = (rotationAngle = 0) => {
  const canvas = wheelCanvas.value
  if (!canvas) return
  const ctx = canvas.getContext('2d')
  if (!ctx) return

  const dpr = window.devicePixelRatio || 1
  const size = canvas.clientWidth
  canvas.width = size * dpr
  canvas.height = size * dpr
  ctx.scale(dpr, dpr)

  const items = activeMenu.value
  const total = items.length

  ctx.clearRect(0, 0, size, size)

  if (total === 0) {
    ctx.fillStyle = '#F1F5F9'
    ctx.beginPath()
    ctx.arc(size / 2, size / 2, size / 2 - 5, 0, Math.PI * 2)
    ctx.fill()
    ctx.fillStyle = '#94A3B8'
    ctx.font = '14px sans-serif'
    ctx.textAlign = 'center'
    ctx.textBaseline = 'middle'
    ctx.fillText('请开启或添加餐食', size / 2, size / 2)
    return
  }

  const arc = (Math.PI * 2) / total
  const centerX = size / 2
  const centerY = size / 2
  const radius = size / 2 - 5

  for (let i = 0; i < total; i++) {
    const angle = rotationAngle + i * arc
    ctx.fillStyle = wheelColors[i % wheelColors.length]
    ctx.beginPath()
    ctx.moveTo(centerX, centerY)
    ctx.arc(centerX, centerY, radius, angle, angle + arc)
    ctx.lineTo(centerX, centerY)
    ctx.fill()

    ctx.strokeStyle = '#FFFFFF'
    ctx.lineWidth = 3
    ctx.stroke()

    ctx.save()
    ctx.translate(centerX, centerY)
    ctx.rotate(angle + arc / 2)
    ctx.fillStyle = '#FFFFFF'
    ctx.font = 'bold 12px sans-serif'
    ctx.textAlign = 'right'
    ctx.textBaseline = 'middle'
    const label = `${items[i].icon} ${items[i].text}`
    ctx.fillText(label.length > 8 ? label.substring(0, 7) + '..' : label, radius - 15, 0)
    ctx.restore()
  }
}

let currentRotation = 0

onMounted(() => {
  const params = new URLSearchParams(window.location.search)
  const sharedItems = params.get('items')
  
  if (sharedItems) {
    try {
      const parsed = JSON.parse(decodeURIComponent(sharedItems))
      if (Array.isArray(parsed) && parsed.length > 0) {
        menu.value = parsed.map(text => {
          const meta = getGourmetMeta(text)
          return { text, icon: meta.icon, bgColor: meta.bg, borderColor: meta.border, textColor: meta.text, enabled: true }
        })
        showToast('已成功同步好友分享的菜单！')
      }
    } catch (e) {
      console.error(e)
    }
  } else {
    const saved = localStorage.getItem('my_lunch_menu_v6')
    if (saved) {
      menu.value = JSON.parse(saved)
    } else {
      loadPreset('fast')
    }
  }

  nextTick(() => drawWheel(currentRotation))
})

watch(menu, (newMenu) => {
  localStorage.setItem('my_lunch_menu_v6', JSON.stringify(newMenu))
  nextTick(() => drawWheel(currentRotation))
}, { deep: true })

watch(mode, () => {
  nextTick(() => drawWheel(currentRotation))
})

const addItem = () => {
  const text = newItem.value.trim()
  if (text && !menu.value.some(m => m.text === text)) {
    const meta = getGourmetMeta(text)
    menu.value.push({ text, icon: meta.icon, bgColor: meta.bg, borderColor: meta.border, textColor: meta.text, enabled: true })
    newItem.value = ''
  }
}

const removeItem = (index: number) => { menu.value.splice(index, 1) }
const toggleItem = (index: number) => { menu.value[index].enabled = !menu.value[index].enabled }

const loadPreset = (key: string) => {
  if (presets[key]) {
    menu.value = presets[key].items.map(text => {
      const meta = getGourmetMeta(text)
      return { text, icon: meta.icon, bgColor: meta.bg, borderColor: meta.border, textColor: meta.text, enabled: true }
    })
  }
}

const resetDefault = () => { loadPreset('fast') }
const clearAll = () => { menu.value = [] }

const startDraw = () => {
  const items = activeMenu.value
  if (items.length === 0 || isRolling.value) return

  isRolling.value = true
  showModal.value = false

  if (mode.value === 'wheel') {
    const totalRounds = 5
    const randomIndex = Math.floor(Math.random() * items.length)
    const arc = (Math.PI * 2) / items.length
    const targetAngle = (Math.PI * 2 * totalRounds) + (items.length - randomIndex - 0.5) * arc - Math.PI / 2

    let start: number | null = null
    const duration = 3500

    const animate = (timestamp: number) => {
      if (!start) start = timestamp
      const progress = Math.min((timestamp - start) / duration, 1)
      const easeOut = (t: number) => 1 - Math.pow(1 - t, 3)
      currentRotation = targetAngle * easeOut(progress)

      drawWheel(currentRotation)

      if (progress < 1) {
        requestAnimationFrame(animate)
      } else {
        isRolling.value = false
        winnerItem.value = items[randomIndex]
        showModal.value = true
        triggerConfetti()
      }
    }
    requestAnimationFrame(animate)
  } else {
    let count = 0
    const totalTicks = 30
    const interval = setInterval(() => {
      const idx = Math.floor(Math.random() * items.length)
      currentDisplay.value = `${items[idx].icon} ${items[idx].text}`
      count++

      if (count >= totalTicks) {
        clearInterval(interval)
        isRolling.value = false
        winnerItem.value = items[count % items.length]
        showModal.value = true
        triggerConfetti()
      }
    }, 70)
  }
}

const excludeAndReroll = () => {
  if (winnerItem.value) {
    const target = menu.value.find(m => m.text === winnerItem.value?.text)
    if (target) target.enabled = false
  }
  showModal.value = false
  setTimeout(() => startDraw(), 300)
}

// ---------------- 悬浮分享弹窗操作 ----------------

const buildShareUrl = () => {
  const activeItems = activeMenu.value.map(i => i.text)
  const encoded = encodeURIComponent(JSON.stringify(activeItems))
  return `${window.location.origin}${window.location.pathname}?items=${encoded}`
}

// 方式 1: 复制链接
const copyMenuUrl = () => {
  const url = buildShareUrl()
  if (navigator.clipboard) {
    navigator.clipboard.writeText(url)
    showToast('菜单链接已复制到剪贴板！')
  } else {
    showToast('当前环境不支持，请手动复制浏览器地址栏')
  }
  showShareMenuModal.value = false
}

// 方式 2: 生成海报
const triggerPosterGenerate = () => {
  showShareMenuModal.value = false
  generatePoster(winnerItem.value?.text || activeMenu.value[0]?.text || '美食')
}

// 方式 3: 原生分享
const triggerNativeShare = async () => {
  const url = buildShareUrl()
  showShareMenuModal.value = false
  if (navigator.share) {
    try {
      await navigator.share({
        title: '今天中午吃什么？',
        text: '我为你挑选了一份午餐决选菜单，快来抽一把！',
        url: url
      })
      showToast('系统分享框已拉起！')
    } catch (err) {
      console.log('取消分享')
    }
  } else {
    copyMenuUrl()
  }
}

// Canvas 绘制带作者与网址的海报
const generatePoster = (foodText: string) => {
  const targetMeta = getGourmetMeta(foodText)

  const canvas = document.createElement('canvas')
  canvas.width = 600
  canvas.height = 800
  const ctx = canvas.getContext('2d')
  if (!ctx) return

  const bgGradient = ctx.createLinearGradient(0, 0, 0, 800)
  bgGradient.addColorStop(0, '#FAF8F5')
  bgGradient.addColorStop(1, '#FFF3E0')
  ctx.fillStyle = bgGradient
  ctx.fillRect(0, 0, 600, 800)

  ctx.fillStyle = '#FFFFFF'
  ctx.shadowColor = 'rgba(0, 0, 0, 0.08)'
  ctx.shadowBlur = 30
  ctx.shadowOffsetY = 10
  ctx.roundRect(40, 50, 520, 700, 24)
  ctx.fill()
  ctx.shadowColor = 'transparent'

  ctx.fillStyle = '#F97316'
  ctx.font = 'bold 24px sans-serif'
  ctx.textAlign = 'center'
  ctx.fillText('✨ 今天中午吃什么？', 300, 115)

  ctx.fillStyle = '#FFF7ED'
  ctx.beginPath()
  ctx.arc(300, 270, 90, 0, Math.PI * 2)
  ctx.fill()

  ctx.font = '80px sans-serif'
  ctx.fillText(targetMeta.icon, 300, 300)

  ctx.fillStyle = '#94A3B8'
  ctx.font = '14px sans-serif'
  ctx.fillText('今日美食决定', 300, 410)

  ctx.fillStyle = '#1E293B'
  ctx.font = 'bold 42px sans-serif'
  ctx.fillText(foodText, 300, 470)

  ctx.strokeStyle = '#E2E8F0'
  ctx.lineWidth = 1
  ctx.beginPath()
  ctx.moveTo(90, 530)
  ctx.lineTo(510, 530)
  ctx.stroke()

  const currentHost = window.location.host && window.location.host !== 'localhost:5173' 
    ? window.location.origin 
    : 'https://what-to-eat.vercel.app'
    
  const dateStr = new Date().toLocaleDateString('zh-CN', { year: 'numeric', month: '2-digit', day: '2-digit' })
  
  ctx.fillStyle = '#64748B'
  ctx.font = '14px sans-serif'
  ctx.fillText(`📅 决定时间：${dateStr}`, 300, 580)
  
  ctx.fillStyle = '#F97316'
  ctx.font = 'bold 15px sans-serif'
  ctx.fillText(`🌐 体验网站：${currentHost}`, 300, 620)

  ctx.fillStyle = '#94A3B8'
  ctx.font = '13px sans-serif'
  ctx.fillText('作者：小鱼哥哥 (t.me/xiaoyuGeG)', 300, 655)

  const imgData = canvas.toDataURL('image/png')
  posterImgUrl.value = imgData

  const link = document.createElement('a')
  link.download = `今日吃什么-${foodText}.png`
  link.href = imgData
  link.click()

  showToast('海报已生成！包含了网址与署名')
}

const triggerConfetti = () => {
  confetti({ particleCount: 100, spread: 80, origin: { y: 0.6 } })
}
</script>

<style scoped>
@keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
@keyframes scaleUp { from { transform: scale(0.9); opacity: 0; } to { transform: scale(1); opacity: 1; } }
.animate-fade-in { animation: fadeIn 0.25s ease-out forwards; }
.animate-scale-up { animation: scaleUp 0.3s cubic-bezier(0.34, 1.56, 0.64, 1) forwards; }
.custom-scrollbar::-webkit-scrollbar { width: 4px; }
.custom-scrollbar::-webkit-scrollbar-track { background: rgba(241, 245, 249, 0.8); border-radius: 4px; }
.custom-scrollbar::-webkit-scrollbar-thumb { background: rgba(203, 213, 225, 0.8); border-radius: 4px; }
</style>