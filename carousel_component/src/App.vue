<template>
  <div>
  <h1>Default</h1>
  <carousel class="my-carousel">
    <carousel-item class="slide1">Page1</carousel-item>
    <carousel-item class="slide2">Page2</carousel-item>
    <carousel-item class="slide3">Page3</carousel-item>
    <carousel-item class="slide4">Page4</carousel-item>
  </carousel>
  <br>
  <h1>Default Picture</h1>
  <carousel class="my-carousel">
    <carousel-item class="slide1"><a href="#"><img alt="Page1"/></a></carousel-item>
    <carousel-item class="slide2"><a href="#"><img alt="Page2"/></a></carousel-item>
    <carousel-item class="slide3"><a href="#"><img alt="Page3"/></a></carousel-item>
    <carousel-item class="slide4"><a href="#"><img alt="Page4"/></a></carousel-item>
  </carousel>
  <br>
  <h1>speed = 30ms</h1>
  <carousel class="my-carousel" :speed="30">
    <carousel-item class="slide1">Page1</carousel-item>
    <carousel-item class="slide2">Page2</carousel-item>
    <carousel-item class="slide3">Page3</carousel-item>
    <carousel-item class="slide4">Page4</carousel-item>
  </carousel>
  <br>
  <h1>timeDelta = 1000ms</h1>
  <carousel class="my-carousel" :timeDelta="1000">
    <carousel-item class="slide1">Page1</carousel-item>
    <carousel-item class="slide2">Page2</carousel-item>
    <carousel-item class="slide3">Page3</carousel-item>
    <carousel-item class="slide4">Page4</carousel-item>
  </carousel>
  <br>
  <h1>loop = false</h1>
  <carousel class="my-carousel" :loop="false">
    <carousel-item class="slide1">Page1</carousel-item>
    <carousel-item class="slide2">Page2</carousel-item>
    <carousel-item class="slide3">Page3</carousel-item>
    <carousel-item class="slide4">Page4</carousel-item>
  </carousel>
  <br>
  <h1>noDragWhenSingle = true</h1>
  <carousel class="my-carousel">
    <carousel-item class="slide1">Single true</carousel-item>
  </carousel>
  <br>
  <h1>noDragWhenSingle = false</h1>
  <carousel class="my-carousel" :noDragWhenSingle="false">
    <carousel-item class="slide1">Single true</carousel-item>
  </carousel>
  <br>
</div>
</template>

<script>
import {Carousel, CarouselItem} from './components/carousel.js'
export default {
  components: {
    carousel: Carousel,
    carouselItem: CarouselItem
  }
}

</script>

<style scoped>
.my-carousel{
  height: 200px;
  color:#fff;
  font-size: 30px;
  text-align: center;
}
.slide1{
  background: #0089dc;
  color: #fff;
}
.slide2{
  background: #ffd705;
  color: #000;
}
.slide3{
  background: #ff2d4b;
  color: #fff;
}
.slide4{
  background: #00ff00;
  color: #000;
}
</style>
