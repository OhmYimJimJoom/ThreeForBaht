<template>
  <div class="fixed bottom-4 right-4 z-50 bg-slate-900/90 border border-slate-700 p-3 rounded-2xl shadow-2xl flex items-center gap-3 backdrop-blur-md text-white">
    <div class="w-10 h-10 rounded-xl bg-cyan-500/20 border border-cyan-500/40 flex items-center justify-center text-cyan-400">
      🎵
    </div>
    <div class="text-xs">
      <p class="font-bold text-slate-200">Gang Radio</p>
      <p class="text-slate-400">Lo-Fi / FiveM Chill</p>
    </div>
    <button 
      @click="togglePlay"
      class="ml-2 w-9 h-9 rounded-xl bg-cyan-600 hover:bg-cyan-500 flex items-center justify-center font-bold transition shadow-lg shadow-cyan-900/50">
      {{ isPlaying ? '⏸' : '▶' }}
    </button>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const isPlaying = ref(false)
let audio = null

// ลิงก์เพลงตัวอย่าง (สามารถเปลี่ยนเป็นลิงก์สตรีมเพลงแก๊งของคุณได้)
const audioUrl = 'https://www.bensound.com/bensound-music/bensound-chillout.mp3'

onMounted(() => {
  audio = new Audio(audioUrl)
  audio.loop = true

  // ดึงสถานะเวลาเดิมจาก localStorage เพื่อให้เล่นต่อเนื่องจากจุดเดิม
  const savedTime = localStorage.getItem('gang_music_time')
  const savedState = localStorage.getItem('gang_music_playing') === 'true'

  if (savedTime) {
    audio.currentTime = parseFloat(savedTime)
  }

  if (savedState) {
    audio.play().then(() => {
      isPlaying.value = true
    }).catch(() => {
      isPlaying.value = false
    })
  }

  // บันทึกตำแหน่งเวลาเพลงทุกๆ 1 วินาที
  const interval = setInterval(() => {
    if (audio && !audio.paused) {
      localStorage.setItem('gang_music_time', audio.currentTime)
    }
  }, 1000)

  onUnmounted(() => {
    clearInterval(interval)
  })
})

const togglePlay = () => {
  if (!audio) return
  if (isPlaying.value) {
    audio.pause()
    isPlaying.value = false
    localStorage.setItem('gang_music_playing', 'false')
  } else {
    audio.play()
    isPlaying.value = true
    localStorage.setItem('gang_music_playing', 'true')
  }
}
</script>