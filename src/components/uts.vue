<script setup>
  import {ref ,computed, watch, onMounted} from 'vue'

  const tombol = ref(null)

  const teks = ref('')

  const lists = ref([
    {text : "Olahraga", status : true},
    {text : "Makan" , status : true},
    {text : "Tidur", status : false}
  ])

  function tambahkan(){
    let isi = {
      text : teks.value,
      status : false
    }

    lists.value.push(isi)
    teks.value = ''
  }

  function hapus(index) {
    lists.value.splice(index, 1)
  }

</script>

<template>

<input type="text" v-model="teks">
<button v-on:click="tambahkan" :disabled="teks == ''">submit</button>

<h2>Daftar Kegiatan</h2>
  <ul>
    <li v-for="(list, index) in lists" :key="index">{{ list.text }}
      <input type="checkbox" v-model="list.status">
      <button v-on:click="hapus(index)">X</button>
    </li>
  </ul>


</template>


<style scoped>

</style>