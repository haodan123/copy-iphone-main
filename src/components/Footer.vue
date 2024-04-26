<template>
  <footer class="py-5 sm:px-10 px-5">
    <div class="screen-max-width">
      <div>
        <p className="font-semibold text-gray text-xs">
          More ways to shop:
          <span className="underline text-blue"> Find an Apple Store </span>
          or
          <span className="underline text-blue"> other retailer </span> near you.
        </p>
        <p className="font-semibold text-gray text-xs">Or call 000800-040-1966</p>
      </div>

      <!-- 横线 -->
      <div class="bg-neutral-700 my-5 h-[1px] w-full"></div>

      <div class="flex md:flex-row flex-col md:items-center justify-between">
        <p class="font-semibold text-gray text-xs">Copright @ 2024 Apple Inc. All rights reserved.</p>
        <div class="flex">
          <p class="text-gray text-xs font-semibold" v-for="(link, index) in footerLinks" :key="index">
            {{ link }}{{ ' ' }}
            <span class="mx-2" v-if="footerLinks.length - 1 !== index">|</span>
          </p>
        </div>
      </div>
    </div>
  </footer>
</template>

<script setup>
import { footerLinks } from '@/constants'
</script>

<style scoped></style>
