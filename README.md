# Vue 3 3D Map Application

A modern Vue 3 application featuring an interactive 3D map built with MapLibre GL JS and OpenStreetMap tiles.

## Features

- **3D Terrain Visualization**: Toggle between 2D and 3D views with terrain elevation
- **Interactive Controls**: Pan, zoom, rotate, and tilt the map
- **Navigation Controls**: Built-in navigation, fullscreen, and geolocation controls
- **Responsive Design**: Modern UI with smooth animations and hover effects
- **Open Source**: Uses OpenStreetMap tiles and MapLibre GL JS

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
- The map tilts to show terrain elevation
- Terrain exaggeration is applied for better visualization
- Hillshading provides depth perception
- You can still pan, zoom, and rotate in 3D mode

## Configuration

### MapLibre GL JS API Key

For full terrain functionality, you'll need to obtain a MapTiler API key:

1. Sign up at [MapTiler](https://www.maptiler.com/)
2. Get your API key from the dashboard
3. Replace `YOUR_MAPTILER_KEY` in `src/components/Map3D.vue` with your actual key

```javascript
// In Map3D.vue, line ~50
url: 'https://api.maptiler.com/tiles/terrain-rgb/tiles.json?key=YOUR_ACTUAL_KEY'
```

### Customization

You can customize the map by modifying the following in `src/components/Map3D.vue`:

- **Initial View**: Change `center` and `zoom` in the map configuration
- **Terrain Exaggeration**: Adjust the `exaggeration` value in `setTerrain()`
- **3D Pitch**: Modify the pitch angle in the `toggle3D()` function
- **Map Style**: Replace the OSM tiles with other tile sources

## Technology Stack

- **Vue 3**: Progressive JavaScript framework with Composition API
- **TypeScript**: Type-safe JavaScript development
- **MapLibre GL JS**: Open-source mapping library
- **OpenStreetMap**: Free, editable world map
- **Vite**: Fast build tool and development server

## Project Structure

```
src/
├── components/
│   └── Map3D.vue          # Main 3D map component
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

- [MapLibre GL JS](https://maplibre.org/) for the mapping library
- [OpenStreetMap](https://www.openstreetmap.org/) for the map tiles
- [Vue.js](https://vuejs.org/) for the frontend framework
