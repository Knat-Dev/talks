<script setup lang="ts">
/**
 * Coralogix Two Columns Layout
 * Light gradient background with two column content
 */
</script>

<template>
  <div class="layout">
    <!-- Logo top-left -->
    <div class="layout-logo">
      <img src="/assets/coralogix-logo-dark.png" alt="Coralogix" />
    </div>

    <!-- Two column container -->
    <div class="layout-grid">
      <div class="layout-left">
        <slot />
      </div>
      <div class="layout-right">
        <slot name="right" />
      </div>
    </div>
  </div>
</template>

<style scoped>
.layout {
  width: 100%;
  height: 100%;
  background: var(--cx-light-gradient);
  padding: 4.5rem 4rem 3rem 4rem;
  display: flex;
  flex-direction: column;
  position: relative;
}

.layout-logo {
  position: absolute;
  top: 1.5rem;
  left: 4rem;
}

.layout-logo img {
  height: 28px;
  width: auto;
}

.layout-grid {
  flex: 1;
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 3rem;
  align-items: center;
}

.layout-left,
.layout-right {
  display: flex;
  flex-direction: column;
  justify-content: center;
  height: 100%;
  min-width: 0;
  overflow: hidden;
}

/* Ensure code blocks fill the column width */
.layout-left :deep(.slidev-code-wrapper),
.layout-right :deep(.slidev-code-wrapper),
.layout-left :deep(pre),
.layout-right :deep(pre) {
  width: 100% !important;
  max-width: 100% !important;
}

/* Typography - matching section layout */
.layout :deep(h1) {
  color: var(--cx-text-dark) !important;
  font-size: 3rem !important;
  font-weight: 500 !important;
  line-height: 1.2 !important;
  margin-bottom: 1rem !important;
}

.layout :deep(h2) {
  color: var(--cx-green) !important;
  font-size: 2rem !important;
  font-weight: 500 !important;
  margin-bottom: 0.75rem !important;
}

.layout :deep(p) {
  color: var(--cx-text);
  font-size: 1.25rem;
  line-height: 1.5;
}

.layout :deep(strong) {
  color: var(--cx-green);
  font-weight: 500;
}
</style>
