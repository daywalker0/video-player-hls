<template>
  <div class="player-container">
    <Info :buffer-size="bufferSize" :player-state="playerState" />
    <VideoPlayer @updateBufferSize="updateBufferSize" @updatePlayerState="updatePlayerState" />
  </div>
</template>

<script setup>
import { ref } from 'vue';
import VideoPlayer from './components/VideoPlayer.vue';
import Info from './components/Info.vue';

const bufferSize = ref(0);
const playerState = ref({
  isPlaying: false,
  isMuted: false,
  areSubtitlesOn: true,
  isFullscreen: false,
  currentTime: 0,
  videoDuration: 0,
});

const updateBufferSize = (size) => {
  bufferSize.value = size;
};

const updatePlayerState = (state) => {
  playerState.value = state;
};
</script>

<style scoped>
.player-container {
  max-width: 900px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-self: center;
  gap: 20px;
}
</style>