<template>
  <div>
    <div class="flex items-center gap-2 mb-4">
      <label>Filter:</label>
      <select v-model="minWorks" class="border rounded px-2 py-1">
        <option value="0">All</option>
        <option value="50">Works > 50</option>
        <option value="100">Works > 100</option>
      </select>

      <label>Sort:</label>
      <select v-model="sortOrder" class="border rounded px-2 py-1">
        <option value="asc">Ascending</option>
        <option value="desc">Descending</option>
      </select>
    </div>

    <div class="grid gap-4">
      <AuthorCard
        v-for="author in sortedAndFilteredAuthors"
        :key="author.key"
        :author="author"
      />
    </div>
  </div>
</template>

<script setup>
import { computed, ref } from 'vue'
import AuthorCard from './AuthorCard.vue'

const props = defineProps({ authors: Array })

const minWorks = ref(0)
const sortOrder = ref('desc')

const sortedAndFilteredAuthors = computed(() => {
  return props.authors
    .filter(a => a.work_count >= minWorks.value)
    .sort((a, b) =>
      sortOrder.value === 'asc'
        ? a.work_count - b.work_count
        : b.work_count - a.work_count
    )
})
</script>
