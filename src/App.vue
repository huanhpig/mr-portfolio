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
      <!-- 网页底层：70颗空间粒子群 -->
      <div class="particles-container" v-if="!isBooting">
        <div v-for="n in 70" :key="n" class="space-particle" :style="generateParticleStyle(n)"></div>
      </div>

      <!-- 全息极光氛围光晕 -->
      <div class="nebula-glow glowing-cyan"></div>
      <div class="nebula-glow glowing-blue"></div>
      <div class="nebula-glow glowing-purple"></div>

      <!-- 顶部标题区 -->
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
          :class="{ 
            active: currentId === item.id,
            'ai-featured-card': item.isAI 
          }"
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

      <!-- 次时代 HUD 视频播放舱 -->
      <main class="showcase-area">
        <div class="video-frame" :class="{ 'cinema-frame': isCinemaMode }">
          <span class="hud-corner top-left"></span>
          <span class="hud-corner top-right"></span>
          <span class="hud-corner bottom-left"></span>
          <span class="hud-corner bottom-right"></span>

          <div class="hud-overlay-header">
            <div class="rec-badge">
              <span class="red-dot"></span>
              <span class="rec-text">REC // XR PASSTHROUGH LIVE</span>
            </div>
            <div class="cinema-controls">
              <button class="cinema-btn" @click="toggleCinemaMode">
                <span>{{ isCinemaMode ? '💡 退出影院' : '🎬 沉浸影院' }}</span>
              </button>
            </div>
          </div>

          <video 
            :key="currentProject.video"
            :src="currentProject.video" 
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
            <span>ENGINE: UNITY URP // CORE INTERACTION</span>
          </div>
        </div>

        <!-- 项目结构化排版区 -->
        <transition name="fade-slide" mode="out-in">
          <div :key="currentProject.id" class="project-details">
            <div class="detail-header">
              <span class="cate-label">{{ currentProject.category }}</span>
              <h2>{{ currentProject.title }}</h2>
            </div>
            
            <div class="structured-desc">
              <!-- 核心概述栏 -->
              <div class="desc-card role-bar" :class="{ 'ai-role-bar': currentProject.isAI }">
                <strong>项目角色：</strong> <span><b>{{ currentProject.role }}</b></span>
                <strong style="margin-left: 20px;">项目描述：</strong> <span>{{ currentProject.summary }}</span>
              </div>
              
              <!-- 亮点三网格排版 -->
              <div class="highlights-grid">
                <div v-for="(hl, i) in currentProject.highlights" :key="i" class="hl-item">
                  <h4>⚡ {{ hl.title }}</h4>
                  <p>{{ hl.content }}</p>
                </div>
              </div>
            </div>

            <!-- 技术标签群 -->
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
const currentStatusText = ref('INITIALIZING ENGINE...')
const statusMessages = ['INITIALIZING ENGINE...', 'LOADING META MRUK...', 'INITIALIZING MCP SERVER...', 'PASSTHROUGH ONLINE.', 'SYSTEM READY.']

