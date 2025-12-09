<template>
  <div class="navbar-wrapper">
    <nav :class="['navbar', { 'is-loading': isLoading }]">
      <div class="navbar-content"> 
          <div class="navbar-left">
            <template v-if="isLoading">
              <div class="skeleton-logo"></div>
              <div class="skeleton-text title"></div>
            </template>
            <template v-else>
              <img :src="logoData.src" :alt="logoData.alt" class="logo" />
              <span class="navbar-title">{{ title }}</span> 
            </template>
          </div>

          <button class="menu-toggle" @click="toggleMenu" v-if="!isLoading">
            <span class="bar"></span>
            <span class="bar"></span>
            <span class="bar"></span>
          </button>

          <div :class="['navbar-right', { 'is-open': isMenuOpen }]">
            <ul class="nav-links">
              <template v-if="isLoading">
                <li v-for="n in 4" :key="n" class="nav-item">
                  <div class="skeleton-text link"></div>
                </li>
              </template>
              <template v-else>
                <li v-for="link in links" :key="link.name" class="nav-item">
                  <a 
                    :href="link.url" 
                    @click="scrollToSection($event, link.url); isMenuOpen = false" 
                    class="nav-link"
                  >
                    {{ link.name }}
                  </a>
                </li>
              </template>
            </ul>
          </div>
      </div>
    </nav>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import logocini from '../assets/logocini2.png'

const NAVBAR_HEIGHT_OFFSET = 95; 
const isMenuOpen = ref(false); 

// --- VERİ VE MANTIK ---

const logoData = ref({
  src: logocini, 
  alt: 'Vue Logo'
});

const links = ref([
  { name: 'Anasayfa', url: '#top' },
  { name: 'Uygulama', url: '#uygulama' },
  { name: 'Hakkımızda', url: '#hakkimizda' },
  { name: 'Temel Özellikler', url: '#ozellikler' }
]);

const isLoading = ref(true);

const toggleMenu = () => {
  isMenuOpen.value = !isMenuOpen.value;
};

const scrollToSection = (event, url) => {
  event.preventDefault(); 

  if (url === '#top') {
    window.scrollTo({ top: 0, behavior: 'smooth' });
    return;
  }

  const targetElement = document.querySelector(url);

  if (targetElement) {
    const elementPosition = targetElement.getBoundingClientRect().top + window.scrollY;
    const offsetPosition = elementPosition - NAVBAR_HEIGHT_OFFSET;

    window.scrollTo({
      top: offsetPosition,
      behavior: 'smooth'
    });
  } else {
    console.warn(`Hedef section bulunamadı: ${url}`);
  }
};


onMounted(() => {
  setTimeout(() => {
    isLoading.value = false;
  }, 2500);
});
</script>

<style scoped>
/* Wrapper: ekranın üstünden ve yanlardan boşluk bırakır, şeffaf */
.navbar-wrapper {
  position: fixed;
  top: 20px;    
  left: 20px;   
  right: 20px;  
  z-index: 1000;
  background-color: transparent;
}

/* Navbar: beyaz arkaplan ve gölge */
.navbar {
  width: 97%; /* Masaüstü varsayılan genişlik */
  background-color: #ffffff;
  border-radius: 12px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
  padding: 15px 25px;
  display: flex;
  align-items: center;
  font-family: 'Poppins', sans-serif;
}

/* İçerik ortalama ve boşluklu */
.navbar-content {
  display: flex;
  align-items: center;       
  justify-content: center;   
  position: relative;        
  max-width: 1300px;
  margin: 0 auto;
  width: 100%;
}

/* Sol Taraf (Logo ve Başlık) */
.navbar-left {
  position: absolute;
  left: 0;
  display: flex;
  align-items: center;
  gap: 10px;
}

/* Logo */
.logo {
  width: 60px; /* Masaüstü varsayılan logo boyutu */
  height: 60px;
  object-fit: cover;
}

/* Sağ Taraf (Navigasyon Linkleri) - Masaüstü Görünümü */
.navbar-right {
  display: flex;
  align-items: center;
  gap: 25px;
  justify-content: flex-end; 
  transition: transform 0.3s ease-out, opacity 0.3s ease-out;
}

