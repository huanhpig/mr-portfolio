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
          <h2 class="intro-title">MR PORTFOLIO</h2>
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
        <p class="subtitle">涵盖 VR/MR 沉浸交互空间与 Unity MCP AI 自动化管线 · 核心实录</p>
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
              <span class="rec-text">REC // PASSTHROUGH LIVE</span>
            </div>
            <div class="cinema-controls">
              <button class="cinema-btn" @click="toggleCinemaMode">
                <span>{{ isCinemaMode ? '💡 退出影院' : '🎬 沉浸影院' }}</span>
              </button>
            </div>
          </div>

          <!-- 🔥 核心升级：全平台防劫持、防下载、防投屏、防长按视频配置 🔥 -->
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

const setVideoRef = (el, url) => {
  if (!el) return;
  // 如果是普通链接，直接赋值；如果想进一步加密，可以在这里 fetch 后转 blob
  // 为了确保兼容性和防劫持，我们不直接暴露原始 URL
  if (!el.src) {
    fetch(url)
      .then(response => response.blob())
      .then(blob => {
        const blobUrl = URL.createObjectURL(blob);
        el.src = blobUrl;
      })
      .catch(() => { el.src = url; }); // 降级处理
  }
};
// 开场屏逻辑
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

// 沉浸影院
const isCinemaMode = ref(false)
const toggleCinemaMode = () => { isCinemaMode.value = !isCinemaMode.value }

// 粒子样式计算
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

// 3D 卡片倾斜
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

