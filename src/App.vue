<template>
  <main class="md:p-[40px] p-[20px] min-h-[100vh] w-full flex items-center main-container">
    <div
      class="container grid grid-cols-1 md:grid-cols-2 max-w-[800px] mx-auto p-[30px] rounded-3xl shadow-2xl backdrop-blur-lg bg-slate-50/10">
      <div class="header md:col-span-2 py-5">
        <h1 class="text-[30px] font-bold text-center">Vue Image Editor</h1>
      </div>
      <div class="editor min-w-[300px] pr-8 flex flex-col justify-center">
        <label class="title mb-3 inline-block text-[30px] font-bold">Filters:</label>
        <div class="slider">
          <div class="filter-info">
            <p class="name">brightness:</p>
            <p class="value text-center">{{ brightness }}%</p>
          </div>
          <input type="range" v-model="brightness" min="0" max="200" class="w-full">
        </div>

        <div class="slider">
          <div class="filter-info">
            <p class="name">Saturation:</p>
            <p class="value text-center">{{ saturation }}%</p>
          </div>
          <input type="range" v-model="saturation" min="0" max="200" class="w-full">
        </div>

        <div class="slider">
          <div class="filter-info">
            <p class="name">Inversion:</p>
            <p class="value text-center">{{ inversion }}%</p>
          </div>
          <input type="range" v-model="inversion" min="0" max="200" class="w-full">
        </div>

        <div class="slider">
          <div class="filter-info">
            <p class="name">Grayscale:</p>
            <p class="value text-center">{{ grayscale }}%</p>
          </div>
          <input type="range" v-model="grayscale" min="0" max="200" class="w-full">
        </div>

        <div class="transform my-5 flex justify-center items-center gap-4">
          <button class="w-[30px] h=[30px] bg-blue-600 rounded-md p-1" @click="rotateImage">
            <RotateIcon />
          </button>
          <button class="w-[30px] h=[30px] bg-blue-600 rounded-md p-1" @click="flipHorizontalImage">
            <FlipIcon />
          </button>
          <button class="w-[30px] h=[30px] bg-blue-600 rounded-md p-1 rotate-90" @click="flipVerticalImage">
            <FlipIcon />
          </button>
        </div>
      </div>
      <div class="image w-full flex flex-col justify-center">
        <figure class="md:w-[365px] md:h-[365px] w-[265px] h-[265px] mb-5">
          <img ref="image" :src="imageSrc" alt="Image" class="w-[100%] h-[100%] object-contain rounded-xl" loading="lazy" width="400" height="400">
        </figure>
        <div class="ctas">
          <input type="file" class="mb-3" @change="changeImageSrc" enctype="multipart/form-data">
          <div class="flex justify-between">
            <button @click="resetImage"
              class="w-[48%] inline-block rounded-lg mb-5 px-[15px] py-[5px] bg-white text-blue-600"
              download="image.jpg">Reset</button>
            <button @click="saveImage" ref="link"
              class="w-[48%] inline-block rounded-lg mb-5 px-[15px] py-[5px] bg-blue-600 text-white"
              download="image.jpg">Descargar</button>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>

<script>
import defaultImage from "./assets/default.jpg";
import FlipIcon from "./components/FlipIcon.vue";
import RotateIcon from "./components/RotateIcon.vue";

export default {
  data() {
    return {
      imageSrc: defaultImage,
      brightness: 100,
      saturation: 100,
      inversion: 0,
      grayscale: 0,
      rotate: 0,
      flip: 0,
      flipHorizontal: 1,
      flipVertical: 1
    }
  },
  watch: {
    brightness(newVal, oldVal) {
      this.$refs.image.style.filter = `brightness(${newVal}%) saturate(${this.saturation}%) invert(${this.inversion}%) grayscale(${this.grayscale}%)`;
    },
    saturation(newVal, oldVal) {
      this.$refs.image.style.filter = `brightness(${this.brightness}%) saturate(${newVal}%) invert(${this.inversion}%) grayscale(${this.grayscale}%)`;
    },
    inversion(newVal, oldVal) {
      this.$refs.image.style.filter = `brightness(${this.brightness}%) saturate(${this.saturation}%) invert(${newVal}%) grayscale(${this.grayscale}%)`;
    },
    grayscale(newVal, oldVal) {
      this.$refs.image.style.filter = `brightness(${this.brightness}%) saturate(${this.saturation}%) invert(${this.inversion}%) grayscale(${newVal}%)`;
    },
  },
  methods: {
    changeImageSrc(event) {
      const file = event.target.files[0];
      if (!file) return;
      this.imageSrc = URL.createObjectURL(file);
    },
    saveImage() {
      const canvas = document.createElement("canvas");
      const ctx = canvas.getContext("2d");
      canvas.width = this.$refs.image.naturalWidth;
      canvas.height = this.$refs.image.naturalHeight;

      ctx.filter = `brightness(${this.brightness}%) saturate(${this.saturation}%) invert(${this.inversion}%) grayscale(${this.grayscale}%)`;
      ctx.translate(canvas.width / 2, canvas.height / 2);
      if (this.rotate !== 0) {
        ctx.rotate(this.rotate * Math.PI / 180);
      }
      ctx.scale(this.flipHorizontal, this.flipVertical);
      ctx.drawImage(this.$refs.image, -canvas.width / 2, -canvas.height / 2, canvas.width, canvas.height);

      const link = document.createElement("a");
      link.download = "image.jpg";
      link.href = canvas.toDataURL();
      link.click();
    },
    resetImage() {
      this.imageSrc = defaultImage
    },
    rotateImage() {
      this.rotate = this.rotate + 90;
      this.$refs.image.style.transform = `rotate(${this.rotate}deg)`
    },
    flipHorizontalImage() {
      this.flipHorizontal = this.flipHorizontal * -1;
      this.$refs.image.style.transform = `scaleX(${this.flipHorizontal})`
    },
    flipVerticalImage() {
      this.flipVertical = this.flipVertical * -1;
      this.$refs.image.style.transform = `scaleY(${this.flipVertical})`
    }
  },
  components: {
    FlipIcon,
    RotateIcon
  }
}
</script>

<style scoped>
.main-container {
  background: rgb(124, 166, 252);
  background: linear-gradient(90deg, rgba(124, 166, 252, 1) 0%, rgba(119, 235, 129, 1) 100%);
}

.container {
  border-left: 2px solid rgba(255, 255, 255, 0.3);
  border-bottom: 2px solid rgba(255, 255, 255, 0.3);
}

input::-webkit-file-upload-button {
  background-color: #0075FF;
  border: none;
  border-radius: 8px;
  padding: 5px 15px;
  color: #fff;
}

svg {
  width: 100%;
  height: 100%;
  object-fit: contain;
  fill: #fff;
}
</style>