<template>
  <div class="max-w-2xl mx-auto p-4">
    <h1 class="text-2xl font-bold mb-4">Search Authors</h1>

    <SearchBar @search="newSearch" />

    <div v-if="!loading && authors.length" class="text-gray-600 mb-2">
      Showing {{ showingStart }}-{{ showingEnd }} of {{ totalResults }} authors
    </div>
    <div
      v-else-if="!loading && currentQuery && !authors.length"
      class="text-gray-600 mb-2"
    >
      No authors found for "{{ currentQuery }}"
    </div>

    <div v-if="loading" class="text-gray-600">Loading...</div>
    <div v-else-if="error" class="text-red-500">{{ error }}</div>

    <AuthorList v-if="authors.length" :authors="authors" />

    <div
      v-if="totalPages > 1"
      class="flex items-center justify-between mt-6 text-sm"
    >
      <button
        @click="prevPage"
        :disabled="currentPage === 1"
        class="px-4 py-2 rounded bg-gray-200 hover:bg-gray-300 disabled:opacity-50"
      >
        ← Previous
      </button>

      <div class="flex flex-col items-center gap-1">
        <div class="flex items-center gap-2">
          <span>Page</span>
          <input
            type="number"
            v-model.number="pageInput"
            @keyup.enter="goToPage"
            min="1"
            :max="totalPages"
            class="w-16 px-2 py-1 border rounded text-center"
          />
          <span
            >of <strong>{{ totalPages }}</strong></span
          >
        </div>
        <div v-if="pageError" class="text-red-500 text-xs">{{ pageError }}</div>
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
import { ref, computed, watch } from "vue";
import axios from "axios";
import SearchBar from "./components/SearchBar.vue";
import AuthorList from "./components/AuthorList.vue";

const authors = ref([]);
const loading = ref(false);
const error = ref(null);
const currentPage = ref(1);
const pageInput = ref(1);
const currentQuery = ref("");
const pageSize = 20;
const totalPages = ref(0);
const totalResults = ref(0);
const pageError = ref(null);

const showingStart = computed(() => {
  return (currentPage.value - 1) * pageSize + 1;
});

const showingEnd = computed(() => {
  const end = currentPage.value * pageSize;
  return end > totalResults.value ? totalResults.value : end;
});

const fetchAuthors = async () => {
  loading.value = true;
  error.value = null;
  authors.value = [];
  pageError.value = null;

  try {
    const res = await axios.get(`https://openlibrary.org/search/authors.json`, {
      params: {
        q: currentQuery.value,
        limit: pageSize,
        offset: (currentPage.value - 1) * pageSize,
      },
    });

    const data = res.data;
    authors.value = data.docs || [];
    totalResults.value = data.numFound || 0;
    totalPages.value = Math.ceil(totalResults.value / pageSize);

    if (
      authors.value.length === 0 &&
      totalResults.value > 0 &&
      currentPage.value > 1
    ) {
      error.value = "No more results available. Try a different page.";
      currentPage.value -= 1;
      await fetchAuthors();
    }
  } catch (e) {
    error.value =
      "Failed to fetch authors. " + (e.response?.data?.error || e.message);
    console.error("API error:", e);
  } finally {
    loading.value = false;
  }
};

const newSearch = async (query) => {
  currentQuery.value = query.trim();
  currentPage.value = 1;
  await fetchAuthors();
};

const nextPage = async () => {
  if (currentPage.value < totalPages.value) {
    currentPage.value += 1;
    await fetchAuthors();
    window.scrollTo({ top: 0, behavior: "smooth" });
  }
};

const prevPage = async () => {
  if (currentPage.value > 1) {
    currentPage.value -= 1;
    await fetchAuthors();
    window.scrollTo({ top: 0, behavior: "smooth" });
  }
};

const goToPage = async () => {
  pageError.value = null;

  if (isNaN(pageInput.value) || pageInput.value < 1) {
    pageInput.value = 1;
    pageError.value = "Page number must be at least 1";
    return;
  }

  if (pageInput.value > totalPages.value) {
    pageInput.value = totalPages.value;
    pageError.value = `Only ${totalPages.value} pages available`;
    return;
  }

  if (pageInput.value !== currentPage.value) {
    currentPage.value = pageInput.value;
    await fetchAuthors();
    window.scrollTo({ top: 0, behavior: "smooth" });
  }
};

watch(currentPage, (newVal) => {
  pageInput.value = newVal;
});
</script>
