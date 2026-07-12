<template>
  <div class="unity-wrap">
    <div v-if="!started" class="unity-poster" @click="start">
      <div class="play-btn">▶ Play</div>
      <div class="poster-hint">Click to load the game</div>
    </div>

    <div v-show="started" class="unity-canvas-holder">
      <canvas ref="canvasEl" id="unity-canvas" class="unity-canvas" tabindex="-1"></canvas>
      <div v-if="loading" class="unity-loading">
        <div class="loading-tagline">ARE YOU BORED? LET'S KILL IT.</div>
        <div class="loading-flavor">Loading firepower...</div>
        <div class="unity-progress">
          <div class="bar">
            <div class="fill" :style="{ width: `${Math.round(progress * 100)}%` }"></div>
          </div>
          <div class="pct">{{ Math.round(progress * 100) }}%</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const props = defineProps({
  buildFolder: { type: String, default: '/Build' },
  buildName: { type: String, required: true },
})

const canvasEl = ref(null)
const started = ref(false)
const loading = ref(false)
const progress = ref(0)

let unityInstance = null

function loadScript(src) {
  return new Promise((resolve, reject) => {
    const script = document.createElement('script')
    script.src = src
    script.onload = resolve
    script.onerror = () => reject(new Error(`Failed to load ${src}`))
    document.body.appendChild(script)
  })
}

async function start() {
  if (started.value) return
  started.value = true
  loading.value = true

  const loaderUrl = `${props.buildFolder}/${props.buildName}.loader.js`
  await loadScript(loaderUrl)

  const config = {
    dataUrl: `${props.buildFolder}/${props.buildName}.data.gz`,
    frameworkUrl: `${props.buildFolder}/${props.buildName}.framework.js.gz`,
    codeUrl: `${props.buildFolder}/${props.buildName}.wasm.gz`,
    streamingAssetsUrl: 'StreamingAssets',
    companyName: 'DefaultCompany',
    productName: 'ShootnRun',
    productVersion: '0.1',
  }

  // eslint-disable-next-line no-undef
  unityInstance = await createUnityInstance(canvasEl.value, config, (p) => {
    progress.value = p
  })

  loading.value = false
}

defineExpose({ start })
</script>

<style scoped>
.unity-wrap {
  width: 100%;
  aspect-ratio: 16 / 9;
  max-width: 1100px;
  margin: 0 auto;
  background: #000;
  border-radius: 12px;
  overflow: hidden;
  position: relative;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.5);
}

.unity-poster {
  position: absolute;
  inset: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 0.75rem;
  cursor: pointer;
  background: radial-gradient(circle at center, #1a1e2b 0%, #05060a 100%);
}

.play-btn {
  font-size: 1.5rem;
  font-weight: 700;
  padding: 0.75rem 2rem;
  border-radius: 999px;
  background: var(--accent);
  color: white;
  letter-spacing: 0.02em;
}

.poster-hint {
  color: var(--muted);
  font-size: 0.9rem;
}

.unity-canvas-holder {
  position: relative;
  width: 100%;
  height: 100%;
}

.unity-canvas {
  width: 100%;
  height: 100%;
  display: block;
}

.unity-loading {
  position: absolute;
  inset: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
  pointer-events: none;
}

.loading-tagline {
  font-size: clamp(1.3rem, 3vw, 2.2rem);
  font-weight: 800;
  letter-spacing: 0.02em;
  color: white;
  text-shadow: 0 0 20px var(--accent), 0 2px 8px rgba(0, 0, 0, 0.6);
}

.loading-flavor {
  font-size: 0.95rem;
  color: var(--muted);
  margin-bottom: 1.5rem;
  letter-spacing: 0.03em;
}

.unity-progress {
  width: 60%;
  display: flex;
  align-items: center;
  gap: 0.75rem;
}

.bar {
  flex: 1;
  height: 6px;
  background: rgba(255, 255, 255, 0.15);
  border-radius: 999px;
  overflow: hidden;
}

.fill {
  height: 100%;
  background: linear-gradient(90deg, var(--accent), var(--accent-2));
  transition: width 0.2s ease;
}

.pct {
  font-size: 0.8rem;
  color: var(--muted);
  min-width: 3ch;
}
</style>
