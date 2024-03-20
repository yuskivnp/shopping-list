<template>
  <div
    class="product-item"
    @click="$emit('done')"
  >
    <span
      class="product-item__description"
      :class="{ done: product.isDone }"
    >
      {{ product.description }}
    </span>


    <Icon
      of="star"
      class="pointer"
      :outlined="!product.isFavorite"
      @click.stop="$emit('toggle')"
    />
    <Icon
      of="delete"
      class="pointer"
      @click.stop="$emit('remove')"
    />
  </div>
</template>

<script setup lang="ts">
import type { IProduct } from '@/models'
import Icon from '@/Icon.vue'

defineProps<{
  product: IProduct
}>()

defineEmits<{
  (event: 'remove'): void
  (event: 'toggle'): void
  (event: 'done'): void
}>()
</script>

<style scoped lang="scss">
.product-item {
  user-select: none;
  display: flex;
  align-items: center;

  &__description {
    flex-grow: 1;
  }
}

.done {
  text-decoration: line-through;
}
</style>
