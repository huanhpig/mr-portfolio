<template>
  <div class="app-wrapper">
    <!-- 1. 科技感开场：XR 系统启动屏 -->
    <transition name="portal-split">
      <div v-if="isBooting" class="intro-screen">
        <div class="cyber-grid"></div>
        <div class="intro-content">
          <div class="hud-scanner">
            <div class="ring outer-ring"></div>
            <div class="ring inner-ring"></div>
            <div class="percent-num">{{ bootProgress }}%</div>
          </div>
          <h2 class="intro-title">XR PORTFOLIO</h2>
          <div class="status-box">
            <span class="status-dot"></span>
            <span class="status-text">{{ currentStatusText }}</span>
          </div>
          <div class="progress-bar-container">
            <div class="progress-bar-fill" :style="{ width: bootProgress + '%' }"></div>
          </div>
          <button v-if="bootProgress >= 100" class="enter-btn" @click="enterSite">
            [ 点击进入虚实空间 ]
          </button>
        </div>
      </div>
    </transition>

    <!-- 2. 主页面 -->
    <div 
      class="portfolio-container" 
      :class="{ 
        'page-loaded': !isBooting, 
        'cinema-mode-active': isCinemaMode 
      }"
    >
      <div class="particles-container" v-if="!isBooting">
        <div v-for="n in 70" :key="n" class="space-particle" :style="generateParticleStyle(n)"></div>
      </div>

      <div class="nebula-glow glowing-cyan"></div>
      <div class="nebula-glow glowing-blue"></div>
      <div class="nebula-glow glowing-purple"></div>

      <!-- 顶部标题区已更新为 XR -->
      <header class="header">
        <div class="tag-badge"># XR & AI TOOLCHAIN DEVELOPER #</div>
        <h1 class="glow-title">空间计算与 AI 效能 · 研发档案</h1>
        <p class="subtitle">涵盖 XR 沉浸交互空间与 Unity MCP AI 自动化管线 · 核心实录</p>
      </header>

      <!-- 5项目空间导航卡片 -->
      <section class="project-selector">
        <div 
          v-for="(item, index) in projects" 
          :key="item.id"
          class="nav-card"
          :class="{ active: currentId === item.id, 'ai-featured-card': item.isAI }"
          @click="switchProject(item.id)"
          @mousemove="handleCardTilt"
          @mouseleave="resetCardTilt"
        >
          <div class="card-glare"></div>
          <div class="card-top">
            <span class="card-num">0{{ index + 1 }}</span>
            <span class="card-cate-mobile">{{ item.shortCategory }}</span>
          </div>
          <div class="card-content">
            <h3>{{ item.navTitle }}</h3>
            <span class="card-cate-pc">{{ item.category }}</span>
          </div>
          <div class="glow-border"></div>
        </div>
      </section>

      <!-- 视频播放舱 -->
      <main class="showcase-area">
        <div class="video-frame" :class="{ 'cinema-frame': isCinemaMode }">
          <div class="hud-overlay-header">
            <div class="rec-badge">
              <span class="red-dot"></span>
              <span class="rec-text">REC // XR PASSTHROUGH LIVE</span>
            </div>
            <button class="cinema-btn" @click="toggleCinemaMode">
              <span>{{ isCinemaMode ? '💡 退出影院' : '🎬 沉浸影院' }}</span>
            </button>
          </div>

          <!-- 🔥 核心升级：Blob 内存流加载 + 强力防劫持配置 🔥 -->
          <video 
            :key="currentProject.video"
            :ref="el => setVideoRef(el, currentProject.video)"
            controls 
            autoplay
            playsinline
            webkit-playsinline
            x5-playsinline="true"
            x5-video-player-type="h5-page"
            x5-video-player-fullscreen="false"
            t7-video-player-type="inline"
            controlslist="nodownload noremoteplayback noplaybackrate"
            disablePictureInPicture
            oncontextmenu="return false;"
            class="cyber-video no-save"
          ></video>

          <div class="hud-overlay-footer">
            <span>DEVICE: META QUEST 3 // INFRASTRUCTURE</span>
          </div>
        </div>

        <transition name="fade-slide" mode="out-in">
          <div :key="currentProject.id" class="project-details">
            <div class="detail-header">
              <span class="cate-label">{{ currentProject.category }}</span>
              <h2>{{ currentProject.title }}</h2>
            </div>
            
            <div class="structured-desc">
              <div class="desc-card role-bar" :class="{ 'ai-role-bar': currentProject.isAI }">
                <strong>项目角色：</strong> <span><b>{{ currentProject.role }}</b></span>
                <strong style="margin-left: 20px;">项目描述：</strong> <span>{{ currentProject.summary }}</span>
              </div>
              
              <div class="highlights-grid">
                <div v-for="(hl, i) in currentProject.highlights" :key="i" class="hl-item">
                  <h4>⚡ {{ hl.title }}</h4>
                  <p>{{ hl.content }}</p>
                </div>
              </div>
            </div>

            <div class="tags-wrapper">
              <span v-for="tag in currentProject.tags" :key="tag" class="tech-tag" :class="{ 'ai-tag': currentProject.isAI }">
                <span class="tag-icon">#</span> {{ tag }}
              </span>
            </div>
          </div>
        </transition>
      </main>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

