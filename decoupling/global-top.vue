<script setup lang="ts">
import { useNav } from '@slidev/client';
import { computed } from 'vue';

const { currentSlideNo, total } = useNav();
const progress = computed(
  () =>
    ((currentSlideNo.value - 1) / (total.value - 1)) * 100
);
</script>

<template>
  <div class="slide-progress-bar">
    <div
      class="slide-progress-fill"
      :style="{ width: `${progress}%` }"
    />
  </div>
</template>

<style>
.slide-progress-bar {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 3px;
  background: rgba(0, 0, 0, 0.1);
  z-index: 100;
}

.slide-progress-fill {
  height: 100%;
  background: var(--slidev-theme-primary);
  transition: width 0.3s ease;
}
</style>
