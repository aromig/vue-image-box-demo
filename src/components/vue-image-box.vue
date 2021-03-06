<template>
  <transition name="image-box">
    <div
      class="imgBox"
      @click="close"
      v-if="imageIndex !== null"
      :style="imgBox_style"
    >
      <button type="button" class="imgBox__close" @click="close">
        &times;
      </button>
      <button
        type="button"
        class="imgBox__previous"
        v-if="hasMultipleImages"
        @click.stop="previousImage"
      >
        &lsaquo;
      </button>
      <div class="imgBox__container" v-if="images">
        <figure>
          <transition name="image-fade">
            <img
              :src="imageUrl"
              :key="imageUrl"
              @click.stop="nextImage"
              :title="imageCaption"
              :alt="imageCaption"
            />
          </transition>
          <figcaption>
            {{ imageCaption }}
          </figcaption>
        </figure>
      </div>
      <button
        type="button"
        class="imgBox__next"
        v-if="hasMultipleImages"
        @click.stop="nextImage"
      >
        &rsaquo;
      </button>
    </div>
  </transition>
</template>

<script>
export default {
  props: ["images", "index", "bgcolor"],
  data() {
    return {
      imageIndex: this.index
    };
  },
  mounted() {
    window.addEventListener("keydown", e => {
      if (e.keyCode === 37) {
        // Left Arrow
        this.previousImage();
      }
      if (e.keyCode === 39) {
        // Right Arrow
        this.nextImage();
      }
      if (e.keyCode === 27) {
        // Escape Key
        this.close();
      }
    });

    window.addEventListener("click", e => {
      if (this.imageIndex === 0) {
        this.preLoad(
          this.images[this.images.length - 1].imageUrl,
          this.images[this.imageIndex + 1].imageUrl
        );
      } else if (this.imageIndex === this.images.length - 1) {
        this.preLoad(
          this.images[this.imageIndex - 1].imageUrl,
          this.images[0].imageUrl
        );
      } else {
        this.preLoad(
          this.images[this.imageIndex - 1].imageUrl,
          this.images[this.imageIndex + 1].imageUrl
        );
      }
    });

    this.preLoad(this.images[0].imageUrl);
  },
  watch: {
    index(value) {
      this.imageIndex = value;
    }
  },
  methods: {
    close: function() {
      this.imageIndex = null;
      this.$emit("close");
    },
    previousImage: function() {
      if (this.imageIndex === null) return;

      this.imageIndex =
        this.imageIndex > 0 ? this.imageIndex - 1 : this.images.length - 1;

      if (this.imageIndex > 0) {
        this.preLoad(this.images[this.imageIndex - 1].imageUrl);
      } else {
        this.preLoad(this.images[this.images.length - 1].imageUrl);
      }
    },
    nextImage: function() {
      if (this.imageIndex === null) return;

      this.imageIndex =
        this.imageIndex < this.images.length - 1 ? this.imageIndex + 1 : 0;

      if (this.imageIndex < this.images.length - 1) {
        this.preLoad(this.images[this.imageIndex + 1].imageUrl);
      } else {
        this.preLoad(this.images[0].imageUrl);
      }
    },
    preLoad: function(...urls) {
      let preloaded = [];
      for (let idx = 0; idx < urls.length; idx++) {
        preloaded[idx] = new Image();
        preloaded[idx].src = urls[idx];
      }
    }
  },
  computed: {
    imageUrl() {
      return this.imageIndex !== null
        ? this.images[this.imageIndex].imageUrl
        : "";
    },
    imageCaption() {
      return this.imageIndex !== null
        ? this.images[this.imageIndex].caption
        : "";
    },
    hasMultipleImages() {
      return this.images.length > 1;
    },
    imgBox_style() {
      let style = "";
      if (this.bgcolor !== null) style += `background-color: ${this.bgcolor};`;
      return style;
    }
  }
};
</script>

<style lang="scss">
$black: #000;
$white: #fff;
$modal__bg: rgba($black, 0.9);

/* Modal Styles */
@mixin modal__base() {
  transition: opacity 0.2s ease;
  position: fixed;
  z-index: 1000;
  top: 0;
  left: 0;
  width: 100%;
  min-height: 100%;
  height: 100vh;
  background-color: $modal__bg;
  display: table;
}

// Modal Entrance Transition
.image-box-enter {
  opacity: 0;
}

// Modal Container & Image
.imgBox {
  @include modal__base();
  &__container {
    position: absolute;
    overflow: hidden;
    cursor: pointer;
    max-width: 100vw;
    height: 100vh;
    margin: 1rem auto;
    left: 0.5rem;
    right: 0.5rem;
  }
  & figure {
    margin: 0;
    height: 100%;
    img {
      margin-top: 3rem;
      height: 100%;
      width: 100%;
      height: calc(100% - 4.5rem);
      object-fit: contain;
    }
    figcaption {
      position: absolute;
      top: 0;
      width: 100%;
      line-height: 2.5rem;
      background-color: rgba(0, 0, 0, 0.25);
      color: $white;
    }
  }
  &__close {
    color: $white;
    position: absolute;
    top: 5px;
    right: 0;
    background-color: transparent;
    border: none;
    font-size: 48px;
    width: 50px;
    height: 50px;
    cursor: pointer;
    z-index: 900;
    &:focus {
      outline: 0;
    }
  }
  &__previous,
  &__next {
    position: absolute;
    top: 50%;
    margin-top: -25px;
    width: 50px;
    height: 50px;
    z-index: 900;
    cursor: pointer;
    color: $white;
    font-size: 64px;
    line-height: 64px;
    background-color: transparent;
    border: none;
    &:focus {
      outline: 0;
    }
  }
  &__previous {
    left: 0;
  }
  &__next {
    right: 0;
  }
}

// Image Change Transition
.image-fade-enter {
  opacity: 0;
}

.image-fade-enter-active {
  transition: all 0.5s ease;
}
</style>
