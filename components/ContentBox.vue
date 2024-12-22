<template>
    <div id="about" class="box">
        <div class="summary">
            <h1>Hi, I'm Naomi. I am an artist and a photographer.</h1>
            <div class="summary-font">I made this site to look at photographs and to get inspired. The text
                below the photograph is the author (usually), and you can filter the images based on that text. You can
                also upload your
                own photos to the gallery. None of the data is saved, it is just for scrolling through pictures for fun.
            </div>
        </div>
        <div class="owner-image-container">
            <img src="../assets/images/naomi.jpg" class="owner-img" alt="owner" loading="lazy" />
        </div>
    </div>
</template>

<style scoped>
.box {
    background: linear-gradient(180deg, #eaeaea 22%, white 89%);
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
    padding: 40px 40px 40px 40px;
}

.owner-image-container {
    width: 35%;
    min-width: 187px;
    min-height: 160px;
    position: relative;
    margin: 10px;
}

.owner-img {
    height: 100%;
    width: 100%;
    object-fit: cover;
    object-position: center;
}

.summary {
    display: flex;
    flex-direction: column;
    justify-content: center;
    width: 60%;
    margin: 10px;
    min-width: 220px;
}

.summary-font {
    line-height: 25px;
}
</style>