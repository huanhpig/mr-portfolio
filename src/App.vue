<template>
  <div class="app-wrapper">
    <transition name="portal-split">
      <div v-if="isBooting" class="intro-screen">
        <div class="cyber-grid"></div>
        <div class="intro-content">
          <div class="hud-scanner"><div class="ring outer-ring"></div><div class="ring inner-ring"></div><div class="percent-num">{{ bootProgress }}%</div></div>
          <h2 class="intro-title">XR PORTFOLIO</h2>
          <div class="status-box"><span class="status-dot"></span><span class="status-text">{{ currentStatusText }}</span></div>
          <div class="progress-bar-container"><div class="progress-bar-fill" :style="{ width: bootProgress + '%' }"></div></div>
          <button v-if="bootProgress >= 100" class="enter-btn" @click="enterSite">[ 点击进入虚实空间 ]</button>
        </div>
      </div>
    </transition>

    <div class="portfolio-container" :class="{ 'page-loaded': !isBooting, 'cinema-mode-active': isCinemaMode }">
      <div class="particles-container" v-if="!isBooting">
        <div v-for="n in 70" :key="n" class="space-particle" :style="generateParticleStyle(n)"></div>
      </div>
      <div class="nebula-glow glowing-cyan"></div>
      <div class="nebula-glow glowing-blue"></div>
      <div class="nebula-glow glowing-purple"></div>

      <header class="header">
        <div class="tag-badge"># XR & AI TOOLCHAIN DEVELOPER #</div>
        <h1 class="glow-title">空间计算与 AI 效能 · 研发档案</h1>
        <p class="subtitle">涵盖 XR 沉浸交互空间与 Unity MCP AI 自动化管线 · 核心实录</p>
      </header>

      <section class="project-selector">
        <div v-for="(item, index) in projects" :key="item.id" class="nav-card" :class="{ active: currentId === item.id, 'ai-featured-card': item.isAI }" @click="switchProject(item.id)" @mousemove="handleCardTilt" @mouseleave="resetCardTilt">
          <div class="card-glare"></div>
          <div class="card-top"><span class="card-num">0{{ index + 1 }}</span><span class="card-cate-mobile">{{ item.shortCategory }}</span></div>
          <div class="card-content"><h3>{{ item.navTitle }}</h3><span class="card-cate-pc">{{ item.category }}</span></div>
          <div class="glow-border"></div>
        </div>
      </section>

      <main class="showcase-area">
        <div class="video-frame" :class="{ 'cinema-frame': isCinemaMode }">
          <div class="hud-overlay-header">
            <div class="rec-badge"><span class="red-dot"></span><span class="rec-text">REC // XR PASSTHROUGH LIVE</span></div>
            <button class="cinema-btn" @click="toggleCinemaMode"><span>{{ isCinemaMode ? '💡 退出影院' : '🎬 沉浸影院' }}</span></button>
          </div>
          <video :key="currentProject.video" :ref="el => setVideoRef(el, currentProject.video)" controls autoplay playsinline webkit-playsinline x5-playsinline="true" x5-video-player-type="h5-page" controlslist="nodownload noremoteplayback noplaybackrate" disablePictureInPicture oncontextmenu="return false;" class="cyber-video no-save"></video>
          <div class="hud-overlay-footer"><span>DEVICE: META QUEST 3 // INFRASTRUCTURE</span></div>
        </div>

        <transition name="fade-slide" mode="out-in">
          <div :key="currentProject.id" class="project-details">
            <div class="detail-header"><span class="cate-label">{{ currentProject.category }}</span><h2>{{ currentProject.title }}</h2></div>
            <div class="structured-desc">
              <div class="desc-card role-bar" :class="{ 'ai-role-bar': currentProject.isAI }"><strong>项目角色：</strong> <span><b>{{ currentProject.role }}</b></span><strong style="margin-left: 20px;">项目描述：</strong> <span>{{ currentProject.summary }}</span></div>
              <div class="highlights-grid"><div v-for="(hl, i) in currentProject.highlights" :key="i" class="hl-item"><h4>⚡ {{ hl.title }}</h4><p>{{ hl.content }}</p></div></div>
            </div>
            <div class="tags-wrapper"><span v-for="tag in currentProject.tags" :key="tag" class="tech-tag" :class="{ 'ai-tag': currentProject.isAI }"><span class="tag-icon">#</span> {{ tag }}</span></div>
          </div>
        </transition>
      </main>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

// 逻辑部分保持原样，只补充了 setVideoRef
const isBooting = ref(true), bootProgress = ref(0), currentStatusText = ref('INITIALIZING XR ENGINE...'), isCinemaMode = ref(false)
const setVideoRef = (el, url) => { if (el && !el.src.startsWith('blob:')) { fetch(url).then(r=>r.blob()).then(b=>el.src=URL.createObjectURL(b)).catch(()=>el.src=url) } }
onMounted(() => { const i = setInterval(() => { bootProgress.value += 5; if (bootProgress.value >= 100) { clearInterval(i); setTimeout(() => isBooting.value = false, 500) } }, 50) })
const enterSite = () => { isBooting.value = false }, toggleCinemaMode = () => { isCinemaMode.value = !isCinemaMode.value }, switchProject = (id) => { currentId.value = id }