onMounted(() => {
  const interval = setInterval(() => {
    bootProgress.value += Math.floor(Math.random() * 8) + 5
    if (bootProgress.value >= 100) {
      bootProgress.value = 100
      currentStatusText.value = statusMessages[4]
      clearInterval(interval)
      setTimeout(() => { isBooting.value = false }, 500)
    } else {
      const msgIndex = Math.min(Math.floor((bootProgress.value / 100) * (statusMessages.length - 1)), statusMessages.length - 2)
      currentStatusText.value = statusMessages[msgIndex]
    }
  }, 50)
})
const enterSite = () => { isBooting.value = false }
const isCinemaMode = ref(false)
const toggleCinemaMode = () => { isCinemaMode.value = !isCinemaMode.value }
const generateParticleStyle = (index) => {
  const size = (index % 4) * 1.5 + 1.5
  const left = (index * 13) % 100
  const top = (index * 19) % 100
  const delay = ((index % 10) * 0.3).toFixed(1)
  const duration = ((index % 5) * 1.5 + 3.5).toFixed(1)
  return {
    width: `${size}px`, height: `${size}px`, left: `${left}%`, top: `${top}%`,
    animationDelay: `${delay}s`, animationDuration: `${duration}s`
  }
}
const handleCardTilt = (e) => {
  if (window.innerWidth <= 768) return
  const card = e.currentTarget
  const rect = card.getBoundingClientRect()
  const x = e.clientX - rect.left
  const y = e.clientY - rect.top
  const centerX = rect.width / 2
  const centerY = rect.height / 2
  const rotateX = ((y - centerY) / centerY) * -14
  const rotateY = ((x - centerX) / centerX) * 14
  card.style.transform = `perspective(1000px) rotateX(${rotateX.toFixed(2)}deg) rotateY(${rotateY.toFixed(2)}deg) scale3d(1.03, 1.03, 1.03)`
  const glare = card.querySelector('.card-glare')
  if (glare) {
    glare.style.opacity = '1'
    glare.style.transform = `translate(${x - rect.width}px, ${y - rect.height}px)`
  }
}
const resetCardTilt = (e) => {
  if (window.innerWidth <= 768) return
  const card = e.currentTarget
  card.style.transform = 'perspective(1000px) rotateX(0deg) rotateY(0deg) scale3d(1, 1, 1)'
  const glare = card.querySelector('.card-glare')
  if (glare) glare.style.opacity = '0'
}
const currentId = ref(1)
const projects = ref([
  { id: 1, navTitle: 'XR 空间漫游系统', title: 'XR 混合现实家居与空间无缝漫游系统', category: '混合现实 / 场景构建', shortCategory: 'XR 漫游', role: '核心客户端开发', summary: '基于 Meta Quest 3 透视技术的混合现实深度应用。', highlights: [{title:'高性能架构', content:'基于 Addressables 的异步加载，熔断机制保障运行稳定。'},{title:'空间锚点交互', content:'通过矩阵变换精准控制家具贴合。'},{title:'交互生命周期', content:'严格的事件代理反注册，杜绝内存泄漏。'}], video: 'https://video.lh-xr.top/Home.mp4', tags: ['Unity 2022', 'C#', 'Meta ISDK', 'MRUK', 'Addressables', 'DOTween'] },
  { id: 2, navTitle: 'XR 叙事互动系统', title: 'Unity XR/MR 赛博叙事与镜头互动系统', category: '虚拟演出 / 镜头控制', shortCategory: 'XR 叙事', role: 'Unity 开发工程师', summary: '基于 Unity 开发的扩展现实叙事项目，负责多场景的 MR 空间定位、全景演出、角色 IK 绑定及物理破碎核心交互开发。', highlights: [{title:'标签驱动空间自动匹配', content:'基于 MR Anchor Label 定位，支持目标自动对齐。'},{title:'Cinemachine 动态演出', content:'路径速度控制，确保平滑视角。'},{title:'Timeline 玩法闭环', content:'联动演出，实现多阶段触发交互。'}], video: 'https://video.lh-xr.top/TianXia_2026630222450.mp4', tags: ['Unity URP', 'Meta XR SDK', 'Cinemachine', 'Timeline'] },
  { id: 3, navTitle: '精工集团 XR 定制系统', title: '精工控股集团 XR 室内空间模块化定制与展示系统', category: '虚拟现实 / BIM可视化', shortCategory: 'XR 定制', role: 'Unity VR 客户端开发', summary: '面向装配式建筑研发的沉浸式空间定制展示系统。', highlights: [{title:'模块化空间锚定', content:'基于 Spatial Anchor 实现家具部件热替换。'},{title:'事件驱动交互', content:'视线检测与全息菜单联动。'},{title:'动态加载机制', content:'多场景方案的平滑无缝切换。'}], video: 'https://video.lh-xr.top/JingGong.mp4', tags: ['Unity', 'Meta XR', 'Spatial Anchor', 'Shader Graph'] },
  { id: 4, navTitle: '西邮周年庆 XR 导览系统', title: '西安邮电大学周年庆 XR 智能校园导览系统', category: '大空间协同 / 数字孪生', shortCategory: 'XR 导览', role: 'Unity XR 开发工程师', summary: '面向高校周年庆开发的校区 XR 导览应用。', highlights: [{title:'空间锚点持久化', content:'锚点分组管理与自动重构恢复。'},{title:'可视化配置流程', content:'手柄增删配置，边界精准对齐。'},{title:'全息数据管线', content:'沙盘平移缩放约束，数据动态渲染。'}], video: 'https://video.lh-xr.top/XiYou.mp4', tags: ['Unity 2022.3', 'Oculus SDK', 'Spatial Anchors'] },
  { id: 5, isAI: true, navTitle: 'Unity MCP · AI 自动化管线', title: '基于 MCP 的 Unity 编辑器 AI 自动化管线', category: 'AI 工具链架构', shortCategory: '🤖 AI 自动化', role: 'AI 工具链开发', summary: '结合 LLM 与 MCP 协议构建的 Unity 智能研发管线。', highlights: [{title:'MCP 协议对接', content:'C# Reflection 深度集成，实现场景智能读写。'},{title:'组件自愈与纠错', content:'针对 XR 空间配置一键诊断修复。'},{title:'自然语言重构', content:'指令驱动场景建构，效率翻倍。'}], video: 'https://video.lh-xr.top/MCP_AI.mp4', tags: ['Unity MCP', 'LLM Agent', 'Editor 工具链'] }
])
const currentProject = computed(() => projects.value.find(p => p.id === currentId.value) || projects.value[0])
const switchProject = (id) => { currentId.value = id }
</script>

