<template>
  <section class="features-section">
    <div class="header-content">
      <h2 class="main-title">Temel Özelliklerimiz</h2>
      <p class="main-subtitle">Motif Analizini Bir Sonraki Seviyeye Taşıyın</p>
    </div>
    
    <div class="features-grid">
      <div 
        v-for="(feature, index) in features" 
        :key="index" 
        class="feature-card"
      >
        <div class="icon-box" :style="{ '--color': feature.color }">
          <i :class="feature.icon"></i>
        </div>
        <h3 class="card-title">{{ feature.title }}</h3>
        <p class="card-description">{{ feature.description }}</p>
      </div>
    </div>
  </section>
</template>

<script setup>
import '@fortawesome/fontawesome-free/css/all.css'

const features = [
  {
    title: 'Gelişmiş Motif Tespiti',
    description: 'Yapay zeka destekli algoritmalarla yüklediğiniz görsellerdeki tekrarlayan desenleri anında algılar ve kutular.',
    icon: 'fa-solid fa-microchip',
    color: '#00bfef' // Mavi
  },
  {
    title: 'Simetri ve Örüntü Analizi',
    description: 'Bir desenin ayna simetrisi, dönme simetrisi veya kayarak yansıma gibi hangi simetri gruplarına ait olduğunu matematiksel olarak hesaplar.',
    icon: 'fa-solid fa-scale-balanced',
    color: '#10b981' // Yeşil
  },
  {
    title: 'Geometrik AI Yorumu',
    description: 'Analiz sonuçlarına dayanarak, eserin geometrik yapısı, kompozisyon dengesi ve sanatsal stili hakkında detaylı bir yorum sunar.',
    icon: 'fa-solid fa-robot',
    color: '#f97316' // Turuncu
  },
];
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;600;700&display=swap');

.features-section {
  padding: 80px 20px;
  background-color: #f7f9fc; /* Hafif gri arka plan */
  font-family: 'Poppins', sans-serif;
  text-align: center;
}

.header-content {
  max-width: 800px;
  margin: 0 auto 50px;
}

.main-title {
  font-size: 2.5rem;
  font-weight: 700;
  color: #0f172a;
  margin-bottom: 10px;
}

.main-subtitle {
  font-size: 1.1rem;
  color: #64748b;
}

.features-grid {
  max-width: 1200px;
  margin: 0 auto;
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 30px;
}

.feature-card {
  background-color: #ffffff;
  padding: 30px;
  border-radius: 16px;
  box-shadow: 0 10px 30px rgba(0,0,0,0.05);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  text-align: center;
  border: 1px solid #e2e8f0;
}

.feature-card:hover {
  transform: translateY(-8px);
  box-shadow: 0 15px 40px rgba(0,0,0,0.1);
  border-color: var(--color, #00bfef); /* Kartın rengine göre border vurgusu */
}

.icon-box {
  --color: #00bfef; /* Varsayılan renk */
  width: 60px;
  height: 60px;
  margin: 0 auto 20px;
  background-color: var(--color);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.8rem;
  color: white;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
}

.card-title {
  font-size: 1.3rem;
  font-weight: 600;
  color: #0f172a;
  margin-bottom: 10px;
}

.card-description {
  font-size: 0.95rem;
  color: #475569;
  line-height: 1.6;
}
</style>