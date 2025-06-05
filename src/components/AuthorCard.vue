<template>
  <div class="border p-4 rounded shadow hover:bg-gray-100 hover:cursor-pointer">
    <h3 class="font-bold text-lg">{{ author.name }}</h3>
    <p class="text-sm text-gray-600">Works: {{ author.work_count }}</p>
    <p class="text-sm text-gray-500 mt-1">
      Topics: 
      <span v-if="author?.top_subjects">
        {{ author.top_subjects.join(', ') }}
      </span>
    </p>
  </div>
</template>

<script setup>
defineProps({ author: Object })
// console.log(author);
</script>
