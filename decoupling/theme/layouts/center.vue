<script setup lang="ts">
/**
 * Coralogix Center Layout
 * Light gradient background with centered content
 */
import { useSlideContext } from '@slidev/client';
const { $page } = useSlideContext();
</script>

<template>
  <div class="layout">
    <!-- Logo top-left -->
    <img
      class="logo"
      src="/assets/coralogix-logo-dark.png"
      alt="Coralogix"
    />

    <!-- Content -->
    <div class="layout-content">
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
  background: var(--cx-light-gradient);
  padding: 2.5rem 4rem;
  box-sizing: border-box;
  overflow: hidden;
  position: relative;
  display: flex;
  flex-direction: column;
  color: var(--cx-text-dark);
}

.logo {
  height: 28px;
  width: auto;
  margin-bottom: 1.5rem;
  flex-shrink: 0;
  object-fit: contain;
  align-self: flex-start;
}

.layout-content {
  width: 75%;
  margin: 0 auto;
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  text-align: center;
}

/* Typography */
.layout :deep(h1) {
  color: var(--cx-text-dark) !important;
  font-size: 3rem !important;
  font-weight: 500 !important;
  margin-bottom: 1rem !important;
}

.layout :deep(h2) {
  color: var(--cx-green) !important;
  font-size: 1.75rem !important;
  font-weight: 500 !important;
  margin-bottom: 0.75rem !important;
}

.layout :deep(p) {
  color: var(--cx-text);
  font-size: 1.5rem;
  line-height: 1.5;
}

.layout :deep(strong) {
  color: var(--cx-green);
  font-weight: 500;
}

.page-number {
  position: absolute;
  bottom: 1.5rem;
  right: 4rem;
  color: var(--cx-text-muted);
  font-size: 0.875rem;
  font-weight: 500;
}
</style>
