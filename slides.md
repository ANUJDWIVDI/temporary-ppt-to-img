---
theme: seriph
background: https://source.unsplash.com/collection/94734566/1920x1080
class: 'text-center'
highlighter: shiki
lineNumbers: false
info: |
  ## Presentation-to-Image Recommender
  A webapp that recommends images for each slide in your presentation.
drawings:
  persist: false
css: unocss
---

# Presentation-to-Image Recommender

Enhance your slides with AI-powered image recommendations

<div class="pt-12">
  <span @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10">
    Press Space for next page <carbon:arrow-right class="inline"/>
  </span>
</div>

---

# How It Works

1. Enter your slide content
2. Our AI analyzes the text
3. Receive tailored image recommendations
4. Choose the perfect image for your slide

<ImageRecommender />

---

# Benefits

- Save time searching for images
- Ensure visual coherence across your presentation
- Discover unique and relevant visuals
- Enhance audience engagement

---

# Get Started

1. Write your slide content in the text area
2. Click "Get Recommendations"
3. Browse through suggested images
4. Select your favorite image

<div class="text-center mt-10">
  <button @click="$slidev.nav.next" class="px-4 py-2 rounded bg-blue-500 text-white">
    Try It Now
  </button>
</div>

---
layout: center
class: text-center
---

# Start Creating Amazing Presentations

Elevate your slides with AI-powered image recommendations

[Get Started](#) · [Learn More](https://github.com/yourusername/presentation-image-recommender)