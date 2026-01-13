<script setup lang="ts">
/**
 * Coralogix Center Layout
 * Light gradient background with centered content
 */
</script>

<template>
  <div class="layout">
    <!-- Logo top-left -->
    <div class="layout-logo">
      <img
        src="/assets/coralogix-logo-dark.png"
        alt="Coralogix"
      />
    </div>

    <!-- Content -->
    <div class="layout-content">
      <slot />
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
  justify-content: center;
  align-items: center;
  text-align: center;
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

.layout-content {
  max-width: 75%;
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
  font-size: 1.25rem;
  line-height: 1.5;
}

.layout :deep(strong) {
  color: var(--cx-green);
  font-weight: 500;
}
</style>
