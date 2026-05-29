<template>
  <div class="container mx-auto py-8">
    <h1 class="text-3xl font-bold mb-6">I Want These Games</h1>
    <GameCategoryList :items="allItems" />
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import GameCategoryList from './components/GameCategoryList.vue'

const allItems = ref([])

import { initializeApp } from 'firebase/app'
import { getDatabase, ref as dbRef, get } from 'firebase/database'

const firebaseConfig = {
        apiKey: "AIzaSyCvOUqQPRbo7rJPtunQnV23jjfN3v73Ee4",
        authDomain: "iwantthesegames-a724e.firebaseapp.com",
        databaseURL: "https://iwantthesegames-a724e.firebaseio.com",
        storageBucket: "iwantthesegames-a724e.appspot.com"
}

const app = initializeApp(firebaseConfig)
const db = getDatabase(app)

onMounted(async () => {
  const snapshot = await get(dbRef(db, '/yourPath'))
  if (snapshot.exists()) {
    allItems.value = snapshot.val()
  } else {
    allItems.value = []
  }
})
</script>

<style scoped>
body {
  font-family: sans-serif;
}
</style>
