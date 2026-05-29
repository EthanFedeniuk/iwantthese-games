<template>
  <div>
    <div class="mb-6">
      <input
        v-model="searchQuery"
        type="text"
        placeholder="Search games..."
        class="border p-2 rounded w-full"
      />
    </div>

    <div class="mb-6">
      <label class="mr-4">Sort by:
        <select v-model="sortOption" class="ml-2 p-1 border rounded">
          <option value="title">Title</option>
          <option value="favorite">Favorites First</option>
        </select>
      </label>
    </div>

    <div v-for="category in categories" :key="category" class="mb-6">
      <h2 class="text-xl font-semibold mb-2">
        <span v-if="categoryIcons[category]">{{ categoryIcons[category] }} </span>{{ category }}
      </h2>

      <div class="mb-4">
        <label><input type="checkbox" v-model="filters[category].showWanted" /> Show Wanted</label>
        <label class="ml-4"><input type="checkbox" v-model="filters[category].showOwned" /> Show Owned</label>
        <label class="ml-4"><input type="checkbox" v-model="filters[category].showFavorites" /> Show Favorites Only</label>
      </div>

      <div>
        <div
          v-for="item in filteredItemsByCategory(category)"
          :key="item.id"
          class="p-4 mb-2 border rounded shadow flex justify-between items-center"
        >
          <div>
            <strong>{{ item.title }}</strong>
            <span class="ml-2 text-sm text-gray-500">({{ item.owned ? 'Owned' : 'Wanted' }})</span>
            <span v-if="item.favorite" class="ml-2">❤️</span>
            <span v-if="item.tags" class="ml-2 text-xs text-gray-700">[{{ item.tags.join(', ') }}]</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { reactive, computed, ref } from 'vue'

const props = defineProps(['items'])

const filters = reactive({})
const searchQuery = ref('')
const sortOption = ref('title')

const categoryIcons = {
  'Switch': '🎮',
  'Board Game': '🎲',
  'Toy': '🧸',
  'Book': '📚',
  'Expansion': '➕'
}

const categories = computed(() => {
  const cats = [...new Set(props.items.map(item => item.category))]
  cats.forEach(cat => {
    if (!filters[cat]) {
      filters[cat] = { showWanted: true, showOwned: true, showFavorites: false }
    }
  })
  return cats
})

function filteredItemsByCategory(category) {
  return props.items
    .filter(item => item.category === category)
    .filter(item =>
      (filters[category].showOwned && item.owned) ||
      (filters[category].showWanted && !item.owned)
    )
    .filter(item => !filters[category].showFavorites || item.favorite)
    .filter(item => item.title.toLowerCase().includes(searchQuery.value.toLowerCase()))
    .sort((a, b) => {
      if (sortOption.value === 'favorite') {
        return (b.favorite === true) - (a.favorite === true) || a.title.localeCompare(b.title)
      }
      return a.title.localeCompare(b.title)
    })
}
</script>
