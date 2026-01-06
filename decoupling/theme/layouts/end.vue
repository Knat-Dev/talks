<script setup lang="ts">
/**
 * Coralogix End/Thank You Layout
 * Mint gradient background with centered content
 */
</script>

<template>
  <div class="layout">
    <!-- Logo top-left -->
    <div class="layout-logo">
      <img src="/assets/coralogix-logo.png" alt="Coralogix" />
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
  background: linear-gradient(135deg, #34d399 0%, #6ee7b7 40%, #a7f3d0 100%);
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
  top: 2rem;
  left: 3rem;
}

.layout-logo img {
  height: 32px;
  width: auto;
}

.layout-content {
  max-width: 70%;
}

/* Typography */
.layout :deep(h1) {
  color: #ffffff !important;
  font-size: 3.5rem !important;
  font-weight: 800 !important;
  margin-bottom: 1.5rem !important;
}

.layout :deep(p) {
  color: #ffffff;
  font-size: 1.5rem;
  opacity: 0.95;
  margin: 0.5rem 0;
}

.layout :deep(strong) {
  font-weight: 700;
}
</style>
