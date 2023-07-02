<template>
  <div
    :class="{
      image: true,
      'image--selected': props.selected
    }"
    @click="handleClick"
    ref="image"
  >
    <div class="image__wrapper">
      <img
        class="image__content"
        :src="imageSrc"
        :alt="photo.author"
      />
    </div>
  </div>

</template>

<script lang="ts" setup>
import type { Photo } from '@/App.vue';
import { computed, onMounted, onUnmounted, reactive, ref, type Ref } from 'vue';

interface Props {
  photo: Photo;
  selected: boolean;
}

interface State {
  observer: IntersectionObserver | null;
  intersected: boolean;
}

const image: Ref<HTMLDivElement | null> = ref(null);
const props = defineProps<Props>();
const state: State = reactive({
  observer: null,
  intersected: false,
});
const photo = computed(() => props.photo);
const imageSrc =  computed(() => state.intersected ? photo.value.download_url : '');
const emit = defineEmits(['click']);

onMounted(() => {
  state.observer = new IntersectionObserver((entries: IntersectionObserverEntry[]) => {
    const image = entries[0];

    if (image.isIntersecting && state.observer) {
      state.intersected = true;
      state.observer.disconnect();
    }
  });

  if (image.value) {
    state.observer.observe(image.value);
  }
});

onUnmounted(() => {
  state.observer?.disconnect();
})

function handleClick() {
  emit('click');
}

</script>

<style lang="scss" scoped>
.image {
  $this: &;

  --border-color: transparent;
  --image-width: 20%;

  height: 100%;
  width: var(--image-width);
  flex-shrink: 0;
  opacity: 0.7;

  &:hover {
    cursor: pointer;
    opacity: 1;
    filter: none;
    --border-color: #8ff8ff;
  }

  &:active {
    transform: scale(.95);
  }

  &:active,
  &--selected {
    opacity: 1;
    --border-color: #4c7e97;
  }

  &__wrapper {
    height: 100%;
    padding: 10px;
    border: 1px solid var(--border-color);
    border-radius: 5px;
    transition: all 0.3s;
  }

  &__content {
    height: 100%;
    width: 100%;
    object-fit: cover;
  }
}
</style>
