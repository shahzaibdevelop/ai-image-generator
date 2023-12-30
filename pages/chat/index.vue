<template>
    <div>
      <input type="file" @change="handleImageUpload" />
      <button @click="classifyImage" :disabled="loading">Classify Image</button>
      <div v-if="prediction" class="result">Prediction: {{ prediction }}</div>
      <div v-if="loading" class="loading">Loading...</div>
      <div v-if="error" class="error">Error: {{ error }}</div>
    </div>
  </template>
  
  <script setup>
  import { ref } from 'vue';
  import { HfInference } from '@huggingface/inference';
  
  let loading = ref(false);
  let prediction = ref('');
  let error = ref('');
  let imageFile = ref(null);
  
  const handleImageUpload = (event) => {
    // Access the selected image file
    imageFile.value = event.target.files[0];
  };
  
  const classifyImage = async () => {
    loading.value = true;
    error.value = ''; // Reset error message
  
    const TOKEN = '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';
    const hf = new HfInference(TOKEN);
  
    try {
      // Check if an image file is selected
      if (!imageFile.value) {
        throw new Error('Please select an image file.');
      }
  
      // Use the FileReader API to read the image file as a data URL
      const reader = new FileReader();
      reader.onload = async () => {
        try {
          // Replace the model name with your image classification model
          const result = await hf.imageClassification({
            inputs: reader.result,
            model: 'Rajaram1996/FacialEmoRecog',
          });
  
          // Access the prediction result
          prediction.value = result.predictions[0].label;
  
        } catch (error) {
          console.error('Error:', error.message);
          error.value = 'Failed to classify the image.';
        } finally {
          loading.value = false;
        }
      };
      reader.readAsDataURL(imageFile.value);
  
    } catch (error) {
      console.error('Error:', error.message);
      error.value = error.message;
      loading.value = false;
    }
  };
  </script>
  
  <style scoped>
  /* Add your styles here */
  .result {
    margin-top: 10px;
  }
  .loading {
    margin-top: 10px;
    color: #007bff;
  }
  .error {
    margin-top: 10px;
    color: #dc3545;
  }
  </style>
  