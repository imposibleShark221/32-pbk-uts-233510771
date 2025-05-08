<script setup>
  import {ref ,computed, onMounted} from 'vue'

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

  const hourAngle = ref(0)
  const minuteAngle = ref(0)
  const secondAngle = ref(0)

  const isDarkMode = ref(false)

  function updateClockAngles() {
    const now = new Date()
    const seconds = now.getSeconds()
    const minutes = now.getMinutes()
    const hours = now.getHours() % 12

    secondAngle.value = seconds * 6
    minuteAngle.value = minutes * 6 + seconds * 0.1
    hourAngle.value = hours * 30 + minutes * 0.5
  }

  function updateTheme() {
    const hour = new Date().getHours()
    isDarkMode.value = hour < 6 || hour >= 18
  }

  onMounted(() => {
    setInterval(() => {
      waktu.value = new Date().toLocaleTimeString()
      updateClockAngles()
    }, 1000)
    updateClockAngles()

    updateTheme()
    setInterval(updateTheme, 60000) // update theme every minute
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
</script>

<template>
<div :class="[{dark: isDarkMode}, 'background']"></div>
<div :class="[{dark: isDarkMode}, 'container']">
  <div class="input-submit-container">
    <input type="text" v-model="teks" placeholder="Add new activity...">
    <button v-on:click="tambahkan" :disabled="teks == ''">Submit</button>
    <svg class="analog-clock" viewBox="0 0 100 100" width="80" height="60" aria-label="Analog clock">
      <circle cx="50" cy="50" r="48" fill="#f5f7fa" stroke="#7a9cc6" stroke-width="3"/>
      <circle cx="50" cy="50" r="2" fill="#7a9cc6"/>
      <line class="hour-hand" x1="50" y1="50" x2="50" y2="30" stroke="#7a9cc6" stroke-width="4" stroke-linecap="round"
        :transform="'rotate(' + hourAngle + ' 50 50)'"/>
      <line class="minute-hand" x1="50" y1="50" x2="50" y2="20" stroke="#5a7aa3" stroke-width="3" stroke-linecap="round"
        :transform="'rotate(' + minuteAngle + ' 50 50)'"/>
      <line class="second-hand" x1="50" y1="50" x2="50" y2="15" stroke="#e07a7a" stroke-width="2" stroke-linecap="round"
        :transform="'rotate(' + secondAngle + ' 50 50)'"/>
    </svg>
  </div>

  <div class="filter-buttons">
    <button v-on:click="filterStatus = 'all'">All: {{ countAll }}</button>
    <button v-on:click="filterStatus = 'done'">Done: {{ countDone }}</button>
    <button v-on:click="filterStatus = 'undone'">Undone: {{ countUndone }}</button>
  </div>

  <h2>Daftar Kegiatan</h2>

  <ul>
    <li v-for="(list, index) in filteredLists" :key="index">
      <div class="activity-text-time">
        <span :class="{ crossed: list.status }">{{ list.text }}</span>
        <div class="time-added">{{ new Date(list.waktu).toLocaleTimeString() }}</div>
      </div>
      <div v-if="list.waktuSelesai" class="done-text">Done</div>
      <div class="time-done" v-if="list.waktuSelesai">{{ new Date(list.waktuSelesai).toLocaleTimeString() }}</div>
      <div class="actions">
        <input type="checkbox" v-model="list.status" @change="onStatusChange(list)">
        <button v-on:click="hapus(index)">X</button>
      </div>
    </li>
  </ul>
</div>
</template>

<style scoped>
  * {
    box-sizing: border-box;
  }

  .background {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    background-color: rgb(223, 255, 255);
    z-index: 0;
  }

  .container {
    width: 720px;
    max-width: 90vw;
    border: 1px #a5b6cc;
    padding: 20px;
    box-sizing: border-box;
    background-color: rgba(181, 211, 250, 0.226);
    border-radius: 12px;
    margin-bottom: 20px;
    display: flex;
    flex-direction: column;
    position: fixed;
    top: 50%;
    left: 50%;
    box-shadow: 0 20px 30px rgba(224, 224, 224, 1);
    transform: translate(-50%, -50%);
    z-index: 1;
  }

  .dark.background {
    background-color: #2b2b2b;
  }

  .dark.container {
    background-color: #3f3f3f;
    box-shadow: 0 20px 30px rgba(5, 5, 5, 0.7);
  }

  .input-submit-container {
    display: flex;
    align-items: center;
    margin-bottom: 15px;
  }

  .analog-clock {
    margin-left: 15px;
    flex-shrink: 0;
  }

  .filter-buttons {
    display: flex;
    justify-content: center;
    gap: 10px;
    margin-bottom: 15px;
  }

  ul {
    list-style-type: none;
    padding: 0;
    margin-top: 0;
    overflow-y: auto;
    flex-grow: 1;
  }

  li {
    background-color: #ffffff;
    margin-bottom: 12px;
    padding: 14px 18px;
    border-radius: 16px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-family: 'Poppins', 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    box-shadow: 0 2px 6px rgba(0,0,0,0.08);
    transition: box-shadow 0.3s ease;
  }

  .dark li {
    background-color: #353535;
    margin-bottom: 12px;
    padding: 14px 18px;
    border-radius: 16px;
    display: flex;
    color: rgb(226, 226, 226);
    align-items: center;
    justify-content: space-between;
    font-family: 'Poppins', 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    box-shadow: 0 2px 6px rgba(0,0,0,0.08);
    transition: box-shadow 0.3s ease;
  }


  input[type="text"] {
    width: 70%;
    padding: 12px 15px;
    margin-right: 10px;
    font-size: 16px;
    border: 1.5px solid #d1d9e6;
    border-radius: 12px;
    background-color: #fff;
    box-shadow: inset 0 1px 3px rgba(0,0,0,0.1);
    transition: border-color 0.3s ease;
  }

  input[type="text"]:focus {
    border-color: #a3b1c6;
    outline: none;
  }

  button {
    padding: 12px 20px;
    font-size: 16px;
    background-color: #7a9cc6;
    color: white;
    border: none;
    border-radius: 12px;
    cursor: pointer;
    box-shadow: 0 4px 8px rgba(122,156,198,0.3);
    transition: background-color 0.3s ease, box-shadow 0.3s ease;
  }

  .dark button {
    background-color: #404142;
    color: white;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
    transition: background-color 0.3s ease, box-shadow 0.3s ease;
  }

  button:disabled {
    background-color: #b7bbc0;
    cursor: not-allowed;
    box-shadow: none;
  }

  button:hover:not(:disabled) {
    background-color: #5a7aa3;
    box-shadow: 0 6px 12px rgba(90,122,163,0.4);
  }

  .dark button:hover:not(:disabled) {
    background-color: #212222;
    box-shadow: 0 6px 12px rgba(3, 3, 3, 0.4);
  }
  .input-submit-container {
    display: flex;
    align-items: center;
  }

  .analog-clock {
    margin-left: 15px;
  }

  .hour-hand {
    transition: transform 0.5s ease-in-out;
  }

  .minute-hand {
    transition: transform 0.5s ease-in-out;
  }

  .second-hand {
    transition: transform 0.1s linear;
  }

  h2 {
    margin-top: 20px;
    font-family: 'Poppins', 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    font-weight: 600;
    color: #3a4a6b;
  }

  .dark h2 {
    margin-top: 20px;
    font-family: 'Poppins', 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    font-weight: 600;
    color: #b8b8b8;
  }

  .filter-buttons {
    display: flex;
    justify-content: center;
    gap: 10px;
    margin-bottom: 10px;
  }

  .filter-buttons > button {
    padding: 12px 20px;
    font-size: 16px;
    background-color: #a3b1c6;
    box-shadow: 0 3px 6px rgba(163,177,198,0.3);
    border: none;
    border-radius: 12px;
    color: white;
    cursor: pointer;
    transition: background-color 0.3s ease, box-shadow 0.3s ease;
  }

  .dark .filter-buttons > button {
    padding: 12px 20px;
    font-size: 16px;
    background-color: #383838;
    box-shadow: 0 3px 6px rgba(0, 0, 0, 0.3);
    border: none;
    border-radius: 12px;
    color: white;
    cursor: pointer;
    transition: background-color 0.3s ease, box-shadow 0.3s ease;
  }

  .filter-buttons > button:hover {
    background-color: #7a8db3;
    box-shadow: 0 4px 8px rgba(122,141,179,0.4);
  }

  .dark .filter-buttons > button:hover {
    background-color: #1b1c1f;
    box-shadow: 0 4px 8px rgba(11, 12, 15, 0.4);
  }

  ul {
    list-style-type: none;
    padding: 0;
    margin-top: 15px;
  }

  li {
    background-color: #ffffff;
    margin-bottom: 12px;
    padding: 14px 18px;
    border-radius: 16px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-family: 'Poppins', 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    box-shadow: 0 2px 6px rgba(0,0,0,0.08);
    transition: box-shadow 0.3s ease;
  }

  li:hover {
    box-shadow: 0 4px 12px rgba(0,0,0,0.12);
  }

  li span {
    flex-grow: 1;
    margin-left: 12px;
    color: #4a5a7a;
  }

  .dark li span {
    color: #a0a0a0;
  }

  .crossed {
    text-decoration: line-through;
    color: #a0a0a0;
  }

  li button {
    background-color: #e07a7a;
    margin-left: 12px;
    border-radius: 12px;
    padding: 6px 12px;
    font-weight: 600;
    box-shadow: 0 2px 6px rgba(224,122,122,0.4);
    transition: background-color 0.3s ease, box-shadow 0.3s ease;
  }

  li button:hover {
    background-color: #b85a5a;
    box-shadow: 0 4px 10px rgba(184,90,90,0.5);
  }

  @media (max-width: 600px) {
    input[type="text"] {
      width: 100%;
      margin-bottom: 10px;
    }

    div > button {
      margin-bottom: 10px;
    }

    li {
      flex-direction: column;
      align-items: flex-start;
    }

    li button {
      margin-left: 0;
      margin-top: 8px;
    }
  }
  .activity-text-time {
    display: flex;
    flex-direction: column;
  }

  .actions {
    display: flex;
    align-items: center;
    gap: 2px;
    width: 80px;
    justify-content: flex-end;
  }
  .done-text {
    font-weight: 600;
    color: #6aca6f;
    margin-bottom: 4px;
    font-family: 'Poppins', 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  }

  .done-text {
    font-weight: 600;
    color: #60dd5b;
    margin-bottom: 4px;
    font-family: 'Poppins', 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  }
  </style>
