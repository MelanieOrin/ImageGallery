<script setup>
import { onMounted, onUnmounted } from 'vue';

definePageMeta({
    layout: 'default'
})

const galleryImages = ref([]);

const query = ref("");

const queryImages = ref([]);

const newImage = ref({
    id: 0,
    url: "",
    author: "",
});

const isModalOpen = ref(false);

const page = ref(1);

const dataStatus = ref({
    isLoading: false,
    hasMoreData: true
});

const handleModal = (value) => {
    isModalOpen.value = value;
    newImage.value = ({ id: 0, url: "", author: "" });
}

const addImages = (data = {}) => {
    if (Array.isArray(data)) {
        galleryImages.value.push(
            ...data.map(({ id, download_url: url, author }) => ({ id, url, author }))
        );
    } else {
        galleryImages.value.push(newImage.value);
    }
    query.value = "";
    queryImages.value = [...galleryImages.value];
};

const filterImages = () => {
    const filtered = galleryImages.value.filter((item) => item.author.toLowerCase().includes(query.value.toLowerCase()))
    queryImages.value = [...filtered];
};

//considered randomizing this, there are ~40 pages
const fetchImages = async (limit = 25) => {
    if (!dataStatus.value.hasMoreData || dataStatus.value.isLoading) return;
    try {
        dataStatus.value.isLoading = true;
        const response = await fetch(`https://picsum.photos/v2/list?page=${page.value}&limit=${limit}`);
        const data = await response.json();
        if (data.length > 0) {
            addImages(data);
            page.value += 1;
        } else {
            dataStatus.value.hasMoreData = false;
        }
    } catch (error) {
        console.error("Error fetching images:", error);
    } finally {
        dataStatus.value.isLoading = false;
    }
};

const scrollContainer = ref(null);
const handleScroll = () => {
    const { scrollTop, scrollHeight, clientHeight } = scrollContainer.value;
    if (scrollTop + clientHeight >= scrollHeight - 100) {  //checks if the scroll is within 100px of the container’s bottom
        fetchImages();
    }
};

onMounted(() => {
    fetchImages();
    scrollContainer.value.addEventListener('scroll', handleScroll);
});

onUnmounted(() => {
    scrollContainer.value.removeEventListener('scroll', handleScroll);
});


</script>

<template>
    <div>
        <div ref="scrollContainer" class="main">
            <ContentBox />
            <ModalBox v-model:newImage="newImage" :is-modal-open="isModalOpen" @handle-modal="handleModal"
                @add-images="addImages" />
            <ImageGallery v-model:query="query" :query-images="queryImages" :data-status="dataStatus"
                :is-modal-open="isModalOpen" @handle-modal="handleModal" @add-images="addImages"
                @filter-images="filterImages" />
            <button :disabled="isModalOpen" class="add" @click="isModalOpen = true">Add Images</button>
            <div class="footer"></div>
        </div>
    </div>
</template>

<style scoped>
.main {
    height: calc(100vh - 60px);
    overflow-y: auto;
}

.add {
    border: none;
    width: 120px;
    height: 35px;
    padding: 10px;
    margin: 20px;
    position: absolute;
    bottom: 0;
    right: 0;
    border-radius: 10px;
    z-index: 10;
    box-shadow: 0 0 3px rgba(0, 0, 0, 0.1), 0 0 10px rgba(0, 0, 0, 0.3);
    background-color: rgb(251, 250, 253);
}

.add:hover {
    box-shadow: 0 0 20px rgba(0, 0, 0, 0.1), 0 0 15px rgba(0, 0, 0, 0.3);
}

.footer {
    background-color: #f4f4f4;
    height: 100px;
}
</style>