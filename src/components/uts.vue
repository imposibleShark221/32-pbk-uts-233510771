<script setup>
  import {ref ,computed, watch, onMounted} from 'vue'

  const tombol = ref(null)

  const teks = ref('')

  const lists = ref([
    {text : "Olahraga", status : true},
    {text : "Makan" , status : true},
    {text : "Tidur", status : false}
  ])


</script>

<template>

<input type="text" v-model="teks">
<button v-on:click="tambahkan" :disabled="teks == ''">submit</button>

<h2>Daftar Kegiatan</h2>
<ul>
  <li v-for="list in lists"> {{ list.id }}. {{ list.text }} : {{ list.status }}
  </li>
</ul>


</template>


<style scoped>

</style>