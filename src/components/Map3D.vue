<template>
  <div class="map-container">
    <div ref="mapContainer" class="map"></div>
    
    <!-- Map Controls -->
    <div class="map-controls">
      <button @click="toggle3D" class="control-btn">
        {{ is3D ? '2D View' : '3D View' }}
      </button>
      <button @click="resetView" class="control-btn">Reset View</button>
      <div class="location-selector">
        <select v-model="selectedLocation" @change="flyToSelectedLocation" class="location-select">
          <option value="">Select Airport</option>
          <option value="heathrow">Heathrow Airport (LHR)</option>
          <option value="riga">Riga Airport (RIX)</option>
          <option value="dubai">Dubai Airport (DXB)</option>
        </select>
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
const selectedLocation = ref('')
const currentAirport = ref<{ name: string; description: string } | null>(null)

// Airport locations data
const airports = {
  heathrow: {
    name: 'Heathrow Airport (LHR)',
    coordinates: [-0.4619, 51.4700] as [number, number],
    zoom: 12,
    description: 'London Heathrow Airport is the busiest airport in the UK and one of the world\'s major aviation hubs.'
  },
  riga: {
    name: 'Riga Airport (RIX)',
    coordinates: [23.9711, 56.9236] as [number, number],
    zoom: 12,
    description: 'Riga International Airport is the largest airport in the Baltic states and serves as a major hub for airBaltic.'
  },
  dubai: {
    name: 'Dubai Airport (DXB)',
    coordinates: [55.2708, 25.2532] as [number, number],
    zoom: 12,
    description: 'Dubai International Airport is the world\'s busiest airport by international passenger traffic.'
  }
}



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
  selectedLocation.value = ''
  currentAirport.value = null
}

const flyToSelectedLocation = () => {
  if (!map.value || !selectedLocation.value) return
  
  const airport = airports[selectedLocation.value as keyof typeof airports]
  if (!airport) return
  
  currentAirport.value = {
    name: airport.name,
    description: airport.description
  }
  
  map.value.flyTo({
    center: airport.coordinates,
    zoom: airport.zoom,
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
    trackUserLocation: true
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
  transition: all 0.3s ease;
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

.location-selector {
  margin-top: 5px;
}

.location-select {
  width: 100%;
  padding: 10px 12px;
  border: 1px solid #ddd;
  border-radius: 6px;
  background: rgba(255, 255, 255, 0.95);
  font-size: 14px;
  font-weight: 500;
  color: #333;
  cursor: pointer;
  transition: all 0.2s ease;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.location-select:hover {
  background: rgba(255, 255, 255, 1);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
}

.location-select:focus {
  outline: none;
  border-color: #007bff;
  box-shadow: 0 0 0 2px rgba(0, 123, 255, 0.25);
}

.map-info {
  position: absolute;
  bottom: 20px;
  left: 20px;
  z-index: 1000;
  transition: all 0.3s ease;
}

.airport-info {
  margin-top: 16px;
  padding-top: 16px;
  border-top: 1px solid #eee;
}

.airport-info h4 {
  margin: 0 0 8px 0;
  font-size: 14px;
  color: #333;
  font-weight: 600;
}

.airport-info p {
  margin: 0;
  font-size: 13px;
  color: #666;
  line-height: 1.4;
}
</style> 