
<template>
  <main>
    <ImageCarousel
      :photos="state.photos"
    />
  </main>
</template>

<script setup lang="ts">
import { onMounted, reactive } from 'vue';
import ImageCarousel from './components/ImageCarousel.vue';

const PHOTOS_URL = "https://picsum.photos/v2/list?page=2&limit=1000";

onMounted(async () => {
  await fetchPhotos();
});

export interface Photo {
  author: string;
  download_url: string;
  height: number;
  id: string;
  url: string;
  width: number;
}

interface State {
  photos: Photo[];
}

const state: State = reactive({
  photos: [],
});

async function fetchPhotos() {
  const response = await fetch(PHOTOS_URL);
  const photos = await response.json();

  if (photos.length) {
    state.photos = [...photos];
  }
}
</script>

<style scoped lang="scss">
main {
  width: 100vw;
  height: 100vh;

  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
}
</style>
