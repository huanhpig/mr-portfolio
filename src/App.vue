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
        <h1 class="glow-title">Unity VR / MR 客户端开发作品集</h1>
        <p class="subtitle">Meta Quest · Unity · 空间计算 · AI 工程实践</p>

       <div class="contact-info-bar">
          <div class="contact-item copyable" @click="copyText('19926648707@163.com', '邮箱')">
            <svg class="c-icon email-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <path d="M4 4h16c1.1 0 2 .9 2 2v12c0 1.1-.9 2-2 2H4c-1.1 0-2-.9-2-2V6c0-1.1.9-2 2-2z"></path>
              <polyline points="22,6 12,13 2,6"></polyline>
            </svg>
            19926648707@163.com
            <span class="copy-hint">[点击复制]</span>
          </div>
          
          <span class="contact-divider">/</span>
          
          <div class="contact-item copyable" @click="copyText('19926648707', '微信号')">
            <svg class="c-icon wechat-icon" viewBox="0 0 1024 1024" fill="currentColor">
              <path d="M682.6 768l64 170.6-170.6-85.3c-42.7 21.3-85.3 21.3-149.3 21.3-170.7 0-320-128-320-298.7 0-149.3 128-277.3 298.6-277.3 170.7 0 298.7 128 298.7 277.3 0 85.3-42.7 170.7-128 192z m-405.3-362.7c-32 0-53.3 21.3-53.3 53.4 0 32 21.3 53.3 53.3 53.3 32 0 53.3-21.3 53.4-53.3 0-32-21.3-53.4-53.4-53.4z m213.3 0c-32 0-53.3 21.3-53.3 53.4 0 32 21.3 53.3 53.3 53.3 32 0 53.3-21.3 53.3-53.3 0-32-21.3-53.4-53.3-53.4z m320-256c-170.7 0-320 128-320 277.3 0 21.3 0 32 5.3 53.4C469.3 405.3 586.7 341.3 725.3 341.3c192 0 341.4 128 341.4 298.7 0 64-21.4 128-85.4 170.6l42.7 128-128-64c-32 12.8-53.3 21.3-85.3 21.3-149.3 0-277.3-106.6-277.3-256 0-149.3 128-256 277.3-256z m-128 170.7c-21.3 0-42.7 15.3-42.7 36.6 0 21.3 15.3 42.7 42.7 42.7s42.7-15.3 42.7-42.7c0-21.3-21.3-36.6-42.7-36.6z m256 0c-21.3 0-42.7 15.3-42.7 36.6 0 21.3 15.3 42.7 42.7 42.7s42.7-15.3 42.7-42.7c0-21.3-21.3-36.6-42.7-36.6z"></path>
            </svg>
            微信: 19926648707
            <span class="copy-hint">[点击复制]</span>
          </div>
        </div>
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

          <HlsPlayer :src="currentProject.video" />

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
            <!-- 新增：颗粒度负责模块清单 -->
            <div v-if="currentProject.responsibilities" class="responsibilities-list" :class="{ 'ai-resp-list': currentProject.isAI }">
              <span class="resp-label">⚡ 本人负责：</span>
              <div class="resp-items">
                <span v-for="(item, index) in currentProject.responsibilities" :key="index" class="resp-item">
                  <span class="check-icon">✔</span> {{ item }}
                </span>
              </div>
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
import HlsPlayer from './HlsPlayer.vue'

