<template>
  <div class="analyzer-modal" v-if="isOpen">
    <div class="modal-wrapper">
      
      <div class="main-panel">
        <button class="close-btn" @click="closeModal">
          <i class="fa-solid fa-xmark"></i>
        </button>

        <div class="analysis-layout">
          
          <div 
            class="image-section" 
            :class="{ 'is-panning': toolMode === 'pan' && isDragging, 'is-selecting': toolMode === 'select' }"
            @wheel.prevent="handleWheel"
            @mousedown="handleMouseDown"
            @mousemove="handleMouseMove"
            @mouseup="handleMouseUp"
            @mouseleave="handleMouseUp"
            
            @touchstart.passive="handleTouchStart"   @touchmove.passive="handleTouchMove"     @touchend="handleTouchEnd"               >
            <div class="toolbar">
               <button 
                :class="{ active: toolMode === 'pan' }" 
                @click="toolMode = 'pan'"
                title="Gezinme Modu (Pan)">
                <i class="fa-solid fa-hand"></i>
              </button>
              <button 
                :class="{ active: toolMode === 'select' }" 
                @click="toolMode = 'select'"
                title="Motif Seçme Modu">
                <i class="fa-solid fa-crop-simple"></i>
              </button>
            </div>

            <div class="zoom-controls">
              <button @click.stop="resetZoom" title="Zoom Sıfırla">
                <i class="fa-solid fa-compress"></i>
              </button>
              <span class="zoom-info">%{{ Math.round(scale * 100) }}</span>
            </div>

            <div 
              class="scan-container" 
              ref="scanContainer"
              :style="{ 
                transform: `translate(${translateX}px, ${translateY}px) scale(${scale})` 
              }"
            >
              <img :src="imageSrc" alt="Analiz" class="target-image" ref="imageRef" draggable="false" />

              <div 
                v-if="selection.active || selection.w > 0"
                class="selection-box"
                :style="{
                  top: selection.y + 'px',
                  left: selection.x + 'px',
                  width: selection.w + 'px',
                  height: selection.h + 'px'
                }"
              >
                <div class="selection-label">Seçilen Motif</div>
              </div>

              <div 
                v-for="(box, i) in detectedBoxes" 
                :key="i"
                class="detection-box"
                :style="{ top: box.top + '%', left: box.left + '%', width: box.width + '%', height: box.height + '%' }"
              >
                <span class="confidence">{{ box.confidence }}%</span>
              </div>
            </div>
          </div>

          <div class="results-section custom-scrollbar">
            <h2>Motif Analizi</h2>
            <p class="subtitle">{{ title }}</p>

            <div v-if="!hasSelection && !resultReady" class="warning-box">
              <i class="fa-solid fa-circle-info"></i>
              <p>Lütfen görsel üzerinden analiz edilecek motifi kutu içine alarak seçin.</p>
            </div>

            <div class="setting-box">
              <div class="setting-header">
                <span>Benzerlik Hassasiyeti</span>
                <strong>%{{ threshold }}</strong>
              </div>
              <input 
                type="range" 
                v-model="threshold" 
                min="30" 
                max="95" 
                step="5" 
                class="slider"
              >
              <p class="hint">Daha az benzer motifleri bulmak için değeri düşürün.</p>
            </div>

            <div class="metrics-grid">
              <div class="metric-card">
                <div class="icon-box symmetry">
                  <i class="fa-solid fa-scale-balanced"></i>
                </div>
                <div class="metric-info">
                  <span>Simetri Skoru</span>
                  <strong v-if="!isScanning && resultReady" :class="getScoreColor(symmetryScore)">
                    {{ symmetryScore }}%
                  </strong>
                  <span v-else>---</span>
                </div>
              </div>

              <div class="metric-card">
                <div class="icon-box count">
                  <i class="fa-solid fa-layer-group"></i>
                </div>
                <div class="metric-info">
                  <span>Bulunan Eşleşme</span>
                  <strong v-if="!isScanning && resultReady">{{ symbolCount }} Adet</strong>
                  <span v-else>---</span>
                </div>
              </div>
            </div>

            <button 
              class="analyze-btn" 
              @click="startAnalysis" 
              :disabled="isScanning || (!hasSelection && !resultReady)"
            >
              <span v-if="!isScanning">
                <i class="fa-solid fa-microchip"></i> Analizi Başlat
              </span>
              <span v-else>
                <i class="fa-solid fa-spinner fa-spin"></i> Taranıyor...
              </span>
            </button>

            <div class="analysis-log" v-if="resultReady">
              <p><i class="fa-solid fa-check"></i> Seçilen şablon analiz edildi.</p>
              <p><i class="fa-solid fa-check"></i> 360° döndürme taraması yapıldı.</p>
              <p><i class="fa-solid fa-check"></i> {{ symbolCount }} benzer motif bulundu.</p>
            </div>
            
            <div class="error-log" v-if="errorMessage">
              <p><i class="fa-solid fa-triangle-exclamation"></i> {{ errorMessage }}</p>
            </div>
          </div>
        </div>
      </div>

      <SideAIPanel :title="title" />

    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import SideAIPanel from './SideAIPanel.vue' // Bileşeni import ettik