/* Navigasyon Bağlantıları */
.nav-links {
  list-style: none;
  display: flex; 
  gap: 25px;
  margin: 0;
  padding: 0;
}

/* Hamburger Buton Stili */
.menu-toggle {
  display: none; 
  flex-direction: column;
  justify-content: space-around;
  width: 30px;
  height: 25px;
  background: transparent;
  border: none;
  cursor: pointer;
  padding: 0;
  position: absolute;
  right: 0; 
  z-index: 10;
}

.menu-toggle .bar {
  width: 100%;
  height: 3px;
  background-color: #00bfef;
  border-radius: 10px;
  transition: all 0.3s linear;
}

.skeleton-logo {
  width: 50px;
  height: 50px;
  border-radius: 10px;
}

.navbar.is-loading {
  padding-top: 22.5px;    
  padding-bottom: 22.5px; 
  transition: padding 0.3s;
}

/* --- MOBİL CİHAZ AYARLARI (max-width: 768px) --- */
@media (max-width: 768px) {
  /* 1. Navbar Wrapper'ı Kenar Boşluğunu Azaltarak Genişlet */
  .navbar-wrapper {
    top: 10px;    /* Üst boşluğu azalt */
    left: 10px;   /* Sol boşluğu azalt */
    right: 10px;  /* Sağ boşluğu azalt */
  }

  /* 2. Navbar Genişliğini Arttır */
  .navbar {
    width: 88%; /* Wrapper'ın tamamını kapla */
    height: 1rem;
  }

  /* 3. Logo Boyutunu Küçült */
  .logo {
    width: 40px; 
    height: 40px;
  }
  
  .navbar-title {
    font-size: 1.1em; /* Başlığı da biraz küçültmek okunurluğu artırır */
  }

  /* Mobil menü stilleri (Önceki adımdan kaldı) */
  .navbar-content {
    justify-content: space-between; 
  }

  .menu-toggle {
    display: flex; 
  }

  .navbar-right {
    position: absolute;
    /* top: 100% yerine daha küçük bir değer kullanarak yukarı çekildi */
    top: 30px; /* Navbar'ın padding + içerik yüksekliğine göre ayarlanmış tahmini değer */
    right: 0;
    left: 0;
    display: none; 
    flex-direction: column;
    width: 100%;
    background-color: #ffffff;
    box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1);
    border-radius: 0 0 12px 12px;
    padding: 0; /* İç ve dış boşluklar kaldırıldı/azaltıldı */
    gap: 0;
    z-index: 5; /* Navbar'ın altında kalmamalı */
  }

  .navbar-right.is-open {
    display: flex;
  }

  .nav-links {
    flex-direction: column;
    width: 100%;
    align-items: center; /* Öğeleri yatayda ortalar */
    gap: 0; /* Dikey boşluklar kaldırıldı */
    padding-left: 2rem;
  }

  .nav-item {
    width: 100%;
    text-align: center;
    padding: 5px 0;
  }
  
  .nav-link {
    display: block;
    padding: 10px 20px;
  }
  .skeleton-logo {
  width: 30px;
  height: 30px;
  border-radius: 10px;
}

.navbar.is-loading {
  padding-top: 1rem;    
  padding-bottom: 1rem; 
}
}

/* --- DİĞER STİLLER --- */

/* Başlık */
.navbar-title {
  font-weight: 600;
  color: #00bfef;
  transition: color 0.3s;
}

/* Navigasyon Bağlantıları */
.nav-item {
  display: flex; 
  align-items: center;
}

.nav-link {
  text-decoration: none;
  color: #00bfef;
  font-weight: 500;
  transition: color 0.3s;
  padding: 5px 0;
}

.nav-link:hover {
  color: #0099cc;
}

/* Skeleton Loading */
.skeleton-text, .skeleton-logo {
  background-color: #e0e0e0;
  border-radius: 4px;
  animation: pulse 1.8s infinite ease-in-out;
}

@keyframes pulse {
  0% { opacity: 0.8; }
  50% { opacity: 0.5; }
  100% { opacity: 0.8; }
}

.skeleton-text.link {
  width: 90px;
  height: 20px;
}


</style>