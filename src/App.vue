<script setup lang="ts">
import AlgoliaSearch from './components/AlgoliaSearch.vue'
</script>

<template>
  <Suspense>
    <AlgoliaSearch />
    <template #fallback>
      <div>Loading...</div>
    </template>
  </Suspense>
</template>