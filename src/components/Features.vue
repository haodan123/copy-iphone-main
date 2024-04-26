<template>
  <section class="h-full common-padding bg-zinc relative overflow-hidden">
    <div class="screen-max-width">
      <div class="mb-12 w-full">
        <h1 class="section-heading" id="features_title">Explore the full story</h1>
      </div>
      <div className="flex flex-col justify-center items-center overflow-hidden">
        <div className="mt-32 mb-24 pl-24">
          <h2 className="text-5xl lg:text-7xl font-semibold">iPhone.</h2>
          <h2 className="text-5xl lg:text-7xl font-semibold">Forged in titanium.</h2>
        </div>

        <div class="flex-center flex-col sm:px-10">
          <div class="relative h-[50vh] w-full flex items-center">
            <video
              class="w-full h-full object-cover object-center"
              preload="none"
              muted
              autoplay
              ref="videoRef"
              id="exploreVideo"
              playsinline
            >
              <source :src="exploreVideo" type="video/mp4" />
            </video>
          </div>

          <div class="flex flex-col w-full relative">
            <div class="feature-video-container">
              <div class="overflow-hidden flex-1 h-[50vh]">
                <img :src="explore1Img" alt="titanium" class="feature-video g_grow" />
              </div>
              <div class="overflow-hidden flex-1 h-[50vh]">
                <img :src="explore2Img" alt="titanium" class="feature-video g_grow" />
              </div>
            </div>

            <div class="feature-text-container">
              <div class="flex-1 flex-center">
                <p className="feature-text g_text">
                  iPhone 15 Pro is
                  <span className="text-white"> the first iPhone to feature an aerospace-grade titanium design </span>,
                  using the same alloy that spacecrafts use for missions to Mars.
                </p>
              </div>
              <div className="flex-1 flex-center">
                <p className="feature-text g_text">
                  Titanium has one of the best strength-to-weight ratios of any metal, making these our
                  <span className="text-white"> lightest Pro models ever. </span>
                  You'll notice the difference the moment you pick one up.
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
import gsap from 'gsap'
import { onMounted, onUnmounted, ref } from 'vue'
import { animateWithGsap } from '@/utils/animations'
import { exploreVideo, explore1Img, explore2Img } from '@/utils'

import { ScrollTrigger } from 'gsap/all'
gsap.registerPlugin(ScrollTrigger)
const videoRef = ref('')
let ctx
onMounted(() => {
  ctx = gsap.context(() => {
    gsap.to('#exploreVideo', {
      //视窗到视频的时候播放动画  动画播放完成后 播放视频
      scrollTrigger: {
        trigger: '#exploreVideo',
        toggleActions: 'play pause reverse restart',
        start: '-10% bottom'
      },
      onComplete: () => {
        videoRef.value.play()
      }
    })

    animateWithGsap('#features_title', { opacity: 1, y: 0 })
    animateWithGsap('.g_grow', { scale: 1, opacity: 1, ease: 'power1' }, { scrub: 5.5 })
    animateWithGsap('.g_text', { y: 0, opacity: 1, ease: 'power2.inOut', duration: 1 })
  })
})
onUnmounted(() => {
  ctx.revert()
})
</script>

<style scoped></style>