// 新增：一键复制文本功能（方便 HR 复制微信号）
const copyText = (text, type) => {
  if (navigator.clipboard) {
    navigator.clipboard.writeText(text).then(() => {
      alert(`✅ ${type} ${text} 已成功复制！期待您的交流。`);
    }).catch(err => {
      console.error('复制失败:', err);
    });
  } else {
    // 兼容老版本浏览器
    alert(`请手动复制${type}: ${text}`);
  }
}
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
    responsibilities: ['MR 空间交互开发', 'MR 空间定位', '家具摆放与空间吸附', 'Addressables 资源管理', 'ScriptableObject 数据驱动', 'ISDK 交互框架', '生命周期与稳定性优化'],
    summary: '基于 Meta Quest 3 Passthrough 开发的 MR 家居应用，实现房间扫描、全局光照/材质特效实时联动、家具摆放及虚实融合交互。',
    highlights: [
      { title: 'Addressables 异步资源管理', content: '封装 Addressables 加载流程，引入资源加载超时保护及状态管理，避免异常情况下加载阻塞，提高资源加载稳定性。' },
      { title: 'MR 空间语义交互', content: '基于 Meta MRUK Scene API 获取墙面、地面等空间语义信息，结合 AnchorPrefabSpawner 实现虚拟家具的自动生成、空间吸附及朝向校正。' },
      { title: '数据驱动配置', content: '使用 ScriptableObject 管理家具配置及 UI 数据，实现资源映射与内容快速扩展。' }
    ],
    video: 'https://video.lh-xr.top/Home.mp4',
    tags: ['Unity 2022', 'C#', 'Meta XR SDK', 'MRUK','Scene API', 'Addressables', 'DOTween']
  },
  {
    id: 2,
    navTitle: 'XR 叙事互动系统',
    title: 'Unity XR 叙事与混合现实互动系统',
    category: '虚拟演出 / 混合现实',
    shortCategory: 'XR 叙事',
    role: 'Unity 开发工程师',
    responsibilities: ['MR 场景定位','Hand Tracking 与自定义手势交互', 'Cinemachine 镜头控制', 'Timeline 剧情演出', 'MR 小游戏玩法开发'],
    summary: '基于 Unity 与 Meta XR SDK 开发的 XR 叙事互动项目，负责 MR 场景理解、空间定位、Hand Tracking 自然交互、剧情演出及核心玩法开发，实现虚实融合的沉浸式交互体验。',
    highlights: [
      { title: 'MR 场景理解与空间自动部署', content: '基于 MRUK Scene API 与 Anchor Label 实现场景对象自动定位，根据空间标签完成物体部署，并支持法线方向修正、Inside/Outside 偏移及朝向校准，提高不同扫描环境下的定位精度与内容复现稳定性。' },
      { title: '混合现实渲染与沉浸式演出', content: '基于 EffectMesh 与 Selective Passthrough Shader，实现真实空间与虚拟内容的混合渲染，通过 Cut Holes 控制墙体遮罩效果，增强 MR 场景沉浸感。' },
      { title: 'Hand Tracking 与自定义手势识别', content: '基于 Meta ISDK 实现 Grab、Pinch 等自然交互，并结合 Hand Grab Pose、Shape Recognizer 及 Finger Flexion 配置实现自定义手势识别，驱动剧情流程、场景事件及交互逻辑。' }
    ],
    video: 'https://video.lh-xr.top/TianXia.mp4',
    tags: ['Unity URP', 'Meta XR SDK','MRUK','Hand Tracking', 'Selective Passthrough','Cinemachine', 'Timeline', 'VFX Graph']
  },
  {
    id: 3,
    navTitle: '精工集团 VR 定制系统',
    title: '精工控股集团 VR 室内空间模块化定制与展示系统',
    category: '虚拟现实 / BIM可视化',
    shortCategory: 'VR 定制',
    role: 'Unity VR 客户端开发',
    responsibilities: ['模块化场景配置', '事件驱动交互开发', 'UI 菜单开发', '动画与视觉表现','Shader Graph','DOTween'],
    summary: '基于 Unity 开发 VR 室内空间展示系统，负责场景模块化配置、交互逻辑开发及动画表现，实现沉浸式室内展示体验。',
    highlights: [
      { title: '模块化场景配置', content: '将墙面、吊顶、材质等房间元素进行模块化设计，实现不同展示方案的动态切换，提高系统扩展性与配置效率。' },
      { title: '事件驱动交互系统', content: '基于事件驱动架构实现视线检测、UI 菜单、动画播放及场景切换等交互逻辑，降低模块耦合，提高交互响应效率。' },
      { title: '动画与视觉表现', content: '使用 Animation、DOTween、Shader Graph 与 AudioMixer 实现墙体移动动画、材质切换、动画反馈及音效控制，提升 VR 场景的沉浸感与展示效果。' }
    ],
    video: 'https://video.lh-xr.top/JingGong.mp4',
    tags: ['Unity', 'Meta XR / Oculus', 'Animation', 'Shader Graph', 'AudioMixer','DOTween', 'Event System']
  },
  {
    id: 4,
    navTitle: '西邮周年庆 XR 导览系统',
    title: '西安邮电大学周年庆 XR 智能校园导览与沙盘交互系统',
    category: 'XR 展示 / 沉浸交互',
    shortCategory: 'XR 导览',
    role: 'Unity XR 开发工程师',
    responsibilities: ['Hand Tracking 手势交互','XR 3D UI 开发', 'UGUI / TextMeshPro 界面开发', '多媒体播放系统','Quest 平台适配'],
    summary: '面向高校周年庆展示场景开发的 XR 导览应用，负责 Meta Quest 平台交互模块开发，实现手势操作、3D UI 菜单及多媒体展示功能。',
    highlights: [
      { title: 'Hand Tracking 手势交互', content: '基于 Meta ISDK 实现 XR 场景手势交互，支持通过手势完成 3D UI 菜单点击、选择等操作，提升自然交互体验。' },
      { title: 'XR 3D UI 交互系统', content: '结合 UGUI 与 TextMeshPro 构建 XR 场景内交互界面，实现菜单展示、信息显示及用户操作反馈。' },
      { title: '多媒体展示系统', content: '集成 VideoPlayer 实现场景内视频播放功能，并与 XR UI 交互流程结合，实现建筑介绍等多媒体内容展示。' }
    ],
    video: 'https://video.lh-xr.top/XiYou.mp4',
    tags: ['Unity 2022', 'Oculus SDK', 'Spatial Anchors', 'VideoPlayer', 'ScriptableObject']
  },
  {
    id: 5,
    isAI: true,
    navTitle: 'Unity MCP AI 辅助开发实践',
    title: '基于 Unity MCP 的 AI 辅助开发实践',
    category: 'AI 辅助开发 / Unity 编辑器效率实践',
    shortCategory: '🤖 AI 自动化实践',
    role: 'Unity 开发工程师',
    summary: '结合 Codex 与 VS Code Copilot，通过 Unity MCP 对 Unity 编辑器进行自然语言控制，演示 AI 在对象创建、组件配置、场景搭建及编辑器操作中的辅助开发能力，探索 AI 提升 Unity 开发效率的实际应用。',
    highlights: [
      { title: '自然语言控制 Unity 编辑器', content: '通过 Unity MCP 使用自然语言完成 GameObject 创建、组件添加、Hierarchy 管理及基础编辑器操作，减少重复性编辑工作，提高开发效率。' },
      { title: 'AI 辅助 XR 场景开发', content: '结合 XR 项目开发流程，演示 AI 辅助完成场景对象创建、组件配置、脚本修改及常见问题排查，提升日常开发效率。' },
      { title: 'Unity MCP 工作流实践', content: '将 Codex、VS Code Copilot 与 Unity MCP 结合使用，探索自然语言驱动的 Unity 编辑器工作流，验证 AI 在 Unity 开发中的辅助价值。' }
    ],
    video: 'https://video.lh-xr.top/AIMCP.mp4',
    tags: ['Unity MCP', 'Codex', 'VS Code Copilot', 'Unity Editor', 'AI Assisted Development']
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

/* --- HR 联络舱样式 --- */
.contact-info-bar {
  margin-top: 18px;
  display: inline-flex;
  align-items: center;
  gap: 16px;
  background: rgba(0, 240, 255, 0.05);
  border: 1px solid rgba(0, 240, 255, 0.25);
  padding: 8px 24px;
  border-radius: 30px;
  backdrop-filter: blur(8px);
  transition: all 0.3s ease;
}
.contact-info-bar:hover {
  background: rgba(0, 240, 255, 0.1);
  border-color: rgba(0, 240, 255, 0.5);
  box-shadow: 0 0 15px rgba(0, 240, 255, 0.15);
}
.contact-item {
  color: #a0aec0;
  font-size: 13px;
  font-family: monospace;
  text-decoration: none;
  display: flex;
  align-items: center;
  gap: 6px;
  cursor: pointer;
  transition: all 0.3s;
}
.contact-item:hover {
  color: #00f0ff;
  text-shadow: 0 0 8px rgba(0, 240, 255, 0.6);
}
.c-icon {
  font-size: 14px;
}
.contact-divider {
  color: rgba(255, 255, 255, 0.2);
  font-size: 12px;
}
.copy-hint {
  font-size: 11px;
  opacity: 0;
  transform: translateX(-10px);
  transition: all 0.3s;
  color: #00f0ff;
  background: rgba(0, 240, 255, 0.15);
  padding: 2px 6px;
  border-radius: 4px;
  margin-left: 4px;
}
.contact-item.copyable:hover .copy-hint {
  opacity: 1;
  transform: translateX(0);
}

/* 手机端适配：放到最下面的 @media (max-width: 768px) 里面 */

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

/* 新增：负责模块清单样式 */
.responsibilities-list {
  display: flex;
  align-items: flex-start;
  gap: 12px;
  margin-bottom: 20px;
  padding: 12px 16px;
  background: rgba(0, 240, 255, 0.03);
  border: 1px dashed rgba(0, 240, 255, 0.25);
  border-radius: 8px;
  transition: all 0.3s;
}
.responsibilities-list:hover {
  background: rgba(0, 240, 255, 0.06);
  border-color: rgba(0, 240, 255, 0.4);
}
.resp-label {
  color: #00f0ff;
  font-size: 14px;
  font-weight: 700;
  white-space: nowrap;
  padding-top: 2px;
}
.resp-items {
  display: flex;
  flex-wrap: wrap;
  gap: 10px 18px;
}
.resp-item {
  font-size: 13px;
  color: #c8e1ff;
  display: flex;
  align-items: center;
  gap: 5px;
  font-weight: 500;
}
.check-icon {
  color: #00f0ff;
  font-size: 12px;
  text-shadow: 0 0 6px rgba(0, 240, 255, 0.6);
}

/* AI 项目专属变色 */
.responsibilities-list.ai-resp-list {
  background: rgba(176, 0, 255, 0.03);
  border-color: rgba(176, 0, 255, 0.3);
}
.responsibilities-list.ai-resp-list:hover {
  background: rgba(176, 0, 255, 0.06);
  border-color: rgba(176, 0, 255, 0.5);
}
.responsibilities-list.ai-resp-list .resp-label, 
.responsibilities-list.ai-resp-list .check-icon {
  color: #d066ff;
  text-shadow: 0 0 6px rgba(176, 0, 255, 0.6);
}

/* 👇 👇 👇 把这段 SVG 图标样式紧挨着加在这里 👇 👇 👇 */
/* --- SVG 图标专属样式 --- */
.c-icon {
  width: 16px;
  height: 16px;
  transition: all 0.3s ease;
}
.email-icon {
  color: #00f0ff; /* 科技感蓝青色 */
}
.wechat-icon {
  color: #07C160; /* 微信官方绿 */
}
.contact-item:hover .wechat-icon {
  filter: drop-shadow(0 0 6px rgba(7, 193, 96, 0.6)); /* 微信绿发光效果 */
}
.contact-item:hover .email-icon {
  filter: drop-shadow(0 0 6px rgba(0, 240, 255, 0.6)); /* 邮箱蓝发光效果 */
}

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
  /* 放到手机端媒体查询里面 */
  .responsibilities-list { flex-direction: column; gap: 8px; padding: 10px 12px; }
  .resp-items { gap: 8px 14px; }

  /* 放到手机端媒体查询里面 */
  .contact-info-bar {
    flex-direction: column;
    gap: 10px;
    padding: 12px 24px;
    border-radius: 12px;
  }
  .contact-divider {
    display: none; /* 手机端垂直排列，不需要斜杠分隔符 */
  }
  .copy-hint {
    opacity: 1; /* 手机端没有 hover，直接显示提示 */
    transform: none;
  }
}
</style>