<template>
  <main>
    <Navbar />
    <FullScreenHero
      :imageUrl="homeImage"
      title="Mathetiles'a Hoş Geldiniz"
      buttonText="Keşfet"
      @click="onHeroClick"
    />
    <PatternAnalyzer/>
  </main>
  <Section/>
  <About/>
  <Feature/>
  <Footer/>
  <DevelopmentNotice />
</template>

<script setup>
import FullScreenHero from '../components/FullScreenHero.vue'
import homeImage from '../assets/homecini.jpg'   // ✔ aynı resmi burada da import ettik
import Navbar from '../components/Navbar.vue'
import Section from '../components/Section.vue'
import PatternAnalyzer from '../components/PatternAnalyzer.vue'
import About from '../components/SectionAbout.vue'
import Feature from '../components/SectionFeatures.vue'
import Footer from '../components/AppFooter.vue'
import DevelopmentNotice from '../components/DevelopmentNotice.vue'

function onHeroClick() {
  console.log('Butona tıklandı!')
}
</script>

<style scoped>
main {
  margin: 0;
  padding: 0;
}
</style>
