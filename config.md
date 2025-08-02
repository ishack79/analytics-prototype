# Configuration Guide

## MapTiler API Key Setup

To enable full 3D terrain functionality, you need to obtain a MapTiler API key:

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

## Alternative: Use Environment Variables

For better security, you can use environment variables:

1. Create a `.env` file in the project root:
```
VITE_MAPTILER_API_KEY=your_actual_api_key_here
```

2. Update the Map3D.vue component to use the environment variable:
```javascript
url: `https://api.maptiler.com/tiles/terrain-rgb/tiles.json?key=${import.meta.env.VITE_MAPTILER_API_KEY}`
```

## Free Tier Limits

MapTiler's free tier includes:
- 100,000 map loads per month
- Terrain data access
- Basic support

This should be sufficient for development and small projects. 