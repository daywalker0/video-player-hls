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
  max-width: 1200px;
  margin: 20px auto;
  display: flex;
  flex-direction: column;
  gap: 20px;
}
</style>