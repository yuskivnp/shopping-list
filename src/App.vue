<template>
  <div
    class="app"
    :class="themeValue"
  >
    <header>
      <h1 class="header">
        <span class="header__title">
          Hello
          <span @click="renameUser"> {{ user }}! </span>

        </span>

        <Toggle v-model="theme" />
      </h1>
      <div class="task-create">
        <input
          class="task-create__input"
          v-model="productName"
          @keypress.enter="addProduct"
        />
        <button
          class="task-create__button pointer"
          :disabled="!trimmedName"
          @click="addProduct"
        >
          Save
        </button>
      </div>

    </header>
    <main class="main">
      <TransitionGroup
        v-if="productList"
        name="list"
        class="container"
      >
        <ProductItem
          class="product"
          v-for="product of sortedProducts"
          :key="product.id"
          :product="product"
          @toggle="product.isFavorite = !product.isFavorite"
          @done="product.isDone = !product.isDone"
          @remove="removeProduct(product.id)"
        />
      </TransitionGroup>
    </main>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onBeforeMount, watch } from 'vue'
import type { IProduct } from '@/models'
import ProductItem from '@/ProductItem.vue'
import Toggle from '@/Toggle.vue'

const USER_KEY = 'user'
const THEME_KEY = 'theme'
const TASKS_KEY = 'tasks'

let id = 0

const theme = ref(true)
const productName = ref('')
const productList = ref<IProduct[]>([])
const user = ref('')

const trimmedName = computed(() => productName.value.trim())
const themeValue = computed(() => theme.value ? 'dark' : 'light')

function getWeight(product: IProduct) {
  return Number(!product.isFavorite) + Number(product.isDone) * 2
}

const sortedProducts = computed(() => [...productList.value].sort((a, b) => {
  const weight = getWeight(a) - getWeight(b);
  if (weight === 0) {
    return a.description.localeCompare(b.description)
  }
  return weight;
}))

function addProduct() {
  const description = trimmedName.value
  if (description.length === 0) {
    return
  }
  productList.value.push({
    description,
    isDone: false,
    isFavorite: true,
    id: id++
  })
  productName.value = ''
}

function removeProduct(id: number) {
  productList.value = productList.value.filter((product) => product.id !== id);
}

function renameUser() {
  user.value = prompt('Enter your name', user.value) || user.value || 'Anonymous'
  localStorage.setItem(USER_KEY, user.value)
}

onBeforeMount(() => {
  user.value = localStorage.getItem(USER_KEY) ?? prompt('Enter your name') ?? 'Anonymous'
  localStorage.setItem(USER_KEY, user.value)

  theme.value = (localStorage.getItem(THEME_KEY) ?? 'dark') === 'dark';

  productList.value = JSON.parse(localStorage.getItem(TASKS_KEY) ?? '[]')
  id =
    1 +
    productList.value.reduce((result, product) => {
      if (product.id && product.id > result) {
        return product.id
      }
      return result
    }, 1)
})

watch(themeValue, () => {
  localStorage.setItem(THEME_KEY, themeValue.value)
})

watch(
  productList,
  () => {
    localStorage.setItem(TASKS_KEY, JSON.stringify(productList.value))
  },
  { deep: true }
)
</script>

<style scoped lang="scss">
header {
  text-align: center;
}

.header {
  display: flex;
  align-items: center;

  &__title {
    flex-grow: 1;
  }
}

.task-create {
  &__input {
    width: 80%;
    padding: 10px;
    margin-right: 20px;
    border-radius: 15px;
  }

  &__button {
    width: 15%;
    max-width: 50px;
    height: 40px;
    border-radius: 30%;
  }

}

.app {
  height: 100vh;
  padding: 50px;
  overflow: hidden;
  display: flex;
  flex-direction: column;

  .main {
    position: relative;
    flex-grow: 1;
    overflow-y: auto;
    overflow-x: hidden;
  }

  &.dark {
    background-color: #3C0753;
    color: #ffd7f6;

    .product {
      border: 1px solid #030637;
      border-radius: 15px;
      margin: 20px;
      padding: 15px 15px 15px 30px;
      box-shadow: 1px 1px 9px #ffd7f6;
      background: radial-gradient(#720455, #420231);
    }
  }

  &.light {
    background-color: #F875AA;
    color: #1f0016;

    .product {
      border: 1px solid #1f0016;
      border-radius: 15px;
      margin: 20px;
      padding: 15px 15px 15px 30px;
      box-shadow: 1px 1px 9px #1f0016;
      background: radial-gradient(#FFDFDF, #AEDEFC);
    }


  }
}

.container {
  position: relative;
  width: 100%;
  overflow-x: visible;
}

.list-move,
.list-enter-active,
.list-leave-active {
  transition: all 0.5s ease;
  width: calc(100% - 40px);
}

.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(30px);
}

.list-leave-active {
  position: absolute;
}
</style>
