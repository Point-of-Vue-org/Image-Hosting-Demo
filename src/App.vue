<script setup>
import { RouterLink, RouterView } from 'vue-router'
</script>

<template>
  <main class="flex justify-center">
    <div class="container h-screen grid grid-cols-1 grid-rows-[auto,auto,1fr]">
      <div class="text-2xl font-bold m-3">Point-of-Vue DEMO</div>
      <nav class="bg-base-200 flex">
        <RouterLink to="/" class="hover:bg-base-300 px-3">Upload</RouterLink>
        <RouterLink to="/storage" class="hover:bg-base-300 px-3">Storage</RouterLink>
      </nav>
      
      <RouterView />
    </div>
  </main>
</template>

<style scoped>
</style>