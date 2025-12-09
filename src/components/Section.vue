<template>
  <section 
    class="cards-carousel-section"
    id ="uygulama" 
    :style="{ '--bg-image': `url(${bgImage})` }"
  >
    <div class="carousel-wrapper">
      <button 
        class="nav-arrow left" 
        @click="prevCard" 
        :disabled="currentIndex === 0" 
        aria-label="Önceki kart"
      >
        <i class="fa-solid fa-angle-left"></i>
      </button>

      <div class="carousel-container">
        <div 
          class="cards-track" 
          :style="{ 
            transform: `translateX(-${currentIndex * (cardWidth + gap)}px)`,
            '--card-width': `${cardWidth}px`,
            '--card-gap': `${gap}px`
          }"
        >
          <div 
            class="card clickable-card" 
            v-for="(card, index) in cards" 
            :key="index"
            @click="handleCardClick(card, index)"
            role="button" 
            tabindex="0"
          >
            <div class="card-image">
              <img :src="card.image" :alt="card.title" loading="lazy" />
            </div>

            <div class="card-content">
              <h3>{{ card.title }}</h3>
              <p>{{ card.description }}</p>

              <!-- GÜNCEL CAD İNDİRME BUTONU -->
              <button 
                class="download-button"
                @click.stop="downloadCad(card.cad)"
              >
                <i class="fa-solid fa-download"></i>
                CAD'i İndir
              </button>

            </div>
          </div>
        </div>
      </div>

      <button 
        class="nav-arrow right" 
        @click="nextCard" 
        :disabled="currentIndex === maxIndex" 
        aria-label="Sonraki kart"
      >
        <i class="fa-solid fa-angle-right"></i>
      </button>
    </div>
  </section>

  <PatternAnalyzer 
    :is-open="isAnalyzerOpen"
    :image-src="selectedImage"
    :title="selectedTitle"
    @close="isAnalyzerOpen = false"
  />
</template>

<script setup>
import '@fortawesome/fontawesome-free/css/all.css'
import { ref, computed } from 'vue'
import PatternAnalyzer from './PatternAnalyzer.vue'

const emit = defineEmits(['card-click'])

const cardWidth = 220
const gap = 20
const visibleCards = 5

const currentIndex = ref(0)

const isAnalyzerOpen = ref(false)
const selectedImage = ref('')
const selectedTitle = ref('')

import bgImage from '../assets/background.png'

import foto1 from '../assets/vectorcini/foto1.png'
import foto2 from '../assets/vectorcini/foto2.png'
import foto3 from '../assets/vectorcini/foto3.png'
import foto4 from '../assets/vectorcini/foto4.png'
import foto5 from '../assets/fotocini5.png'
import foto7 from '../assets/vectorcini/foto7.png'
import foto8 from '../assets/vectorcini/foto8.png'
import foto9 from '../assets/vectorcini/foto9.png'
import foto10 from '../assets/vectorcini/foto10.png'
import foto11 from '../assets/vectorcini/foto5.png'

/* → Her kart için CAD dosyası */
const cadFiles = [
  '/cad/pano1.dwg',
  '/cad/pano2.dwg',
  '/cad/pano3.dwg',
  '/cad/pano4.dwg',
  '/cad/pano5.dwg',
  '/cad/pano6.dwg',
  '/cad/pano7.dwg',
  '/cad/pano8.dwg',
  '/cad/pano9.dwg',
  '/cad/pano10.dwg'
]

const cardImages = [foto1, foto2, foto3, foto4, foto5, foto7, foto8, foto9, foto10, foto11]

const cardTitles = [
  'Pano 1', 'Pano 2', 'Pano 3', 'Pano 4', 'Pano 5',
  'Pano 6', 'Pano 7', 'Pano 8', 'Pano 9', 'Pano 10'
]

