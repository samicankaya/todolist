<script setup>
import { ref, watch, onMounted, computed } from 'vue'

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
  <div class="min-h-screen bg-blue-700 flex items-center justify-center p-4">
    <div class="bg-blue-500 shadow-md rounded-lg w-full max-w-md p-6">
      <h1 class="text-2xl font-bold mb-4 text-center text-white">To-Do List</h1>
      <div class="flex mb-4">
        <input
          v-model="yeniGorev"
          @keyup.enter="gorevEkle"
          type="text"
          placeholder="Yeni görev ekle..."
          class="flex-grow border rounded-l px-3 py-2 text-gray-900 focus:outline-none focus:ring focus:border-gray-300"
        />
        <button
          @click="gorevEkle"
          class="bg-cyan-500 text-white px-4 py-2 rounded-r hover:bg-cyan-600/50 transition-colors"
        >
          Ekle
        </button>
      </div>
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
              class="text-red-500 opacity-100"
            >
              Sil
            </button>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>
  

<style>
/* Custom transitions or tweaks can go here, but most is handled by Tailwind! */
.group:hover .opacity-0 {
  opacity: 1;
}
</style>