// ---------- 作品集核心精炼数据清单 ----------
const currentId = ref(1)
const projects = ref([
  {
    id: 1,
    navTitle: 'MR 空间漫游系统',
    title: 'MR 混合现实家居与空间无缝漫游系统',
    category: '混合现实 / 场景构建',
    shortCategory: 'MR 漫游',
    role: '核心客户端开发',
    summary: '一款基于 Meta Quest 3 透视技术的混合现实深度应用，支持真实房间扫描、虚拟家具物理吸附及全局光照/材质特效实时联动。',
    highlights: [
      { title: '异步资产加载与防死锁', content: '重构 Addressables 两段式管线，引入 SafetyTimeoutGuard 熔断机制与协程状态机，彻底消除了异步调用引发的静默崩溃与加载死锁困境。' },
      { title: '空间感知与精准对齐', content: '基于 Meta XR SDK 与 MRUK 构建物理吸附系统与真实房间空间锚点（WallSnapZone），利用矩阵变换精准控制家具贴合，攻克了物体偏移难题。' },
      { title: '数据驱动 UI 与底层防护', content: '使用 ScriptableObject 解耦 UI 逻辑与实体数据。深度封装 ISDK 事件代理并规范 OnDestroy 生命周期清理，根除了重载场景时的游离内存泄漏。' }
    ],
    video: 'https://video.lh-xr.top/Home.mp4',
    tags: ['Unity 2022', 'C#', 'Meta ISDK', 'MRUK', 'Addressables', 'DOTween']
  },
  {
    id: 2,
    navTitle: 'XR/MR 叙事互动系统',
    title: 'Unity XR/MR 赛博叙事与镜头互动系统',
    category: '虚拟演出 / 镜头控制',
    shortCategory: 'XR 叙事',
    role: 'Unity 开发工程师',
    summary: '基于 Unity 开发的扩展现实叙事项目，负责多场景的 MR 空间定位、全景演出、角色 IK 绑定及物理破碎核心交互开发。',
    highlights: [
      { title: '标签驱动空间自动匹配', content: '设计基于 MR Anchor Label 的定位方案，支持目标物体按锚点标签自动对齐、法线方向判定与 Inside/Outside 偏移锁定，大幅提升复现稳定性。' },
      { title: 'Cinemachine 编排化动态演出', content: '利用 DollyTrack/DollyCart 结合路径速度控制与触发点事件驱动镜头轨道演出，确保全沉浸视角的平滑飞行动画表现。' },
      { title: 'Timeline 驱动与玩法闭环', content: '通过 Timeline 联动剧情演出与窗口遮挡转场；自研弹弓打靶玩法，实现包含命中判定、多阶段触发在内的完整交互闭环。' }
    ],
    video: 'https://video.lh-xr.top/TianXia_2026630222450.mp4',
    tags: ['Unity URP', 'Meta XR SDK', 'Cinemachine', 'Timeline', 'VFX Graph']
  },
  {
    id: 3,
    navTitle: '精工集团 VR 定制系统',
    title: '精工控股集团 VR 室内空间模块化定制与展示系统',
    category: '虚拟现实 / BIM可视化',
    shortCategory: 'VR 定制',
    role: 'Unity VR 客户端 / 交互开发',
    summary: '面向装配式建筑研发的沉浸式空间定制展示系统，围绕空间锚定与视线导航构建全场景模块化配置流程。',
    highlights: [
      { title: '模块化配置与空间锚定', content: '基于 Spatial Anchor 规范房间对象的初始化与状态恢复，将硬装结构拆分为可配置模块，支持墙面、吊顶及材质等部件的无缝动态热替换。' },
      { title: '事件驱动型交互生态', content: '设计视线检测（Gaze Interaction）、全息菜单与动画状态联动的事件流，构建多通道交互闭环，大幅提升 VR 空间内的操作效率。' },
      { title: '多维环境渲染与平滑反馈', content: '应用数据层隔离控制多套大空间设计方案的解耦切换，联动动态加载机制与平滑缓动反馈，达成高沉浸的样板间漫游。' }
    ],
    video: 'https://video.lh-xr.top/JingGong.mp4',
    tags: ['Unity', 'Meta XR / Oculus', 'Spatial Anchor', 'Shader Graph', 'AudioMixer']
  },
  {
    id: 4,
    navTitle: '西邮周年庆 XR 导览系统',
    title: '西安邮电大学周年庆 XR 智能校园导览与沙盘交互系统',
    category: '大空间协同 / 数字孪生',
    shortCategory: 'XR 导览',
    role: 'Unity XR 开发工程师',
    summary: '面向高校周年庆开发的校区 XR 导览应用，支持空间锚点持久化、全息沙盘多媒体讲解，适配 Quest 设备沉浸体验。',
    highlights: [
      { title: '空间锚点持久化分组', content: '基于 Meta XR Spatial Anchors 实现空间锚点的创建、擦除与持久化，支持多锚点分组管理与场景重建后的全自动恢复。' },
      { title: '可视化手柄配置流程', content: '设计手柄交互式锚点配置组件，结合预览物体、辅助连线与状态面板，达成空间位置的可视化增删与边界对齐。' },
      { title: '全息沙盘动态数据管线', content: '利用 ScriptableObject 组装建筑多媒体数据；沙盘控制系统支持平移缩放约束，并通过自定义 Shader 参数同步渲染高亮半径。' }
    ],
    video: 'https://video.lh-xr.top/XiYou.mp4',
    tags: ['Unity 2022.3', 'Oculus SDK', 'Spatial Anchors', 'Newtonsoft.Json', 'VideoPlayer']
  },
  {
    id: 5,
    isAI: true,
    navTitle: 'Unity MCP · AI 自动化管线',
    title: '基于 Model Context Protocol 的 Unity 编辑器 AI 自动化开发管线',
    category: 'AI 工具链架构 / 研发效能赋能',
    shortCategory: '🤖 AI 自动化',
    role: 'AI 工具链开发工程师',
    summary: '结合大语言模型 (LLM Agent) 与 MCP 开放协议，构建面向 Unity 编辑器的智能助手与自动化研发管线，把工程初始化与组件配置效率翻倍。',
    highlights: [
      { title: 'MCP 协议对接与 Server 架构', content: '基于 C# Reflection 构建 Unity 内置 MCP Server，暴露预制体生成、资产查询、层级检索等原子工具，让大模型能精准读写场景。' },
      { title: '组件自愈与智能纠错扫描', content: '编写定制化 Prompts 检索器，针对 XR 项目复杂的空间锚点挂载错误、缺少 ISDK 碰撞器等场景隐患进行一键诊断与自动修复。' },
      { title: '自然语言驱动场景重构', content: '彻底变革手动拖拽与繁琐配置的重复劳动，支持通过自然语言下发复杂场景建构指令，单次工程场景搭建时间缩短 80% 以上。' }
    ],
    video: 'https://video.lh-xr.top/MCP_AI.mp4',
    tags: ['Unity MCP Protocol', 'LLM Agent', 'Editor 工具链', 'C# Reflection', '自动化管线', 'DevOps']
  }
])

