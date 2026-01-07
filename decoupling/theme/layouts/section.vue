<script setup>
/**
 * Coralogix Section Layout
 * For section dividers and key statement slides
 */
import { useSlideContext } from '@slidev/client'
const { $page } = useSlideContext()
</script>

<template>
  <div class="section">
    <!-- Logo top-left -->
    <div class="section-logo">
      <img src="/assets/coralogix-logo-dark.png" alt="Coralogix" />
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
  padding: 4.5rem 4rem 3rem 4rem;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: flex-start;
  position: relative;
}

.section-logo {
  position: absolute;
  top: 1.5rem;
  left: 4rem;
}

.section-logo img {
  height: 28px;
  width: auto;
}

.section-content {
  max-width: 70%;
}

.page-number {
  position: absolute;
  bottom: 1.5rem;
  right: 4rem;
  color: var(--cx-text-muted);
  font-size: 0.875rem;
  font-weight: 500;
}

/* Typography */
.section :deep(h1) {
  color: var(--cx-text-dark) !important;
  font-size: 3rem !important;
  font-weight: 500 !important;
  line-height: 1.2 !important;
  margin-bottom: 1rem !important;
}

.section :deep(h2) {
  color: var(--cx-green-dark) !important;
  font-size: 2rem !important;
  font-weight: 500 !important;
  margin-bottom: 0.75rem !important;
}

.section :deep(p) {
  color: var(--cx-text);
  font-size: 1.25rem;
  line-height: 1.5;
}

.section :deep(strong) {
  color: var(--cx-green-dark);
  font-weight: 500;
}
</style>
