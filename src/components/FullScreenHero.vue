<template>
  <section class="hero" :style="heroStyle">
    <div class="overlay">
      <div class="card">
        <h1 class="title">{{ title }}</h1>
        <button class="cta" @click="$emit('click')">{{ buttonText }}</button>
      </div>
    </div>
  </section>
</template>

<script setup>
import { computed } from 'vue'
import homeImage from '../assets/homecini.jpg'

const props = defineProps({
  imageUrl: { type: String, default: homeImage },
  title: { type: String, default: 'Hoşgeldiniz' },
  buttonText: { type: String, default: 'Başla' }
})

const heroStyle = computed(() => ({
  backgroundImage: `url(${props.imageUrl})`
}))
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;700&display=swap'); /* 600 -> 700 */

.hero {
  min-height: 95vh;
  width: 100%;
  background-size: cover;
  background-position: center;
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  font-family: 'Poppins', sans-serif;
}

.overlay {
  position: absolute;
  inset: 0;
  /* Daha modern ve doygun bir mavi-turkuaz */
  background: rgba(0, 150, 200, 0.65);
  display: flex;
  align-items: center;
  justify-content: center;
}

.card {
  background: transparent;
  text-align: center;
  display: flex;
  flex-direction: column;
  /* Buton ile başlık arasına boşluk eklendi */
  gap: 1.5rem; 
  align-items: center;
}

.title {
  color: white;
  font-size: 4rem;
  /* Daha güçlü bir başlık için */
  font-weight: 700; 
  text-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
}

.cta {
  background: white;
  /* Daha canlı bir renk tonu */
  color: #0099cc; 
  border: none;
  padding: 1.2rem 2.5rem;
  border-radius: 999px;
  font-weight: 600; /* Font ağırlığı artırıldı */
  cursor: pointer;
  font-size: 1.3rem; /* Font boyutu biraz küçültüldü */
  /* Daha yumuşak, modern bir gölge */
  box-shadow: 0 8px 16px rgba(0,0,0,0.2); 
  transition: transform 0.2s ease-out, box-shadow 0.2s ease-out, background-color 0.2s;
}

.cta:hover {
  /* Daha belirgin bir hover efekti */
  box-shadow: 0 12px 24px rgba(0, 0, 0, 0.3);
  transform: scale(1.05); /* Hafifçe büyütme */
  background-color: #f0f0f0; /* Hafif bir renk değişimi */
}
</style>