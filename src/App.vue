<script setup>
import { ref, computed } from 'vue'

// 1. 定义你的项目数据源（保持之前的数据，增加简单的数字 ID）
const projectList = ref([
  {
    id: 'xr-song-dynasty',
    name: '🎨 宋代文化大空间 MR 交互项目',
    tag: '混合现实 / 场景构建',
    icon: '🏛️', // 增加一个简单的图标，增强视觉辨识度
    desc: '基于 Meta Quest 3 开发的沉浸式文化体验。项目实现了高性能的 Passthrough 穿透效果，并在移动端硬件上将总面数严格控制在安全线内。利用空间锚点（Spatial Anchors）实现了大空间内的虚实精准对齐。',
    techStack: ['Unity', 'Meta MRUK', 'C#', '混合现实'],
    videoUrl: 'https://video.lh-xr.top/11.mp4'
  },
  {
    id: 'cross-platform-mall',
    name: '🛋️ 跨平台三维家居可视化系统',
    tag: '全平台应用 / 工具类',
    icon: '🛋️', // 增加图标
    desc: '一套打通 iOS、Android 和 XR 端的家具网格自动吸附与动态加载系统。前端基于组件化架构开发，资源管理部分采用 Addressables 进行按需加载，完美解决了多平台数据同步与本地渲染的性能瓶颈。',
    techStack: ['Vue 3', 'Tailwind CSS', 'C#', 'Addressables'],
    videoUrl: 'https://video.lh-xr.top/11.mp4'
  }
])

// 2. 响应式变量：记录当前选中的项目 ID
const currentProjectId = ref(projectList.value[0].id)

// 3. 计算属性：查表逻辑
const currentProject = computed(() => {
  return projectList.value.find(p => p.id === currentProjectId.value)
})
</script>

<template>
  <div class="portfolio-app">
    <header class="hero-section">
      <div class="header-content">
        <span class="site-tag"># XR Developer #</span>
        <h1>YOUR PORTFOLIO</h1>
        <p class="subtitle">个人项目 Demo 线上展示站</p>
      </div>
    </header>

    <section class="project-navigation">
      <div class="navigation-content">
        <h3 class="nav-title">请选择要查看的项目：</h3>
        <div class="project-tabs-container">
          <button 
            v-for="project in projectList" 
            :key="project.id"
            :class="['project-tab', { 'active-tab': project.id === currentProjectId }]"
            @click="currentProjectId = project.id"
          >
            <div class="tab-icon">{{ project.icon }}</div>
            <div class="tab-details">
              <span class="tab-name">{{ project.name }}</span>
              <span class="tab-tag">{{ project.tag }}</span>
            </div>
          </button>
        </div>
      </div>
    </section>

    <main class="content-display">
      <article v-if="currentProject" :key="currentProject.id" class="project-showcase">
        <div class="video-container">
          <video
           :src="currentProject.videoUrl"
           controls
           preload="metadata"
           playsinline
           class="main-video"
           controlslist="nodownload" 
           oncontextmenu="return false;"
         >
            您的浏览器不支持播放该视频。
          </video>
        </div>

        <div class="project-details-card">
          <div class="card-meta">
            <span class="project-tag-chip">{{ currentProject.tag }}</span>
          </div>
          <h2 class="project-title">{{ currentProject.name }}</h2>
          <p class="project-description">{{ currentProject.desc }}</p>
          
          <div class="tech-stack-container">
            <span v-for="tech in currentProject.techStack" :key="tech" class="tech-tag">
              {{ tech }}
            </span>
          </div>
        </div>
      </article>
    </main>
  </div>
</template>

<style scoped>
/* 全新设计的极简、高贵现代样式，深度优化 HR 体验 */

/* 1. 布局与字体设置 */
.portfolio-app {
  max-width: 1200px; /* 增加整体宽度，气场更足 */
  margin: 0 auto;
  padding: 60px 20px;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
  color: #1a1a1a; /* 极深灰，比纯黑更有质感 */
  background-color: #fcfcfc; /* 接近白，但减少刺眼感 */
  line-height: 1.6;
}

