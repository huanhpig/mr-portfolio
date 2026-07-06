<!-- HlsPlayer.vue -->
<template>
  <video ref="videoRef" controls class="cyber-video no-save" oncontextmenu="return false;"></video>
</template>

<script setup>
import { ref, watch, onMounted, onBeforeUnmount } from 'vue';
import Hls from 'hls.js';

const props = defineProps(['src']);
const videoRef = ref(null);
let hls = null;

const initPlayer = () => {
  const video = videoRef.value;
  if (!video) return;

  // 1. 如果支持 HLS (大部分桌面浏览器和 Android)
  if (Hls.isSupported()) {
    hls = new Hls();
    hls.loadSource(props.src.replace('.mp4', '.m3u8')); // 自动映射到 m3u8 地址
    hls.attachMedia(video);
  } 
  // 2. 如果是 Safari (原生支持 HLS)
  else if (video.canPlayType('application/vnd.apple.mpegurl')) {
    video.src = props.src.replace('.mp4', '.m3u8');
  } 
  // 3. 兜底策略：自动降级播放 mp4
  else {
    video.src = props.src;
  }
};

watch(() => props.src, initPlayer);
onMounted(initPlayer);
onBeforeUnmount(() => { if (hls) hls.destroy(); });
</script>