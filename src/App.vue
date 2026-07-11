<template>
  <div class="page">
    <header class="hero">
      <h1 class="title">SHOOT<span class="accent">N</span>RUN</h1>
      <p class="tagline">Fast, punchy, low-poly arena shooter. Built in Unity, playable right here.</p>
    </header>

    <main class="game-section">
      <UnityGame build-name="WebGLBuild" build-folder="/Build" />
    </main>

    <footer class="footer">
      <p>ShootnRun &middot; built with Unity WebGL &middot; hosted on Vercel</p>
    </footer>
  </div>
</template>

<script setup>
import UnityGame from './components/UnityGame.vue'
</script>

<style scoped>
.page {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 3rem 1rem 2rem;
  gap: 2rem;
  min-height: 100vh;
}

.hero {
  text-align: center;
  max-width: 700px;
}

.title {
  font-size: clamp(2.5rem, 6vw, 4.5rem);
  font-weight: 800;
  letter-spacing: 0.03em;
  margin: 0;
}

.accent {
  color: var(--accent);
}

.tagline {
  color: var(--muted);
  font-size: 1.1rem;
  margin-top: 0.5rem;
}

.game-section {
  width: 100%;
  display: flex;
  justify-content: center;
}

.footer {
  color: var(--muted);
  font-size: 0.8rem;
  margin-top: auto;
  padding-top: 1rem;
}
</style>
