<template>
  <div class="ai-side-panel">
    <div class="panel-header">
      <div class="ai-avatar" :class="{ 'pulse': loading }">
        <i class="fa-solid fa-robot"></i>
      </div>
      <div class="header-text">
        <h3>MathetilesAI</h3>
        <span class="status">{{ statusText }}</span>
      </div>
    </div>

    <div class="content-area custom-scrollbar">
      <div v-if="displayedText" class="response-text">
        {{ displayedText }}<span v-if="isTyping" class="cursor">|</span>
        
        <div v-if="tags.length > 0 && !isTyping" class="tags-container">
          <span v-for="tag in tags" :key="tag" class="geo-tag">
            #{{ tag }}
          </span>
        </div>
        </div>
      
      <div v-else class="placeholder-state">
        <div class="icon-wrap">
          <i class="fa-solid fa-wand-magic-sparkles"></i>
        </div>
        <p>Seçilen eserin geometrik açıdan yorumlanması için aşağıdaki butonu kullanın.</p>
      </div>
    </div>

    <div class="panel-footer">
      <button 
        class="comment-btn" 
        @click="startInterpretation" 
        :disabled="loading || isTyping"
      >
        <span v-if="!loading">
          <i class="fa-regular fa-comment-dots"></i> Yorumla
        </span>
        <span v-else>
          <i class="fa-solid fa-spinner fa-spin"></i> Düşünüyor...
        </span>
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, computed } from 'vue'

const props = defineProps({
  title: String
})

// --- STATE ---
const loading = ref(false)
const isTyping = ref(false)
const displayedText = ref("")
const tags = ref([]) // Geometrik tag'ler için state

const statusText = computed(() => {
  if (loading.value) return "Analiz ediliyor..."
  if (isTyping.value) return "Yazılıyor..."
  if (displayedText.value) return "Tamamlandı"
  return "Hazır"
})

