<template>
  <div class="flex gap-2 my-4">
    <input v-model="searchInput" type="text" placeholder="Enter author name..." class="border p-2 w-full rounded" />
    <button @click="emitSearch" class="bg-blue-600 text-white px-4 rounded">Search</button>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const emit = defineEmits(['search'])
const searchInput = ref('')

const emitSearch = () => {
  emit('search', searchInput.value)
}
</script>
