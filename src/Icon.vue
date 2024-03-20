<template>
  <span
    class="material-symbols-outlined"
    :class="[{ outlined }, of]"
  >
    {{ of }}
  </span>
</template>

<script setup lang="ts">
defineProps<{
  of: string;
  outlined?: boolean;
}>()
</script>

<style>
.material-symbols-outlined {
  color: var(--icon-color);
  user-select: none;
  font-variation-settings:
    'FILL' 1,
    'wght' 400,
    'GRAD' 0,
    'opsz' 24;
}

.material-symbols-outlined.outlined {
  font-variation-settings: 'FILL' 0;
}
</style>
