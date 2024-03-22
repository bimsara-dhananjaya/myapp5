<template>
  <q-page>
    <q-container>
      <q-header>
        <q-toolbar>
          <q-toolbar-title>Upload and Display Image</q-toolbar-title>
        </q-toolbar>
      </q-header>

      <!-- Image upload -->
      <q-card class="q-mt-md" v-if="!imageUrl">
        <q-card-section>
          <q-file
            v-model="uploadedFile"
            label="Choose an image file"
            accept=".jpg,.jpeg,.png"
          ></q-file>
        </q-card-section>
      </q-card>

      <!-- Show button -->
      <q-card v-if="uploadedFile && !imageUrl">
        <q-card-section>
          <q-btn @click="showImage" label="Show Image" color="primary"></q-btn>
          <q-btn @click="cancelUpload" label="Cancel" color="negative"></q-btn>
        </q-card-section>
      </q-card>

      <!-- Image display -->
      <q-card v-if="imageUrl">
        <q-card-section>
          <q-img
            :src="imageUrl"
            caption="Original Image"
            :style="{ maxWidth: imageSize + 'px', maxHeight: imageSize + 'px' }"
          ></q-img>
        </q-card-section>
        <q-card-section v-if="grayscaleImageUrl">
          <q-img
            v-if="!showGrayscale"
            :src="imageUrl"
            caption="Color Image"
            :style="{ maxWidth: imageSize + 'px', maxHeight: imageSize + 'px' }"
          ></q-img>
          <q-img
            v-else
            :src="grayscaleImageUrl"
            caption="Grayscale Image"
            :style="{ maxWidth: imageSize + 'px', maxHeight: imageSize + 'px' }"
          ></q-img>
        </q-card-section>
        <q-card-section>
          <q-slider
            v-model="imageSize"
            label="Image Size (px)"
            :min="50"
            :max="500"
            :step="10"
          ></q-slider>
          <q-btn
            @click="toggleGrayscale"
            :label="showGrayscale ? 'Show Color Image' : 'Show Grayscale Image'"
            color="primary"
          ></q-btn>
          <q-btn
            @click="generateGrayscale"
            label="Generate Grayscale"
            color="primary"
          ></q-btn>
        </q-card-section>
        <q-card-section v-if="histogramData.length > 0">
          <canvas ref="histogramCanvas" width="300" height="150"></canvas>
        </q-card-section>
        <q-card-section>
          <q-btn @click="cancelUpload" label="Cancel" color="negative"></q-btn>
        </q-card-section>
      </q-card>
    </q-container>
  </q-page>
</template>

<script>
export default {
  name: "UploadAndDisplayImage",
  data() {
    return {
      uploadedFile: null,
      imageUrl: "",
      imageSize: 300, // Initial image size
      grayscaleImageUrl: "",
      histogramData: [], // Array to store histogram data
      showGrayscale: false,
    };
  },
  methods: {
    showImage() {
      const reader = new FileReader();
      reader.onload = () => {
        this.imageUrl = reader.result;
      };
      reader.readAsDataURL(this.uploadedFile);
    },
    cancelUpload() {
      this.uploadedFile = null;
      this.imageUrl = "";
      this.grayscaleImageUrl = ""; // Reset grayscale image URL
      this.histogramData = []; // Reset histogram data
      this.showGrayscale = false;
    },
    toggleGrayscale() {
      this.showGrayscale = !this.showGrayscale;
      this.drawHistogram(); // Redraw histogram when toggling grayscale
    },
    generateGrayscale() {
      const canvas = document.createElement("canvas");
      const ctx = canvas.getContext("2d");
      const image = new Image();
      image.onload = () => {
        canvas.width = image.width;
        canvas.height = image.height;
        ctx.drawImage(image, 0, 0);
        const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
        const data = imageData.data;
        const histogram = new Array(256).fill(0); // Initialize histogram array
        if (!this.grayscaleImageUrl) {
          // Apply grayscale conversion only if grayscale image is not generated
          for (let i = 0; i < data.length; i += 4) {
            const gray = Math.round(
              0.299 * data[i] + 0.587 * data[i + 1] + 0.114 * data[i + 2]
            );
            histogram[gray]++; // Increment histogram bin
            data[i] = data[i + 1] = data[i + 2] = gray; // Set RGB to grayscale value
          }
          this.histogramData = histogram;
        }
        // Generate grayscale image
        ctx.putImageData(imageData, 0, 0);
        this.grayscaleImageUrl = canvas.toDataURL();
        // Draw histogram
        this.drawHistogram();
      };
      image.src = this.imageUrl;
    },
    drawHistogram() {
      const canvas = this.$refs.histogramCanvas;
      const ctx = canvas.getContext("2d");
      const histogramData = this.histogramData;
      const barWidth = canvas.width / histogramData.length;
      const maxFrequency = Math.max(...histogramData);
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      ctx.fillStyle = "black";
      for (let i = 0; i < histogramData.length; i++) {
        const x = i * barWidth;
        const height = (histogramData[i] / maxFrequency) * canvas.height;
        const y = canvas.height - height;
        ctx.fillRect(x, y, barWidth, height);
      }
    },
  },
};
</script>

<style>
/* Add your custom styles here */
</style>
