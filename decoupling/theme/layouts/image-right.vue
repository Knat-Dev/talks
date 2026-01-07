<script setup>
/**
 * Coralogix Split Layout
 * 30% green left with title, 70% white right with dynamic content
 */
import { useSlideContext } from '@slidev/client'
const { $page } = useSlideContext()
</script>

<template>
  <div class="layout">
    <!-- Left panel - green -->
    <div class="left-panel">
      <div class="left-logo">
        <img src="/assets/coralogix-logo.png" alt="Coralogix" />
      </div>
      <div class="left-content">
        <slot name="left" />
      </div>
    </div>

    <!-- Right panel - white -->
    <div class="right-panel">
      <slot />
    </div>

    <!-- Page number -->
    <div class="page-number">{{ $page }}</div>
  </div>
</template>

<style scoped>
.layout {
  width: 100%;
  height: 100%;
  display: grid;
  grid-template-columns: 30% 70%;
  position: relative;
}

.left-panel {
  background: var(--cx-green-gradient-vertical);
  padding: 2rem;
  display: flex;
  flex-direction: column;
  justify-content: center;
  position: relative;
}

.left-logo {
  position: absolute;
  top: 1.5rem;
  left: 2rem;
}

.left-logo img {
  height: 28px;
  width: auto;
}

.left-content {
  margin-top: 2rem;
}

.right-panel {
  background: var(--cx-white);
  padding: 3rem;
  display: flex;
  flex-direction: column;
  justify-content: center;
  overflow: hidden;
}

.page-number {
  position: absolute;
  bottom: 1.5rem;
  right: 2rem;
  color: var(--cx-text-muted);
  font-size: 0.875rem;
  font-weight: 500;
}

/* Left panel typography - white text */
.left-panel :deep(h1) {
  color: var(--cx-white) !important;
  font-size: 2.5rem !important;
  font-weight: 500 !important;
  line-height: 1.15 !important;
  margin: 0 !important;
}

.left-panel :deep(h2) {
  color: var(--cx-white) !important;
  font-size: 1.25rem !important;
  font-weight: 500 !important;
  opacity: 0.9;
  margin-top: 0.5rem !important;
}

.left-panel :deep(p) {
  color: var(--cx-white);
  font-size: 1rem;
  opacity: 0.9;
  margin: 0.25rem 0;
}

/* Right panel typography - dark text */
.right-panel :deep(h2) {
  color: var(--cx-text-dark) !important;
  font-size: 1.5rem !important;
  font-weight: 500 !important;
  margin-bottom: 1rem !important;
}

.right-panel :deep(p) {
  color: var(--cx-text);
  font-size: 1rem;
  line-height: 1.6;
}

.right-panel :deep(strong) {
  color: var(--cx-green-dark);
  font-weight: 500;
}

.right-panel :deep(img) {
  max-width: 100%;
  max-height: 100%;
  object-fit: contain;
}
</style>
