<script setup lang="ts">
import { computed } from 'vue'

/**
 * Coralogix Image Right Layout
 * Light gradient background, content left, image right
 */

const props = defineProps<{
  image?: string
}>()

const imageSrc = computed(() => {
  return props.image || $slidev.nav.currentPage?.meta?.frontmatter?.image
})
</script>

<template>
  <div class="layout">
    <!-- Logo top-left -->
    <div class="layout-logo">
      <img src="/assets/coralogix-logo-dark.png" alt="Coralogix" />
    </div>

    <!-- Two column container -->
    <div class="layout-grid">
      <div class="layout-content">
        <slot />
      </div>
      <div class="layout-image">
        <img v-if="imageSrc" :src="imageSrc" alt="" />
        <slot name="image" />
      </div>
    </div>
  </div>
</template>

<style scoped>
.layout {
  width: 100%;
  height: 100%;
  background: linear-gradient(180deg, #d1fae5 0%, #ecfdf5 40%, #ffffff 100%);
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
  gap: 2rem;
  align-items: center;
}

.layout-content {
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.layout-image {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100%;
}

.layout-image img {
  max-width: 100%;
  max-height: 90%;
  object-fit: contain;
  border-radius: 12px;
}

/* Typography */
.layout :deep(h1) {
  color: #111827 !important;
  font-size: 2.25rem !important;
  font-weight: 700 !important;
  margin-bottom: 1rem !important;
}

.layout :deep(h2) {
  color: #059669 !important;
  font-size: 1.25rem !important;
  font-weight: 600 !important;
  margin-bottom: 0.75rem !important;
}

.layout :deep(p) {
  color: #374151;
  font-size: 1rem;
  line-height: 1.5;
}

.layout :deep(strong) {
  color: #059669;
  font-weight: 700;
}
</style>
