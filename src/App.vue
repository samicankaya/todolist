<script setup>
import { ref, watch, onMounted } from 'vue'

const yeniGorev = ref('')
const gorevler = ref([])

// 1. Sayfa yüklendiğinde tarayıcı belleğinden (localStorage) görevleri çek
onMounted(() => {
  const kayitliGorevler = localStorage.getItem('toDoListGorevler')
  if (kayitliGorevler) {
    gorevler.value = JSON.parse(kayitliGorevler)
  }
})

// 2. 'gorevler' dizisinde bir şey değiştiğinde belleğe kaydet
watch(gorevler, (yeniDeger) => {
  localStorage.setItem('toDoListGorevler', JSON.stringify(yeniDeger))
}, { deep: true })

const gorevEkle = () => {
  const eklenecekMetin = yeniGorev.value.trim()
  
  if (eklenecekMetin === '') return 

  const gorevVar = gorevler.value.some(
    gorev => gorev.metin.toLowerCase() === eklenecekMetin.toLowerCase()
  )

  if (gorevVar) {
    alert('Bu görev zaten listenizde mevcut!') 
    return 
  }
  
  gorevler.value.push({
    id: Date.now(), 
    metin: eklenecekMetin,
    yapildi: false
  })
  
  yeniGorev.value = '' 
}

const gorevSil = (silinecekGorev) => {
  gorevler.value = gorevler.value.filter(gorev => gorev.id !== silinecekGorev.id)
}
</script>

<template>
  <div class="min-h-screen bg-indigo-700 text-slate-100 flex flex-col justify-between relative overflow-hidden py-12 px-4 sm:px-6 lg:px-8">
    <main class="w-full max-w-lg mx-auto z-10 flex-grow flex flex-col justify-center">
      
      <div class="text-center mb-8">
        
        <h1 class="text-3xl font-extrabold tracking-tight bg-gradient-to-r from-violet-200 via-indigo-100 to-slate-200 bg-clip-text text-transparent">
          To-Do List
        </h1>
      </div>

      <form @submit.prevent="gorevEkle" class="flex gap-2 mb-6">
          <input 
            v-model="yeniGorev" 
            type="text"
            placeholder="Yeni bir görev ekle..." 
            class="flex-grow bg-slate-950/80 border border-slate-800 focus:border-fuschia-500/80 focus:ring-2 focus:ring-violet-500/15 text-slate-100 placeholder-slate-500 text-sm rounded-2xl px-4 py-3.5 transition-all duration-200 outline-indigo-500/50 focus:outline-none focus:ring focus:ring-indigo-500/20"
          />
          <button 
            type="submit" 
            class="bg-gradient-to-r from-violet-600 to-indigo-600 hover:from-violet-500 hover:to-indigo-500 active:scale-95 text-white font-extrabold text-sm px-6 py-3.5 rounded-2xl transition-all duration-200 shadow-lg shadow-indigo-500/10 hover:shadow-indigo-500/20"
          >
            Ekle
          </button>
        </form>
      <ul>
        <li
          v-for="gorev in gorevler"
          :key="gorev.id"
          class="flex items-center justify-between bg-slate-900/50 p-2 mb-2 rounded group hover:bg-slate-800/30 transition-colors"
        >
          <span :class="{'line-through text-gray-400': gorev.yapildi, 'text-white': !gorev.yapildi}">
            {{ gorev.metin }}
          </span>
          <div class="flex items-center space-x-2">
            <input type="checkbox" v-model="gorev.yapildi" />
            <button
              @click="gorevSil(gorev)"
              class="text-red-500 opacity-100 group-hover:opacity-100 transition-opacity"
            >
              Sil
            </button>
          </div>
        </li>
      </ul>

    </main>
  </div>
  </template>

<style>

</style>