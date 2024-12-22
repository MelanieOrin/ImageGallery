<template>
    <div class="header">
        <img src="../assets/images/camera.png" class="camera" alt="camera" loading="lazy" />
        <div class="header-buttons">
            <div class="nav-button">
                <a class="anchor-tag" href="#about">About</a>
            </div>
            <div class="nav-button">
                <a class="anchor-tag" href="#gallery">Gallery</a>
            </div>
        </div>
    </div>
</template>

<style scoped>
.header {
    display: flex;
    height: 60px;
    align-items: center;
    z-index: 10;
    background-color: white;
    position: sticky;
    width: 100%;
    top: 0;
}

.header-buttons {
    display: flex;
    position: absolute;
    left: 50%;
}

.anchor-tag {
    text-decoration: none;
    color: black;
    padding: 5px;
}

.nav-button {
    text-align: center;
    margin-right: 30px;
}

.anchor-tag:hover {
    color: gray;
}

.camera {
    height: 70%;
    margin-left: 40px;
}
</style>