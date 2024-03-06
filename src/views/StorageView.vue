<script setup>
import { ref, onBeforeMount } from 'vue'

const images = ref([])

async function fetchImages() {
  let result
  await fetch('http://localhost:5000/images')
    .then(response => response.json())
    .then(data => {
      console.log('Success:', data)
      result = data
    })
    .catch(error => {
      console.error('Error:', error)
    })
  return result
}

onBeforeMount(async () => {
  images.value = await fetchImages()
})

const deleteImage = async (id) => {

  if (!window.confirm('Are you sure you want to delete this image?')) return

  await fetch(`http://localhost:5000/images/${id}`, {
    method: 'DELETE'
  })
  images.value = await fetchImages()
}

</script>

<template>
  <main>
    <div class="text-4xl font-bold my-5">Storage</div>
    
    <div class="flex gap-10 items-start flex-wrap">
      <div v-for="image in images" :key="image.id" @click="deleteImage(image.id)" class="bg-base-200 p-2 rounded-lg">
        <img :src="image.url" alt="image" class="w-64 h-64 object-cover" />
      </div>
    </div>
  </main>
</template>

<style scoped>

</style>
