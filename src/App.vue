<script setup>
import { ref, watch, onMounted } from 'vue'

const yeniGorev = ref('')
const gorevler = ref([])

onMounted(() => {
  const kayitliGorevler = localStorage.getItem('toDoListGorevler')
  if (kayitliGorevler) {
    gorevler.value = JSON.parse(kayitliGorevler)
  }
})

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
  <div class="min-h-screen bg-gradient-to-r from-violet-600 to-indigo-600 text-slate-100 flex flex-col justify-between relative overflow-hidden py-12 px-4 sm:px-6 lg:px-8">
    <main class="w-full max-w-lg mx-auto z-10 flex-grow flex flex-col justify-center">
      
      <form @submit.prevent="gorevEkle" class="grid grid-cols-[1fr_auto] gap-x-2 items-start">
        
        <div class="col-start-1 col-end-2 text-center mb-8">
          <h1 class="text-3xl font-extrabold tracking-tight bg-gradient-to-r from-violet-200 via-indigo-100 to-slate-200 bg-clip-text text-transparent">
            To-Do List
          </h1>
        </div>

        <input 
          v-model="yeniGorev" 
          type="text"
          placeholder="Yeni bir görev ekle..." 
          class="col-start-1 col-end-2 bg-slate-950/70 border border-slate-800 focus:border-violet-500/80 focus:ring-2 focus:ring-violet-500/15 text-slate-100 placeholder-slate-500 text-sm rounded-2xl px-4 py-3.5 transition-all duration-200 outline-none mb-6"
        />
        
        <button 
          type="submit" 
          class="col-start-2 col-end-3 bg-violet-600 hover:bg-violet-700 active:scale-95 text-white font-extrabold text-sm px-6 py-3.5 rounded-2xl transition-all duration-200 shadow-lg shadow-violet-600 hover:shadow-violet-700/20"
        >
          Ekle
        </button>

        <div class="col-start-1 col-end-2 bg-slate-950/60 border border-slate-800/50 rounded-3xl p-5 shadow-2xl backdrop-blur-md">
          
          <div v-if="gorevler.length === 0" class="text-center py-6 text-slate-400 text-sm">
            Henüz bir görev eklemediniz.
          </div>
          
          <ul v-else class="space-y-2.5">
            <li
              v-for="gorev in gorevler"
              :key="gorev.id"
              class="flex items-center justify-between bg-gradient-to-r from-indigo-800 to-violet-800 p-3 rounded-xl hover:brightness-110 transition-all duration-200"
            >
              <span :class="{'line-through text-slate-400/80': gorev.yapildi, 'text-white': !gorev.yapildi}">
                {{ gorev.metin }}
              </span>
              <div class="flex items-center space-x-3">
                <input 
                  type="checkbox" 
                  v-model="gorev.yapildi" 
                  class="w-4 h-5 accent-blue-700 cursor-pointer transition-all duration-200 rounded-sm hover:scale-110" 
                />
                <button
                  type="button"
                  @click="gorevSil(gorev)"
                  class="text-red-400 hover:text-red-500 font-medium transition-colors text-sm"
                >
                  Sil
                </button>
              </div>
            </li>
          </ul>
        </div>

      </form>

    </main>
  </div>
</template>

<style>
/* Tasarım tamamen Tailwind ile yönetilmektedir. */
</style>