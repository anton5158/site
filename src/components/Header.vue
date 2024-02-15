<script setup>
const emit = defineEmits(['openDrawer'])
</script>

<template>
  <div>
    <header class="flex justify-between border-b border-slate-200 border-grey px-10 py-8">
      <div class="flex items-center gap-4">
        <img src="/logo.png" alt="Logo" class="w-10" />
        <div>
          <h2 class="text-xl font-bold uppercase">Мой магазин</h2>
          <p class="text-slate-400">Магазин картин</p>
        </div>
      </div>

      <ul class="flex items-center gap-10">
        <li
          @click="() => emit('openDrawer')"
          class="flex items-center cursor-pointer gap-3 text-gray-500 hover:text-black"
        >
          <img src="/cart.svg" alt="Cart" />
          <b>48000 руб.</b>
        </li>
        <li class="flex items-center cursor-pointer gap-3 text-gray-500 hover:text-black">
          <img src="/heart.svg" alt="Heart" />
          <span>Закладки</span>
        </li>
        <li class="flex items-center cursor-pointer gap-3 text-gray-500 hover:text-black">
          <img src="/profile.svg" alt="Profile" />
          <span>Профиль</span>
        </li>
      </ul>
    </header>
  </div>
</template>
