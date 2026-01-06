<script setup lang="ts">
/**
 * Coralogix Section Layout
 * For section dividers and key statement slides
 */
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
  </div>
</template>

<style scoped>
.section {
  width: 100%;
  height: 100%;
  background: linear-gradient(180deg, #d1fae5 0%, #ecfdf5 40%, #ffffff 100%);
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

/* Typography */
.section :deep(h1) {
  color: #111827 !important;
  font-size: 3rem !important;
  font-weight: 800 !important;
  line-height: 1.2 !important;
  margin-bottom: 1rem !important;
}

.section :deep(h2) {
  color: #059669 !important;
  font-size: 2rem !important;
  font-weight: 600 !important;
  margin-bottom: 0.75rem !important;
}

.section :deep(p) {
  color: #374151;
  font-size: 1.25rem;
  line-height: 1.5;
}

.section :deep(strong) {
  color: #059669;
  font-weight: 700;
}
</style>
