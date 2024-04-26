<template>
  <header class="w-full py-5 sm:px-10 px-5 flex justify-between items-center">
    <nav class="flex w-full screen-max-width">
      <img :src="appleImg" alt="apple" :width="14" :height="18" />

      <div class="flex flex-1 justify-center max-sm:hidden">
        <div
          v-for="(nav, index) in navLists"
          :key="index"
          class="px-5 text-sm cursor-pointer text-gray hover:text-white transition-all"
        >
          {{ nav }}
        </div>
      </div>

      <div class="flex items-baseline gap-7 max-sm:justify-end max-sm:flex-1">
        <img :src="searchImg" class="cursor-pointer" alt="searchimg" :width="18" :height="18" />
        <img :src="bagImg" class="cursor-pointer" alt="bag" :width="18" :height="18" />
      </div>
    </nav>
  </header>
</template>

<script setup>
import { appleImg, searchImg, bagImg } from '@/utils'
import { navLists } from '@/constants'
</script>

<style scoped></style>
