<template>
  <section id="heiglights" class="w-screen h-full overflow-hidden common-padding bg-zinc">
    <div class="screen-max-width">
      <div class="mb-12 w-full items-end justify-between md:flex">
        <h1 id="title" class="section-heading">Get the higlights.</h1>

        <div class="flex flex-wrap items-end gap-5">
          <p class="link">
            Watch the film
            <img :src="watchImg" alt="watchImg" class="ml-2" />
          </p>
          <p class="link">
            Watch the event
            <img :src="rightImg" alt="rightImg" class="ml-2" />
          </p>
        </div>
      </div>
      <VideoCarousel></VideoCarousel>
    </div>
  </section>
</template>

<script setup>
import gsap from 'gsap'
import { onMounted, onUnmounted } from 'vue'
import { watchImg, rightImg } from '@/utils'
import VideoCarousel from '@/components/VideoCarousel.vue'

import { animateWithGsap } from '@/utils/animations'

let ctx
onMounted(() => {
  ctx = gsap.context(() => {
    animateWithGsap('#title', {
      opacity: 1,
      y: 0
    })
    animateWithGsap('.link', {
      opacity: 1,
      y: 0,
      duration: 1,
      stagger: 0.5
    })
  })
})
onUnmounted(() => {
  ctx.revert() // <- revert your animation when it unmounts
})
</script>

<style scoped></style>
