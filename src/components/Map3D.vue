<template>
  <div class="map-container">
    <div ref="mapContainer" class="map"></div>
    <div class="map-controls">
      <button @click="toggle3D" class="control-btn">
        {{ is3D ? '2D View' : '3D View' }}
      </button>
      <button @click="resetView" class="control-btn">Reset View</button>
      <button @click="flyToLocation" class="control-btn">Fly to Location</button>
    </div>
    <div class="map-info">
      <div class="info-panel">
        <h3>3D Map Controls</h3>
        <ul>
          <li>Drag to pan</li>
          <li>Scroll to zoom</li>
          <li>Right-click + drag to rotate</li>
          <li>Ctrl + scroll to tilt</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'
import maplibregl from 'maplibre-gl'
import 'maplibre-gl/dist/maplibre-gl.css'

const mapContainer = ref<HTMLElement>()
const map = ref<maplibregl.Map>()
const is3D = ref(false)

const toggle3D = () => {
  if (!map.value) return
  
  is3D.value = !is3D.value
  
  if (is3D.value) {
    // Enable 3D view with pitch
    map.value.setPitch(60)
    map.value.setBearing(0)
    
    // Add terrain source using OpenTopography
    map.value.addSource('terrain', {
      type: 'raster-dem',
      url: 'https://api.maptiler.com/tiles/terrain-rgb/tiles.json?key=YOUR_MAPTILER_KEY',
      tileSize: 256
    })
    
    // Add hillshade layer
    map.value.addLayer({
      id: 'hillshade',
      type: 'hillshade',
      source: 'terrain',
      paint: {
        'hillshade-shadow-color': '#000000',
        'hillshade-highlight-color': '#FFFFFF',
        'hillshade-accent-color': '#000000'
      }
    })
    
    // Add terrain layer
    map.value.addLayer({
      id: 'terrain-fill',
      type: 'fill',
      source: 'terrain',
      paint: {
        'fill-color': '#000000',
        'fill-opacity': 0.1
      }
    })
    
    // Set terrain
    map.value.setTerrain({ source: 'terrain', exaggeration: 1.5 })
  } else {
    // Disable 3D view
    map.value.setPitch(0)
    map.value.setBearing(0)
    map.value.setTerrain(null)
    
    // Remove terrain layers
    if (map.value.getLayer('hillshade')) {
      map.value.removeLayer('hillshade')
    }
    if (map.value.getLayer('terrain-fill')) {
      map.value.removeLayer('terrain-fill')
    }
    if (map.value.getSource('terrain')) {
      map.value.removeSource('terrain')
    }
  }
}

const resetView = () => {
  if (!map.value) return
  
  map.value.flyTo({
    center: [0, 0],
    zoom: 2,
    pitch: 0,
    bearing: 0,
    duration: 2000
  })
  is3D.value = false
}

const flyToLocation = () => {
  if (!map.value) return
  
  // Fly to a specific location (e.g., Mount Everest)
  map.value.flyTo({
    center: [86.9250, 27.9881],
    zoom: 10,
    pitch: is3D.value ? 60 : 0,
    bearing: 0,
    duration: 3000
  })
}

onMounted(() => {
  if (!mapContainer.value) return

  map.value = new maplibregl.Map({
    container: mapContainer.value,
    style: {
      version: 8,
      sources: {
        'osm': {
          type: 'raster',
          tiles: [
            'https://a.tile.openstreetmap.org/{z}/{x}/{y}.png',
            'https://b.tile.openstreetmap.org/{z}/{x}/{y}.png',
            'https://c.tile.openstreetmap.org/{z}/{x}/{y}.png'
          ],
          tileSize: 256,
          attribution: '© OpenStreetMap contributors'
        }
      },
      layers: [
        {
          id: 'osm-tiles',
          type: 'raster',
          source: 'osm',
          minzoom: 0,
          maxzoom: 18
        }
      ]
    },
    center: [0, 0],
    zoom: 2,
    pitch: 0,
    bearing: 0,
    maxPitch: 85
  })

  // Add navigation controls
  map.value.addControl(new maplibregl.NavigationControl(), 'top-right')
  
  // Add fullscreen control
  map.value.addControl(new maplibregl.FullscreenControl(), 'top-right')
  
  // Add geolocate control
  map.value.addControl(new maplibregl.GeolocateControl({
    positionOptions: {
      enableHighAccuracy: true
    },
    trackUserLocation: true,
    showUserHeading: true
  }), 'top-right')
  
  // Add scale control
  map.value.addControl(new maplibregl.ScaleControl({
    maxWidth: 80,
    unit: 'metric'
  }), 'bottom-left')
})

onUnmounted(() => {
  if (map.value) {
    map.value.remove()
  }
})
</script>

<style scoped>
.map-container {
  position: relative;
  width: 100%;
  height: 100vh;
}

.map {
  width: 100%;
  height: 100%;
}

.map-controls {
  position: absolute;
  top: 20px;
  left: 20px;
  z-index: 1000;
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.control-btn {
  background: rgba(255, 255, 255, 0.95);
  border: 1px solid #ddd;
  border-radius: 6px;
  padding: 10px 16px;
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s ease;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  color: #333;
}

.control-btn:hover {
  background: rgba(255, 255, 255, 1);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  transform: translateY(-1px);
}

.control-btn:active {
  transform: translateY(0);
}

.map-info {
  position: absolute;
  bottom: 20px;
  left: 20px;
  z-index: 1000;
}

.info-panel {
  background: rgba(255, 255, 255, 0.95);
  border-radius: 8px;
  padding: 16px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  max-width: 250px;
}

.info-panel h3 {
  margin: 0 0 12px 0;
  font-size: 16px;
  color: #333;
}

.info-panel ul {
  margin: 0;
  padding-left: 20px;
  font-size: 14px;
  color: #666;
  line-height: 1.4;
}

.info-panel li {
  margin-bottom: 4px;
}
</style> 