const currentProject = computed(() => projects.value.find(p => p.id === currentId.value) || projects.value[0])
const switchProject = (id) => { currentId.value = id }
</script>

<style scoped>
* { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }

/* 开场屏 */
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

/* 主体容器 */
.portfolio-container {
  position: relative; min-height: 100vh; background-color: #0b0e14;
  background-image: linear-gradient(rgba(255, 255, 255, 0.015) 1px, transparent 1px), linear-gradient(90deg, rgba(255, 255, 255, 0.015) 1px, transparent 1px);
  background-size: 50px 50px; color: #e0e6ed; font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
  padding: 40px 20px 80px; transition: all 0.6s cubic-bezier(0.25, 0.8, 0.25, 1); overflow: hidden;
}

.nebula-glow { position: absolute; border-radius: 50%; pointer-events: none; filter: blur(120px); opacity: 0.22; z-index: 0; }
.glowing-cyan { width: 400px; height: 400px; background: #00f0ff; top: -100px; left: 10%; }
.glowing-blue { width: 500px; height: 500px; background: #0044ff; bottom: -150px; right: 5%; }
.glowing-purple { width: 350px; height: 350px; background: #b000ff; top: 30%; right: 20%; opacity: 0.15; }

.particles-container { position: absolute; top: 0; left: 0; width: 100%; height: 100%; pointer-events: none; z-index: 1; }
.space-particle { position: absolute; background: rgba(0, 240, 255, 0.6); border-radius: 50%; box-shadow: 0 0 10px rgba(0, 240, 255, 0.8), 0 0 4px #fff; animation: floatUpFast linear infinite; }
@keyframes floatUpFast {
  0% { transform: translateY(80px) translateX(0) scale(0.6); opacity: 0; }
  15% { opacity: 0.85; }
  85% { opacity: 0.85; }
  100% { transform: translateY(-380px) translateX(45px) scale(1.1); opacity: 0; }
}

.cinema-mode-active .nebula-glow { opacity: 0.03; transition: all 0.6s ease; }
.cinema-mode-active .space-particle { opacity: 0.08; }
.cinema-mode-active .header, .cinema-mode-active .project-selector, .cinema-mode-active .project-details { opacity: 0.1; filter: blur(3px); pointer-events: none; }
.cinema-mode-active { background-color: #030406; }

.header { text-align: center; margin-bottom: 36px; position: relative; z-index: 2; transition: all 0.6s ease; }
.tag-badge { display: inline-block; padding: 4px 14px; background: rgba(0, 240, 255, 0.1); border: 1px solid #00f0ff; color: #00f0ff; font-size: 12px; font-weight: 600; border-radius: 20px; margin-bottom: 12px; }
.glow-title { font-size: clamp(26px, 6vw, 38px); font-weight: 800; letter-spacing: 2px; margin: 0 0 8px; background: linear-gradient(90deg, #ffffff, #b8c7ff); -webkit-background-clip: text; -webkit-text-fill-color: transparent; text-shadow: 0 0 20px rgba(0, 240, 255, 0.2); }
.subtitle { color: #8ba0b5; font-size: 14px; margin: 0; }

/* 5等宽控制台网格 */
.project-selector { max-width: 1100px; margin: 0 auto 28px; display: grid; grid-template-columns: repeat(5, 1fr); gap: 14px; position: relative; z-index: 2; transition: all 0.6s ease; }
.nav-card { position: relative; background: rgba(18, 24, 38, 0.75); border: 1px solid rgba(255, 255, 255, 0.08); border-radius: 12px; padding: 14px; cursor: pointer; overflow: hidden; display: flex; flex-direction: column; justify-content: space-between; transform-style: preserve-3d; will-change: transform; transition: transform 0.18s ease-out, background 0.3s, border-color 0.3s, box-shadow 0.3s; backdrop-filter: blur(5px); }
.nav-card:hover { transform: translateY(-4px); background: rgba(28, 38, 58, 0.9); border-color: rgba(0, 240, 255, 0.4); }

.nav-card.ai-featured-card { border-color: rgba(176, 0, 255, 0.35); background: linear-gradient(145deg, rgba(28, 18, 48, 0.8), rgba(18, 24, 38, 0.75)); }
.nav-card.ai-featured-card:hover { border-color: #b000ff; box-shadow: 0 0 20px rgba(176, 0, 255, 0.3); }

.card-glare { position: absolute; top: 0; left: 0; width: 200%; height: 200%; background: radial-gradient(circle, rgba(255, 255, 255, 0.12) 0%, transparent 50%); pointer-events: none; opacity: 0; transition: opacity 0.3s ease; mix-blend-mode: overlay; z-index: 1; }
.card-top, .card-content { position: relative; z-index: 2; transform: translateZ(24px); transition: transform 0.3s ease; }
.card-top { display: flex; justify-content: space-between; align-items: center; }
.card-num { font-size: 18px; font-weight: 800; color: rgba(255, 255, 255, 0.18); transition: color 0.3s; }
.card-cate-mobile { display: none; }
.card-content h3 { font-size: 13px; margin: 10px 0 6px; color: #fff; line-height: 1.4; display: -webkit-box; -webkit-line-clamp: 2; -webkit-box-orient: vertical; overflow: hidden; font-weight: 700; }
.card-cate-pc { font-size: 11px; color: #788a9e; }

.nav-card.active { background: rgba(16, 29, 54, 0.85); border-color: #00f0ff; box-shadow: 0 0 25px rgba(0, 240, 255, 0.25); }
.nav-card.active .card-num { color: #00f0ff; }
.nav-card.active .card-cate-pc { color: #b4cded; }
.nav-card.ai-featured-card.active { border-color: #b000ff; box-shadow: 0 0 25px rgba(176, 0, 255, 0.4); }
.nav-card.ai-featured-card.active .card-num { color: #d066ff; }

.glow-border { position: absolute; bottom: 0; left: 0; width: 100%; height: 3px; background: linear-gradient(90deg, #00f0ff, #0051ff); opacity: 0; transition: opacity 0.3s; z-index: 2; }
.nav-card.active .glow-border { opacity: 1; }
.nav-card.ai-featured-card.active .glow-border { background: linear-gradient(90deg, #b000ff, #00f0ff); }

/* HUD 展示舱 */
.showcase-area { max-width: 1100px; margin: 0 auto; background: rgba(16, 22, 34, 0.7); border: 1px solid rgba(255, 255, 255, 0.08); border-radius: 16px; padding: 24px; backdrop-filter: blur(25px); position: relative; z-index: 2; }
.video-frame { position: relative; width: 100%; border-radius: 12px; overflow: hidden; background: #000; box-shadow: 0 20px 45px rgba(0, 0, 0, 0.85), 0 0 60px rgba(0, 140, 255, 0.25); transition: all 0.6s cubic-bezier(0.25, 0.8, 0.25, 1); border: 1px solid rgba(0, 240, 255, 0.25); -webkit-touch-callout: none; -webkit-user-select: none; }
.video-frame.cinema-frame { transform: scale(1.02); box-shadow: 0 25px 60px rgba(0, 0, 0, 0.95), 0 0 90px rgba(0, 240, 255, 0.4); border-color: #00f0ff; z-index: 100; }
.hud-corner { position: absolute; width: 16px; height: 16px; pointer-events: none; z-index: 10; border-color: #00f0ff; border-style: solid; }
.top-left { top: 10px; left: 10px; border-width: 2px 0 0 2px; }
.top-right { top: 10px; right: 10px; border-width: 2px 2px 0 0; }
.bottom-left { bottom: 10px; left: 10px; border-width: 0 0 2px 2px; }
.bottom-right { bottom: 10px; right: 10px; border-width: 0 2px 2px 0; }
.hud-overlay-header { position: absolute; top: 0; left: 0; width: 100%; padding: 16px 24px; display: flex; justify-content: space-between; align-items: center; background: linear-gradient(180deg, rgba(0,0,0,0.7) 0%, transparent 100%); z-index: 8; pointer-events: none; }
.rec-badge { display: flex; align-items: center; gap: 8px; }
.red-dot { width: 10px; height: 10px; background: #ff2a2a; border-radius: 50%; box-shadow: 0 0 8px #ff2a2a; animation: recBlink 1s infinite; }
@keyframes recBlink { 0%, 100% { opacity: 1; } 50% { opacity: 0.2; } }
.rec-text { font-family: monospace; font-size: 12px; font-weight: 700; color: #fff; letter-spacing: 1px; }
.cinema-controls { pointer-events: auto; }
.cinema-btn { background: rgba(0, 240, 255, 0.18); border: 1px solid #00f0ff; color: #fff; padding: 6px 14px; border-radius: 20px; font-size: 12px; cursor: pointer; transition: all 0.3s; backdrop-filter: blur(5px); }
.cinema-btn:hover { background: #00f0ff; color: #000; box-shadow: 0 0 15px rgba(0, 240, 255, 0.6); }
.hud-overlay-footer { position: absolute; bottom: 0; left: 0; width: 100%; padding: 12px 24px; display: flex; justify-content: space-between; align-items: center; background: linear-gradient(0deg, rgba(0,0,0,0.7) 0%, transparent 100%); font-family: monospace; font-size: 11px; color: rgba(255, 255, 255, 0.5); pointer-events: none; z-index: 8; }
.cyber-video { width: 100%; aspect-ratio: 16 / 9; display: block; }

/* 🔥 新增防长按、防拖拽保存定制样式 🔥 */
.cyber-video.no-save {
  -webkit-touch-callout: none !important;
  -webkit-user-select: none !important;
  user-select: none !important;
  pointer-events: auto;
}
video::-webkit-media-controls-enclosure {
  overflow: hidden;
}
video::-webkit-media-controls-panel {
  width: calc(100% + 35px);
}

/* 文本结构化区 */
.project-details { margin-top: 28px; padding: 4px 10px 0; transition: all 0.3s ease; }
.detail-header { margin-bottom: 16px; }
.cate-label { color: #00f0ff; font-size: 13px; font-weight: 600; letter-spacing: 1px; }
.detail-header h2 { font-size: 26px; margin: 6px 0 0; color: #fff; font-weight: 800; }

.structured-desc { margin-bottom: 24px; }
.desc-card.role-bar { background: rgba(0, 240, 255, 0.06); border-left: 4px solid #00f0ff; padding: 12px 16px; border-radius: 0 8px 8px 0; margin-bottom: 18px; font-size: 14px; color: #d1e3ff; line-height: 1.6; }
.desc-card.role-bar strong { color: #00f0ff; font-weight: 700; margin-right: 6px; }
.desc-card.role-bar b { color: #fff; text-decoration: underline; text-underline-offset: 4px; }

.desc-card.role-bar.ai-role-bar { background: rgba(176, 0, 255, 0.08); border-left-color: #b000ff; color: #eed4ff; }
.desc-card.role-bar.ai-role-bar strong { color: #d066ff; }

.highlights-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 16px; }
.hl-item { background: rgba(255, 255, 255, 0.02); border: 1px solid rgba(255, 255, 255, 0.06); padding: 16px; border-radius: 10px; transition: all 0.3s; }
.hl-item:hover { background: rgba(0, 240, 255, 0.04); border-color: rgba(0, 240, 255, 0.25); transform: translateY(-3px); }
.hl-item h4 { font-size: 14px; color: #00f0ff; margin: 0 0 8px; font-weight: 700; }
.hl-item p { font-size: 13px; color: #9ab0cf; line-height: 1.6; margin: 0; }

.desc { color: #a1b0cb; font-size: 15px; line-height: 1.6; margin-bottom: 24px; }

/* 技术标签强化 */
.tags-wrapper { display: flex; flex-wrap: wrap; gap: 10px; }
.tech-tag { background: rgba(14, 28, 48, 0.8); border: 1px solid rgba(0, 240, 255, 0.2); padding: 6px 16px; border-radius: 6px; font-size: 13px; color: #c8e1ff; transition: all 0.2s; font-weight: 500; display: flex; align-items: center; gap: 6px; }
.tag-icon { color: #00f0ff; font-weight: 800; }
.tech-tag:hover { background: #00f0ff; color: #000; box-shadow: 0 0 15px rgba(0, 240, 255, 0.6); transform: translateY(-2px); }
.tech-tag:hover .tag-icon { color: #000; }

.tech-tag.ai-tag { border-color: rgba(176, 0, 255, 0.3); color: #ecd4ff; }
.tech-tag.ai-tag .tag-icon { color: #d066ff; }
.tech-tag.ai-tag:hover { background: #b000ff; color: #fff; box-shadow: 0 0 15px rgba(176, 0, 255, 0.6); }

.fade-slide-enter-active, .fade-slide-leave-active { transition: all 0.3s ease; }
.fade-slide-enter-from { opacity: 0; transform: translateY(15px); }
.fade-slide-leave-to { opacity: 0; transform: translateY(-15px); }

/* 手机端自适应 */
@media (max-width: 768px) {
  .portfolio-container { padding: 24px 12px 60px; }
  .nebula-glow { filter: blur(70px); opacity: 0.15; }
  .glowing-cyan { width: 250px; height: 250px; }
  .glowing-blue { width: 300px; height: 300px; }
  .glowing-purple { display: none; }
  .space-particle { display: none; }
  .header { margin-bottom: 24px; }
  
  .project-selector { grid-template-columns: repeat(2, 1fr); gap: 10px; margin-bottom: 18px; }
  .nav-card.ai-featured-card { grid-column: span 2; }
  
  .nav-card { padding: 12px; border-radius: 10px; }
  .card-top { transform: none; margin-bottom: 6px; }
  .card-num { font-size: 16px; }
  .card-cate-mobile { display: inline-block; font-size: 10px; padding: 2px 6px; background: rgba(255, 255, 255, 0.05); border-radius: 4px; color: #94a3b8; }
  .nav-card.active .card-cate-mobile { background: rgba(0, 240, 255, 0.15); color: #00f0ff; }
  .nav-card.ai-featured-card.active .card-cate-mobile { background: rgba(176, 0, 255, 0.2); color: #d066ff; }
  .card-content h3 { font-size: 13px; margin: 0; }
  .card-cate-pc { display: none; }
  .showcase-area { padding: 12px; border-radius: 12px; }
  .video-frame { border-radius: 8px; }
  .hud-overlay-footer { display: none; }
  .hud-overlay-header { padding: 10px 12px; }
  .rec-text { font-size: 10px; }
  .cinema-btn { padding: 4px 10px; font-size: 11px; }
  .project-details { margin-top: 16px; padding: 4px 4px 0; }
  .detail-header h2 { font-size: 20px; }
  .highlights-grid { grid-template-columns: 1fr; gap: 12px; }
  .desc-card.role-bar { font-size: 13px; }
  .tags-wrapper { gap: 6px; }
  .tech-tag { font-size: 11px; padding: 4px 10px; }
}
</style>