<template>
  <div
    class="toggler"
    :class="{ checked: data }"
    @click="data = !data"
  >
    <div class="toggler__button" />
  </div>
</template>

<script setup lang="ts">
const data = defineModel()
</script>

<style scoped lang="scss">
.toggler {
  --toggler-padding: 2px;
  --toggler-height: 24px;
  background: var(--toggle-background);
  width: 48px;
  height: var(--toggler-height);

  border-radius: 12px;
  padding: var(--toggler-padding);
  border: 1px solid white;

  &__button {
    width: calc(var(--toggler-height) - 2 * var(--toggler-padding));
    height: calc(var(--toggler-height) - 2 * var(--toggler-padding));
    background: var(--toggle-button-background);
    border-radius: 50%;
  }
}

.checked {
  .toggler__button {
    float: right;
  }
}
</style>