// --- MOCK DATABASE (Yorumlar) ---
const commentDatabase = {
  'Pano 1': "Bu Osmanlı çini motifi, natüralist stilizasyonun karakteristik özelliklerini taşıyan, yüksek derecede organize bir desendir. Kompozisyon, merkezdeki dikey eksen boyunca mükemmel ayna simetrisiyle dengelenmiştir. Merkezden yayılan ve kendi içlerinde genellikle 8 veya 12 katlı noktasal dönme simetrisi gösteren Hataî (stilize çiçek) motifleri bu simetrik yapıyı destekler. Desenin akıcılığı, sert geometrik çizgiler yerine S-eğrilerine benzeyen, kıvrımlı ve birbirine geçmiş Rumi (yaprak ve dal) motiflerinden kaynaklanır. Kullanılan baskın renkler olan kobalt mavisi, turkuaz ve kiremit kırmızısı (İznik kırmızısı), beyaz zeminle çarpıcı bir kontrast oluşturarak motiflerin görsel etkisini güçlendirir ve kompozisyonun estetik bütünlüğünü pekiştirir.",
  'Pano 2': "Bu çini motifi, çevresel alandaki akıcı natüralist stilizasyon ile merkezde yer alan minyatür tarzı temsili bir sahneyi keskin bir kare çerçeve ile birleştiren katmanlı bir kompozisyon sergiler. Çevresel kısım, kobalt mavisi ve turkuaz tonlarının kullanıldığı bir paletle, Saz Yolu stili unsurları ve Hataî (stilize rozet çiçekler) motiflerini içerir. Merkezdeki karede ise, çevresel alanın akıcı hatlarının aksine, Kâbe ve Mescid-i Haram'ı tasvir eden, basitleştirilmiş ve iki boyutlu geometrik bir perspektifle sunulan kutsal bir mekân resmi yer alır.",
  'Pano 3': "Bu Osmanlı çini motifi, natüralist stilizasyon ile geometrik düzenin başarılı bir sentezini sunan, dikey bir şerit deseni örneğidir. Şeridin merkez bölümü, kalın, koyu mavi, stilize Rumi/İslimi dalların birbirini sürekli olarak çaprazladığı karmaşık bir örgü (geçme) yapısıyla karakterizedir; bu örgü, sonsuzluğu çağrıştıran güçlü bir dikey ritim oluşturur. Merkezi kafes, her iki yanda da kiremit kırmızısı ve turuncu tonlardaki lale motifleri ile beyaz karanfil ve küçük rozet çiçeklerinden oluşan canlı, natüralist stilize bordürlerle çevrelenmiştir. Desende, temel olarak kobalt mavisi (arka plan ve ana dallar), turkuaz (detaylar) ve kiremit kırmızısı (çiçekler) kullanılarak zengin bir İznik paleti sergilenmiş; kompozisyonun bütünü ise her iki bordürü ve merkezi örgüyü kapsayacak şekilde kusursuz bir dikey ayna simetrisi ile tasarlanmıştır.",
  'Pano 4': "Bu Osmanlı çini motifi, natüralist stilizasyonun zengin detaylarını sergileyen, tüm yüzeyi kaplayan (alan deseni) yoğun bir kompozisyondur. Desenin ana motifi, keskin hatlı, yoğun yapraklı ve merkezinde stilize edilmiş çiçek çekirdeği bulunan büyük, loblu bir palmet veya Hataî formudur; bu motifler, akıcı, ince dallarla birbirine bağlanarak kesintisiz bir yüzey dokusu oluşturur. Desen, İznik çinilerinin klasik üçlüsü olan kobalt mavisi, kiremit kırmızısı ve turkuaz renklerini, beyaz zemin üzerindeki yüksek motif doluluğu ile birleştirir. Beyaz zemin, özellikle küçük, ayrıntılı papatya benzeri çiçeklerin (bazıları kırmızı merkezli) arka planı doldurmasıyla motifin bir parçası haline gelmiştir. Desen, tüm yüzeyde yatay, dikey ve köşegen eksenler boyunca güçlü bir tekrar ve simetri sergileyerek sonsuzluk hissi veren bir bütünlük yaratır.",
  'Pano 5': "Bu Osmanlı çini motifi, natüralist stilizasyon ve Saz Yolu stilinin güçlü etkilerini taşıyan, dikey bir şerit deseni örneğidir; kompozisyonun merkezi, beyaz zemin üzerinde turkuaz ve kobalt mavisi tonlarının zıtlığıyla vurgulanan, sivri ve dişli kenarlı yapraklara sahip, büyük, stilize bir palmet veya Hataî motifine odaklanır. Ana motifin her iki yanından kıvrılarak yükselen akıcı dallar, alt ve üst kısımlardaki diğer çiçek ve yaprak formlarıyla birleşerek kesintisiz bir dikey akış yaratır. Desenin geneli, yoğun kobalt mavisi, canlı turkuaz ve az miktarda kullanılan kiremit kırmızısı tonlarıyla görsel bir denge kurarken, tüm şerit boyunca güçlü bir dikey ayna simetrisi sergiler; ana motifin kendisi ise merkezinden yayılan dönme simetrisi prensibiyle inşa edilmiştir.",
  'Pano 6': "Bu Osmanlı çini motifi, natüralist stilizasyonun en güçlü ve dramatik örneklerinden birini temsil eder; kompozisyonun merkezi, beyaz zemin üzerinde turkuaz ve kobalt mavisi tonlarıyla boyanmış, keskin, sivri uçlu yaprakları olan büyük, stilize bir palmet veya Saz Yolu tarzı yaprak motifine odaklanır. Bu ana motif, etrafı saran ve kobalt mavisi, kiremit kırmızısı ve turkuaz renklerini barındıran akıcı dal ve küçük Hataî (stilize çiçek) rozetleriyle kontrast oluşturur. Merkezdeki iri yaprak formu, kendi içinde 8'li dönme simetrisi prensibine göre düzenlenmişken, ana dallar, gözü kompozisyonun her yerine taşıyan S-eğrileri ile tanımlanmış, sonsuzluğa uzanan dinamik bir ritim oluşturur. Desenin geneli, tüm motifin köşegenler boyunca yansıtılması ve sürekli tekrarlanması prensibine dayanır.",
  'Pano 7': "Bu Osmanlı çini motifi, natüralist stilizasyonun en canlı ve simetrik örneklerinden birini temsil eden, dikey bir şerit deseni örneğidir. Kompozisyonun merkezi, beyaz zemin üzerinde büyük, katmanlı, sivri uçlu yaprakları olan stilize bir palmet veya Hataî motifine odaklanırken, bunun etrafında iki yana doğru yükselen, belirgin ve kıvrık hatlara sahip lale motifleri yer alır. Desen, İznik çinilerinin ana renkleri olan yoğun kobalt mavisi, canlı kiremit kırmızısı ve turkuaz detaylarının güçlü kontrastıyla görsel bir dinamizm yaratır. Tüm şerit, merkezi dikey bir eksen boyunca kusursuz ayna simetrisi (yansıma) sergiler; ana çiçek ve lale motifleri de dâhil olmak üzere tüm elemanlar, dikey ritmin bir parçası olarak yukarı ve aşağı doğru tekrarlı öteleme ile birbirine bağlanmıştır.",
  'Pano 8': "Bu Osmanlı çini motifi, natüralist stilizasyon ve geometrik düzenin birleşimini sergileyen, göz alıcı bir alan deseni (field pattern) örneğidir. Desenin merkezi, beyaz zemin üzerinde, ortasında kiremit kırmızısı ve kobalt mavisi detayları bulunan, katmanlı yapraklara sahip, stilize edilmiş büyük bir rozet veya Hataî çiçeğidir. Bu merkezi motif, koyu mavi renkteki, iç kısımları beyaz ve kırmızı detaylarla bezenmiş, kıvrımlı ve süslü bulut veya stilize edilmiş Rumi formlarını andıran orantılı bir çerçeve (cartouche) içine yerleştirilmiştir. Desen, yoğun kobalt mavisi ve sıcak kiremit kırmızısı renklerini beyaz zeminin geniş boşluklarıyla birleştirerek güçlü bir kontrast ve odak noktası yaratır. Motifler, tüm yüzeyde yatay ve dikey eksenler boyunca düzenli bir tekrar ve simetri sergileyerek kesintisiz bir doku oluşturur.",
  'Pano 9': "Bu pano, İznik çinileri sanatına özgü Rumi ve Hatai gibi stilize bitkisel motifleri, güçlü bir geometrik düzen içinde sunar. Pano, dikey bir şerit düzenine sahiptir ve merkezdeki ana motifleri çevreleyen sınır şeritleri de dahil olmak üzere, tüm kompozisyon bir eksenel simetri etrafında kurulmuştur. Tekrarlayan ve dönüşümlü olarak kullanılan bitkisel (palmet, nar çiçeği, stilize yapraklar) ve kafes benzeri girift geometrik örgüler (muhtemelen münhani veya zencirek tarzında) dikkat çeker. Bu geometrik örgüler, panoda bir ritim ve denge oluşturur. Motifler arasındaki boşluklar, özellikle yoğun kobalt mavisi zemin üzerinde, motiflerin keskin hatlarını vurgulayarak negatif alanın bileşimdeki rolünü artırır. Geometrik kafesler, motifleri dikey olarak birbirine bağlayarak bütünlük sağlar ve izleyiciye yukarıdan aşağıya doğru akan bir dikey hareket hissi verir.",
  'Pano 10': "Bu Osmanlı çini motifi, natüralist stilizasyonun en yoğun ve tekrarlı örneklerinden biridir ve tüm yüzeyi dolduran tek tip bir alan deseni (field pattern) oluşturur. Desenin temel birimi, birbirine sıkıca bağlanmış dallar ve yapraklarla çevrili, katmanlı taç yapraklara sahip büyük, stilize edilmiş rozet veya Hataî çiçeğidir. Kompozisyon, kobalt mavisi ve turkuaz tonlarının hâkim olduğu iki renkli bir paletle (kiremit kırmızısı bu desende belirgin değildir) beyaz zemin üzerinde güçlü bir kontrast yaratır ve motiflerin yoğunluğu yaklaşık %70-80 oranında bir doluluk sağlar. Desen, dikey, yatay ve köşegen eksenler boyunca kusursuz bir tekrar ve simetri sergileyerek, yüzeyde sonsuzluğa uzanan, ritmik ve dengeli bir halı etkisi yaratır. Çiçeklerin kendi içindeki düzeni ise merkezden yayılan güçlü bir dönme simetrisi prensibine dayanır."
}

