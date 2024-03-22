<template>
  <q-page class="flex flex-center">
    <div class="carousel-container">
      <q-carousel
        navigation
        infinite
        autoplay
        arrows
        control-color="white"
        class="carousel"
      >
        <q-carousel-slide v-for="(image, index) in carouselImages" :key="index">
          <img :src="image.src" :alt="image.alt" class="carousel-image" />
        </q-carousel-slide>
      </q-carousel>
    </div>
  </q-page>
</template>

<script setup>
const carouselImages = [
  {
    src: "https://www.getyourguide.com/dambulla-l104454/sri-lanka-10-days-tour-package-best-of-sri-lanka-t541515/?ranking_uuid=f1319201-0a86-4983-9083-9912a59dd262",
    alt: "Image 1",
  },
  { src: "https://via.placeholder.com/800x400", alt: "Image 2" },
  { src: "https://via.placeholder.com/800x400", alt: "Image 3" },
];
</script>

<style scoped>
.carousel-container {
  position: relative;
  width: 100%;
  max-width: 800px; /* Adjust the maximum width as needed */
}

.carousel-image {
  width: 100%;
  height: auto;
}
</style>
