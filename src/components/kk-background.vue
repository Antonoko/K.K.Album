<template>
  <div :class="['background', { stopped: !isPlaying }]">
    <img v-if="isPlaying" :src="wave[0]" class="wave wave1" />
    <img v-if="isPlaying" :src="wave[0]" class="wave wave1" style="left: 100%;" />
    <img v-if="isPlaying" :src="wave[1]" class="wave wave2" />
    <img v-if="isPlaying" :src="wave[1]" class="wave wave2" style="left: 100%;" /> 
    <img :src="wave[2]" :class="['wave', 'wave3', { 'stopped-wave': !isPlaying }]" />
    <img :src="wave[2]" :class="['wave', 'wave3', { 'stopped-wave': !isPlaying }]" style="left: 100%;" />
  </div>
</template>

<script>
import { useMainStore } from '@/stores/mainStore'
import { computed, watch, onMounted } from 'vue'

import wave1 from '@/assets/img/kk-background/wave1.png';
import wave2 from '@/assets/img/kk-background/wave2.png';
import wave3 from '@/assets/img/kk-background/wave3.png';

export default {
  name: 'KK-Background',
  setup() {
    const store = useMainStore();
    const isPlaying = computed(() => store.isPlaying);

    // Watch for changes in isPlaying and update theme color
    watch(isPlaying, (newVal) => {
      const themeColor = newVal ? '#5AD0F2' : '#5467e7';
      document.querySelector('meta[name="theme-color"]').setAttribute('content', themeColor);
    });

    onMounted(() => {
      document.addEventListener('touchstart', function (event) {
        if (event.touches.length > 1) {
          event.preventDefault();
        }
      });

      let lastTouchEnd = 0;
      document.addEventListener('touchend', function (event) {
        const now = (new Date()).getTime();
        if (now - lastTouchEnd <= 300) {
          event.preventDefault();
        }
        lastTouchEnd = now;
      }, false);

      document.addEventListener('gesturestart', function (event) {
        event.preventDefault();
      });
    });

    return {
      isPlaying,
      wave: [wave1, wave2, wave3]
    }
  }
}
</script>

<style scoped>
.background {
  position: absolute;
  background-color: #5AD0F2;
  height: 100%;
  width: 100%;
  z-index: -100;
  overflow: hidden;
  transition: background-color 0.5s;
  user-select:none;
}

.background.stopped {
  background-color: #5467e7;
}

.wave {
  position: absolute;
  bottom: 0;
  width: 100%;
  animation: move 10s linear infinite;
  animation-fill-mode: both;
  transition: bottom 1s;
}

.wave1 {
  animation-duration: 20s;
}

.wave2 {
  animation-duration: 10s;
}

.wave3 {
  bottom: 10%;
  animation-duration: 15s;
  animation-direction: reverse;
}

.stopped-wave {
  animation: move 15s linear infinite, sink 1s ease-out forwards alternate;
}

@keyframes move {
  0% {
    transform: translateX(0);
  }
  100% {
    transform: translateX(-100%);
  }
}

@keyframes sink {
  0% {
    bottom: 0;
  }
  100% {
    bottom: -2%;
  }
}
</style>