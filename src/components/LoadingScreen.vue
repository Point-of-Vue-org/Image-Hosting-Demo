<script setup>
import { capitalize } from '../../libs/util';


defineProps({
    isLoading: {
        type: Boolean,
        required: true
    },
    uploadingStatus: {
        validator: (value) => {
            return ['uploading', 'uploaded', 'failed'].includes(value)
        },
        required: true
    }
})
</script>

<template>
    <div v-show="isLoading" class="fixed z-10 top-0 left-0 bg-[#000c] w-full h-full flex flex-col justify-center items-center gap-5">
        <div class="text-slate-400 text-2xl">{{ capitalize(uploadingStatus) }}</div>
        <span class="text-slate-400 loading loading-dots loading-lg"></span>
    </div>
</template>

<style scoped>

</style>