<script>
import { ref, watch } from 'vue';

export default {
    props: {
        isModalOpen: Boolean,
        newImage: Object,
    },
    emits: ['handle-modal', 'update:newImage', 'add-images'],
    setup(props, { emit }) {
        const localImage = ref({ ...props.newImage });

        const imageUploadId = ref(-1);

        const handleImageUpload = (event) => {
            if (/\.(jpg|jpeg|png|gif|webp|svg)$/i.test(event.target.value)) {
                localImage.value.url = event.target.value;
                localImage.value.id = imageUploadId.value;
                emit('update:newImage', { ...localImage.value });
                imageUploadId.value -= 1;
            } else {
                console.error('Invalid file type.');
            }
        };

        const handleFileUpload = (event) => {
            const file = event.target.files[0];
            if (file && file.type.startsWith('image/')) {
                const imageUrl = URL.createObjectURL(file);
                localImage.value.url = imageUrl;
                localImage.value.id = imageUploadId.value;
                emit('update:newImage', { ...localImage.value });
                imageUploadId.value -= 1;
            } else {
                console.error('Invalid file type.');
            }
        };

        const handleImageAuthor = (event) => {
            const sanitized = event.target.value.replace(/[^a-zA-Z]/g, '');
            localImage.value.author = sanitized;
            emit('update:newImage', { ...localImage.value });
        };

        const handleSubmit = () => {
            if (localImage.value.url != "" || localImage.value.author != "") {
                emit('add-images', localImage.value);
                emit('handle-modal', false);
            }
        };

        watch(
            () => props.newImage,
            (newVal) => {
                localImage.value = { ...newVal };
            },
            { deep: true }
        );

        return {
            localImage,
            handleImageUpload,
            handleFileUpload,
            handleImageAuthor,
            handleSubmit,
        };
    },
};
</script>

<template>
    <div v-if="isModalOpen" class="modal">
        <div class="modal-content">
            <h1>Add Image</h1>
            <form @submit.prevent="handleSubmit">
                <div class="modal-inputs">
                    <input type="file" accept="image/*" class="upload-file" @change="handleFileUpload" />
                    <input v-model="localImage.url" type="text" placeholder="Image (URL or file)*" class="modal-input"
                        @input="handleImageUpload" />
                    <input v-model="localImage.author" type="text" placeholder="Author / Description*"
                        class="modal-input" maxlength="30" @input="handleImageAuthor" />
                </div>
                <div class="preview">
                    <img v-if="localImage.url" class="preview-image" :src="localImage.url" alt="image-preview" />
                </div>
                <div class="modal-buttons">
                    <button class="upload-button" type="submit"
                        :disabled="!(localImage.url && localImage.author)">Upload</button>
                    <button class="close-button" @click="$emit('handle-modal', false)">Close</button>
                </div>
            </form>
        </div>
    </div>
</template>

<style scoped>
.modal {
    background-color: white;
    height: 60%;
    min-height: 600px;
    width: 30%;
    min-width: 300px;
    text-align: center;
    z-index: 10;
    border-radius: 20px;
    box-shadow: 0 10px 30px 0 rgba(0, 0, 0, 0.14);
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}

.modal-content {
    margin: 40px;
    height: calc(100% - 80px);
    position: relative;
}

.preview {
    width: 150px;
    height: 100px;
    margin: auto;
    border: 1px solid lightgray;
}

.preview-image {
    width: 100%;
    height: 100%;
    object-fit: cover;
    object-position: center;
}

.modal-inputs {
    margin-top: 20px;
    margin-bottom: 20px;
}

.upload-file {
    width: 90%;
    margin: 20px;
}

.modal-input {
    outline: none;
    border-top: none;
    border-left: none;
    border-right: none;
    border-bottom: 1px solid black;
    width: 90%;
    margin: 20px;
}

.modal-input:focus {
    border-bottom: 1px solid #8cc800;
}

.upload-button {
    background-color: #B0FB00;
    height: 35px;
    width: 120px;
    font-weight: 500;
    border-radius: 5px;
    border: none;
    margin: 10px;
}

.upload-button:hover {
    background-color: #a2e700
}

.close-button {
    background-color: #dedfe4;
    border: none;
    height: 35px;
    width: 120px;
    border-radius: 5px;
    margin: 10px;
}

.close-button:hover {
    background-color: #c8c9d2;
}

.modal-buttons {
    position: absolute;
    bottom: 0;
    width: 100%;
}
</style>