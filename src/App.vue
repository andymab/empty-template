<template>
  <div class="app-wrapper">
    <div class="game-container" :style="gameContainerStyle">
      <div class="viewport">
        <!-- здесь абсолютные игровые объекты в логических координатах -->
        <div class="ship"></div>
        <div class="landing-pad"></div>
      </div>

      <div class="controls">
        <button>THRUST</button>
        <button>RESET</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount, computed } from 'vue'

const DESIGN_WIDTH = 1200
const DESIGN_HEIGHT = 850

const windowWidth = ref(window.innerWidth)
const windowHeight = ref(window.innerHeight)

const onResize = () => {
  windowWidth.value = window.innerWidth
  windowHeight.value = window.innerHeight
}

onMounted(() => {
  window.addEventListener('resize', onResize)
})

onBeforeUnmount(() => {
  window.removeEventListener('resize', onResize)
})


const MIN_SCALE = 0.6 // подберём комфортный минимум

const scale = computed(() => {
  const byH = windowHeight.value / DESIGN_HEIGHT

  // только по высоте, без учёта ширины
  let s = byH

  // не увеличиваем > 1 и не уменьшаем < MIN_SCALE
  if (s > 1) s = 1
  if (s < MIN_SCALE) s = MIN_SCALE

  return s
})


const gameContainerStyle = computed(() => ({
  width: DESIGN_WIDTH + 'px',
  height: DESIGN_HEIGHT + 'px',
  transform: `scale(${scale.value})`
}))
</script>

<style scoped>
.app-wrapper {
  height: 100vh;
  height: 100dvh;
  display: flex;
  justify-content: center;
  align-items: flex-start;
  background: #020713;
  overflow: hidden;
}

/* сам «канвас» 1200×850 */
.game-container {
  position: relative;
  transform-origin: top center;
  background: #000;
  color: #fff;
  box-shadow: 0 0 20px rgba(0, 0, 0, 0.7);
  display: flex;
  flex-direction: column;
  max-width: 100vw;
  /* важно для мобильных, чтобы не казалось уже экрана */
}

/* верхняя часть: игровое поле 650px */
.viewport {
  position: relative;
  width: 100%;
  height: 650px;
  background: radial-gradient(circle at 50% 0, #555 0, #111 60%, #000 100%);
  overflow: hidden;
}

/* нижняя часть: контролы 200px */
.controls {
  height: 200px;
  padding: 16px;
  display: flex;
  justify-content: center;
  gap: 12px;
  align-items: flex-end;
  background: #050815;
}

/* безопасная зона снизу на iOS */
@supports (padding: max(0px)) {
  .controls {
    padding-bottom: max(16px, env(safe-area-inset-bottom));
  }
}


/* пример абсолютных объектов в логических координатах */
.ship {
  position: absolute;
  width: 60px;
  height: 90px;
  left: 50%;
  top: 100px;
  /* Y в координатах viewport 0–650 */
  transform: translateX(-50%);
  background: linear-gradient(#ccc, #888);
  border-radius: 30px 30px 10px 10px;
}

.landing-pad {
  position: absolute;
  width: 200px;
  height: 20px;
  left: 50%;
  bottom: 30px;
  /* тоже в координатах viewport */
  transform: translateX(-50%);
  background: #2ecc71;
  border-radius: 10px;
}
</style>