const defaultComment = "Bu eser, Osmanlı çini sanatının klasik dönem özelliklerini taşımaktadır. Renk kullanımı, sır tekniği ve kompozisyon dengesi, dönemin estetik anlayışını yansıtır."

// --- MOCK DATABASE (Tagler) ---
const tagDatabase = {
  'Pano 1': ['Bilateral Simetri', 'Sınır Örüntüsü Grubu', 'Merkezi Dönme', 'Kayarak Yansıma', 'Girift Bağlantı ve Hiyerarşi'],
  'Pano 2': ['Köşegen Simetri', 'Dörtlü Dönme (90°)', 'Sonsuz Tekrar Formu', 'Dikey ve Yatay Aynalama', 'Dönüşüm Zinciri'],
  'Pano 3': ['Aynalama Simetrisi', 'Dikey Periyodisite', 'Çoklu Dönme Ekseni', 'Yansıtmalı Kaydırma', 'Zencirek Tekniği'],
  'Pano 4': ['Periyodik Kaydırma', 'Modüler Tekrarlama', 'Ondalık Dönme (36°)', 'Tam Simetri Grubu', 'İzdüşüm ve Kombinasyon'], 
  'Pano 5': ['Eksenel Simetri', 'Dikey Öteleme', 'Merkezi Dönme Noktası', 'Kayarak Yansıma', 'Negatif Alan Etkileşimi'],
  'Pano 6': ['Yatay ve Dikey Örüntüleme', 'Modüler Yerleştirme', 'Altılı Dönme (60°)', 'Bilateral Simetri', 'Fraktal Benzerlik (Örüntü İçi)'],
  'Pano 7': ['Aynalama ve İkili Simetri', 'Örüntü Kaydırma', 'Dönme Merkezi', 'Yansıtmalı Kaydırma', 'İç İçe Geçen Geometriler'],
  'Pano 8': ['Öteleme Grubu (P1)', 'Kareli Izgara Yapısı', 'Noktasal Dönme', 'Dikey Aynalama', 'Yatay ve Dikey Kombinasyon'],
  'Pano 9': ['Sürekli Öteleme', 'Çapraz Yansıma', 'Açısal Dönüşüm', 'Ölçek/Boyut Değişimi', 'Periyodik Yüzey Kaplama'],
  'Pano 10': ['İki Yönlü Öteleme', 'Dikdörtgen Izgara Sistemi', 'Merkezi Dönme', 'Köşegen Simetri', 'Grup Teorisi Uygulaması']
}

