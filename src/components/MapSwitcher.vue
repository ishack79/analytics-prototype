<template>
  <div class="map-switcher">
    <div class="switcher-controls">
      <button 
        @click="switchToMapLibre" 
        :class="['switch-btn', { active: currentMap === 'maplibre' }]"
      >
        <span class="btn-icon">🗺️</span>
        <span class="btn-text">MapLibre GL JS</span>
      </button>
      <button 
        @click="switchToMapbox" 
        :class="['switch-btn', { active: currentMap === 'mapbox' }]"
      >
        <span class="btn-icon">🗺️</span>
        <span class="btn-text">Mapbox GL JS</span>
      </button>
    </div>
    
    <div class="map-container">
      <Map3D v-if="currentMap === 'maplibre'" />
      <MapboxMap v-else-if="currentMap === 'mapbox'" />
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import Map3D from './Map3D.vue'
import MapboxMap from './MapboxMap.vue'

const currentMap = ref<'maplibre' | 'mapbox'>('maplibre')

const switchToMapLibre = () => {
  currentMap.value = 'maplibre'
}

const switchToMapbox = () => {
  currentMap.value = 'mapbox'
}
</script>

<style scoped>
.map-switcher {
  width: 100%;
  height: 100vh;
  position: relative;
}

.switcher-controls {
  position: absolute;
  top: 20px;
  right: 20px;
  z-index: 2000;
  display: flex;
  gap: 10px;
  background: rgba(255, 255, 255, 0.95);
  padding: 10px;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.switch-btn {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 10px 16px;
  border: 1px solid #ddd;
  border-radius: 6px;
  background: rgba(255, 255, 255, 0.9);
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s ease;
  color: #333;
}

.switch-btn:hover {
  background: rgba(255, 255, 255, 1);
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  transform: translateY(-1px);
}

.switch-btn.active {
  background: #007bff;
  color: white;
  border-color: #0056b3;
  box-shadow: 0 2px 8px rgba(0, 123, 255, 0.3);
}

.switch-btn.active:hover {
  background: #0056b3;
  box-shadow: 0 4px 12px rgba(0, 123, 255, 0.4);
}

.btn-icon {
  font-size: 16px;
}

.btn-text {
  font-weight: 600;
}

.map-container {
  width: 100%;
  height: 100%;
}
</style> 