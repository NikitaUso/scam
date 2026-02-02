<script setup>
import { ref } from 'vue'

const hasSaidYes = ref(false)
const noBtnStyle = ref({})
const noCount = ref(0) 
const showPopup = ref(false)
const popupText = ref('')
const isAnimating = ref(false)
const isLaunched = ref(false)

function onYes() {
  hasSaidYes.value = true
}

function closePopup() {
  showPopup.value = false
}

function moveNoButton() {
  if (showPopup.value || isAnimating.value || isLaunched.value) return

  isAnimating.value = true
  setTimeout(() => {
    isAnimating.value = false
  }, 300)

  noCount.value++
  
  // --- POPUPS ---
  if (noCount.value === 5) {
    popupText.value = "Men nu räcker det!"
    showPopup.value = true
  } else if (noCount.value === 10) {
    popupText.value = "Seriöst..."
    showPopup.value = true
  } else if (noCount.value === 15) {
    popupText.value = "Testa nu då"
    showPopup.value = true
  }

  // --- NEJ KNAPPEN DRAR IVÄG (Hejdå!) ---
  if (noCount.value >= 20) {
    isLaunched.value = true
    
    // Nollställ positionen så animationen tar över
    noBtnStyle.value = {
      position: 'fixed',
      left: noBtnStyle.value.left || '50%', 
      top: noBtnStyle.value.top || '50%',
      transition: 'none' 
    }
    return
  }

  // --- VANLIGT HOPPANDE ---
  const maxX = window.innerWidth - 250
  const maxY = window.innerHeight - 100
  const x = Math.max(20, Math.random() * maxX)
  const y = Math.max(20, Math.random() * maxY)

  noBtnStyle.value = {
    position: 'fixed',
    left: `${x}px`,
    top: `${y}px`,
    zIndex: 50,
    // Krymp Nej-knappen när vi närmar oss slutet
    transform: noCount.value >= 15 ? 'scale(0.4)' : 'scale(1)',
    transition: 'top 0.3s, left 0.3s'
  }
}
</script>

<template>
  <div class="page-container">
    
    <div v-if="showPopup" class="overlay" @click="closePopup">
      <div class="popup-box">
        <p>{{ popupText }}</p>
        <span class="click-hint"></span>
      </div>
    </div>

    <div v-if="!hasSaidYes" class="content-wrapper">
      <h1 class="title">Will you be my valentine?</h1>
      
      <div class="button-group">
        
        <button 
          @click="onYes" 
          class="btn yes-btn" 
          :class="{ 
            'gigantic': noCount >= 15 && noCount < 20,
            'colossal': noCount >= 20 
          }"
        >
          Ja
        </button>
        
        <button 
          @mouseover="moveNoButton" 
          @touchstart="moveNoButton" 
          @click="moveNoButton" 
          :style="noBtnStyle" 
          class="btn no-btn"
          :class="{ 'fly-away': isLaunched }"
        >
          Nej
        </button>
      </div>
    </div>

    <div v-else class="success-message">
      <h1 class="title">❤️</h1>
      <p>Vi ses den 14:e!</p>
    </div>

  </div>
</template>

<style>
/* Reset */
html, body, #app {
  width: 100%;
  height: 100%;
  margin: 0 !important;
  padding: 0 !important;
  max-width: none !important;
  display: block !important;
  overflow: hidden;
}
</style>

<style scoped>
/* --- ANIMATIONER --- */

@keyframes popIn {
  from { transform: scale(0.5); opacity: 0; }
  to { transform: scale(1); opacity: 1; }
}

@keyframes pulse {
  0% { transform: scale(2.5); }
  50% { transform: scale(2.7); }
  100% { transform: scale(2.5); }
}

/* Ny animation för den ENORMA knappen */
@keyframes superPulse {
  0% { transform: scale(5) rotate(0deg); }
  50% { transform: scale(5.2) rotate(2deg); }
  100% { transform: scale(5) rotate(0deg); }
}

