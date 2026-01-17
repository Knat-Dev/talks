<script setup>
/**
 * Coralogix Default Layout
 * Light gradient background with dark text - for content slides
 */
import { useSlideContext } from '@slidev/client';
const { $page } = useSlideContext();
</script>

<template>
  <div class="slidev-layout default">
    <!-- Logo top-left -->
    <img
      class="logo"
      src="/assets/coralogix-logo-dark.png"
      alt="Coralogix"
    />

    <!-- Title slot (fixed position) -->
    <div class="title-area">
      <slot name="title" />
    </div>

    <!-- Content -->
    <div class="content">
      <slot />
    </div>

    <!-- Page number -->
    <div class="page-number">{{ $page }}</div>
  </div>
</template>

<style scoped>
.default {
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

.title-area {
  margin-bottom: 1rem;
}

.content {
  width: 100%;
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.page-number {
  position: absolute;
  bottom: 1.5rem;
  right: 4rem;
  color: var(--cx-text-muted);
  font-size: 0.875rem;
  font-weight: 500;
}

/* Ensure code blocks fill the content width */
.content :deep(.slidev-code-wrapper),
.content :deep(pre) {
  width: 100% !important;
  max-width: 100% !important;
}

/* Typography */
.default :deep(h1) {
  color: var(--cx-text-dark) !important;
  font-size: 2.5rem !important;
  font-weight: 500 !important;
  margin: 0 !important;
}

.default :deep(h2) {
  color: var(--cx-green) !important;
  font-size: 1.5rem !important;
  font-weight: 500 !important;
  margin: 0 !important;
}

.default :deep(h3) {
  color: var(--cx-text) !important;
  font-size: 1.25rem !important;
  font-weight: 500 !important;
  margin: 0 !important;
}

.default :deep(p) {
  color: var(--cx-text);
  font-size: 1.125rem;
  line-height: 1.6;
}

.default :deep(strong) {
  color: var(--cx-green);
  font-weight: 500;
}

.default :deep(li) {
  color: var(--cx-text);
}
</style>
