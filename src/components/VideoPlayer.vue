<template>
  <div
    @mousemove="showControls"
    @mouseleave="hideControls"
    @click="handlePlayerClick"
    tabindex="0"
    class="video-wrapper"
  >
    <video ref="videoEl" class="video-player" @timeupdate="updateTime">
      <track
        ref="subtitleTrack"
        kind="subtitles"
        label="English"
        srclang="en"
        default
      />
    </video>
    <button
      :class="{ 'control-btn': true, 'control-btn-big': true, 'active': isShowedControls }"
    >
      {{ isPlaying ? '❚❚' : '▶' }}
    </button>
    <div
      :class="{ 'custom-controls': true, 'active': isShowedControls }"
      @click.stop
    >
      <div class="custom-controls-range">
        <input type="range" v-model="currentTime" :max="videoDuration" @input="seekVideo" class="seek-bar" />
      </div>
      <div class="custom-controls-buttons">
        <div class="left-controls">
          <button @click.stop="togglePlayPause" class="control-btn">{{ isPlaying ? '❚❚' : '▶' }}</button>
          <button @click.stop="toggleMute" class="control-btn">{{ isMuted ? '🔇' : '🔊' }}</button>
          <span class="time">{{ formatTime(currentTime) }} / {{ formatTime(videoDuration) }}</span>
        </div>
        <div class="right-controls">
          <button @click.stop="toggleSubtitles" class="control-btn">{{ areSubtitlesOn ? 'CC' : 'CC-' }}</button>
          <button @click.stop="toggleFullscreen" class="control-btn">⛶</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, defineEmits } from 'vue';
import Hls from 'hls.js';

const emit = defineEmits(['updateBufferSize', 'updatePlayerState']);

const videoEl = ref(null);
const subtitleTrack = ref(null);
const currentTime = ref(0);
const isPlaying = ref(false);
const isMuted = ref(false);
const videoDuration = ref(0);
const isShowedControls = ref(false);
const areSubtitlesOn = ref(true);
const subtitleTracks = ref([]);
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
      subtitleTracks.value = hls.subtitleTracks;
      if (subtitleTracks.value.length > 0) {
        hls.subtitleTrack = areSubtitlesOn.value ? 0 : -1;
      }
    });
    hls.on(Hls.Events.BUFFER_APPENDED, () => {
      updateBufferSize();
    });
  } else if (video.canPlayType('application/vnd.apple.mpegurl')) {
    video.src = videoSrc;
  }

  video.onloadedmetadata = () => {
    videoDuration.value = Math.floor(video.duration);
    emitPlayerState();
  };
};

const updateTime = () => {
  const video = videoEl.value;
  currentTime.value = Math.floor(video.currentTime);
  isPlaying.value = !video.paused;
  emitPlayerState();
};

const updateBufferSize = () => {
  const video = videoEl.value;
  if (video.buffered.length > 0) {
    const bufferedEnd = video.buffered.end(video.buffered.length - 1);
    emit('updateBufferSize', Math.floor(bufferedEnd - video.currentTime));
  }
};

const emitPlayerState = () => {
  emit('updatePlayerState', {
    isPlaying: isPlaying.value,
    isMuted: isMuted.value,
    areSubtitlesOn: areSubtitlesOn.value,
    isFullscreen: !!document.fullscreenElement,
    currentTime: currentTime.value,
    videoDuration: videoDuration.value,
  });
};

const togglePlayPause = () => {
  const video = videoEl.value;
  if (video.paused) {
    video.play();
  } else {
    video.pause();
  }
  emitPlayerState();
};

const toggleMute = () => {
  const video = videoEl.value;
  video.muted = !video.muted;
  isMuted.value = video.muted;
  emitPlayerState();
};

const toggleFullscreen = () => {
  const video = videoEl.value;
  if (!document.fullscreenElement) {
    video.requestFullscreen().catch(err => console.error('Ошибка:', err));
  } else {
    document.exitFullscreen().catch(err => console.error('Ошибка:', err));
  }
  emitPlayerState();
};

const seekVideo = () => {
  videoEl.value.currentTime = currentTime.value;
  emitPlayerState();
};

const formatTime = (seconds) => {
  const minutes = Math.floor(seconds / 60);
  const secs = Math.floor(seconds % 60);
  return `${minutes.toString().padStart(2, '0')}:${secs.toString().padStart(2, '0')}`;
};

const showControls = () => {
  isShowedControls.value = true;
};

const hideControls = () => {
  isShowedControls.value = false;
};

const toggleSubtitles = () => {
  if (hls && subtitleTracks.value.length > 0) {
    areSubtitlesOn.value = !areSubtitlesOn.value;
    hls.subtitleTrack = areSubtitlesOn.value ? 0 : -1;
  } else if (videoEl.value.textTracks.length > 0) {
    areSubtitlesOn.value = !areSubtitlesOn.value;
    videoEl.value.textTracks[0].mode = areSubtitlesOn.value ? 'showing' : 'hidden';
  }
  emitPlayerState();
};

const handlePlayerClick = () => {
  togglePlayPause();
};

const handleKeyPress = (event) => {
  event.preventDefault();
  switch (event.key.toLowerCase()) {
    case ' ':
      togglePlayPause();
      break;
    case 'c':
      toggleSubtitles();
      break;
    case 'm':
      toggleMute();
      break;
    case 'f':
      toggleFullscreen();
      break;
  }
};

onMounted(() => {
  initPlayer();
  window.addEventListener('keydown', handleKeyPress);
});

onUnmounted(() => {
  if (hls) {
    hls.destroy();
  }
  window.removeEventListener('keydown', handleKeyPress);
});
</script>

<style scoped>

.video-wrapper {
  position: relative;
  width: 100%;
}

.video-player {
  width: 100%;
  height: auto;
  background: black;
  display: block;
  border-radius: 10px;
}

.custom-controls {
  position: absolute;
  bottom: 0;
  display: flex;
  flex-direction: column;
  width: 100%;
  padding: 10px;
  gap: 5px;
  background-color: rgba(0, 0, 0, 0.7);
  opacity: 0;
  visibility: hidden;
  transition: opacity 0.3s ease, visibility 0s 0.3s;
}

.custom-controls.active {
  opacity: 0.8;
  visibility: visible;
  transition: opacity 0.3s ease, visibility 0s;
}

.custom-controls:hover {
  opacity: 1;
}

.custom-controls-buttons {
  display: flex;
  justify-content: space-between;
}

.left-controls, .right-controls {
  display: flex;
  gap: 10px;
  align-items: center;
}

.seek-bar {
  width: 100%;
  cursor: pointer;
}

.control-btn {
  width: 40px;
  height: 40px;
  border: none;
  border-radius: 50%;
  cursor: pointer;
  font-size: 18px;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: rgba(255, 255, 255, 0.8);
}

.control-btn-big {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 100px;
  height: 100px;
  opacity: 0;
  visibility: hidden;
  transition: opacity 0.3s ease, visibility 0s 0.3s;
}

.control-btn-big.active {
  opacity: 1;
  visibility: visible;
  transition: opacity 0.3s ease, visibility 0s;
}

.control-btn:hover {
  background-color: rgba(0, 0, 0, 0.6);
  color: #f5f5f5;
}

.time {
  color: #f5f5f5;
}
</style>