<script setup lang="ts">
/**
 * Coralogix Main Cover Layout (A_Main Cover 2)
 * Solid green background with two-column grid layout
 */
</script>

<template>
  <div class="cover">
    <div class="cover-grid">
      <!-- Left column with logo and content -->
      <div class="cover-left">
        <div class="cover-logo">
          <img
            src="/assets/coralogix-logo.png"
            alt="Coralogix"
          />
        </div>
        <div class="cover-content">
          <slot />
        </div>
      </div>
      <!-- Right column for optional content -->
      <div class="cover-right">
        <slot name="right" />
      </div>
    </div>
  </div>
</template>

<style scoped>
.cover {
  width: 100%;
  height: 100%;
  background: var(--cx-green-gradient);
  padding: 0;
  display: flex;
  align-items: center;
  overflow: hidden;
}

.cover-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap:4rem;
  width: 100%;
  height: 100%;
  padding: 0 5rem;
  align-items: center;
}

.cover-left {
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.cover-right {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.cover-logo {
  margin-bottom: 1.25rem;
}

.cover-logo img {
  height: 44px;
  width: auto;
}

.cover-content {
}

/* Typography for cover slides */
.cover :deep(h1) {
  color: var(--cx-white) !important;
  font-size: 4.5rem !important;
  font-weight: 500 !important;
  line-height: 1.05 !important;
  margin: 0 0 1rem 0 !important;
  letter-spacing: -0.02em;
}

.cover :deep(h2) {
  color: var(--cx-white) !important;
  font-size: 1.35rem !important;
  font-weight: 400 !important;
  opacity: 0.95;
  margin: 0 0 2rem 0 !important;
  line-height: 1.4;
}

.cover :deep(p) {
  color: var(--cx-white);
  font-size: 1rem;
  opacity: 0.9;
  margin: 0.2rem 0;
  line-height: 1.5;
}

.cover :deep(strong) {
  font-weight: 500;
}
</style>
