<template>
  <div class="player-info">
    <div class="hotkeys">
      <h2>Горячие клавиши:</h2>
      <ul class="hotkeys-list">
        <li class="hotkeys-list-item">
          <div class="hotkeys-list-item-block">
            <p>Воспроизведение / Пауза: <span>Пробел (Space)</span></p>
            <span>{{ playerState.isPlaying ? 'Воспроизведение' : 'Пауза' }}</span>
          </div>
        </li>
        <li class="hotkeys-list-item">
          <div class="hotkeys-list-item-block">
            <p>Вкл / Выкл звука: <span>M</span></p>
            <span>{{ playerState.isMuted ? 'Выкл' : 'Вкл' }}</span>
          </div>
        </li>
        <li class="hotkeys-list-item">
          <div class="hotkeys-list-item-block">
            <p>Вкл / Выкл субтитры: <span>C</span></p>
            <span>{{ playerState.areSubtitlesOn ? 'Вкл' : 'Выкл' }}</span>
          </div>
        </li>
        <li class="hotkeys-list-item">
          <div class="hotkeys-list-item-block">
            <p>Вкл / Выкл полноэкранный режим: <span>F</span></p>
            <span>{{ playerState.isFullscreen ? 'Вкл' : 'Выкл' }}</span>
          </div>
        </li>
      </ul>
    </div>

    <hr>

    <div class="info">
      <h2>Информация:</h2>
      <p>Размер буфера: <span>{{ bufferSize }} сек</span></p>
    </div>
  </div>
</template>

<script setup>
import { defineProps } from 'vue';

const props = defineProps({
  bufferSize: Number,
  playerState: Object,
});
</script>

<style scoped>
.player-info {
  padding: 10px;
  background: #f5f5f5;
  border-radius: 10px;
}

.hotkeys {
  margin-bottom: 15px;
}

.hotkeys-list-item-block {
  display: flex;
  justify-content: space-between;
  span {
    color: black;
    font-weight: 700;
  }
}

.info {
  margin-top: 15px;
}

p {
  span {
    color: black;
    font-weight: 700;
  }
}
</style>