@keyframes yeet {
  0% { transform: scale(0.4) translateX(0); }
  20% { transform: scale(0.4) translateX(-50px) rotate(-10deg); }
  40% { transform: scale(0.4) translateX(-50px) rotate(-10deg); }
  100% { transform: scale(0.4) translateX(2000px) rotate(720deg); opacity: 0; }
}

.fly-away {
  animation: yeet 1.5s forwards cubic-bezier(0.6, -0.28, 0.735, 0.045);
  pointer-events: none; 
}


/* --- STIL --- */
.overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  backdrop-filter: blur(5px);
  z-index: 999;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
}

.popup-box {
  background: white;
  padding: 30px 50px;
  border-radius: 20px;
  text-align: center;
  box-shadow: 0 10px 30px rgba(0,0,0,0.3);
  animation: popIn 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
}

.popup-box p {
  font-size: 2rem;
  font-weight: bold;
  color: #333;
  margin: 0 0 10px 0;
  font-family: 'Arial', sans-serif;
}

.click-hint {
  font-size: 0.9rem;
  color: #888;
}

.page-container {
  background: linear-gradient(135deg, #ff7eb3 0%, #ff758c 100%);
  min-height: 100vh;
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  text-align: center;
  position: relative;
}

.title {
  color: white;
  font-family: 'Arial', sans-serif;
  font-size: 3rem;
  margin-bottom: 2rem;
  text-shadow: 2px 2px 8px rgba(0,0,0,0.3);
  font-weight: bold;
  pointer-events: none;
  transition: all 0.5s ease;
  z-index: 20; 
}

.button-group {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
  align-items: center;
  width: 100%;
  justify-content: center;
  min-height: 200px;
  position: relative;
}

.btn {
  background-color: #FFCCFD;
  color: #333;
  border: none;
  padding: 20px;
  font-size: 1.5rem;
  font-weight: bold;
  border-radius: 50px;
  cursor: pointer;
  width: 80%;
  max-width: 300px;
  box-shadow: 0 4px 15px rgba(0,0,0,0.2);
  user-select: none;
  -webkit-user-select: none;
  transition: all 0.3s ease;
}

.yes-btn {
  z-index: 10;
  position: relative;
  /* Vi lägger till en transition för transform så den växer mjukt */
  transition: transform 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275);
}

/* STADIE 1: GIGANTISK (15-19 klick) */
.gigantic {
  background-color: #5eff82;
  box-shadow: 0 10px 40px rgba(0,0,0,0.5);
  animation: pulse 1.5s infinite ease-in-out;
  /* Vi tar bort statisk transform här och låter animationen sköta skalan */
}

/* STADIE 2: COLOSSAL (20+ klick - Finalen!) */
.colossal {
  background-color: #5eff82;
  box-shadow: 0 0 100px rgba(94, 255, 130, 0.8); /* Kraftigt grönt sken */
  z-index: 100; /* Allra överst */
  
  /* Lås fast den i mitten av skärmen och gör den ENORM */
  position: fixed;
  top: 50%;
  left: 50%;
  /* translate(-50%, -50%) centrerar den. scale(5) gör den 5 gånger större */
  transform: translate(-50%, -50%) scale(5); 
  
  animation: superPulse 1s infinite ease-in-out; /* Snabbare, galnare puls */
}

.yes-btn:active {
  transform: scale(0.95);
}
.gigantic:active {
  animation: none;
  transform: scale(2.4);
}
.colossal:active {
  animation: none;
  transform: translate(-50%, -50%) scale(4.8);
}

.success-message p {
  color: white;
  font-size: 2rem;
  font-family: 'Arial', sans-serif;
}

@media (min-width: 768px) {
  .title {
    font-size: 5rem;
  }
  .button-group {
    flex-direction: row;
    justify-content: center;
  }
  .btn {
    width: auto;
    padding: 15px 60px;
  }
}
</style>