<template>
  <div
    class="border border-gray-200 p-4 rounded shadow-lg bg-gray-50 hover:bg-gray-100 hover:cursor-pointer"
  >
    <h3 class="font-bold text-xl">{{ author.name }}</h3>
    <p class="text-sm text-gray-600">
      Works: <span class="font-semibold">{{ author.work_count }} books </span>
    </p>
    <!-- <p class="text-sm text-gray-600">Key: <span class="text-">{{ author.key }}</span></p> -->
    <p class="text-sm text-gray-500 mt-1">
      Topics:
      <span v-if="author?.top_subjects" class="font-semibold">
        {{ author.top_subjects.join(", ") }}
      </span>
    </p>
  </div>
</template>

<script setup>
defineProps({ author: Object });
// console.log(author);
</script>
