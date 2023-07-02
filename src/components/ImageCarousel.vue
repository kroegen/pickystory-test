<template>
  <div class="carousel">
    <div class="carousel__body">
      <span class="carousel__action" @click="handleRotatePrev">
        <i class="arrow left"></i>
      </span>
      <div class="carousel__wrapper" ref="carousel">
        <ImageItem
          v-for="photo in photos"
          :key="photo.id"
          :photo="photo"
          :selected="selectedPhotosIds.includes(photo.id)"
          @click="handleSelectPhoto(photo)"
        />
      </div>
      <span class="carousel__action" @click="handleRotateNext">
        <i class="arrow right"></i>
      </span>
    </div>
    <div class="carousel__list">
      <ul class="carousel__list-wrapper">
        <li
          v-for="selectedPhoto in state.selectedPhotos"
          :key="selectedPhoto.id"
          class="carousel__list-item"
        >
          <a :href="selectedPhoto.url" target="_blank" rel="noopener noreferrer">
            {{ selectedPhoto.url }}
          </a>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup lang="ts">
import type { Photo } from '@/App.vue';
import ImageItem from './ImageItem.vue';
import { computed, ref, type Ref, reactive } from 'vue';

interface Props {
  photos: Photo[];
}

interface State {
  selectedPhotos: Photo[];
}

const props = defineProps<Props>();
const carousel: Ref<HTMLDivElement | null> = ref(null);
const state: State = reactive({
  selectedPhotos: []
});

const photos = computed(() => {
  return props.photos || [];
});

const selectedPhotosIds = computed(() => {
  return state.selectedPhotos.map((photo: Photo) => photo.id);
});

function handleRotatePrev() {
  const carouselWidth = carousel.value?.offsetWidth;

  if (carouselWidth) {
    if (carousel!.value!.scrollLeft <= 0) {
      carousel!.value!.scrollLeft = carousel!.value!.scrollWidth - (carouselWidth - 20);
    } else {
      carousel!.value!.scrollLeft -= (carouselWidth - 20);
    }
  }
}

function handleRotateNext() {
  const carouselWidth = carousel.value?.offsetWidth;

  if (carouselWidth) {
    if (carousel!.value!.scrollLeft + carouselWidth === carousel!.value!.scrollWidth) {
      carousel!.value!.scrollLeft = 0;
    } else {
      carousel!.value!.scrollLeft += (carouselWidth - 20);
    }
  }
}

function handleSelectPhoto(photo: Photo) {
  if (selectedPhotosIds.value.includes(photo.id)) {
    removePhoto(photo.id)
  } else {
    state.selectedPhotos.push(photo);
  }
}

function removePhoto(photoId: string) {
  const photoIndex = state.selectedPhotos.findIndex(photo => photo.id === photoId)

  if (photoIndex >= 0) {
    state.selectedPhotos.splice(photoIndex, 1);
  }
}

</script>

<style lang="scss" scoped>
.carousel {
  --action-width: 35px;
  --carousel-max-width: 1200px;

  max-width: var(--carousel-max-width);
  min-width: 300px;
  width: auto;
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 20%;

  &__body {
    width: 100%;
    height: 300px;
    position: relative;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  &__wrapper {
    height: 100%;
    width: calc(100% - 2*var(--action-width));

    background: var(--vt-c-grey);
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: flex-start;
    z-index: 1;
    padding: 10px;

    overflow: hidden;
    overflow-x: overlay;
    transition: all 0.3s;
  }

  &__wrapper::-webkit-scrollbar {
    display: none;
  }

  &__actions {
    width: 100%;
    height: 100%;
    display: flex;
    align-items: center;
    justify-content: space-between;
    position: absolute;
  }

  &__action {
    display: flex;
    height: 100%;
    align-items: center;
    width: var(--action-width);
    justify-content: center;

    &:active {
      transform: scale(.95);
    }
  }

  &__list {
    margin-top: 20px;
    width: 100%;
    display: flex;
    justify-content: center;
    align-items: flex-start;
  }

  &__list-wrapper {
    list-style: none;
  }

  &__list-item a {
    color: var(--color-text);
  }
}
.arrow {
  opacity: .8;
  border: solid var(--color-text);
  border-width: 0 10px 10px 0;
  display: inline-block;
  padding: 10px;

  &:hover {
    opacity: 1;
    cursor: pointer;
  }
}

.right {
  transform: rotate(-45deg);
  -webkit-transform: rotate(-45deg);
}

.left {
  transform: rotate(135deg);
  -webkit-transform: rotate(135deg);
}

@media all and (min-width: 1280px) {

}

@media all and (min-width: 1024px) and (max-width: 1280px) {
  .carousel {
    --carousel-max-width: 1024px;
  }

  :deep(.image) {
    --image-width: calc(100%/4);
  }
}

@media all and (min-width: 768px) and (max-width: 1024px) {
  .carousel {
    --carousel-max-width: 768px;
  }

  :deep(.image) {
    --image-width: calc(100%/3);
  }
}

@media all and (min-width: 480px) and (max-width: 768px) {
  .carousel {
    --carousel-max-width: 480px;
  }

  :deep(.image) {
    --image-width: calc(100%/2);
  }
}

@media all and (max-width: 480px) {
  .carousel {
    --carousel-max-width: 320px;
  }

  :deep(.image) {
    --image-width: calc(100%/1);
  }
}

</style>
