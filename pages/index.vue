<template>
    <div class="container w-100 vh-100 d-flex flex-column justify-content-center align-items-center">
    <h1 class="mb-4">Image Generator</h1>
      <input
        type="text"
        class="form-control mb-3"
        placeholder="Enter Your Prompt"
        v-model="prompt"
      >
      <button class="btn btn-outline-dark mb-3" @click="generateImage" :disabled="loading">{{ loading ? "Generating..." : "Generate"}}</button>
      <img v-if="imageDataUrl" :src="imageDataUrl" alt="Generated Image" class="mb-3 img-fluid" id="generated-image"/>
      <button v-if="imageDataUrl" class="btn btn-success" @click="downloadImage">Download Image</button>
    </div>
  </template>
  
  <script setup>
  import { ref } from "vue";
  import { HfInference } from "@huggingface/inference";
  
  let prompt = ref("");
  let imageDataUrl = ref("");
  let loading = ref(false);
  
  const generateImage = async () => {
    loading.value = true;
    const TOKEN = "Your_Token_Here";
    const hf = new HfInference(TOKEN);
  
    try {
      const blob = await hf.textToImage({
        inputs: prompt.value,
        model: "stabilityai/stable-diffusion-xl-base-1.0",
      });
      imageDataUrl.value = URL.createObjectURL(blob);
      loading.value = false;
    } catch (error) {
      console.error("Error:", error.message);
      loading.value = false;
    }
  };
  
  const downloadImage = async () => {
    const downloadLink = document.createElement("a");
    downloadLink.href = await imageDataUrl.value;
    downloadLink.download = "output_image.png";
    downloadLink.click();
    alert("Image download initiated successfully!");
  };
  </script>
<style scoped>
    #generated-image{
        width: 500px;
        object-fit: contain;
    }
</style>
