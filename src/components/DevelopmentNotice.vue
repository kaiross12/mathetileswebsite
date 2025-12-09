<template>
  <transition name="modal-fade">
    <div class="analyzer-modal-overlay" v-if="isVisible">
      <div class="development-modal">
        
        <div class="modal-header">
          <div class="header-icon-box">
            <i class="fa-solid fa-code-branch"></i>
          </div>
          <div class="header-text">
            <h3>Geliştirme Aşamasında (Alpha Sürümü)</h3>
            <span class="status">Sürüm: 0.9.1 - Kararsız</span>
          </div>
          <button class="close-btn" @click="closeModal" aria-label="Kapat">
            <i class="fa-solid fa-xmark"></i>
          </button>
        </div>
        
        <div class="modal-body custom-scrollbar">
          <p class="intro-message">
            Mathetiles platformuna hoş geldiniz. Uygulamamız şu anda **yoğun geliştirme ve test sürecindedir.** Lütfen aşağıdaki önemli noktaları dikkate alın:
          </p>
          
          <div class="info-card">
            <h4><i class="fa-solid fa-vial-circle-check"></i> Test Ortamı Uyarısı</h4>
            <ul>
              <li><i class="fa-solid fa-bug warning"></i> Bazı analiz sonuçları hatalı veya eksik olabilir.</li>
              <li><i class="fa-solid fa-database warning"></i> Kullanıcı verileri (profil, kayıtlar) haber verilmeden sıfırlanabilir.</li>
              <li><i class="fa-solid fa-wand-magic-sparkles warning"></i> Özellikler sürekli değişmekte ve yeni fonksiyonlar eklenmektedir.</li>
            </ul>
          </div>
          
          <p class="feedback-message">
            Karşılaştığınız hataları veya önerilerinizi bize bildirmeniz, platformu hızla olgunlaştırmamız için kritik öneme sahiptir.
          </p>
        </div>

        <div class="modal-footer">
          <button class="acknowledge-btn" @click="closeModal">
            <i class="fa-solid fa-clipboard-check"></i> Bilgilendirmeyi Okudum ve Kabul Ediyorum
          </button>
        </div>
      </div>
    </div>
  </transition>
</template>

<script setup>
import '@fortawesome/fontawesome-free/css/all.css'
import { ref, onMounted } from 'vue'

const isVisible = ref(false)
const STORAGE_KEY = 'mathetiles_dev_notice_closed_analyzer'

// --- Methods ---

const closeModal = () => {
  isVisible.value = false
  // Kullanıcı bir kez kapattığında, bir daha göstermemek için yerel depolamaya kaydet
  localStorage.setItem(STORAGE_KEY, 'true')
}

// --- Lifecycle ---

onMounted(() => {
  const closedStatus = localStorage.getItem(STORAGE_KEY)

  // Eğer daha önce kapatılmadıysa göster
  if (closedStatus !== 'true') {
    isVisible.value = true
  }
})
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;600;700&display=swap');

/* 1. Overlay (PatternAnalyzer ile aynı) */
.analyzer-modal-overlay {
  position: fixed; top: 0; left: 0; width: 100%; height: 100%;
  background: rgba(22, 45, 51, 0.85); backdrop-filter: blur(8px);
  display: flex; justify-content: center; align-items: center; z-index: 9999;
  font-family: 'Poppins', sans-serif;
  overflow: auto; 
}

/* 2. Modal Kutusu (PatternAnalyzer ana paneli stili) */
.development-modal {
  background: white;
  width: 90%;
  max-width: 550px;
  border-radius: 20px; /* Analyzer ile aynı yuvarlaklık */
  box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.25); /* Analyzer ile aynı gölge */
  overflow: hidden;
  position: relative;
  /* Animasyon PatternAnalyzer'ın popIn'ine benzer */
  animation: popIn 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275); 
}

/* Modal Başlık (SideAIPanel/MainPanel başlık stili) */
.modal-header {
  background: #0f172a; /* Koyu arka plan */
  color: white;
  padding: 20px 30px;
  display: flex;
  align-items: center;
  gap: 15px;
  position: relative;
}

.header-icon-box {
  width: 42px; height: 42px;
  background: #0ea5e9; /* Mavi vurgu */
  color: white;
  border-radius: 50%;
  display: flex; align-items: center; justify-content: center;
  font-size: 1.2rem;
  flex-shrink: 0;
}

.header-text {
  flex-grow: 1;
}

.header-text h3 {
  font-size: 1.2rem;
  font-weight: 700;
  margin: 0;
}

.status {
  font-size: 0.8rem;
  color: #90e0ef; /* Açık mavi metin */
}

/* Kapatma Butonu */
.close-btn {
  position: absolute; 
  top: 15px; 
  right: 15px; 
  background: rgba(255, 255, 255, 0.2);
  border: none; 
  width: 32px; 
  height: 32px; 
  border-radius: 50%;
  cursor: pointer; 
  z-index: 20; 
  transition: all 0.2s;
  color: white;
}
.close-btn:hover { 
  background: rgba(255, 255, 255, 0.4); 
  transform: rotate(90deg); 
}

/* Modal İçeriği */
.modal-body {
  padding: 30px;
  color: #334155;
  font-size: 0.95rem;
  line-height: 1.6;
  max-height: 400px;
  overflow-y: auto;
}

.intro-message {
  margin-bottom: 20px;
}

.info-card {
  background: #f8fafc; /* Hafif gri */
  border: 1px solid #e2e8f0;
  padding: 20px;
  border-radius: 12px;
  margin-bottom: 25px;
}

.info-card h4 {
  font-size: 1rem;
  font-weight: 600;
  color: #0f172a;
  margin-bottom: 15px;
  display: flex;
  align-items: center;
  gap: 8px;
}

.info-card ul {
  list-style: none;
  padding: 0;
  margin: 0;
}

.info-card ul li {
  display: flex;
  align-items: flex-start;
  gap: 10px;
  margin-bottom: 10px;
  font-size: 0.9rem;
  color: #475569;
}

.info-card ul li i.warning {
  color: #f97316; /* Turuncu vurgu */
  margin-top: 4px;
  flex-shrink: 0;
}

.feedback-message {
  font-size: 0.85rem;
  color: #64748b;
  font-style: italic;
  padding-top: 15px;
  border-top: 1px dashed #e2e8f0;
}

/* Modal Footer (Aksiyon butonu) */
.modal-footer {
  padding: 0 30px 30px;
}

.acknowledge-btn {
  width: 100%;
  padding: 14px;
  background: #0ea5e9; /* Mavi buton */
  color: white;
  border: none;
  border-radius: 12px;
  font-weight: 600;
  font-size: 1rem;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
  transition: background-color 0.2s, transform 0.2s;
}

.acknowledge-btn:hover {
  background: #0284c7; 
  transform: translateY(-2px);
}

/* Animasyonlar */
@keyframes popIn {
  from { transform: scale(0.8); opacity: 0; }
  to { transform: scale(1); opacity: 1; }
}

.modal-fade-enter-active,
.modal-fade-leave-active {
  transition: opacity 0.3s ease;
}

.modal-fade-enter-from,
.modal-fade-leave-to {
  opacity: 0;
}
</style>