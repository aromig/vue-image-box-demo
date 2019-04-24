<template>
  <div id="app">
    <h1>Vue Image Box</h1>
    <div class="gallery">
      <img
        v-for="(image, idx) in images"
        :key="idx"
        :src="image"
        @click="onClick(idx)"
        class="gallery__image"
      />
      <ImageBox
        :images="images"
        :index="index"
        @close="index = null"
      ></ImageBox>
    </div>
    <img src="@/assets/logo.png" alt="Vue Logo" class="vue-logo" />
  </div>
</template>

<script>
import ImageBox from "@/components/vue-image-box.vue";

export default {
  name: "app",
  components: {
    ImageBox
  },
  methods: {
    onClick: function(idx) {
      this.index = idx;
    }
  },
  data() {
    return {
      index: null,
      images: [
        "https://placekitten.com/800/600",
        "https://placekitten.com/825/600",
        "https://placekitten.com/803/600",
        "https://placekitten.com/833/600",
        "https://placekitten.com/831/600",
        "https://placekitten.com/806/600",
        "https://placekitten.com/855/600",
        "https://placekitten.com/847/600",
        "https://placekitten.com/832/600",
        "https://placekitten.com/883/600",
        "https://placekitten.com/873/600",
        "https://placekitten.com/895/600"
      ]
    };
  }
};
</script>

<style lang="scss">
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

.vue-logo {
  display: block;
  width: 50px;
  margin: 1rem auto;
}

.gallery {
  display: grid;
  grid-gap: 20px;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  grid-auto-rows: auto;
  grid-auto-flow: row;
  &__image {
    width: 100%;
    height: 100%;
    object-fit: cover;
    box-shadow: -2px 2px 10px 0px rgba(#444, 0.4);
    transition: transform 0.3s ease-in-out;
    cursor: pointer;
    &:hover {
      transform: scale(1.05);
    }
  }
}

@media (max-width: 500px) {
  .gallery {
    grid-template-columns: repeat(1, 0.75fr);
    grid-template-rows: repeat(1, minmax(0.75fr, 75%));
    justify-content: center;
  }
}
</style>
