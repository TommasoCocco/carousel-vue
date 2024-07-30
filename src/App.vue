<script setup lang="ts">
import Carousel from './components/Carousel.vue';

const cards = [
  {
    img: "src/assets/images/Colorful.jpeg",
    title: "Card 1",
    content: "Card content",
  },
  {
    img: "src/assets/images/e89fc829-8542-4077-b990-7ae60a341608.jpeg",
    title: "Card 2",
    content: "Card content",
  },
  {
    img: "src/assets/images/foto-11.jpg",
    title: "Card 3",
    content: "Card content",
  },
  {
    img: "src/assets/images/36dd9c91-4230-4731-a476-1605822ad829.jpeg",
    title: "Card 4",
    content: "Card content",
  },
  {
    img: "src/assets/images/38a95cfc-df90-44d5-978a-d9f6498d931f.jpeg",
    title: "Card 5",
    content: "Card content",
  },
  {
    img: "src/assets/images/foto-1.jpg",
    title: "Card 6",
    content: "Card content",
  },
  {
    img: "src/assets/images/foto-7.jpg",
    title: "Card 7",
    content: "Card content",
  },
  {
    img: "src/assets/images/foto-10.jpg",
    title: "Card 8",
    content: "Card content",
  },
  {
    img: "src/assets/images/8eb0bc14-e03c-407f-b199-0de77dfc4de2.jpeg",
    title: "Card 9",
    content: "Card content",
  }
]

const prevSlide = () => {
  console.log("button-left click")
}

const nextSlide = () => {
  console.log("button-right click")
}

</script> 


<!-- questa é una prova di commit -->
<!-- questa é una prova di commit -->
<template>

  <Carousel @prev-slide="prevSlide" @next-slide="nextSlide" :cards></Carousel>

</template>

<style scoped></style>