<style scoped>
* { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
.intro-screen { position: fixed; top: 0; left: 0; width: 100vw; height: 100vh; background: #05070a; z-index: 9999; display: flex; align-items: center; justify-content: center; overflow: hidden; }
.cyber-grid { position: absolute; width: 200%; height: 200%; background-image: radial-gradient(circle, rgba(0, 240, 255, 0.1) 1px, transparent 1px), linear-gradient(rgba(0, 240, 255, 0.05) 1px, transparent 1px), linear-gradient(90deg, rgba(0, 240, 255, 0.05) 1px, transparent 1px); background-size: 40px 40px; transform: perspective(500px) rotateX(60deg); animation: gridMove 10s linear infinite; }
@keyframes gridMove { 0% { transform: perspective(500px) rotateX(60deg) translateY(0); } 100% { transform: perspective(500px) rotateX(60deg) translateY(40px); } }
.intro-content { position: relative; z-index: 2; display: flex; flex-direction: column; align-items: center; width: 85%; max-width: 320px; }
.hud-scanner { position: relative; width: 100px; height: 100px; display: flex; align-items: center; justify-content: center; margin-bottom: 24px; }
.ring { position: absolute; border-radius: 50%; }
.outer-ring { width: 100%; height: 100%; border: 2px dashed rgba(0, 240, 255, 0.3); border-top: 2px solid #00f0ff; animation: spin 3s linear infinite; }
.inner-ring { width: 78%; height: 78%; border: 1px solid rgba(0, 81, 255, 0.5); border-left: 2px solid #0051ff; animation: spinReverse 2s linear infinite; }
.percent-num { font-size: 20px; font-weight: 800; color: #00f0ff; font-family: monospace; }
@keyframes spin { 100% { transform: rotate(360deg); } }
@keyframes spinReverse { 100% { transform: rotate(-360deg); } }
.intro-title { font-size: 18px; letter-spacing: 3px; color: #fff; margin: 0 0 12px; }
.status-box { display: flex; align-items: center; gap: 8px; margin-bottom: 20px; }
.status-dot { width: 8px; height: 8px; background: #00f0ff; border-radius: 50%; box-shadow: 0 0 8px #00f0ff; animation: blink 0.8s infinite; }
.status-text { font-family: monospace; font-size: 12px; color: #64748b; }
@keyframes blink { 50% { opacity: 0.3; } }
.progress-bar-container { width: 100%; height: 4px; background: rgba(255, 255, 255, 0.1); border-radius: 2px; overflow: hidden; }
.progress-bar-fill { height: 100%; background: linear-gradient(90deg, #0051ff, #00f0ff); transition: width 0.1s ease; box-shadow: 0 0 10px #00f0ff; }
.enter-btn { margin-top: 20px; background: transparent; border: 1px solid #00f0ff; color: #00f0ff; padding: 10px 20px; font-size: 13px; cursor: pointer; border-radius: 4px; }
.portal-split-leave-active { transition: all 0.7s cubic-bezier(0.7, 0, 0.3, 1); }
.portal-split-leave-to { opacity: 0; transform: scale(1.1); filter: blur(8px); }
.portfolio-container { position: relative; min-height: 100vh; background-color: #0b0e14; background-image: linear-gradient(rgba(255, 255, 255, 0.015) 1px, transparent 1px), linear-gradient(90deg, rgba(255, 255, 255, 0.015) 1px, transparent 1px); background-size: 50px 50px; color: #e0e6ed; padding: 40px 20px 80px; transition: all 0.6s; overflow: hidden; }
.nebula-glow { position: absolute; border-radius: 50%; pointer-events: none; filter: blur(120px); opacity: 0.22; z-index: 0; }
.glowing-cyan { width: 400px; height: 400px; background: #00f0ff; top: -100px; left: 10%; }
.glowing-blue { width: 500px; height: 500px; background: #0044ff; bottom: -150px; right: 5%; }
.glowing-purple { width: 350px; height: 350px; background: #b000ff; top: 30%; right: 20%; opacity: 0.15; }
.particles-container { position: absolute; top: 0; left: 0; width: 100%; height: 100%; pointer-events: none; z-index: 1; }
.space-particle { position: absolute; background: rgba(0, 240, 255, 0.6); border-radius: 50%; box-shadow: 0 0 10px rgba(0, 240, 255, 0.8), 0 0 4px #fff; animation: floatUpFast linear infinite; }
@keyframes floatUpFast { 0% { transform: translateY(80px) translateX(0) scale(0.6); opacity: 0; } 15% { opacity: 0.85; } 85% { opacity: 0.85; } 100% { transform: translateY(-380px) translateX(45px) scale(1.1); opacity: 0; } }
.header { text-align: center; margin-bottom: 36px; position: relative; z-index: 2; }
.tag-badge { display: inline-block; padding: 4px 14px; background: rgba(0, 240, 255, 0.1); border: 1px solid #00f0ff; color: #00f0ff; font-size: 12px; font-weight: 600; border-radius: 20px; margin-bottom: 12px; }
.glow-title { font-size: clamp(26px, 6vw, 38px); font-weight: 800; margin: 0 0 8px; background: linear-gradient(90deg, #ffffff, #b8c7ff); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
.subtitle { color: #8ba0b5; font-size: 14px; }
.project-selector { max-width: 1100px; margin: 0 auto 28px; display: grid; grid-template-columns: repeat(5, 1fr); gap: 14px; position: relative; z-index: 2; }
.nav-card { position: relative; background: rgba(18, 24, 38, 0.75); border: 1px solid rgba(255, 255, 255, 0.08); border-radius: 12px; padding: 14px; cursor: pointer; transition: transform 0.18s; backdrop-filter: blur(5px); }
.nav-card:hover { transform: translateY(-4px); background: rgba(28, 38, 58, 0.9); border-color: rgba(0, 240, 255, 0.4); }
.nav-card.active { border-color: #00f0ff; background: rgba(16, 29, 54, 0.85); box-shadow: 0 0 25px rgba(0, 240, 255, 0.25); }
.card-content h3 { font-size: 13px; margin: 10px 0 6px; color: #fff; font-weight: 700; }
.card-cate-pc { font-size: 11px; color: #788a9e; }
.showcase-area { max-width: 1100px; margin: 0 auto; background: rgba(16, 22, 34, 0.7); border: 1px solid rgba(255, 255, 255, 0.08); border-radius: 16px; padding: 24px; backdrop-filter: blur(25px); position: relative; z-index: 2; }
.video-frame { position: relative; width: 100%; border-radius: 12px; overflow: hidden; background: #000; box-shadow: 0 20px 45px rgba(0,0,0,0.85); border: 1px solid rgba(0, 240, 255, 0.25); }
.cyber-video { width: 100%; aspect-ratio: 16 / 9; display: block; }
.no-save { -webkit-touch-callout: none !important; -webkit-user-select: none !important; user-select: none !important; }
.role-bar { background: rgba(0, 240, 255, 0.06); border-left: 4px solid #00f0ff; padding: 12px 16px; border-radius: 0 8px 8px 0; margin-bottom: 18px; font-size: 14px; color: #d1e3ff; }
.role-bar strong { color: #00f0ff; font-weight: 700; }
.highlights-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 16px; }
.hl-item { background: rgba(255, 255, 255, 0.02); border: 1px solid rgba(255, 255, 255, 0.06); padding: 16px; border-radius: 10px; }
.hl-item h4 { font-size: 14px; color: #00f0ff; margin: 0 0 8px; font-weight: 700; }
.hl-item p { font-size: 13px; color: #9ab0cf; line-height: 1.6; }
.tech-tag { display: inline-flex; align-items: center; gap: 6px; background: rgba(14, 28, 48, 0.8); border: 1px solid rgba(0, 240, 255, 0.2); padding: 6px 16px; border-radius: 6px; font-size: 13px; color: #c8e1ff; margin-right: 10px; }
.tag-icon { color: #00f0ff; font-weight: 800; }
</style>