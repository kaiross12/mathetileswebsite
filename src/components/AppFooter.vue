<template>
  <footer class="app-footer">
    <div class="footer-content">
      
      <div class="brand-info">
        <div class="logo-group">
          <i class="fa-solid fa-compass-drafting footer-icon"></i>
          <span class="brand-name">Mathetiles</span>
        </div>
        <p class="tagline">
          Sanatın Geometrisini Keşfedin.
        </p>
        <div class="social-links">
          <a href="#" aria-label="Twitter">
            <i class="fa-brands fa-x-twitter"></i>
          </a>
          <a href="#" aria-label="Instagram">
            <i class="fa-brands fa-instagram"></i>
          </a>
          <a href="#" aria-label="LinkedIn">
            <i class="fa-brands fa-linkedin-in"></i>
          </a>
        </div>
      </div>
      
      <div class="footer-links-group">
        <h4>Keşfet</h4>
        <ul>
          <li><a href="#">Analiz Platformu</a></li>
          <li><a href="#">Motif Kütüphanesi</a></li>
          <li><a href="#">Simetri Grupları</a></li>
        </ul>
      </div>
      
      <div class="footer-links-group">
        <h4>Destek</h4>
        <ul>
          <li><a href="#">SSS</a></li>
          <li><a href="#">Gizlilik Politikası</a></li>
          <li><a href="#">Kullanım Koşulları</a></li>
          <li><a href="#">Bize Ulaşın</a></li>
        </ul>
      </div>

    </div>
    
    <div class="footer-bottom">
      <p>&copy; {{ currentYear }} Mathetiles. Tüm hakları saklıdır. Geometri ve Sanat.</p>
    </div>
  </footer>
</template>

<script setup>
import '@fortawesome/fontawesome-free/css/all.css'
import { ref } from 'vue'

const currentYear = ref(new Date().getFullYear())
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;500;600;700&display=swap');

.app-footer {
  /* Mavi arka plan: Navbar'daki mavinin daha koyu bir tonu */
  background-color: #0077b6; 
  color: white;
  font-family: 'Poppins', sans-serif;
  padding: 40px 20px 0;
}

.footer-content {
  max-width: 1200px;
  margin: 0 auto;
  display: flex;
  justify-content: space-between;
  gap: 40px;
  padding-bottom: 30px;
}

/* Sol Taraf: Marka ve Sosyal Medya */
.brand-info {
  flex: 2;
}

.logo-group {
  display: flex;
  align-items: center;
  margin-bottom: 15px;
}

.footer-icon {
  font-size: 2.2rem;
  color: #90e0ef; /* Açık mavi vurgu */
  margin-right: 10px;
}

.brand-name {
  font-size: 1.8rem;
  font-weight: 700;
  color: white;
}

.tagline {
  font-size: 1rem;
  font-weight: 400;
  color: #ade8f4; /* Hafif metin rengi */
  margin-bottom: 25px;
}

.social-links {
  display: flex;
  gap: 15px;
}

.social-links a {
  color: white;
  font-size: 1.3rem;
  transition: color 0.2s;
}

.social-links a:hover {
  color: #90e0ef; /* Hover'da açık mavi vurgu */
}

/* Orta ve Sağ Taraf: Link Grupları */
.footer-links-group {
  flex: 1;
}

.footer-links-group h4 {
  font-size: 1.1rem;
  font-weight: 600;
  margin-bottom: 15px;
  color: #90e0ef; /* Başlıklar açık mavi */
}

.footer-links-group ul {
  list-style: none;
  padding: 0;
  margin: 0;
}

.footer-links-group li {
  margin-bottom: 10px;
}

.footer-links-group a {
  text-decoration: none;
  color: white;
  font-size: 0.95rem;
  font-weight: 400;
  transition: color 0.2s;
}

.footer-links-group a:hover {
  color: #ade8f4;
  text-decoration: underline;
}

/* Alt Çubuk (Copyright) */
.footer-bottom {
  border-top: 1px solid rgba(255, 255, 255, 0.2);
  padding: 15px 0;
  text-align: center;
}

.footer-bottom p {
  font-size: 0.85rem;
  color: #ade8f4;
  margin: 0;
}

/* Mobil Uyum */
@media (max-width: 768px) {
  .footer-content {
    flex-direction: column;
    gap: 30px;
  }
  .brand-info {
    text-align: center;
    border-bottom: 1px solid rgba(255, 255, 255, 0.1);
    padding-bottom: 20px;
  }
  .logo-group {
    justify-content: center;
  }
  .social-links {
    justify-content: center;
  }
  .footer-links-group {
    text-align: center;
  }
}
</style>