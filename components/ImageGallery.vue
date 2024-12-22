<script setup>
import { toRefs, ref, onMounted } from 'vue';
import { useIntersectionObserver } from "@vueuse/core";

const props = defineProps({
  isModalOpen: Boolean,
  query: String,
  queryImages: Array,
  filterImages: Function,
  images: Array,
  dataStatus: Object
});

const emit = defineEmits(['handle-modal', 'update:query', 'filter-images', 'add-images']);

const handleInput = (event) => {
  const sanitizedValue = event.target.value.replace(/[^a-zA-Z]/g, '');
  emit('update:query', sanitizedValue);
  emit('filter-images');
};
const { isLoading, hasMoreData } = toRefs(props.dataStatus);

const visibleImages = ref([]);
const imageRefs = ref([]);

const observeImages = () => {
  imageRefs.value.forEach((el, index) => {
    const id = props.queryImages[index].id;

    useIntersectionObserver(
      el,
      ([entry]) => {
        if (entry.isIntersecting) {
          if (!visibleImages.value.includes(id)) {
            visibleImages.value.push(id);
          }
        } else {
          visibleImages.value = visibleImages.value.filter((visibleId) => visibleId !== id);
        }
      },
      {
        threshold: 0.05, // Consider the image visible when 5% of it is in view
        root: null, // Use the default root (viewport)
        rootMargin: '300px 0px', // Load images a little before they enter the viewport
      }
    );
  });
};

onMounted(() => {
  observeImages();
});

watch(() => props.queryImages, async () => {
  await nextTick();
  observeImages();
});

</script>

<template>
  <div id="gallery" class="gallery">
    <div class="gallery-options">
      <input class="filter" placeholder="Search..." type="text" maxlength="20" :value="query" @input="handleInput" />
      <button :disabled="props.isModalOpen" class="add-images-button" @click="$emit('handle-modal', true)">Add
        Image</button>
    </div>
    <!-- no virtual scroll, not buggy -->
    <!-- <div class="image-gallery">
      <div v-for="(image) in queryImages" :key="image.id" class="image-container">
          <nuxt-img class="image-size" :src="image.url" :alt="image.author" loading="lazy" />
          <div>{{ image.author }}</div>
      </div>
    </div> -->
    <!-- virtual scroll, buggy when images are being filtered -->
    <div class="image-gallery">
      <div v-for="(image) in queryImages" :key="image.id" ref="imageRefs" class="image-container">
        <template v-if="visibleImages.includes(image.id)">
          <nuxt-img class="image-size" :src="image.url" :alt="image.author" loading="lazy" />
          <div>{{ image.author }}</div>
        </template>
      </div>
    </div>
    <div class="bottom">
      <div v-if="isLoading" class="spinner"></div>
      <div v-if="!hasMoreData">No more data</div>
    </div>
  </div>
</template>

<style>
.gallery {
  background: linear-gradient(180deg, white 22%, #f4f4f4 89%);
  padding: 40px 40px 40px 40px;
  min-height: 400px;
}

.gallery-options {
  text-align: center;
  margin-bottom: 10px;
}

.image-gallery {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
}

.image-container {
  width: 300px;
  height: 200px;
  border: 1px solid lightgray;
  text-align: center;
  margin-bottom: 10px;
  position: relative;
  padding-bottom: 20px;
}

.image-size {
  width: 90%;
  height: 90%;
  object-fit: cover;
}

.add-images-button {
  background-color: #B0FB00;
  height: 35px;
  width: 120px;
  font-weight: 500;
  border-radius: 5px;
  border: none;
  margin: 10px;
}

.add-images-button:hover {
  background-color: #a2e700
}

.filter {
  outline: none;
  border-top: none;
  border-left: none;
  border-right: none;
  border-bottom: 1px solid black;
  margin-right: 20px;
  width: 200px;
  margin: 10px;
}

.filter:focus {
  border-bottom: 1px solid #8cc800;
}

.spinner {
  border: 4px solid #f3f3f3;
  border-top: 4px solid #B0FB00;
  border-radius: 50%;
  width: 30px;
  height: 30px;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }

  100% {
    transform: rotate(360deg);
  }
}

.bottom {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 50px;
}
</style>