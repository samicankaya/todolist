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

// 2. 'gorevler' dizisinde bir şey değiştiğinde (ekleme, silme, yapıldı işareti) belleğe kaydet
// deep: true parametresi, obje içindeki 'yapildi' durumunun değişimini de izlememizi sağlar.
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
  // Silme işlemini objenin tamamı yerine benzersiz ID üzerinden yapmak daha sağlıklıdır
  gorevler.value = gorevler.value.filter(gorev => gorev.id !== silinecekGorev.id)
}
</script>

<template>
  <div class="kutu">
    <h1 style="padding-left: 100px;">To-Do List</h1>
    
    <form @submit.prevent="gorevEkle" class="ekleme-alani">
      <input v-model="yeniGorev" placeholder="Yeni bir görev yaz..." />
      <button type="submit">Ekle</button>
    </form>

    <div v-if="gorevler.length === 0">
      <p>Henüz görev eklenmedi.</p>
    </div>
    
    <ul>
      <li v-for="gorev in gorevler" :key="gorev.id">
        <label>
          <input type="checkbox" v-model="gorev.yapildi" />
          <span :class="{ ustuCizili: gorev.yapildi }">
            {{ gorev.metin }}
          </span>
        </label>
        <button @click="gorevSil(gorev)" class="sil-butonu">Sil</button>
      </li>
    </ul>
  </div>
</template>

<style>
.kutu {
  max-width: 400px;
  margin: 50px auto;
  font-family: sans-serif;  
}
.ekleme-alani {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}
.ekleme-alani input {
  flex: 1;
  padding: 10px;
}
ul {
  padding: 0;
}
li {
  list-style: none;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  background: #f4f4f4;
  margin-bottom: 8px;
  border-radius: 4px;
}
.ustuCizili {
  text-decoration: line-through;
  color: gray;
}
.sil-butonu {
  background: #ff4757;
  color: white;
  border: none;
  padding: 5px 10px;
  border-radius: 3px;
  cursor: pointer;
}
</style>