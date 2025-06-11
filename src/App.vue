<script setup>
import { ref, onMounted, onUnmounted } from 'vue';
import { BrowserMultiFormatReader } from '@zxing/library';

const wynik_skanu = ref('');
const codeReader = ref(null);
const isScanning = ref(true);
const buttonMsg = ref('Zatrzymaj skanowanie');

onMounted(async () => {
  codeReader.value = new BrowserMultiFormatReader();
  await startScanning();
});

const startScanning = async () => {
  try {
    wynik_skanu.value = 'Skanowanie...';
    
    await codeReader.value.decodeFromVideoDevice(
      undefined,
      'skaner',
      (result, error) => {
        if (result) {
          wynik_skanu.value = result.getText();
        }
        if (error) {
          console.error('Błąd skanowania:', error);
        }
      }
    );
  } catch (err) {
    console.error('Błąd inicjalizacji:', err);
    isScanning.value = false;
  }
};

const toggleScanning = () => {
  if (isScanning.value) {
    codeReader.value.reset();
    wynik_skanu.value = 'Skanowanie zatrzymane';
    buttonMsg.value = 'Wznów skanowanie';
  } else {
    buttonMsg.value = 'Zatrzymaj skanowanie';
    startScanning();
  }
  isScanning.value = !isScanning.value;
};

onUnmounted(() => {
  if (codeReader.value) {
    codeReader.value.reset();
  }
});
</script>

<template>
  <section>
    <h1>Inwentaryzacja PBŚ</h1>
    <div id="skaner-container">
      <video id="skaner" style="width: 100%; height: 100%; object-fit: cover;"></video>
    </div>
    <p id="skan">{{ wynik_skanu || 'Brak wyniku' }}</p>
    <button @click="toggleScanning" :class="{ 'stop-btn': isScanning, 'start-btn': !isScanning }"> {{ buttonMsg }} </button>
  </section>
</template>

<style scoped>
* {
  box-sizing: border-box;
  -webkit-tap-highlight-color: transparent;
}

section {
  text-align: center;
  font-family: "Saira", sans-serif;
  font-weight: 600;
  display: flex;
  flex-direction: column;
  align-items: center;
  min-height: 90vh;
}

h1 {
  font-size: clamp(1.5rem, 5vw, 2rem);
  font-weight: bold;
  background: #830e21;
  color: white;
  width: 100%;
  padding: 15px;
  margin: 0 0 15px 0;
}

#skaner-container {
  width: min(95%, 500px);
  height: clamp(200px, 30vh, 300px);
  margin: 0 auto 15px;
  background: #f0f0f0;
  border: 2px solid #ddd;
  overflow: hidden;
  position: relative;
}

button {
  font-family: "Saira", sans-serif;
  font-weight: 600;
  padding: 12px 20px;
  background: #830e21;
  color: white;
  border: none;
  border-radius: 8px;
  font-size: clamp(1rem, 4vw, 1.2rem);
  cursor: pointer;
  margin-top: 15px;
}

button:hover {
  background: #6a0b1a;
}

#skan {
  width: min(95%, 500px);
  background: #f0f0f0;
  border: 2px solid #ddd;
  font-size: clamp(1rem, 4vw, 1.2rem);
  margin: 15px 0;
  padding: 12px;
  border-radius: 4px;
}
</style>