const defaultTags = ['Osmanlı Sanatı', 'Klasik Dönem', 'Kompozisyon Dengesi', 'Sır Tekniği', 'Estetik Anlayış']

watch(() => props.title, () => {
  resetState()
})

const resetState = () => {
  displayedText.value = ""
  loading.value = false
  isTyping.value = false
  tags.value = [] // State'i sıfırla
}

const startInterpretation = async () => {
  if (loading.value || isTyping.value) return
  
  loading.value = true
  displayedText.value = ""
  tags.value = [] // Her yorumdan önce tag'leri sıfırla

  await new Promise(resolve => setTimeout(resolve, 1500))
  
  loading.value = false
  
  const fullText = commentDatabase[props.title] || defaultComment
  typeWriterEffect(fullText)
}

const typeWriterEffect = async (text) => {
  isTyping.value = true
  
  for (let i = 0; i < text.length; i++) {
    displayedText.value += text[i]
    const delay = text[i] === '.' || text[i] === ',' ? 40 : 15
    await new Promise(resolve => setTimeout(resolve, delay))
  }
  
  isTyping.value = false
  
  // Yorumlama bittiğinde tag'leri yükle
  tags.value = tagDatabase[props.title] || defaultTags 
}
</script>

<style scoped>
.ai-side-panel {
  width: 360px;
  height: 700px;
  background: white;
  border-radius: 20px;
  display: flex;
  flex-direction: column;
  box-shadow: 0 20px 40px rgba(0,0,0,0.15); /* Gölge hafifletildi */
  margin-left: 20px;
  border: 1px solid #e2e8f0;
  animation: slideIn 0.4s ease-out;
  overflow: hidden;
}

