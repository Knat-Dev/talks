<script setup>
/**
 * Coralogix Section Layout
 * For section dividers and key statement slides
 */
import { useSlideContext } from '@slidev/client';
const { $page } = useSlideContext();
</script>

<template>
  <div class="slidev-layout section">
    <!-- Logo top-left -->
    <img
      class="logo"
      src="/assets/coralogix-logo-dark.png"
      alt="Coralogix"
    />

    <!-- Title slot (fixed position - same as default) -->
    <div class="title-area">
      <slot name="title" />
    </div>

    <!-- Content -->
    <div class="section-content">
      <slot />
    </div>

    <!-- Page number -->
    <div class="page-number">{{ $page }}</div>
  </div>
</template>

<style scoped>
.section {
  width: 100%;
  height: 100%;
  background: var(--cx-light-gradient);
  padding: 4rem;
  box-sizing: border-box;
  overflow: hidden;
  position: relative;
  display: flex;
  flex-direction: column;
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

.section-content {
  width: 100%;
  max-width: 70%;
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

/* Typography - same h1 as default */
.section :deep(h1) {
  color: var(--cx-text-dark) !important;
  font-size: 2.5rem !important;
  font-weight: 500 !important;
  margin: 0 !important;
}

.section :deep(h2) {
  color: var(--cx-green) !important;
  font-size: 2rem !important;
  font-weight: 500 !important;
  margin: 0 !important;
}

.section :deep(p) {
  color: var(--cx-text);
  font-size: 1.25rem;
  line-height: 1.5;
}

.section :deep(strong) {
  color: var(--cx-green);
  font-weight: 500;
}
</style>
