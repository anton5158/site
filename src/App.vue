<script setup>
import { onMounted, ref, reactive, provide, watch } from 'vue'
import axios from 'axios'
import Header from './components/Header.vue'
import Cardlist from './components/cardlist.vue'
import Drawer from './components/Drawer.vue'

const items = ref([])
const cart = ref([])
const drawerOpen = ref(false)

const closeDrawer = () => {
  drawerOpen.value = false
}

const openDrawer = () => {
  drawerOpen.value = true
}

const filters = reactive({
  sortBy: 'title',
  searchQuery: ''
})

const addToCart = (item) => {
  if (!item.isAdded) {
    cart.value.push(item)
    item.isAdded = true
  } else {
    cart.value.splice(cart.value.indexOf(item), 1)
    item.isAdded = false
  }

  console.log(cart)
}

const onChangeSelect = (event) => {
  filters.sortBy = event.target.value
}

const onChangeSearchInput = (event) => {
  filters.searchQuery = event.target.value
}

const fetchFavorites = async () => {
  try {
    const { data: favorites } = await axios.get('https://02214981a13a45f6.mokky.dev/favorites')

    items.value = items.value.map((item) => {
      const favorite = favorites.find((favorite) => favorite.parentId === item.id)
      if (!favorite) {
        return item
      }
      return {
        ...item,
        favoriteId: favorite.id,
        isFavorite: true
      }
    })
  } catch (err) {
    console.log(err)
  }
}

const addToFavorite = async (item) => {
  try {
    if (!item.isFavorite) {
      const obj = {
        parentId: item.id
      }
      const { data } = await axios.post('https://02214981a13a45f6.mokky.dev/favorites', obj)
      item.isFavorite = true
      item.favoriteId = data.id
    } else {
      await axios.delete(`https://02214981a13a45f6.mokky.dev/favorites/${item.favoriteId}`)
      item.isFavorite = false
      item.favoriteId = null
    }
  } catch (err) {
    console.log(err)
  }
}

const fetchItems = async () => {
  try {
    const params = {
      sortBy: filters.sortBy
    }

    if (filters.searchQuery) {
      params.title = `*${filters.searchQuery}*`
    }
    const { data } = await axios.get('https://02214981a13a45f6.mokky.dev/test', {
      params
    })
    items.value = data.map((obg) => ({
      ...obg,
      isFavorite: false,
      favoriteId: null,
      isAdded: false
    }))
  } catch (err) {
    console.log(err)
  }
}

onMounted(async () => {
  await fetchItems()
  await fetchFavorites()
})

watch(filters, fetchItems)

provide('cart', {
  cart,
  closeDrawer,
  openDrawer
})

provide('addToFavorite', addToFavorite)
</script>

<template>
  <Drawer v-if="drawerOpen" />
  <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14">
    <Header @openDrawer="openDrawer" />
    <div class="p-10">
      <div class="flex justify-between items-center">
        <h2 class="text-3xl font-bold mb-8">Каталог</h2>
        <div class="flex gap-4">
          <select @change="onChangeSelect" class="py-2 px-3 border rounded-md outline-none">
            <option value="name">По названию</option>
            <option value="price">По цене(по возрастанию)</option>
            <option value="-price">По цене(по убыванию)</option>
          </select>
          <div class="relative">
            <img class="absolute left-4 top-3" src="/search.svg" />
            <input
              @input="onChangeSearchInput"
              class="border rounded-md py-2 pl-11 pr-4 outline-none focus:border-gray-400"
              type="text"
              placeholder="Поиск..."
            />
          </div>
        </div>
      </div>

      <div class="mt-10">
        <cardlist :items="items" @addToFavorite="addToFavorite" @addToCart="addToCart" />
      </div>
    </div>
  </div>
</template>
