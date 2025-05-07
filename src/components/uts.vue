<script setup>
  import {ref ,computed, watch, onMounted} from 'vue'

  const tombol = ref(null)

  const teks = ref('')

  const lists = ref([
    {text : "Olahraga", status : false, waktu: new Date().toLocaleString(), waktuSelesai: ''},
    {text : "Makan" , status : false, waktu: new Date().toLocaleString(), waktuSelesai: ''},
    {text : "Tidur", status : false, waktu: new Date().toLocaleString(), waktuSelesai: ''}
  ])

  const filterStatus = ref('all')

  const filteredLists = computed(() => {
    if (filterStatus.value === 'done') {
      return lists.value.filter(item => item.status === true)
    } else if (filterStatus.value === 'undone') {
      return lists.value.filter(item => item.status === false)
    } else {
      return lists.value
    }
  })

  const countDone = computed(() => lists.value.filter(item => item.status === true).length)
  const countUndone = computed(() => lists.value.filter(item => item.status === false).length)
  const countAll = computed(() => lists.value.length)

  const waktu = ref(new Date().toLocaleTimeString())

  onMounted(() => {
    setInterval(() => {
      waktu.value = new Date().toLocaleTimeString()
    }, 1000)
  })
  
  function tambahkan(){
    let isi = {
      text : teks.value,
      status : false,
      waktu: new Date().toLocaleString(),
      waktuSelesai: ''
    }
    lists.value.push(isi)
    teks.value = ''
  }

  function hapus(index) {
    lists.value.splice(index, 1)
  }

  function onStatusChange(list) {
    if (list.status) {
      list.waktuSelesai = new Date().toLocaleString()
    } else {
      list.waktuSelesai = ''
    }
  }

  const selesai = computed(() => {
    return lists.value.filter((x) => x.status == true)
  })

  const belum = computed(() => {
    return lists.value.filter((x) => x.status == false)
  })

</script>

<template>

<input type="text" v-model="teks">
<button v-on:click="tambahkan" :disabled="teks == ''">submit</button>

  <h2>Daftar Kegiatan</h2>
  <div>
    <button v-on:click="filterStatus = 'all'">All: {{ countAll }}</button>
    <button v-on:click="filterStatus = 'done'">Done: {{ countDone }}</button>
    <button v-on:click="filterStatus = 'undone'">Undone: {{ countUndone }}</button>
  </div>
    <ul>
      <li v-for="(list, index) in filteredLists" :key="index">
        <span :class="{ crossed: list.status }">{{ list.text }}</span>
        <span> (Added: {{ list.waktu }})</span>
        <span v-if="list.waktuSelesai"> (Done at: {{ list.waktuSelesai }})</span>
        <input type="checkbox" v-model="list.status" @change="onStatusChange(list)">
        <button v-on:click="hapus(index)">X</button>
      </li>
    </ul>
    <div>
      <h3>{{ waktu }}</h3>
    </div>
</template>


<style scoped>
.crossed {
  text-decoration: line-through;
}
</style>
