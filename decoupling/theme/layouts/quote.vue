<script setup lang="ts">
/**
 * Coralogix Quote Layout
 * Light gradient background with quote styling
 */
defineProps<{
  author?: string
  role?: string
  avatar?: string
}>()
</script>

<template>
  <div class="layout">
    <!-- Logo top-left -->
    <div class="layout-logo">
      <img src="/assets/coralogix-logo-dark.png" alt="Coralogix" />
    </div>

    <!-- Quote container -->
    <div class="layout-container">
      <div class="layout-content">
        <div class="quote-mark">"</div>
        <blockquote class="quote-text">
          <slot />
        </blockquote>
        <div class="quote-attribution" v-if="author">
          <img v-if="avatar" :src="avatar" :alt="author" class="quote-avatar" />
          <div class="quote-author-info">
            <div class="quote-author">{{ author }}</div>
            <div class="quote-role" v-if="role">{{ role }}</div>
          </div>
        </div>
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
  justify-content: center;
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

.layout-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100%;
}

.layout-content {
  max-width: 800px;
  position: relative;
}

.quote-mark {
  position: absolute;
  top: -2rem;
  left: -1rem;
  font-size: 6rem;
  font-weight: 500;
  color: #059669;
  opacity: 0.2;
  line-height: 1;
  font-family: Georgia, serif;
}

.quote-text {
  font-size: 1.75rem;
  font-weight: 400;
  line-height: 1.5;
  color: #111827;
  margin: 0;
  padding-left: 2rem;
}

.quote-text :deep(p) {
  margin: 0;
}

.quote-attribution {
  display: flex;
  align-items: center;
  gap: 1rem;
  margin-top: 2rem;
  padding-left: 2rem;
}

.quote-avatar {
  width: 56px;
  height: 56px;
  border-radius: 50%;
  object-fit: cover;
}

.quote-author {
  font-weight: 500;
  color: #111827;
  font-size: 1.1rem;
}

.quote-role {
  color: #6b7280;
  font-size: 0.95rem;
}
</style>
