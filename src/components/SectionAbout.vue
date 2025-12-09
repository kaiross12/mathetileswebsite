<template>
  <section class="about-section">
    <div class="content-wrapper">
      <div class="text-content">
        <h2 class="title">Mathetiles : Sanat ve Matematiği Birleştiriyor</h2>
        <p class="subtitle">
          Osmanlı ve İslam sanatındaki gizli geometrik desenleri ve simetriyi keşfetme yolculuğunuz.
        </p>
        <p class="description">
          Mathetiles, geleneksel çini motiflerinin derinliklerine inen, yapay zeka destekli bir analiz platformudur. İlgi gören geleneksel desenleri; **simetri grupları**, **dönme merkezleri** ve **periyodik örüntüleme** gibi matematiksel prensiplere göre inceler. Bu sayede, yüzyıllardır süregelen estetik dehanın ardındaki karmaşık geometrik yapıyı görünür kılar. Sanat tarihçileri, mimarlar ve geometri meraklıları için vazgeçilmez bir araçtır.
        </p>
        <div class="stats-grid">
          <div class="stat-item">
            <strong>10</strong>
            <span>Analiz Edilen Motif</span>
          </div>
          <div class="stat-item">
            <strong>83%</strong>
            <span>Doğruluk Oranı (Tahmini Hesap)</span>
          </div>
        </div>
      </div>
      
      <div class="image-placeholder">
        <div class="icon-bg">
          <i class="fa-solid fa-compass-drafting"></i>
        </div>
        <p>Gizli Geometriyi Keşfet</p>
      </div>
    </div>
  </section>
</template>

<script setup>
// Font Awesome importu gerekli
import '@fortawesome/fontawesome-free/css/all.css'
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;600;700&display=swap');

.about-section {
  padding: 80px 20px;
  background-color: #ffffff; /* Açık arka plan */
  font-family: 'Poppins', sans-serif;
}

.content-wrapper {
  max-width: 1200px;
  margin: 0 auto;
  display: flex;
  gap: 60px;
  align-items: center;
}

.text-content {
  flex: 1.5;
}

.title {
  font-size: 2.2rem;
  font-weight: 700;
  color: #0f172a; /* Koyu metin */
  margin-bottom: 10px;
}

.subtitle {
  font-size: 1.1rem;
  font-weight: 400;
  color: #00bfef; /* Mavi vurgu */
  margin-bottom: 25px;
}

.description {
  font-size: 1rem;
  color: #475569; /* Orta koyulukta metin */
  line-height: 1.7;
  margin-bottom: 30px;
}

.stats-grid {
  display: flex;
  gap: 40px;
}

.stat-item {
  display: flex;
  flex-direction: column;
}

.stat-item strong {
  font-size: 2rem;
  font-weight: 700;
  color: #00bfef;
  margin-bottom: 5px;
}

.stat-item span {
  font-size: 0.9rem;
  color: #64748b;
  font-weight: 500;
}

.image-placeholder {
  flex: 1;
  min-height: 350px;
  background: #f0f9ff; /* Çok açık mavi */
  border-radius: 16px;
  box-shadow: 0 10px 30px rgba(0, 191, 239, 0.1);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center;
}

.icon-bg {
  width: 100px;
  height: 100px;
  background: #00bfef;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 20px;
}

.icon-bg i {
  font-size: 3rem;
  color: white;
}

.image-placeholder p {
  font-size: 1.2rem;
  font-weight: 600;
  color: #0099cc;
}

/* Mobil Uyum */
@media (max-width: 900px) {
  .content-wrapper {
    flex-direction: column;
    gap: 40px;
  }
  .image-placeholder {
    width: 100%;
    min-height: 250px;
  }
}
</style>