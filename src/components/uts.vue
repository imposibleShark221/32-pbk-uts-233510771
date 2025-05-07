<script setup>
  import {ref ,computed, watch, onMounted} from 'vue'

  const tombol = ref(null)

  const teks = ref('')

  const lists = ref([
    {text : "Olahraga", status : true},
    {text : "Makan" , status : true},
    {text : "Tidur", status : false}
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
        <input type="checkbox" v-model="list.status">
        <button v-on:click="hapus(index)">X</button>
      </li>
    </ul>

</template>


<style scoped>

.crossed {
  text-decoration: line-through;
}
</style>
