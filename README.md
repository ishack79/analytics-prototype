# Vue 3 Dual 3D Map Application

A modern Vue 3 application featuring interactive 3D maps built with both MapLibre GL JS and Mapbox GL JS, with the ability to switch between them seamlessly.

## Features

- **Dual Map Support**: Switch between MapLibre GL JS and Mapbox GL JS
- **3D Terrain Visualization**: Toggle between 2D and 3D views with terrain elevation
- **3D Building Rendering**: Built-in 3D buildings in Mapbox GL JS
- **Interactive Controls**: Pan, zoom, rotate, and tilt the map
- **Navigation Controls**: Built-in navigation, fullscreen, and geolocation controls
- **Airport Navigation**: Fly to specific airports (Heathrow, Riga, Dubai)
- **Responsive Design**: Modern UI with smooth animations and hover effects
- **Open Source Option**: MapLibre GL JS is open source, Mapbox GL JS is proprietary

## Getting Started

### Prerequisites

- Node.js (v18 or higher)
- npm or yarn

### Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd analytics-prototype
```

2. Install dependencies:
```bash
npm install
```

3. Start the development server:
```bash
npm run dev
```

4. Open your browser and navigate to `http://localhost:5173`

## Usage

### Map Controls

- **Map Switcher**: Use the buttons in the top-right to switch between MapLibre GL JS and Mapbox GL JS
- **Pan**: Click and drag to move around the map
- **Zoom**: Use mouse wheel or pinch gestures
- **Rotate**: Right-click and drag to rotate the view
- **Tilt**: Hold Ctrl and scroll to adjust the pitch
- **3D Toggle**: Click the "3D View" button to enable terrain visualization
- **Reset**: Click "Reset View" to return to the default view
- **Airport Selector**: Use the dropdown to fly to specific airports:
  - Heathrow Airport (LHR) - London, UK
  - Riga Airport (RIX) - Riga, Latvia
  - Dubai Airport (DXB) - Dubai, UAE

### 3D Features

When 3D mode is enabled:
- **MapLibre GL JS**: The map tilts to show terrain elevation with hillshading
- **Mapbox GL JS**: The map tilts and shows 3D buildings with realistic depth
- Terrain exaggeration is applied for better visualization
- You can still pan, zoom, and rotate in 3D mode

## Configuration

### API Keys Setup

This application supports both MapLibre GL JS and Mapbox GL JS. You'll need to configure API keys for full functionality.

#### MapTiler API Key (for MapLibre GL JS)

For full terrain functionality in MapLibre GL JS, you'll need to obtain a MapTiler API key:

1. Sign up at [MapTiler](https://www.maptiler.com/)
2. Get your API key from the dashboard
3. Replace `YOUR_MAPTILER_KEY` in `src/components/Map3D.vue` with your actual key

```javascript
// In Map3D.vue, line ~50
url: 'https://api.maptiler.com/tiles/terrain-rgb/tiles.json?key=YOUR_ACTUAL_KEY'
```

#### Mapbox Access Token (for Mapbox GL JS)

For Mapbox GL JS functionality, you'll need to obtain a Mapbox access token:

1. Sign up at [Mapbox](https://www.mapbox.com/)
2. Get your access token from the dashboard
3. Replace `YOUR_MAPBOX_ACCESS_TOKEN` in `src/components/MapboxMap.vue` with your actual token

```javascript
// In MapboxMap.vue, line ~150
mapboxgl.accessToken = 'YOUR_ACTUAL_MAPBOX_TOKEN'
```

### Customization

You can customize the maps by modifying the following:

**MapLibre GL JS (`src/components/Map3D.vue`):**
- **Initial View**: Change `center` and `zoom` in the map configuration
- **Terrain Exaggeration**: Adjust the `exaggeration` value in `setTerrain()`
- **3D Pitch**: Modify the pitch angle in the `toggle3D()` function
- **Map Style**: Replace the OSM tiles with other tile sources

**Mapbox GL JS (`src/components/MapboxMap.vue`):**
- **Initial View**: Change `center` and `zoom` in the map configuration
- **Map Style**: Change the style URL (e.g., `mapbox://styles/mapbox/satellite-v9`)
- **3D Buildings**: Customize building appearance in the `toggle3D()` function
- **3D Pitch**: Modify the pitch angle in the `toggle3D()` function

## Technology Stack

- **Vue 3**: Progressive JavaScript framework with Composition API
- **TypeScript**: Type-safe JavaScript development
- **MapLibre GL JS**: Open-source mapping library
- **Mapbox GL JS**: Proprietary mapping library with advanced 3D features
- **OpenStreetMap**: Free, editable world map (MapLibre)
- **Mapbox Tiles**: High-quality map tiles (Mapbox)
- **Vite**: Fast build tool and development server

## Project Structure

```
src/
├── components/
│   ├── Map3D.vue          # MapLibre GL JS 3D map component
│   ├── MapboxMap.vue       # Mapbox GL JS 3D map component
│   └── MapSwitcher.vue     # Map switcher component
├── App.vue                 # Root application component
└── main.ts                # Application entry point
```

## Development

### Available Scripts

- `npm run dev` - Start development server
- `npm run build` - Build for production
- `npm run preview` - Preview production build
- `npm run format` - Format code with Prettier
- `npm run lint` - Lint code with ESLint

### Building for Production

```bash
npm run build
```

The built files will be in the `dist/` directory.

## Browser Support

This application works best in modern browsers that support:
- WebGL
- ES6+ features
- CSS Grid and Flexbox

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Submit a pull request

## License

This project is open source and available under the [MIT License](LICENSE).

## Acknowledgments

- [MapLibre GL JS](https://maplibre.org/) for the open-source mapping library
- [Mapbox GL JS](https://www.mapbox.com/mapbox-gl-js/) for the proprietary mapping library
- [OpenStreetMap](https://www.openstreetmap.org/) for the free map tiles
- [Mapbox](https://www.mapbox.com/) for the high-quality map tiles
- [Vue.js](https://vuejs.org/) for the frontend framework