const props = defineProps({
  isOpen: Boolean,
  imageSrc: String,
  title: String
})

const emit = defineEmits(['close'])

// --- STATE ---
const isScanning = ref(false)
const resultReady = ref(false)
const errorMessage = ref("")
const symmetryScore = ref(0)
const symbolCount = ref(0)
const detectedBoxes = ref([])
const threshold = ref(60) // Varsayılan hassasiyet %60

// Zoom / Pan / Selection State
const toolMode = ref('select') 
const scale = ref(1)
const translateX = ref(0)
const translateY = ref(0)
const isDragging = ref(false)
const dragStart = ref({ x: 0, y: 0 })

// Seçim Alanı State
const selection = ref({ x: 0, y: 0, w: 0, h: 0, active: false })
const imageRef = ref(null)

const hasSelection = computed(() => selection.value.w > 5 && selection.value.h > 5)

const closeModal = () => {
  emit('close')
  resetData()
}

const resetData = () => {
  resultReady.value = false
  errorMessage.value = ""
  symmetryScore.value = 0
  symbolCount.value = 0
  detectedBoxes.value = []
  selection.value = { x: 0, y: 0, w: 0, h: 0, active: false }
  resetZoom()
}

// --- MOUSE & TOUCH EVENTS HANDLER ---

// Fare ve dokunma olaylarında koordinatları almak için yardımcı fonksiyon
const getCoords = (e) => {
  // Eğer dokunma olayı ise ilk dokunuşun koordinatlarını al
  if (e.touches && e.touches.length > 0) {
    return { clientX: e.touches[0].clientX, clientY: e.touches[0].clientY }
  }
  // Fare olayı ise fare koordinatlarını al
  return { clientX: e.clientX, clientY: e.clientY }
}

const getMousePosInImage = (e) => {
  if (!imageRef.value) return { x: 0, y: 0 }
  const { clientX, clientY } = getCoords(e)
  const rect = imageRef.value.getBoundingClientRect()
  const x = (clientX - rect.left) / scale.value
  const y = (clientY - rect.top) / scale.value
  return { x, y }
}

// Ortak Etkileşim Mantığı

const startInteraction = (e) => {
    isDragging.value = true
    
    if (toolMode.value === 'pan') {
      const { clientX, clientY } = getCoords(e)
      dragStart.value = { 
        x: clientX - translateX.value, 
        y: clientY - translateY.value 
      }
    } else if (toolMode.value === 'select') {
      const pos = getMousePosInImage(e)
      selection.value = {
        x: pos.x,
        y: pos.y,
        w: 0,
        h: 0,
        startX: pos.x,
        startY: pos.y,
        active: true
      }
      resultReady.value = false
      detectedBoxes.value = []
    }
}

const moveInteraction = (e) => {
    if (!isDragging.value) return

    if (toolMode.value === 'pan') {
      const { clientX, clientY } = getCoords(e)
      translateX.value = clientX - dragStart.value.x
      translateY.value = clientY - dragStart.value.y
    } else if (toolMode.value === 'select' && selection.value.active) {
      const pos = getMousePosInImage(e)
      const currentX = pos.x
      const currentY = pos.y
      const startX = selection.value.startX
      const startY = selection.value.startY
      
      selection.value.x = Math.min(currentX, startX)
      selection.value.y = Math.min(currentY, startY)
      selection.value.w = Math.abs(currentX - startX)
      selection.value.h = Math.abs(currentY - startY)
    }
}

const endInteraction = () => {
    isDragging.value = false
    if (toolMode.value === 'select') {
      selection.value.active = false
    }
}

