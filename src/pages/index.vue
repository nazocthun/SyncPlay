<script setup lang="ts">
const tempVideoUrl = ref('')
const videoUrl = ref('')
const video = ref<HTMLVideoElement>()
function changeVideoUrl() {
  videoUrl.value = tempVideoUrl.value
}
const formatted = useDateFormat(useNow(), 'YYYY-MM-DD HH:mm:ss')

const today = formatted.value.split(' ')[0]
const nowTime = useDateFormat(useNow(), 'HH:mm').value

const setTime = ref(nowTime)

const targetTime = ref<Date>(new Date('2099-12-31 23:59'))
function setTargetTime() {
  targetTime.value = new Date(`${today} ${setTime.value}`)
  resume()
}

const now = useNow()
const { pause, resume } = watchPausable(
  now,
  v => {
    if (v >= targetTime.value) {
      video.value!.play()
      pause()
    }
  },
  {
    immediate: true,
  },
)
</script>

<template>
<div flex flex-col justify-center items-center>
  <div text-3xl>SyncPlayer</div>
  <Footer mb-2 />
  <div w-200>
    <div flex m-2>
      <input text-gray-500 flex-1 rounded border="~ gray-500" v-model="tempVideoUrl" placeholder=" Video Url">
      <button w-24 ml-2 btn @click="changeVideoUrl">Submit</button>
    </div>
    <div flex m-2>
      <div inline-block flex-1 text-center my-auto>
        现在时间：{{ formatted }}
      </div>
      <input text-gray-500 rounded border="~ gray-500" id="time" type="time" v-model="setTime">
      <button w-24 btn ml-2 float-right @click="setTargetTime">
        Set
      </button>
    </div>
    <video ref="video" w-200 aspect-video mt-5 controls :src="videoUrl"/>
  </div>
</div>
</template>