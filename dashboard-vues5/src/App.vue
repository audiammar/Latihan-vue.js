<script setup>
import { ref, computed, watch } from 'vue';
import UserCard from './components/UserCard.vue';

const queryPencarian = ref('');
const riwayatPencarian = ref([]);
const judul = 'Dashboard Vue — Pertemuan 5';
const users = ref([]);
const keadan = ref('idle');

const penggunaTersaring = computed(() => {
  const q = queryPencarian.value.toLowerCase().trim();
  if (!q) return users.value;
  return users.value.filter((user) =>
    user.name.toLowerCase().includes(q)
  );
});

watch(queryPencarian, (nilaiBaru) => {
  if (nilaiBaru.trim() !== '') {
    riwayatPencarian.value.push(nilaiBaru);
  }
});

async function muatPengguna() {
  keadan.value = 'loading';
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/users');
    if (!response.ok) {
      throw new Error('status HTTP: ' + response.status);
    }

    const data = await response.json();
    if (data.length === 0) {
      keadan.value = 'empty';
      return;
    }

    users.value = data;
    keadan.value = 'success';
  } catch (error) {
    console.error('Gagal memuat data pengguna:', error);
    keadan.value = 'error';
  }
}
</script>

<template>
  <header class="app-header">
    <h1>{{ judul }}</h1>
  </header> 
  <main class="app-main">
    <button @click="muatPengguna">Muat Pengguna</button>
    <input
      v-model="queryPencarian"
      type="text"
      placeholder="Cari username..." />
    <p v-if="keadan === 'loading'">Memuat data pengguna...</p>
    <p v-else-if="keadan === 'empty'">Tidak ada data pengguna ditemukan.</p>
    <p v-else-if="keadan === 'error'">Gagal memuat data pengguna. Coba lagi.</p>
    <ul v-else-if="keadan === 'success'">
      <UserCard v-for="user in penggunaTersaring" :key="user.id" :user="user" />
    </ul>
  </main>
</template>

<style scoped>
.app-header {
  padding: 1.5rem;
  background: #0b4f6c;
  color: white;
  }
  .app-main {
    padding: 1.5rem;
  }
</style>