// Fare Olayları
const handleMouseDown = (e) => {
  if (e.button !== 0) return 
  startInteraction(e)
}
const handleMouseMove = (e) => {
  if (!isDragging.value) return
  e.preventDefault()
  moveInteraction(e)
}
const handleMouseUp = () => {
  endInteraction()
}

// Dokunmatik Olaylar
const handleTouchStart = (e) => {
    if (e.touches.length > 1) return; // Multi-touch'ı engelle
    startInteraction(e);
}
const handleTouchMove = (e) => {
    if (e.touches.length > 1) return;
    e.preventDefault(); // Kaydırmayı engelle (Pan veya Seçim yaparken kritik)
    moveInteraction(e);
}
const handleTouchEnd = () => {
    endInteraction();
}


const handleWheel = (e) => {
  const zoomFactor = 0.1
  const direction = e.deltaY > 0 ? -1 : 1
  let newScale = scale.value + (direction * zoomFactor)
  newScale = Math.min(Math.max(0.5, newScale), 5)
  scale.value = newScale
}

const resetZoom = () => {
  scale.value = 1
  translateX.value = 0
  translateY.value = 0
}

// --- API ---

const startAnalysis = async () => {
  if (isScanning.value) return
  if (!hasSelection.value) {
    errorMessage.value = "Lütfen önce analiz edilecek motifi seçin."
    return
  }

  isScanning.value = true
  errorMessage.value = ""
  detectedBoxes.value = []

  await runAIMotifAnalysis()
  
  isScanning.value = false
}

const runAIMotifAnalysis = async () => {
  try {
    const response = await fetch(props.imageSrc)
    const blob = await response.blob()

    const formData = new FormData()
    formData.append("file", blob)
    
    const renderedW = imageRef.value.width 
    const renderedH = imageRef.value.height 
    
    const ratioX = selection.value.x / renderedW
    const ratioY = selection.value.y / renderedH
    const ratioW = selection.value.w / renderedW
    const ratioH = selection.value.h / renderedH
    
    formData.append("x", ratioX)
    formData.append("y", ratioY)
    formData.append("w", ratioW)
    formData.append("h", ratioH)
    
    // Slider değerini 0.0-1.0 formatına çevir
    formData.append("threshold", threshold.value / 100) 

    const apiResponse = await fetch("https://mathetilebackend.onrender.com/analyze-template", {
      method: "POST",
      body: formData
    });

    if (!apiResponse.ok) {
        const errData = await apiResponse.json();
        throw new Error(errData.error || "Sunucu hatası");
    }

    const data = await apiResponse.json()

    symmetryScore.value = Math.round(data.symmetry)
    
    detectedBoxes.value = data.motifs.map(m => ({
      top: m.y * 100,
      left: m.x * 100,
      width: m.w * 100,
      height: m.h * 100,
      confidence: Math.round(m.confidence * 100),
      label: m.label 
    }))

    symbolCount.value = data.count
    resultReady.value = true

  }
  catch (err) {
    console.error("AI analysis failed:", err)
    errorMessage.value = err.message
  }
}

const getScoreColor = (score) => {
  if (score >= 90) return 'text-green'
  if (score >= 70) return 'text-blue'
  return 'text-orange'
}
</script>

<style scoped>
.analyzer-modal {
  position: fixed; top: 0; left: 0; width: 100%; height: 100%;
  background: rgba(22, 45, 51, 0.85); backdrop-filter: blur(8px);
  display: flex; 
  justify-content: center; 
  align-items: flex-start; /* Mobil/Tablet için modalın üstten başlamasını sağlar */
  z-index: 9999;
  font-family: 'Poppins', sans-serif;
  overflow-y: auto; /* Modal içeriği çok büyükse kaydırma çubuğu çıkar */
  padding-top: 40px; /* Popup'ı üstten uygun bir boşlukla aşağıya iter */
  padding-bottom: 40px; 
}

/* --- Responsive Düzenlemeler --- */

.modal-wrapper {
  display: flex;
  align-items: flex-start; /* Dikeyde üstten hizala */
  justify-content: center; /* Yatayda ortala */
  width: 100%;
  padding: 10px;
  box-sizing: border-box;
  padding-top: 7rem;
}

.main-panel {
  background: white; 
  width: 100%; 
  max-width: 1000px; 
  height: 700px;
  max-height: 95vh;
  border-radius: 20px; 
  position: relative; 
  overflow: hidden;
  box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.25); 
  display: flex;
  flex-shrink: 0;
}

