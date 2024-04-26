<template>
  <div>
    <div class="flex items-center" v-if="hightlightsSlides.length > 0">
      <div id="slider" class="sm:pr-20 pr-10" v-for="(list, index) in hightlightsSlides" :key="index">
        <div class="video-carousel_container">
          <div class="w-full h-full flex-center rounded-3xl overflow-hidden bg-black">
            <video
              muted
              id="video"
              ref="videoRef"
              @play="onPlayVideo"
              :class="`${list.id == 2 && 'translate-x-44 '} pointer-events-none`"
              :playsInline="true"
              preload="auto"
              :key="list.video"
              @ended="videoEnd(index)"
              @loadedMetadata="handleLoadedMetaData(list.video)"
            >
              <!-- :onLoadedMetadata="e => handleLoadedMetaData(index, e)" -->
              <source :src="list.video" type="video/mp4" />
            </video>
          </div>
          <div class="absolute top-12 left-[5%] z-10">
            <p class="md:text-2xl text-xl font-medium" v-for="(text, index) in list.textLists" :key="index">
              {{ text }}
            </p>
          </div>
        </div>
      </div>
    </div>
    <!-- 进度条 -->
    <div class="flex-center relative mt-10">
      <div class="flex-center py-5 px-7 bg-gray-300 backdrop-blur rounded-full">
        <!-- 每个进度条的小点 -->
        <span
          class="mx-2 w-3 h-3 relative bg-gray-200 rounded-full cursor-pointer"
          v-for="(_, index) in videoRef"
          ref="videoDivRef"
          :key="index"
        >
          <!-- 每个小点内的进度条 -->
          <span class="w-full h-full absolute rounded-full" ref="videoSpanRef"></span>
        </span>
      </div>
      <button class="control-btn">
        <!-- isLastVideo为true 代表最后一个视频 放重播图片 没播放视频显示开始的图片 已播放显示暂停的图片 -->
        <img
          :src="video.isLastVideo ? replayImg : !video.isPlaying ? playImg : pauseImg"
          :alt="video.isLastVideo ? 'replay' : !video.isPlaying ? 'play' : 'pause'"
          @click="
            video.isLastVideo
              ? handleProcess('video-reset')
              : !video.isPlaying
                ? handleProcess('play')
                : handleProcess('pause')
          "
        />
      </button>
    </div>
  </div>
</template>

<script setup>
import { hightlightsSlides } from '@/constants'
import { replayImg, playImg, pauseImg } from '@/utils'
import gsap from 'gsap'
import { nextTick, onMounted, ref, watch } from 'vue'
import { ScrollTrigger } from 'gsap/all'
gsap.registerPlugin(ScrollTrigger)
const videoRef = ref([])
const videoSpanRef = ref([])
const videoDivRef = ref([])

const video = ref({
  isEnd: false, //视频是否播放完
  startPlay: false, //视频是否开始播放
  videoId: 0,
  isLastVideo: false, //这个视频是否是最后一个视频
  isPlaying: false //视频是否正在播放
})

const loadedData = ref([]) //加载是数据

// 监听视频结束 id 变化来处理动画
onMounted(() => {
  // nextTick(() => {
  //   console.log('播放视频')
  //   gsap.to('#video', {
  //     scrollTrigger: {
  //       trigger: '#video',
  //       toggleActions: 'restart none none none'
  //     },
  //     // 动画完成后
  //     onComplete: () => {
  //       // console.log('动画播放完成')
  //       //当id位video的div出现在视窗内时  把动画开始 和 已经在播放中的状态设置为true
  //       video.value = {
  //         ...video.value,
  //         isPlaying: true,
  //         startPlay: true
  //       }
  //     }
  //   })
  // })
})
watch(
  [() => video.value.isEnd, () => video.value.videoId],
  () => {
    nextTick(() => {
      // 每次videoId变化都轮播 视频
      gsap.to('#slider', {
        transform: `translateX(${-100 * video.value.videoId}%)`,
        duration: 2,
        ease: 'power2.inOut' // show visualizer https://gsap.com/docs/v3/Eases
      })

      gsap.to('#video', {
        scrollTrigger: {
          trigger: '#video',
          toggleActions: 'restart none none none'
        },
        // 动画完成后
        onComplete: () => {
          // console.log('动画播放完成')
          //当id位video的div出现在视窗内时  把动画开始 和 已经在播放中的状态设置为true
          video.value = {
            ...video.value,
            isPlaying: true,
            startPlay: true
          }
        }
      })
    })
  },
  { immediate: true }
)
// 监听视频是否开始 id 是否正在播放 变化来处理动画
watch(
  [() => video.value.startPlay, () => video.value.videoId, () => video.value.isPlaying, () => loadedData.value],
  () => {
    nextTick(() => {
      // loadedData.value有未知bug暂时不处理
      // console.log('变更状态', loadedData.value)
      // if (loadedData.value.length > 3) {
      // 如果加载数据大于三条
      if (!video.value.isPlaying) {
        // 视频没在播放
        videoRef.value[video.value.videoId].pause()
      } else {
        // 视频已经开始了就 播放
        video.value.startPlay && videoRef.value[video.value.videoId].play()
      }
      // 如果是最后一个视频 直接暂停不播放了
      if (video.value.isLastVideo) {
        videoRef.value[video.value.videoId].pause()
      }
      // }
    })
  },
  { immediate: true }
)

