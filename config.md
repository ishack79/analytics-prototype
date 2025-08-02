# Configuration Guide

## API Keys Setup

This application supports both MapLibre GL JS and Mapbox GL JS. You'll need to configure API keys for full functionality.

### MapTiler API Key (for MapLibre GL JS)

To enable full 3D terrain functionality in MapLibre GL JS, you need to obtain a MapTiler API key:

1. **Sign up for MapTiler**: Visit [https://www.maptiler.com/](https://www.maptiler.com/) and create a free account

2. **Get your API key**: 
   - Log into your MapTiler dashboard
   - Navigate to the API keys section
   - Copy your API key

3. **Update the code**:
   - Open `src/components/Map3D.vue`
   - Find the line with `YOUR_MAPTILER_KEY` (around line 50)
   - Replace it with your actual API key

```javascript
// Replace this line in Map3D.vue
url: 'https://api.maptiler.com/tiles/terrain-rgb/tiles.json?key=YOUR_ACTUAL_KEY'
```

### Mapbox Access Token (for Mapbox GL JS)

To enable Mapbox GL JS functionality, you need to obtain a Mapbox access token:

1. **Sign up for Mapbox**: Visit [https://www.mapbox.com/](https://www.mapbox.com/) and create a free account

2. **Get your access token**: 
   - Log into your Mapbox dashboard
   - Navigate to the Access Tokens section
   - Copy your default public token

3. **Update the code**:
   - Open `src/components/MapboxMap.vue`
   - Find the line with the access token (around line 150)
   - Replace it with your actual access token

```javascript
// Replace this line in MapboxMap.vue
mapboxgl.accessToken = 'YOUR_ACTUAL_MAPBOX_TOKEN'
```

**Note**: The application currently uses a public token for testing. For production use, you should replace it with your own token.

## Alternative: Use Environment Variables

For better security, you can use environment variables:

1. Create a `.env` file in the project root:
```
VITE_MAPTILER_API_KEY=your_maptiler_api_key_here
VITE_MAPBOX_ACCESS_TOKEN=your_mapbox_access_token_here
```

2. Update the components to use environment variables:

**In Map3D.vue:**
```javascript
url: `https://api.maptiler.com/tiles/terrain-rgb/tiles.json?key=${import.meta.env.VITE_MAPTILER_API_KEY}`
```

**In MapboxMap.vue:**
```javascript
mapboxgl.accessToken = import.meta.env.VITE_MAPBOX_ACCESS_TOKEN
```

## Free Tier Limits

### MapTiler Free Tier:
- 100,000 map loads per month
- Terrain data access
- Basic support

### Mapbox Free Tier:
- 50,000 map loads per month
- 3D building data
- Basic support

This should be sufficient for development and small projects.

## Map Features Comparison

| Feature | MapLibre GL JS | Mapbox GL JS |
|---------|----------------|--------------|
| **3D Terrain** | ✅ With MapTiler | ✅ Built-in |
| **3D Buildings** | ❌ Limited | ✅ Built-in |
| **Open Source** | ✅ Yes | ❌ No |
| **Free Tier** | ✅ 100k loads | ✅ 50k loads |
| **Custom Styling** | ✅ Full control | ✅ Full control | 