.close-btn {
  position: absolute; top: 15px; right: 15px; background: rgba(0,0,0,0.1);
  border: none; width: 36px; height: 36px; border-radius: 50%;
  cursor: pointer; z-index: 20; transition: all 0.2s;
}
.close-btn:hover { background: rgba(0,0,0,0.2); transform: rotate(90deg); }

/* Sol Taraf */
.analysis-layout { display: flex; width: 100%; height: 100%; }
.image-section {
  flex: 3; background: #f0f2f5; display: flex; align-items: center;
  justify-content: center; padding: 20px; position: relative;
  overflow: hidden; user-select: none;
}
.image-section.is-panning { cursor: grab; }
.image-section.is-panning:active { cursor: grabbing; }
.image-section.is-selecting { cursor: crosshair; }

.toolbar {
  position: absolute; top: 20px; left: 20px; display: flex; gap: 8px; z-index: 50;
  background: white; padding: 5px; border-radius: 8px; box-shadow: 0 4px 12px rgba(0,0,0,0.1);
}
.toolbar button {
  width: 36px; height: 36px; border: none; background: transparent;
  border-radius: 6px; cursor: pointer; color: #64748b; font-size: 1.1rem; transition: all 0.2s;
}
.toolbar button:hover { background: #f1f5f9; color: #0f172a; }
.toolbar button.active { background: #0f172a; color: white; }

.scan-container { position: relative; box-shadow: 0 10px 30px rgba(0,0,0,0.15); transform-origin: center center; }
.target-image { display: block; max-width: 100%; max-height: 550px; object-fit: contain; pointer-events: none; }

.selection-box {
  position: absolute; border: 2px dashed #ff0055; background: rgba(255, 0, 85, 0.1);
  z-index: 10; pointer-events: none;
}
.selection-label {
  position: absolute; top: -22px; left: -2px; background: #ff0055; color: white;
  font-size: 0.7rem; padding: 2px 6px; border-radius: 4px 4px 0 0; white-space: nowrap;
}

.detection-box {
  position: absolute; border: 2px solid #00ff88; box-shadow: 0 0 5px rgba(0, 255, 136, 0.5);
  background: rgba(0, 255, 136, 0.1); z-index: 5;
  animation: boxPop 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
}
.detection-box .confidence {
  position: absolute; top: -18px; left: 0; background: #00ff88; color: #000;
  font-size: 0.6rem; font-weight: bold; padding: 1px 4px; display: none;
}
.detection-box:hover .confidence { display: block; }
@keyframes boxPop { from { transform: scale(0); opacity: 0; } to { transform: scale(1); opacity: 1; } }

.zoom-controls {
  position: absolute; bottom: 20px; left: 20px; background: rgba(255, 255, 255, 0.9);
  padding: 8px 12px; border-radius: 8px; box-shadow: 0 4px 12px rgba(0,0,0,0.1);
  display: flex; align-items: center; gap: 10px; z-index: 50; font-size: 0.85rem; color: #334155;
}
.zoom-controls button { background: none; border: none; cursor: pointer; color: #0f172a; padding: 4px; }

/* Sağ Taraf (Analiz Sonuçları) */
.results-section {
  flex: 1.2; padding: 40px 30px; background: #ffffff; display: flex; flex-direction: column;
  border-left: 1px solid #e2e8f0; 
  overflow-y: auto; /* Analiz sonuçları bölümünün scrollbar'ı */
}
.custom-scrollbar::-webkit-scrollbar { width: 6px; }
.custom-scrollbar::-webkit-scrollbar-track { background: #f1f1f1; }
.custom-scrollbar::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 4px; }
.subtitle { color: #64748b; font-size: 0.9rem; margin-bottom: 20px; }
.warning-box {
  background: #fffbeb; border: 1px solid #fcd34d; color: #92400e; padding: 12px;
  border-radius: 8px; font-size: 0.85rem; margin-bottom: 20px; display: flex; gap: 10px; align-items: center;
}
.setting-box {
  margin-bottom: 20px; padding: 15px; background: #f8fafc; border-radius: 12px; border: 1px solid #e2e8f0;
}
.setting-header { display: flex; justify-content: space-between; margin-bottom: 10px; font-size: 0.9rem; color: #334155; }
.slider { width: 100%; height: 6px; background: #cbd5e1; border-radius: 5px; outline: none; -webkit-appearance: none; }
.slider::-webkit-slider-thumb {
  -webkit-appearance: none; width: 20px; height: 20px; background: #0f172a; border-radius: 50%;
  cursor: pointer; transition: background .15s ease-in-out;
}
.slider::-webkit-slider-thumb:hover { background: #334155; }
.hint { font-size: 0.75rem; color: #94a3b8; margin-top: 8px; }

.metrics-grid { display: grid; gap: 15px; margin-bottom: 30px; }
.metric-card { display: flex; align-items: center; background: #f8fafc; padding: 15px; border-radius: 12px; border: 1px solid #e2e8f0; }
.icon-box { width: 45px; height: 45px; border-radius: 10px; display: flex; align-items: center; justify-content: center; font-size: 1.2rem; margin-right: 15px; }
.icon-box.symmetry { background: #e0f2fe; color: #0284c7; }
.icon-box.count { background: #f0fdf4; color: #16a34a; }
.metric-info span { font-size: 0.8rem; color: #64748b; }
.metric-info strong { font-size: 1.2rem; color: #0f172a; margin-left: 0.2rem; }

.analyze-btn {
  background: #0f172a; color: white; border: none; padding: 16px; border-radius: 12px;
  font-size: 1rem; font-weight: 600; cursor: pointer; transition: all 0.3s;
  display: flex; align-items: center; justify-content: center; gap: 10px; margin-top: auto;
}
.analyze-btn:hover { background: #334155; }
.analyze-btn:disabled { background: #94a3b8; cursor: not-allowed; }
.analysis-log { margin-top: 25px; padding-top: 20px; border-top: 1px solid #e2e8f0; font-size: 0.9rem; color: #475569; }
.analysis-log p { margin-bottom: 8px; display: flex; align-items: center; gap: 10px; }
.error-log { margin-top: 15px; color: #ef4444; font-size: 0.85rem; }
.text-green { color: #16a34a; } .text-blue { color: #0284c7; } .text-orange { color: #ea580c; }


/* --- Responsive Kırılma Noktaları --- */

/* Tablet (Max 1200px) ve Küçük Ekran Düzenlemeleri */
@media (max-width: 1200px) {
  .modal-wrapper {
    flex-direction: column;
    align-items: center; 
    gap: 20px;
    padding: 0; 
  }
  
  .main-panel {
    width: 95vw;
    height: auto; 
    min-height: 600px;
  }
}

/* Mobil Düzenlemeler (Max 768px) */
@media (max-width: 768px) {
  .modal-wrapper{
    /* Tablet ve mobil görünümde wrapper padding'i kaldırıldı, 
       üstten boşluk .analyzer-modal'daki padding-top ile sağlanır. */
    padding: 0; 
  }
  
  .main-panel {
    width: 90vw;
    min-height: auto;
    border-radius: 12px;
  }

  /* Ana paneli dikey bölümlere ayır */
  .analysis-layout {
    flex-direction: column; 
    height: auto;
  }

  /* Görsel bölümü */
  .image-section {
    flex: none; 
    height: 350px; 
    min-height: 300px;
    padding: 10px;
  }
  
  /* Görseli kaplayan konteynerin ölçeği */
  .target-image { 
      max-height: 100%; 
  }

  /* Araç çubuğunu ve zoom kontrolünü küçült */
  .toolbar {
    top: 10px; left: 10px;
    padding: 3px;
    gap: 5px;
  }
  .toolbar button {
    width: 32px; height: 32px; font-size: 1rem;
  }

  .zoom-controls {
    bottom: 10px; right: 10px; left: auto; 
    padding: 6px 10px;
    font-size: 0.75rem;
  }

  /* Sonuçlar bölümü */
  .results-section {
    flex: none; 
    padding: 20px 15px; 
    border-left: none;
    border-top: 1px solid #e2e8f0; 
  }

  .metrics-grid {
    grid-template-columns: 1fr; 
    gap: 10px;
  }
  
  .analyze-btn {
    padding: 12px;
  }

  h2 { font-size: 1.5rem; }
  .subtitle { font-size: 0.8rem; }
}

/* Çok Küçük Ekranlar (Max 480px) */
@media (max-width: 480px) {
  .metric-card {
    padding: 10px;
  }
  .icon-box {
    width: 35px; height: 35px; font-size: 1rem; margin-right: 10px;
  }
  .metric-info strong { font-size: 1rem; }
  .analyze-btn {
    font-size: 0.9rem;
  }
}
</style>