/* 2. 头部样式 */
.hero-section {
  text-align: center;
  margin-bottom: 80px;
}

.site-tag {
  display: inline-block;
  font-size: 0.8rem;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 2px;
  background-color: #e6f7ff;
  color: #1890ff;
  padding: 4px 12px;
  border-radius: 20px;
  margin-bottom: 15px;
}

.hero-section h1 {
  font-size: 3rem;
  letter-spacing: 3px;
  font-weight: 800;
  margin: 0 0 10px 0;
}

.subtitle {
  color: #6a6a6a;
  font-size: 1.2rem;
  letter-spacing: 1px;
}

/* 3. 【核心样式】项目卡片导航 */
.project-navigation {
  margin-bottom: 60px;
  background: #ffffff;
  border-radius: 16px;
  padding: 30px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.03); /* 超淡投影 */
  border: 1px solid #f0f0f0;
}

.nav-title {
  font-size: 1.1rem;
  color: #1a1a1a;
  margin-bottom: 20px;
  font-weight: 600;
}

.project-tabs-container {
  display: flex;
  gap: 20px;
  flex-wrap: wrap; /* 适配移动端 */
}

/* 基础卡片样式 */
.project-tab {
  flex: 1; /* 平分剩余空间 */
  min-width: 280px;
  display: flex;
  align-items: flex-start;
  gap: 15px;
  padding: 20px;
  background-color: #ffffff;
  border: 2px solid #e8e8e8; /* 较淡的边框 */
  border-radius: 12px;
  cursor: pointer;
  text-align: left;
  transition: all 0.3s cubic-bezier(0.23, 1, 0.32, 1);
}

/* 悬停时的反馈 */
.project-tab:hover {
  border-color: #3498db;
  transform: translateY(-2px);
  box-shadow: 0 8px 24px rgba(52, 152, 219, 0.08);
}

/* 选中后的样式（最关键） */
.project-tab.active-tab {
  background-color: #3498db; /* 使用醒目的蓝色 */
  border-color: #3498db;
  box-shadow: 0 8px 24px rgba(52, 152, 219, 0.2);
}

.tab-icon {
  font-size: 1.5rem;
  margin-top: 2px;
}

.tab-details {
  display: flex;
  flex-direction: column;
}

.tab-name {
  font-size: 1rem;
  font-weight: 700;
  margin-bottom: 3px;
  transition: color 0.3s;
}

.tab-tag {
  font-size: 0.8rem;
  color: #7f8c8d;
  font-weight: 500;
  transition: color 0.3s;
}

/* 选中后文字颜色的反转 */
.active-tab .tab-name,
.active-tab .tab-tag {
  color: #ffffff;
}

/* 4. 内容区域样式 */
.project-showcase {
  background: #ffffff;
  border-radius: 16px;
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.05);
  overflow: hidden;
  border: 1px solid #f0f0f0;
}

.video-container {
  width: 100%;
  background: #000;
  aspect-ratio: 16 / 9;
}

.main-video {
  width: 100%;
  height: 100%;
  object-fit: contain;
}

/* 5. 详情卡片区域样式 */
.project-details-card {
  padding: 40px;
}

.card-meta {
  margin-bottom: 12px;
}

.project-tag-chip {
  font-size: 0.8rem;
  font-weight: 700;
  color: #7f8c8d;
  text-transform: uppercase;
  letter-spacing: 1px;
}

.project-title {
  font-size: 2rem;
  font-weight: 800;
  margin: 0 0 20px 0;
  letter-spacing: 1px;
}

.project-description {
  font-size: 1.1rem;
  line-height: 1.8;
  color: #4a5568;
  margin-bottom: 30px;
}

/* 6. 技术栈标签样式 */
.tech-stack-container {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
}

.tech-tag {
  padding: 7px 16px;
  font-size: 0.85rem;
  font-weight: 600;
  background-color: #f7fafc;
  color: #3a3f45;
  border: 1px solid #e2e8f0;
  border-radius: 20px;
}
</style>
