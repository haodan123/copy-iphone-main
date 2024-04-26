<template>
  <section class="common-padding">
    <div class="screen-max-width">
      <div id="chip" class="flex-center w-full my-20">
        <img :src="chipImg" alt="chipImg" width="180" height="180" />
      </div>

      <div class="felx flex-col items-center">
        <h2 className="hiw-title">
          A17 Pro chip.
          <br />
          A monster win for gaming.
        </h2>

        <p className="hiw-subtitle">It's here. The biggest redesign in the history of Apple GPUs.</p>
      </div>

      <div class="mt-10 md:mt-20 mb-14">
        <div class="relative h-full flex-center">
          <div class="overflow-hidden">
            <img :src="frameImg" alt="frame" class="bg-transparent relative z-10" />
          </div>
          <div class="hiw-video">
            <video className="pointer-events-none" playsInline preload="none" muted autoPlay ref="videoRef">
              <source :src="frameVideo" type="video/mp4" />
            </video>
          </div>
        </div>
        <p className="text-gray font-semibold text-center mt-3">Honkai: Star Rail</p>
      </div>

      <div className="hiw-text-container">
        <div className="flex flex-1 justify-center flex-col">
          <p className="hiw-text g_fadeIn">
            A17 Pro is an entirely new class of iPhone chip that delivers our
            <span className="text-white">best graphic performance by far</span>.
          </p>

          <p className="hiw-text g_fadeIn">
            Mobile <span className="text-white">games will look and feel so immersive</span>, with incredibly detailed
            environments and characters.
          </p>
        </div>

        <div className="flex-1 flex justify-center flex-col g_fadeIn">
          <p className="hiw-text">New</p>
          <p className="hiw-bigtext">Pro-class GPU</p>
          <p className="hiw-text">with 6 cores</p>
        </div>
      </div>
    </div>
  </section>
</template>

<script setup>
import { chipImg, frameImg, frameVideo } from '@/utils'
import { animateWithGsap } from '@/utils/animations'
import gsap from 'gsap'
import { ScrollTrigger } from 'gsap/all'
import { onMounted, onUnmounted, ref } from 'vue'

const videoRef = ref()

gsap.registerPlugin(ScrollTrigger)
let ctx
onMounted(() => {
  ctx = gsap.context(() => {
    // 给芯片的图片加一个 入场动画
    gsap.from('#chip', {
      scrollTrigger: {
        trigger: '#chip',
        start: '20% bottom',
        //视窗进来的时候动画  出去的时候也有动画  如果只想要一次性动画可以注释下面这行
        toggleActions: 'restart reverse restart reverse'
      },
      opacity: 0,
      scale: 2,
      duration: 2,
      ease: 'power1.inOut'
    })

    // 给视频加一个动画  每次进入视窗都重新播放视频
    gsap.to(videoRef.value, {
      scrollTrigger: {
        trigger: videoRef.value,
        toggleActions: 'restart reverse restart reverse' //视窗进来的时候动画  出去的时候也有动画
      },
      onComplete: () => {
        videoRef.value.play()
      }
    })
    // 给文字加上动画
    animateWithGsap('.g_fadeIn', {
      opacity: 1,
      y: 0,
      duration: 1,
      ease: 'power2.inOut'
    })
  })
})
onUnmounted(() => {
  ctx.revert()
})
</script>

<style scoped></style>