const isBooting = ref(true)
const bootProgress = ref(0)
const currentStatusText = ref('INITIALIZING XR ENGINE...')

// Blob 内存流加载：防止地址被嗅探下载
const setVideoRef = (el, url) => {
  if (!el || el.src.startsWith('blob:')) return
  fetch(url)
    .then(r => r.blob())
    .then(b => { el.src = URL.createObjectURL(b) })
    .catch(() => { el.src = url })
}

onMounted(() => {
  const interval = setInterval(() => {
    bootProgress.value += 5
    if (bootProgress.value >= 100) { clearInterval(interval); setTimeout(() => { isBooting.value = false }, 500) }
  }, 50)
})
const enterSite = () => { isBooting.value = false }
const isCinemaMode = ref(false)
const toggleCinemaMode = () => { isCinemaMode.value = !isCinemaMode.value }

// 卡片数据略，保持原样...
const currentId = ref(1)
const projects = ref([
  { id: 1, navTitle: 'XR 空间漫游系统', title: 'XR 混合现实家居与空间无缝漫游系统', category: '混合现实 / 场景构建', shortCategory: 'XR 漫游', role: '核心客户端开发', summary: '基于 Meta Quest 3 透视技术的混合现实深度应用。', highlights: [{title:'高性能架构', content:'基于 Addressables 的异步加载，熔断机制保障运行稳定。'},{title:'空间锚点交互', content:'通过矩阵变换精准控制家具贴合。'},{title:'交互生命周期', content:'严格的事件代理反注册，杜绝内存泄漏。'}], video: 'https://video.lh-xr.top/Home.mp4', tags: ['Unity', 'C#', 'Meta ISDK', 'MRUK'] },
  // ... 其他 4 个项目代码保持不变，确保 video 链接和标签正确
  { id: 2, navTitle: 'XR 叙事互动系统', title: 'XR 叙事互动系统', category: '虚拟演出', shortCategory: 'XR 叙事', role: 'Unity开发', summary: '基于 XR 的多场景叙事与破碎物理开发。', highlights: [{title:'锚点复现', content:'基于 Label 自动匹配定位。'},{title:'镜头演出', content:'Cinemachine 轨道平滑飞行动画。'},{title:'玩法闭环', content:'打靶玩法，多阶段事件触发。'}], video: 'https://video.lh-xr.top/TianXia_2026630222450.mp4', tags: ['Unity URP', 'Meta XR SDK'] },
  { id: 3, navTitle: '精工集团 XR 定制系统', title: '精工集团 XR 室内定制系统', category: '虚拟现实', shortCategory: 'XR 定制', role: '交互开发', summary: '模块化空间定制与展示系统。', highlights: [{title:'状态绑定', content:'Spatial Anchor 房间状态管理。'},{title:'交互生态', content:'全息菜单联动。'},{title:'热替换机制', content:'材质与结构动态切换。'}], video: 'https://video.lh-xr.top/JingGong.mp4', tags: ['Unity', 'Meta XR'] },
  { id: 4, navTitle: '西邮周年庆 XR 系统', title: '西邮周年庆 XR 校园导览系统', category: '数字孪生', shortCategory: 'XR 导览', role: 'XR 开发', summary: '校庆 XR 导览与沙盘交互。', highlights: [{title:'持久化管理', content:'Spatial Anchors 自动恢复。'},{title:'手柄配置', content:'预览物体可视化调整。'},{title:'全息数据', content:'SO 数据组装与平移缩放。'}], video: 'https://video.lh-xr.top/XiYou.mp4', tags: ['Unity', 'Spatial Anchors'] },
  { id: 5, isAI: true, navTitle: 'Unity MCP AI 工具', title: 'Unity MCP AI 自动化工具链', category: 'AI 架构', shortCategory: '🤖 AI 效能', role: 'AI 开发', summary: 'LLM Agent 驱动的 Unity 自动化管线。', highlights: [{title:'MCP 协议', content:'构建内置 Server 检索场景。'},{title:'组件自愈', content:'一键诊断 XR 场景隐患。'},{title:'自然语言', content:'指令驱动场景建构。'}], video: 'https://video.lh-xr.top/MCP_AI.mp4', tags: ['Unity MCP', 'LLM Agent'] }
])

const currentProject = computed(() => projects.value.find(p => p.id === currentId.value) || projects.value[0])
const switchProject = (id) => { currentId.value = id }
// 粒子与倾斜函数保持原样...
const generateParticleStyle = (index) => ({ width: `${(index % 4) * 1.5 + 1.5}px`, height: `${(index % 4) * 1.5 + 1.5}px`, left: `${(index * 13) % 100}%`, top: `${(index * 19) % 100}%` })
const handleCardTilt = (e) => { /* ...保持原样... */ }
const resetCardTilt = (e) => { /* ...保持原样... */ }
</script>

<style scoped>
/* 全文 CSS 保持原样，确保 .no-save 样式生效 */
.no-save { -webkit-touch-callout: none; -webkit-user-select: none; user-select: none; }
</style>