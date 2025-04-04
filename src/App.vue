<template>
  <div class="player-container">
    <div class="video-wrapper">
      <video ref="videoEl" class="video-player" @timeupdate="updateTime"></video>
      <button @click="togglePlayPause" class="control-btn control-btn-big">{{ isPlaying ? '❚❚' : '▶' }}</button>
      <div class="custom-controls">
        <div class="left-controls">
          <button @click="togglePlayPause" class="control-btn">{{ isPlaying ? '❚❚' : '▶' }}</button>
          <button @click="toggleMute" class="control-btn">{{ isMuted ? '🔇' : '🔊' }}</button>
        </div>
        <input type="range" v-model="currentTime" :max="videoDuration" @input="seekVideo" class="seek-bar" />
        <button @click="toggleFullscreen" class="control-btn">⛶</button>
      </div>
    </div>
    <div class="player-info">
      <p>Текущая позиция: {{ currentTime }} сек</p>
      <p>Размер буфера: {{ bufferSize }} сек</p>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue';
import Hls from 'hls.js';

const videoEl = ref(null);
const currentTime = ref(0);
const bufferSize = ref(0);
const isPlaying = ref(false);
const isMuted = ref(false);
const videoDuration = ref(0);
let hls = null;

const initPlayer = () => {
  const video = videoEl.value;
  const videoSrc = 'https://devstreaming-cdn.apple.com/videos/streaming/examples/img_bipbop_adv_example_ts/master.m3u8';

  if (Hls.isSupported()) {
    hls = new Hls();
    hls.loadSource(videoSrc);
    hls.attachMedia(video);
    hls.on(Hls.Events.MANIFEST_PARSED, () => {
      console.log('Манифест загружен');
    });
    hls.on(Hls.Events.BUFFER_APPENDED, () => {
      updateBufferSize();
    });
  } else if (video.canPlayType('application/vnd.apple.mpegurl')) {
    video.src = videoSrc;
  }

  video.onloadedmetadata = () => {
    videoDuration.value = Math.floor(video.duration);
  };
};

const updateTime = () => {
  const video = videoEl.value;
  currentTime.value = Math.floor(video.currentTime);
  isPlaying.value = !video.paused;
};

const updateBufferSize = () => {
  const video = videoEl.value;
  if (video.buffered.length > 0) {
    const bufferedEnd = video.buffered.end(video.buffered.length - 1);
    bufferSize.value = Math.floor(bufferedEnd - video.currentTime);
  }
};

const togglePlayPause = () => {
  const video = videoEl.value;
  if (video.paused) {
    video.play();
  } else {
    video.pause();
  }
};

const toggleMute = () => {
  const video = videoEl.value;
  video.muted = !video.muted;
  isMuted.value = video.muted;
};

const toggleFullscreen = () => {
  const video = videoEl.value;
  if (!document.fullscreenElement) {
    video.requestFullscreen();
  } else {
    document.exitFullscreen();
  }
};

const seekVideo = () => {
  videoEl.value.currentTime = currentTime.value;
};

onMounted(() => {
  initPlayer();
});

onUnmounted(() => {
  if (hls) {
    hls.destroy();
  }
});
</script>

<style scoped>
.player-container {
  max-width: 800px;
  margin: 20px auto;
  font-family: Arial, sans-serif;
}

.video-wrapper {
  position: relative;
  width: 100%;
}

.video-player {
  width: 100%;
  height: auto;
  background: black;
  display: block;
}

.custom-controls {
  position: absolute;
  bottom: 10px;
  display: flex;
  width: 100%;
  padding: 0 10px;
  align-items: center;
  gap: 10px;
  opacity: 0.8;
  transition: opacity 0.3s;
}

.left-controls {
  display: flex;
  gap: 10px;
}

.seek-bar {
  width: 100%;
  cursor: pointer;
}

.video-wrapper:hover .custom-controls {
  opacity: 1;
}

.control-btn {
  width: 40px;
  height: 40px;
  background-color: rgba(0, 0, 0, 0.7);
  color: white;
  border: none;
  border-radius: 50%;
  cursor: pointer;
  font-size: 18px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.control-btn-big {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 100px;
  height: 100px;
}

.control-btn:hover {
  background-color: rgba(0, 0, 0, 0.9);
}

.player-info {
  margin-top: 10px;
  padding: 10px;
  background: #f5f5f5;
  border-radius: 5px;
}
</style>