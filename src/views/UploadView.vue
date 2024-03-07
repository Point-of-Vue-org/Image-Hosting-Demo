<script setup>
import { computed, ref, watch } from 'vue'
import LoadingScreen from '@/components/LoadingScreen.vue'

const file = ref(null)
const images = ref(null)
const dragOverState = ref(false)
const isLoading = ref(false)
const uploadingStatus = ref('uploading')

watch(file, (newFile) => {
  console.log(newFile)

  if (newFile) {
    const reader = new FileReader()
    reader.onload = (e) => {
      images.value = e.target.result
      console.log(e.target.result)
    }
    reader.readAsDataURL(newFile)
  }

  if (!newFile) {
    images.value = null
  }
})

const handleDrop = (e) => {
  e.preventDefault()
  const dt = e.dataTransfer
  const files = dt.files

  if (files.length > 0) {
    file.value = files[0]
  }

  dragOverState.value = false
}

const handleDragOver = (e) => {
  e.preventDefault()
  console.log('dragging')

  dragOverState.value = true
}

const handleDragLeave = (e) => {
  e.preventDefault()
  console.log('leaving')

  dragOverState.value = false
}

const onFileChange = (e) => {
  file.value = e.target.files[0]
}

const uploadImage = async () => {
  if (!file.value) return
  isLoading.value = true
  uploadingStatus.value = 'uploading'
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
      uploadingStatus.value = 'uploaded'
      setTimeout(() => {
        isLoading.value = false
      }, 1000)
    })
    .catch(error => {
      uploadingStatus.value = 'failed'
      console.error('Error:', error)
      setTimeout(() => {
        isLoading.value = false
      }, 1000)
    })

    saveImage(result.data.display_url)
}

function saveImage(imageUrl) {
  fetch(`${import.meta.env.VITE_API_URL}/images`, {
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
  <LoadingScreen :isLoading="isLoading" :uploadingStatus="uploadingStatus" />
  <main>
    <div class="text-4xl font-bold my-5">IMAGE UPLOADER</div>
    
    <div class="flex flex-col gap-4 items-start">
      <div>
        <input
          id="file-input"
          type="file"
          @change="onFileChange"
          class="file-input file-input-bordered w-full max-w-xs"
        />
      </div>
      <div
      @drop="handleDrop($event)"
      @dragover="handleDragOver($event)"
      @dragleave="handleDragLeave($event)"
      :class="dragOverState ? 'bg-base-300' : ''"
      class="w-96 h-96 border-2 border-dashed border-base-200 flex flex-col justify-center items-center rounded-lg"
      >
      <img v-if="file" :src="images" alt="preview" class="pointer-events-none w-64 h-64 object-contain" />
        <p v-else class="pointer-events-none">Drag one or more files to this <i>drop zone</i>.</p>
      </div>
      <button @click="uploadImage" type="button" class="btn btn-warning" :disabled="!file">Upload</button>
    </div>
  </main>
</template>

<style scoped>
</style>