const cardDescriptions = [
  'Rüstem Paşa Camii - Duvar motif panosu',
  'Rüstem Paşa Camii - Cemaat Mahalli',
  'Rüstem Paşa Camii - Duvar motif panosu',
  'Rüstem Paşa Camii - Kadınlar Mahfili',
  'Rüstem Paşa Camii - Duvar motif panosu',
  'Rüstem Paşa Camii - Duvar motif panosu',
  'Rüstem Paşa Camii - Duvar motif panosu',
  'Rüstem Paşa Camii - Duvar motif panosu',
  'Rüstem Paşa Camii - Duvar motif panosu',
  'Rüstem Paşa Camii - Duvar motif panosu'
]

/* → Kart nesneleri */
const cards = cardImages.map((img, i) => ({
  image: img,
  title: cardTitles[i],
  description: cardDescriptions[i],
  cad: cadFiles[i]
}))

const maxIndex = computed(() => Math.max(0, cards.length - visibleCards))

/* Slider fonksiyonları */
const nextCard = () => {
  currentIndex.value = (currentIndex.value + 1) % (maxIndex.value + 1)
}

const prevCard = () => {
  currentIndex.value = (currentIndex.value - 1 + (maxIndex.value + 1)) % (maxIndex.value + 1)
}

const handleCardClick = (card, index) => {
  selectedImage.value = card.image
  selectedTitle.value = card.title
  isAnalyzerOpen.value = true
  emit('card-click', { card, index })
}

/* GERÇEK İNDİRME FONKSİYONU */
const downloadCad = (filePath) => {
  const link = document.createElement('a')
  link.href = filePath
  link.download = filePath.split('/').pop()
  link.click()
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;500;600;700&display=swap');

.cards-carousel-section {
  background-color: #f7f9fc;
  background-image: var(--bg-image);
  background-size: cover;
  padding: 60px 20px;
  font-family: 'Poppins', sans-serif;
}

.carousel-wrapper {
  position: relative;
  max-width: calc(v-bind(visibleCards) * v-bind(cardWidth) + (v-bind(visibleCards) - 1) * v-bind(gap) * 1px);
  margin: 0 auto;
  display: flex;
  align-items: center;
}

.carousel-container {
  overflow: hidden;
  flex: 1;
  padding: 2rem;
}

.cards-track {
  display: flex;
  transition: transform 0.3s ease;
  gap: var(--card-gap);
}

.card {
  background-color: #ffffff;
  border-radius: 16px;
  min-width: var(--card-width);
  box-shadow: 0 6px 18px rgba(0,0,0,0.08);
  display: flex;
  flex-direction: column;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.clickable-card:hover {
  transform: translateY(-8px);
  box-shadow: 0 14px 30px rgba(0,0,0,0.15);
}

.card-image img {
  width: 100%;
  height: 140px;
  object-fit: cover;
}

.card-content { padding: 20px 15px; text-align: center; padding-bottom: 15px; display: flex; flex-direction: column; align-items: center; } .card-content h3 { font-size: 1.2rem; font-weight: 600; margin-bottom: 8px; color: #2c3e50; } .card-content p { font-size: 0.95rem; color: #7f8c8d; line-height: 1.4; margin-bottom: 15px; }

/* -------------------------------------------------- */
/*               YENİ GRADIENT CAD BUTONU             */
/* -------------------------------------------------- */
.download-button {
  padding: 5px 10px;
  font-size: 0.85rem;
  font-weight: 600;
  background: linear-gradient(45deg, #a071f0, #f39c12);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  border: none;
  background-color: transparent;
  cursor: pointer;
  display: flex;
  align-items: center;
  gap: 5px;
  margin-right: auto;   /* ← BUTONU SOLA ALIR */
}

.download-button i {
  font-size: 0.8rem; /* Küçük ikon */
  color: #a071f0;    /* fallback */
}

.download-button:hover {
  opacity: 0.7;
}
/* -------------------------------------------------- */

.nav-arrow {
  background-color: #0077b6;
  border: none;
  border-radius: 50%;
  width: 45px;
  height: 45px;
  cursor: pointer;
  display: flex;
  justify-content: center;
  align-items: center;
  transition: background-color 0.3s;
  flex-shrink: 0;
}
</style>
