<template>
  <div class="player-info">
    <div class="hotkeys">
      <h2>Горячие клавиши:</h2>
      <ul class="list">
        <li class="list-item">
          <div class="list-item-block">
            <p>Воспроизведение / Пауза: <span>Пробел (Space)</span></p>
            <span>{{ playerState.isPlaying ? 'Воспроизведение' : 'Пауза' }}</span>
          </div>
        </li>
        <li class="list-item">
          <div class="list-item-block">
            <p>Вкл / Выкл звука: <span>M</span></p>
            <span>{{ playerState.isMuted ? 'Выкл' : 'Вкл' }}</span>
          </div>
        </li>
        <li class="list-item">
          <div class="list-item-block">
            <p>Вкл / Выкл субтитры: <span>C</span></p>
            <span>{{ playerState.areSubtitlesOn ? 'Вкл' : 'Выкл' }}</span>
          </div>
        </li>
        <li class="list-item">
          <div class="list-item-block">
            <p>Вкл / Выкл полноэкранный режим: <span>F</span></p>
            <span>{{ playerState.isFullscreen ? 'Вкл' : 'Выкл' }}</span>
          </div>
        </li>
      </ul>
    </div>

    <hr>

    <div class="info">
      <h2>Информация:</h2>
      <ul class="list">
        <li class="list-item">
          <div class="list-item-block-info">
            <p>Текущее время: </p>
            <span>{{ formatTime(playerState.currentTime) }}</span>
          </div>
        </li>
        <li class="list-item">
          <div class="list-item-block-info">
            <p>Общая длительность: </p>
            <span>{{ formatTime(playerState.videoDuration) }}</span>
          </div>
        </li>
        <li class="list-item">
          <div class="list-item-block-info">
            <p>Размер буфера: </p>
            <span>{{ bufferSize }} сек</span>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { defineProps } from 'vue';

const props = defineProps({
  bufferSize: Number,
  playerState: Object,
});

const formatTime = (seconds) => {
  const minutes = Math.floor(seconds / 60);
  const secs = Math.floor(seconds % 60);
  return `${minutes.toString().padStart(2, '0')}:${secs.toString().padStart(2, '0')}`;
};
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

.list-item-block {
  display: flex;
  justify-content: space-between;
  span {
    color: black;
    font-weight: 700;
  }
}

.list-item-block-info {
  display: flex;
  span {
    margin-left: 5px;
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