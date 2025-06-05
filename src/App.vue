<template>
  <div class="max-w-2xl mx-auto p-4">
    <h1 class="text-2xl font-bold mb-4">Author Search</h1>

    <SearchBar @search="newSearch" />

    <div v-if="loading" class="text-gray-600">Loading...</div>
    <div v-else-if="error" class="text-red-500">{{ error }}</div>

    <AuthorList v-if="authors.length" :authors="authors" />

    <!-- Pagination Controls -->
    <div v-if="totalPages > 1" class="flex items-center justify-between mt-6 text-sm">
      <button
        @click="prevPage"
        :disabled="currentPage === 1"
        class="px-4 py-2 rounded bg-gray-200 hover:bg-gray-300 disabled:opacity-50"
      >
        ← Previous
      </button>

      <div class="text-center">
        Page <strong>{{ currentPage }}</strong> of <strong>{{ totalPages }}</strong>
      </div>

      <button
        @click="nextPage"
        :disabled="currentPage === totalPages"
        class="px-4 py-2 rounded bg-blue-600 text-white hover:bg-blue-700 disabled:opacity-50"
      >
        Next →
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'
import SearchBar from './components/SearchBar.vue'
import AuthorList from './components/AuthorList.vue'

const authors = ref([])
const loading = ref(false)
const error = ref(null)
const currentPage = ref(1)
const currentQuery = ref('')
const pageSize = 5
const totalPages = ref(0)

const fetchAuthors = async () => {
  loading.value = true
  error.value = null
  authors.value = []

  try {
    const res = await axios.get(`https://openlibrary.org/search/authors.json?q=${currentQuery.value}&page=${currentPage.value}`)
    const data = res.data
    const docs = data?.docs || []

    authors.value = docs.slice(0, pageSize)
    totalPages.value = Math.ceil(data.numFound / pageSize)
  } catch (e) {
    error.value = 'Failed to fetch authors.'
  } finally {
    loading.value = false
  }
}

const newSearch = async (query) => {
  currentQuery.value = query
  currentPage.value = 1
  await fetchAuthors()
}

const nextPage = async () => {
  if (currentPage.value < totalPages.value) {
    currentPage.value += 1
    await fetchAuthors()
  }
}

const prevPage = async () => {
  if (currentPage.value > 1) {
    currentPage.value -= 1
    await fetchAuthors()
  }
}
</script>