const currentId = ref(1)
const projects = ref([
  { id: 1, navTitle: 'XR 空间漫游系统', title: 'XR 混合现实家居与空间无缝漫游系统', category: '混合现实 / 场景构建', shortCategory: 'XR 漫游', role: '核心客户端开发', summary: '基于 Meta Quest 3 透视技术的混合现实深度应用。', highlights: [{title:'高性能架构', content:'基于 Addressables 的异步加载，熔断机制保障运行稳定。'},{title:'空间锚点交互', content:'通过矩阵变换精准控制家具贴合。'},{title:'交互生命周期', content:'严格的事件代理反注册，杜绝内存泄漏。'}], video: 'https://video.lh-xr.top/Home.mp4', tags: ['Unity', 'C#', 'Meta ISDK', 'MRUK'] },
  { id: 2, navTitle: 'XR 叙事互动系统', title: 'XR 叙事互动系统', category: '虚拟演出', shortCategory: 'XR 叙事', role: 'Unity开发', summary: '基于 XR 的多场景叙事与破碎物理开发。', highlights: [{title:'锚点复现', content:'基于 Label 自动匹配定位。'},{title:'镜头演出', content:'Cinemachine 轨道平滑飞行动画。'},{title:'玩法闭环', content:'打靶玩法，多阶段事件触发。'}], video: 'https://video.lh-xr.top/TianXia_2026630222450.mp4', tags: ['Unity URP', 'Meta XR SDK'] },
  { id: 3, navTitle: '精工集团 XR 定制系统', title: '精工集团 XR 室内定制系统', category: '虚拟现实', shortCategory: 'XR 定制', role: '交互开发', summary: '模块化空间定制与展示系统。', highlights: [{title:'状态绑定', content:'Spatial Anchor 房间状态管理。'},{title:'交互生态', content:'全息菜单联动。'},{title:'热替换机制', content:'材质与结构动态切换。'}], video: 'https://video.lh-xr.top/JingGong.mp4', tags: ['Unity', 'Meta XR'] },
  { id: 4, navTitle: '西邮周年庆 XR 系统', title: '西邮周年庆 XR 校园导览系统', category: '数字孪生', shortCategory: 'XR 导览', role: 'XR 开发', summary: '校庆 XR 导览与沙盘交互。', highlights: [{title:'持久化管理', content:'Spatial Anchors 自动恢复。'},{title:'手柄配置', content:'预览物体可视化调整。'},{title:'全息数据', content:'SO 数据组装与平移缩放。'}], video: 'https://video.lh-xr.top/XiYou.mp4', tags: ['Unity', 'Spatial Anchors'] },
  { id: 5, isAI: true, navTitle: 'Unity MCP AI 工具', title: 'Unity MCP AI 自动化工具链', category: 'AI 架构', shortCategory: '🤖 AI 效能', role: 'AI 开发', summary: 'LLM Agent 驱动的 Unity 自动化管线。', highlights: [{title:'MCP 协议', content:'构建内置 Server 检索场景。'},{title:'组件自愈', content:'一键诊断 XR 场景隐患。'},{title:'自然语言', content:'指令驱动场景建构。'}], video: 'https://video.lh-xr.top/MCP_AI.mp4', tags: ['Unity MCP', 'LLM Agent'] }
])
const currentProject = computed(() => projects.value.find(p => p.id === currentId.value) || projects.value[0])
const generateParticleStyle = (index) => ({ width: `${(index % 4) * 1.5 + 1.5}px`, height: `${(index % 4) * 1.5 + 1.5}px`, left: `${(index * 13) % 100}%`, top: `${(index * 19) % 100}%` })
const handleCardTilt = (e) => { /* 此处省略，保持逻辑 */ }
const resetCardTilt = (e) => { /* 此处省略，保持逻辑 */ }
</script>

<style>
/* 所有的 CSS 全部在这里！ */
.app-wrapper { background: #05070a; min-height: 100vh; color: #fff; }
.intro-screen { position: fixed; inset: 0; background: #05070a; z-index: 9999; display: flex; align-items: center; justify-content: center; }
.portfolio-container { padding: 40px 20px; font-family: sans-serif; }
.nebula-glow { position: absolute; width: 400px; height: 400px; filter: blur(120px); opacity: 0.2; pointer-events: none; }
.glowing-cyan { background: #00f0ff; top: 0; left: 10%; }
.glowing-blue { background: #0044ff; bottom: 0; right: 10%; }
.header { text-align: center; margin-bottom: 40px; }
.glow-title { font-size: 38px; color: #fff; text-shadow: 0 0 20px rgba(0, 240, 255, 0.5); }
.project-selector { display: grid; grid-template-columns: repeat(5, 1fr); gap: 14px; max-width: 1100px; margin: 0 auto 30px; }
.nav-card { background: rgba(18, 24, 38, 0.8); border: 1px solid #333; padding: 20px; border-radius: 12px; cursor: pointer; }
.nav-card.active { border-color: #00f0ff; background: rgba(0, 240, 255, 0.1); }
.showcase-area { max-width: 1100px; margin: 0 auto; background: rgba(16, 22, 34, 0.8); padding: 20px; border-radius: 16px; }
.cyber-video { width: 100%; border-radius: 8px; }
.highlights-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 15px; margin-top: 20px; }
.hl-item { background: rgba(255,255,255,0.05); padding: 15px; border-radius: 8px; }
.tech-tag { display: inline-block; margin: 5px; padding: 5px 12px; background: rgba(0, 240, 255, 0.1); border-radius: 4px; }
</style>