// 监听视频id 和视频是否开始的变化来处理动画
watch(
  [() => video.value.videoId, () => video.value.startPlay],
  () => {
    nextTick(() => {
      let currentProgress = 0
      let span = videoSpanRef.value

      if (span[video.value.videoId]) {
        let anim = gsap.to(span[video.value.videoId], {
          //视频更新后 更新后触发的事件
          onUpdate: () => {
            // 获取视频的进度条
            const progress = Math.ceil(anim.progress() * 100)
            // console.log(progress)

            if (progress != currentProgress) {
              // 如果进度条不等于0的时候触发
              currentProgress = progress

              // 改变对应视频的进度条长度
              gsap.to(videoDivRef.value[video.value.videoId], {
                width:
                  window.innerWidth < 760
                    ? '10vw' // mobile
                    : window.innerWidth < 1200
                      ? '10vw' // tablet
                      : '4vw' // laptop
              })
              // 改变对应视频进度条的北京颜色
              gsap.to(span[video.value.videoId], {
                width: `${currentProgress}%`,
                background: '#fff'
              })
            }
          },
          // 动画发生后
          onComplete: () => {
            // 播放完成后 把播放完的进度条设置为原来的样式

            if (video.value.isPlaying) {
              videoDivRef.value.forEach(item => {
                gsap.to(item, {
                  width: '12px'
                })
                gsap.to(item, {
                  backgroundColor: '#afafaf'
                })
              })
              // gsap.to(videoDivRef.value[video.value.videoId], {
              //   width: '12px'
              // })
              // gsap.to(span[video.value.videoId], {
              //   backgroundColor: '#afafaf'
              // })
            }
          }
        })

        if (video.value.videoId == 0) {
          anim.restart()
        }

        // update the progress bar
        const animUpdate = () => {
          anim.progress(
            videoRef.value[video.value.videoId].currentTime / hightlightsSlides[video.value.videoId].videoDuration
          )
        }

        if (video.value.isPlaying) {
          // ticker to update the progress bar
          gsap.ticker.add(animUpdate)
        } else {
          // remove the ticker when the video is paused (progress bar is stopped)
          gsap.ticker.remove(animUpdate)
        }
      }
    })
  }
  // { immediate: true }
)

// 视频开始播放
const onPlayVideo = () => {
  // console.log('视频开始播放', e)
  video.value = {
    ...video.value,
    isPlaying: true
  }
  if (video.value.isLastVideo && video.value.videoId == 3) {
    videoRef.value[video.value.videoId].pause()
  }
  // console.log(video.value)
}

// 点击进度条旁边的按钮
const handleProcess = (type, i) => {
  // console.log(type, i)
  switch (type) {
    case 'video-end':
      // 视频播放结束
      // 把视频播放结束的状态设置为true  把视频的id加1 切换到下一个视频
      video.value = { ...video.value, isEnd: true, videoId: i + 1 }

      break
    case 'video-last':
      // 播放最后一个视频
      console.log('播放最后一个视频')
      video.value = { ...video.value, isLastVideo: true }
      break

    case 'video-reset':
      // 重新播放视频
      video.value = { ...video.value, isLastVideo: false, videoId: 0 }
      break
    case 'play':
      // 播放视频
      video.value = { ...video.value, isPlaying: !video.value.isPlaying }
      break
    case 'pause':
      // 暂停视频
      video.value = { ...video.value, isPlaying: !video.value.isPlaying }
      break

    default:
      return video.value
  }
  // console.log(type, video.value)
}

// 存储loadeddata的数据  改方法有未知报错
const handleLoadedMetaData = () => {
  // console.log('视屏有数据后', e)
  // loadedData.value = [...loadedData.value, e]
  // console.log(loadedData.value)
}
// 视频播放结束
const videoEnd = i => {
  // console.log('视频播放完成', i)
  i !== 3 ? handleProcess('video-end', i) : handleProcess('video-last')
}

onMounted(() => {
  // console.log(videoRef.value[video.value.videoId])
  // videoRef.value[video.value.videoId].play()
})
</script>

<style scoped></style>
