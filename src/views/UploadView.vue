<script setup>
import { ref } from 'vue'

const file = ref(null)
const isLoading = ref(false)

const onFileChange = (e) => {
  file.value = e.target.files[0]
}

const uploadImage = async () => {
  if (!file.value) return
  isLoading.value = true
  const data = new FormData()
  data.append('image', file.value)

  let result

  await fetch(`https://api.imgbb.com/1/upload?key=${import.meta.env.VITE_IMGBB_KEY}`, {
    method: 'POST',
    body: data
  })
    .then(response => response.json())
    .then(data => {
      console.log('Success:', data)
      result = data
      isLoading.value = false
    })
    .catch(error => {
      console.error('Error:', error)
    })

    saveImage(result.data.display_url)
}

function saveImage(imageUrl) {
  fetch('http://localhost:5000/images', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      url: imageUrl
    })
  })
    .then(response => response.json())
    .then(data => {
      console.log('Success:', data)
    })
    .catch(error => {
      console.error('Error:', error)
    })
}
</script>

<template>
  <div v-show="isLoading" class="fixed z-10 top-0 left-0 bg-[#000c] w-full h-full flex flex-col justify-center items-center gap-5">
    <div class="text-slate-400 text-2xl">Uploading</div>
    <span class="text-slate-400 loading loading-dots loading-lg"></span>
  </div>
  <main>
    <div class="text-4xl font-bold my-5">IMAGE UPLOADER</div>
    
    <div class="flex flex-col gap-4 items-start">
      <div>
        <input type="file" @input="onFileChange" class="file-input file-input-bordered w-full max-w-xs" />
      </div>
      <button @click="uploadImage" type="button" class="btn btn-warning">Upload</button>
    </div>
  </main>
</template>