.panel-header {
  padding: 20px;
  background: #f0f9ff; /* Çok açık mavi (sky-50) */
  border-bottom: 1px solid #e0f2fe;
  display: flex; align-items: center; gap: 12px;
}

.ai-avatar {
  width: 42px; height: 42px;
  /* Mavi Gradyan Geçişi */
  background: linear-gradient(135deg, #38bdf8, #0284c7); 
  color: white; border-radius: 50%;
  display: flex; align-items: center; justify-content: center;
  font-size: 1.2rem;
  box-shadow: 0 4px 12px rgba(14, 165, 233, 0.3); /* Mavi gölge */
}
.ai-avatar.pulse { animation: pulse 1.5s infinite; }

.header-text h3 { font-size: 1rem; color: #0f172a; margin: 0; font-weight: 600; }
.status { font-size: 0.75rem; color: #64748b; }

.content-area {
  flex: 1; padding: 25px;
  background: #ffffff;
  overflow-y: auto;
  position: relative;
}

.response-text {
  font-size: 0.95rem; color: #334155; line-height: 1.7;
  font-family: 'Poppins', sans-serif;
}

/* YENİ: Tag Stilleri */
.tags-container {
  margin-top: 20px;
  display: flex;
  flex-wrap: wrap;
  gap: 8px; /* Tagler arası boşluk */
  border-top: 1px dashed #e0e7ff; /* Açık mavi kesikli çizgi */
  padding-top: 15px;
}

.geo-tag {
  background-color: #e0f2fe; /* Çok açık mavi (sky-100) */
  color: #0c4a6e; /* Koyu mavi (sky-800) */
  padding: 5px 10px;
  border-radius: 6px;
  font-size: 0.7rem;
  font-weight: 500;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  transition: background-color 0.2s;
}

.geo-tag:hover {
  background-color: #bae6fd; /* Daha belirgin mavi */
}
/* YENİ: Tag Stilleri SONU */


.placeholder-state {
  height: 100%; display: flex; flex-direction: column;
  align-items: center; justify-content: center;
  text-align: center; color: #94a3b8; opacity: 0.8;
}
.icon-wrap { font-size: 2.5rem; margin-bottom: 15px; color: #bfdbfe; /* Açık mavi ikon */ }

.panel-footer {
  padding: 20px; border-top: 1px solid #f1f5f9; background: #fff;
}

.comment-btn {
  width: 100%; padding: 14px;
  background: #0ea5e9; /* Sky-500 Mavisi */
  color: white; border: none; border-radius: 12px;
  font-weight: 600; font-size: 1rem; cursor: pointer;
  display: flex; align-items: center; justify-content: center; gap: 10px;
  transition: all 0.2s;
  box-shadow: 0 4px 12px rgba(14, 165, 233, 0.25);
}
.comment-btn:hover { background: #0284c7; /* Hover'da daha koyu mavi */ transform: translateY(-2px); }
.comment-btn:disabled { background: #cbd5e1; cursor: not-allowed; box-shadow: none; transform: none; }

.cursor { display: inline-block; width: 2px; height: 16px; background: #0ea5e9; margin-left: 2px; animation: blink 1s infinite; vertical-align: middle; }

/* Scrollbar */
.custom-scrollbar::-webkit-scrollbar { width: 5px; }
.custom-scrollbar::-webkit-scrollbar-track { background: transparent; }
.custom-scrollbar::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 3px; }

@keyframes slideIn { from { opacity: 0; transform: translateX(20px); } to { opacity: 1; transform: translateX(0); } }
/* Pulse animasyonu maviye çevrildi */
@keyframes pulse { 0% { box-shadow: 0 0 0 0 rgba(14, 165, 233, 0.6); } 70% { box-shadow: 0 0 0 10px rgba(14, 165, 233, 0); } 100% { box-shadow: 0 0 0 0 rgba(14, 165, 233, 0); } }
@keyframes blink { 50% { opacity: 0; } }
</style>