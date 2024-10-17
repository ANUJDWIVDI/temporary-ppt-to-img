<template>
  <div class="image-recommender">
    <textarea v-model="slideContent" placeholder="Enter your slide content here..." rows="4" class="w-full p-2 border rounded"></textarea>
    <button @click="getRecommendations" class="mt-2 px-4 py-2 bg-blue-500 text-white rounded">Get Recommendations</button>
    <div v-if="loading" class="mt-4">Loading recommendations...</div>
    <div v-if="error" class="mt-4 text-red-500">{{ error }}</div>
    <div v-if="recommendations.length" class="mt-4 grid grid-cols-3 gap-4">
      <div v-for="(image, index) in recommendations" :key="index" class="image-item">
        <img :src="image.url" :alt="image.alt" class="w-full h-32 object-cover rounded">
        <button @click="selectImage(image)" class="mt-1 px-2 py-1 bg-green-500 text-white rounded text-sm">Select</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'

const slideContent = ref('')
const recommendations = ref([])
const loading = ref(false)
const error = ref('')

const API_KEY = 'AIzaSyCIb1GEyb2zm5k4bfat3SEAIwSkyIwzF58'
const API_URL = 'https://generativelanguage.googleapis.com/v1beta/models/gemini-pro:generateContent'

const getRecommendations = async () => {
  if (!slideContent.value.trim()) {
    error.value = 'Please enter some slide content.'
    return
  }

  loading.value = true
  error.value = ''
  recommendations.value = []
  
  try {
    console.log('Sending request to Gemini API...')
    const response = await axios.post(
      `${API_URL}?key=${API_KEY}`,
      {
        contents: [
          {
            parts: [
              {
                text: `Suggest 3 image descriptions for a presentation slide with the following content: "${slideContent.value}". For each image, provide a brief description and a relevant keyword for image search. Format the response as a JSON array with objects containing 'description' and 'keyword' properties.`
              }
            ]
          }
        ]
      }
    )
    console.log('Received response from Gemini API:', JSON.stringify(response.data, null, 2))

    if (!response.data || !response.data.candidates || !response.data.candidates[0] || !response.data.candidates[0].content || !response.data.candidates[0].content.parts || !response.data.candidates[0].content.parts[0].text) {
      throw new Error('Unexpected API response structure')
    }

    const generatedText = response.data.candidates[0].content.parts[0].text
    console.log('Generated text:', generatedText)
    const imageDescriptions = JSON.parse(generatedText)

    if (!Array.isArray(imageDescriptions) || imageDescriptions.length === 0) {
      throw new Error('Invalid or empty image descriptions')
    }

    console.log('Parsed image descriptions:', JSON.stringify(imageDescriptions, null, 2))

    recommendations.value = await Promise.all(imageDescriptions.map(async (item) => {
      if (!item.keyword) {
        throw new Error('Missing keyword in image description')
      }
      console.log('Fetching image for keyword:', item.keyword)
      const imageResponse = await axios.get(`https://source.unsplash.com/featured/?${encodeURIComponent(item.keyword)}`)
      console.log('Image URL:', imageResponse.request.responseURL)
      return {
        url: imageResponse.request.responseURL,
        alt: item.description || 'Image description not available'
      }
    }))

    console.log('Final recommendations:', JSON.stringify(recommendations.value, null, 2))
  } catch (err) {
    console.error('Detailed error:', {
      message: err.message,
      stack: err.stack,
      response: err.response ? {
        data: err.response.data,
        status: err.response.status,
        headers: err.response.headers
      } : 'No response data',
      request: err.request ? 'Request made but no response received' : 'No request made',
      config: err.config
    })
    error.value = `Failed to fetch recommendations: ${err.message || 'Unknown error'}. Please check the console for more details and try again.`
  } finally {
    loading.value = false
  }
}

const selectImage = (image) => {
  console.log('Selected image:', image)
  alert(`Image selected: ${image.alt}`)
}
</script>