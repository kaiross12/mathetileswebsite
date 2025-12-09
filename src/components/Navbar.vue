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

          <div class="navbar-right">
            <ul class="nav-links">
              <template v-if="isLoading">
                <li v-for="n in 4" :key="n" class="nav-item">
                  <div class="skeleton-text link"></div>
                </li>
              </template>
              <template v-else>
                <li v-for="link in links" :key="link.name" class="nav-item">
                  <a :href="link.url" class="nav-link">{{ link.name }}</a>
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

// --- VERİ VE MANTIK ---
const logoData = ref({
  src: logocini, 
  alt: 'Vue Logo'
});

const links = ref([
  { name: 'Anasayfa', url: '/' },
  { name: 'Ayarlar', url: '/settings' },
  { name: 'Profilim', url: '/profile' },
  { name: 'Çıkış Yap', url: '/logout' }
]);

const isLoading = ref(true);

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
  top: 20px;    /* Üstten boşluk */
  left: 20px;   /* Soldan boşluk */
  right: 20px;  /* Sağdan boşluk */
  z-index: 1000;
  background-color: transparent;
}

/* Navbar: beyaz arkaplan ve gölge */
.navbar {
  width: 97%;
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
  align-items: center;       /* Dikey ortalama */
  justify-content: center;   /* Yatay ortalama başlık ve linkler */
  position: relative;        /* Logo için absolute konumlandırmaya izin verir */
  max-width: 1300px;
  margin: 0 auto;
  width: 100%;
}

/* Sol ve Sağ Taraflar */
.navbar-left {
  position: absolute;
  left: 0;
  display: flex;
  align-items: center;
  gap: 10px;
}

.navbar-right {
  display: flex;
  align-items: center;
  gap: 25px;
  justify-content: flex-end; /* Sağ taraf en sağda */
}

/* Logo */
.logo {
  width: 60px;
  height: 60px;
  object-fit: cover;
}

/* Başlık */
.navbar-title {
  font-size: 1.3em;
  font-weight: 600;
  color: #00bfef;
  transition: color 0.3s;
}

/* Navigasyon Bağlantıları */
.nav-links {
  list-style: none;
  display: flex;
  gap: 25px;
  margin: 0;
  padding: 0;
}

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

.skeleton-logo {
  width: 50px;
  height: 50px;
  border-radius: 10px;
}

.skeleton-text.link {
  width: 90px;
  height: 20px;
}

.navbar.is-loading {
  padding-top: 22.5px;    /* Normal padding 15px + 10px üstten */
  padding-bottom: 22.5px; /* Normal padding 15px + 10px alttan */
  transition: padding 0.3s;
}
</style>
