<template>
  <main class="p-[40px] h-[100vh] w-full flex items-center main-container">
    <div
      class="container grid grid-cols-1 md:grid-cols-2 max-w-[800px] mx-auto p-[30px] rounded-3xl shadow-2xl backdrop-blur-lg bg-slate-50/10">
      <div class="header md:col-span-2 py-5">
        <h1 class="text-[30px] font-bold text-center">Vue Image Editor</h1>
      </div>
      <div class="editor min-w-[300px] pr-8">
        <label class="title mb-3 inline-block text-[30px] font-bold">Filters:</label>
        <div class="slider">
          <div class="filter-info">
            <p class="name">Brighteness:</p>
            <p class="value text-center">{{ brighteness }}%</p>
          </div>
          <input type="range" v-model="brighteness" min="0" max="200" class="w-full">
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
      </div>
      <div class="image w-full flex flex-col justify-center">
        <img ref="image" :src="imageSrc" alt="Image" class="w-full rounded-xl mb-5">
        <div class="ctas">
          <input type="file" class="mb-3" @change="changeImageSrc" enctype="multipart/form-data">
          <button @click="saveImage" ref="link"
            class="w-[50%] block rounded-lg mb-5 px-[15px] py-[5px] bg-blue-600 text-white"
            download="image.jpg">Descargar</button>
        </div>
      </div>
    </div>
  </main>
</template>

<script>
import defaultImage from "./assets/default.jpg";

export default {
  data() {
    return {
      imageSrc: defaultImage,
      brighteness: 100,
      saturation: 100,
      inversion: 0,
      grayscale: 0
    }
  },
  watch: {
    brighteness(newVal, oldVal) {
      this.$refs.image.style.filter = `brightness(${newVal}%) saturate(${this.saturation}%) invert(${this.inversion}%) grayscale(${this.grayscale}%)`;
    },
    saturation(newVal, oldVal) {
      this.$refs.image.style.filter = `brightness(${this.brighteness}%) saturate(${newVal}%) invert(${this.inversion}%) grayscale(${this.grayscale}%)`;
    },
    inversion(newVal, oldVal) {
      this.$refs.image.style.filter = `brightness(${this.brighteness}%) saturate(${this.saturation}%) invert(${newVal}%) grayscale(${this.grayscale}%)`;
    },
    grayscale(newVal, oldVal) {
      this.$refs.image.style.filter = `brightness(${this.brighteness}%) saturate(${this.saturation}%) invert(${this.inversion}%) grayscale(${newVal}%)`;
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
      // if (rotate !== 0) {
      //   ctx.rotate(rotate * Math.PI / 180);
      // }
      // ctx.scale(flipHorizontal, flipVertical);
      ctx.drawImage(this.$refs.image, -canvas.width / 2, -canvas.height / 2, canvas.width, canvas.height);

      // this.$refs.link.download = "image.jpg"
      // this.$refs.link.href = canvas.toDataURL()

      const link = document.createElement("a");
      link.download = "image.jpg";
      link.href = canvas.toDataURL();
      link.click();
    }
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
</style>