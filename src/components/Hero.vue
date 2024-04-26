<template>
  <section class="w-full nav-height bg-black relative">
    <div class="h-5/6 w-full flex-center flex-col">
      <p id="hero" class="hero-title">iPhone 15 Pro</p>
      <div class="w-9/12 md:w-10/12">
        <video class="pointer-events-none" autoplay muted :playsinline="true" :key="videoSrc">
          <source :src="videoSrc" type="video/mp4" />
        </video>
      </div>
    </div>
    <div id="cta" class="flex flex-col items-center translate-y-20 opacity-0">
      <a href="#highlights" class="btn">Buy</a>
      <p class="font-normal text-xl">Form $199/ month or $999</p>
    </div>
  </section>
</template>

<script setup>
import gsap from 'gsap'
import { onMounted, onUnmounted, ref } from 'vue'
import { heroVideo, smallHeroVideo } from '@/utils'
import { animateWithGsap } from '@/utils/animations'

// 如果是宽度小于760的屏幕播放竖向的小视频 大于播放横向的大视频
const videoSrc = ref(window.innerWidth < 760 ? smallHeroVideo : heroVideo)
// 如果是宽度小于760的屏幕播放竖向的小视频 大于播放横向的大视频
const handeVideoSrcSet = () => {
  if (window.innerWidth < 760) {
    videoSrc.value = smallHeroVideo
  } else {
    videoSrc.value = heroVideo
  }
}

let ctx
onMounted(() => {
  // 监听浏览器的是否缩放
  window.addEventListener('resize', handeVideoSrcSet)

  ctx = gsap.context(() => {
    animateWithGsap('#hero', { opacity: 1, delay: 2 })

    // gsap.to('#hero', { opacity: 1, delay: 2 })
    gsap.to('#cta', {
      opacity: 1,
      y: 0,
      delay: 1
    })
  })
})
onUnmounted(() => {
  window.removeEventListener('resize', handeVideoSrcSet)
  ctx.revert() // <- revert your animation when it unmounts
})
</script>

<style scoped></style>
