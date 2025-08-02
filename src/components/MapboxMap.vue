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
import mapboxgl from 'mapbox-gl'
import 'mapbox-gl/dist/mapbox-gl.css'

const mapContainer = ref<HTMLElement>()
const map = ref<mapboxgl.Map>()
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
    
    // Add 3D building layer
    map.value.addLayer({
      id: '3d-buildings',
      source: 'composite',
      'source-layer': 'building',
      filter: ['==', 'extrude', 'true'],
      type: 'fill-extrusion',
      minzoom: 15,
      paint: {
        'fill-extrusion-color': '#aaa',
        'fill-extrusion-height': [
          'interpolate',
          ['linear'],
          ['zoom'],
          15,
          0,
          15.05,
          ['get', 'height']
        ],
        'fill-extrusion-base': [
          'interpolate',
          ['linear'],
          ['zoom'],
          15,
          0,
          15.05,
          ['get', 'min_height']
        ],
        'fill-extrusion-opacity': 0.6
      }
    })
  } else {
    // Disable 3D view
    map.value.setPitch(0)
    map.value.setBearing(0)
    
    // Remove 3D building layer
    if (map.value.getLayer('3d-buildings')) {
      map.value.removeLayer('3d-buildings')
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

  // Set your Mapbox access token here
  // For testing, you can use a public token or get your own from https://www.mapbox.com/
  mapboxgl.accessToken = 'pk.eyJ1IjoiaXNoYWNrNzkiLCJhIjoiY21kdTZpb3BpMTlkZjJtczY3eXdqNGdwcyJ9.8bSM9Ew2c8QUrmRO0IGCrw'

  map.value = new mapboxgl.Map({
    container: mapContainer.value,
    style: 'mapbox://styles/mapbox/streets-v12',
    center: [0, 0],
    zoom: 2,
    pitch: 0,
    bearing: 0,
    maxPitch: 85
  })

  // Add navigation controls
  map.value.addControl(new mapboxgl.NavigationControl(), 'top-right')
  
  // Add fullscreen control
  map.value.addControl(new mapboxgl.FullscreenControl(), 'top-right')
  
  // Add geolocate control
  map.value.addControl(new mapboxgl.GeolocateControl({
    positionOptions: {
      enableHighAccuracy: true
    },
    trackUserLocation: true
  }), 'top-right')
  
  // Add scale control
  map.value.addControl(new mapboxgl.ScaleControl({
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