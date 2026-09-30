# Highway Emergency Fuel Assistance - Complete Setup Guide

## 🚀 Project Overview

**Highway Emergency Fuel Assistance** is a web application that helps travelers find nearby fuel stations in real-time using:
- **Frontend**: React 18 + Vite + Tailwind CSS
- **Backend**: Node.js + Express
- **Maps**: Google Places API + Google Maps Directions
- **Location**: Browser Geolocation API

### Key Features
✅ Real-time GPS location tracking
✅ Nearby fuel station search (Google Places API)
✅ Trusted fuel brands filtering (HP, IndianOil, BPCL, Shell, Reliance)
✅ Distance calculation with Haversine formula
✅ Estimated arrival time
✅ Direct navigation to stations
✅ Call station functionality
✅ Production-ready error handling
✅ Responsive mobile-first design

---

## 📋 Prerequisites

### System Requirements
- Node.js 16+ (check: `node --version`)
- npm 8+ (check: `npm --version`)
- Modern browser with Geolocation API support
- Internet connection for API calls

### API Requirements
1. **Google Places API Key**
   - Free tier available ($200/month credit)
   - 5,000+ requests/month included

2. **Google Maps API Key** (same key can be used)
   - Enable: Places API
   - Enable: Maps JavaScript API
   - Enable: Roads API (optional)

---

## 🔧 Installation & Setup

### Step 1: Get Google API Key

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project:
   ```
   Project name: "Highway Emergency Fuel Assistance"
   ```
3. Enable APIs:
   - Go to **APIs & Services** → **Library**
   - Search and enable:
     - **Places API**
     - **Maps JavaScript API**
     - **Roads API** (optional)

4. Create API Key:
   - Go to **APIs & Services** → **Credentials**
   - Click **+ Create Credentials** → **API Key**
   - Copy the generated key

5. (Recommended) Restrict the key:
   - Click on your API key
   - Under **Application restrictions**:
     - Select **HTTP referrers (web sites)**
     - Add: `localhost:*` and your production domain
   - Under **API restrictions**:
     - Select **Restrict key**
     - Choose: Places API + Maps JavaScript API

### Step 2: Clone & Setup Frontend

```bash
# Navigate to project directory (already set up with Vite/React)
cd fuelora-on-road-main

# Install dependencies (if not already done)
npm install

# Create .env file from example
cp .env.example .env

# Add API URL to .env
echo "VITE_API_URL=http://localhost:5000" >> .env

# Start development server
npm run dev
```

Frontend will be available at: **http://localhost:5173**

### Step 3: Setup & Start Backend

```bash
# Navigate to backend directory
cd backend

# Install dependencies
npm install

# Create .env file from example
cp .env.example .env

# Edit .env and add your Google Places API key
nano .env
# OR on Windows:
# notepad .env
```

**Required .env contents:**
```env
PORT=5000
NODE_ENV=development
GOOGLE_PLACES_API_KEY=your_api_key_here
NEARBY_SEARCH_RADIUS=5000
TRUSTED_FUEL_BRANDS=HP,IndianOil,BPCL,Shell,Reliance
CORS_ORIGIN=http://localhost:5173
LOG_LEVEL=info
```

Start backend:
```bash
# Development mode (with auto-reload)
npm run dev

# OR production mode
npm start
```

Backend will be available at: **http://localhost:5000**

---

## 🗂️ Project Structure

```
fuelora-on-road-main/
├── src/
│   ├── components/
│   │   └── NearbyFuelStations.tsx      # Main fuel station component
│   ├── services/
│   │   └── fuelStationsService.js     # API service for backend calls
│   ├── pages/
│   │   └── traveller/
│   │       └── NearbyFuelStationsPage.tsx
│   ├── hooks/
│   │   └── useGPS.ts                  # GPS tracking hook
│   ├── App.fixed.tsx                  # Main app with routes
│   └── main.tsx
├── backend/
│   ├── server.js                      # Express server
│   ├── routes/
│   │   └── fuelStations.js           # API routes
│   ├── services/
│   │   └── placesService.js          # Google Places integration
│   ├── utils/
│   │   └── distanceCalculator.js    # Distance & ETA math
│   ├── .env.example
│   ├── package.json
│   └── README.md
├── .env.example                       # Frontend env template
└── package.json
```

---

## 🎯 How It Works

### User Flow
1. User navigates to **"Find Fuel Stations"**
2. Browser requests location permission
3. GPS location acquired
4. Frontend sends lat/lon to backend
5. Backend calls Google Places API
6. Results filtered by trusted brands
7. Stations sorted by distance
8. User sees nearby stations with:
   - Distance & ETA
   - Ratings & reviews count
   - Operating status
   - Navigate & Call buttons

### Technical Flow

```
Browser (Client)
    ↓
useGPS() hook → Gets user's latitude/longitude
    ↓
NearbyFuelStations component
    ↓
fuelStationsService.js → POST /api/fuel-stations/nearby
    ↓
Backend (Express)
    ↓
placesService.js → Google Places Nearby Search API
    ↓
distanceCalculator.js → Calculate distance & ETA
    ↓
Filter by trusted brands
    ↓
Return JSON response
    ↓
Frontend renders UI with fuel stations
```

---

## 🔗 API Endpoints

### Health Check
```
GET /api/health
```

### Nearby Fuel Stations
```
POST /api/fuel-stations/nearby
Content-Type: application/json

{
  "latitude": 28.6139,
  "longitude": 77.2090,
  "radius": 5000
}
```

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "place_id": "ChIJ...",
      "name": "HP Fuel Station - Delhi",
      "rating": 4.5,
      "user_ratings_total": 120,
      "distance": 0.85,
      "arrivalTime": "5 mins",
      "latitude": 28.6150,
      "longitude": 77.2105,
      "address": "New Delhi, India",
      "isOpen": true
    }
  ],
  "count": 1
}
```

---

## 🛠️ Troubleshooting

### Issue: "API key not configured"
**Solution:**
```bash
# Check .env file exists
cat backend/.env

# Verify GOOGLE_PLACES_API_KEY is set
# Restart backend server
npm run dev
```

### Issue: CORS errors
**Solution:**
- Ensure backend is running: `http://localhost:5000`
- Check CORS_ORIGIN in `.env` matches frontend URL
- Frontend default: `http://localhost:5173`
- Restart backend after changing .env

### Issue: Location permission denied
**Solution:**
- Browser shows permission prompt on first visit
- Check browser address bar for permission icon
- Grant location access
- Refresh page

### Issue: No fuel stations found
**Possible causes:**
- No actual fuel stations in search radius
- Search area in middle of ocean/rural area
- Trusted brand filter too restrictive
- Google API quota exceeded

**Solutions:**
- Try in a city area (Delhi, Mumbai, Bangalore, etc.)
- Increase radius in `.env`: `NEARBY_SEARCH_RADIUS=10000`
- Check API usage at Google Cloud Console

### Issue: "REQUEST_DENIED" from Google API
**Solution:**
- Verify API key is correct
- Check Places API is enabled (Google Cloud Console → APIs)
- Check API key restrictions (Application restrictions)
- Ensure no typos in API key

---

## 📊 Testing

### Test with Sample Coordinates

**Delhi (has many fuel stations):**
```json
{
  "latitude": 28.6139,
  "longitude": 77.2090
}
```

**Mumbai:**
```json
{
  "latitude": 19.0760,
  "longitude": 72.8777
}
```

**Bangalore:**
```json
{
  "latitude": 12.9716,
  "longitude": 77.5946
}
```

### Using cURL to Test Backend

```bash
curl -X POST http://localhost:5000/api/fuel-stations/nearby \
  -H "Content-Type: application/json" \
  -d '{
    "latitude": 28.6139,
    "longitude": 77.2090,
    "radius": 5000
  }'
```

---

## 🚀 Production Deployment

### Frontend (Vercel/Netlify)
```bash
# Build frontend
npm run build

# Deploy to Vercel (recommended)
npm i -g vercel
vercel

# OR deploy to Netlify
npm run build
# Upload dist/ folder to Netlify
```

**Vercel .env production:**
```
VITE_API_URL=https://your-backend-domain.com
```

### Backend (Render/Railway/AWS)

#### Using Render.com
1. Go to [render.com](https://render.com)
2. Create new Web Service
3. Connect GitHub repo
4. Build command: `cd backend && npm install`
5. Start command: `cd backend && npm start`
6. Add environment variables in dashboard:
   - `GOOGLE_PLACES_API_KEY`
   - `NODE_ENV=production`
   - `CORS_ORIGIN=https://your-frontend-domain.vercel.app`
   - `PORT=5000`

#### Using Railway.app
```bash
npm i -g @railway/cli
railway login
railway init
# Select Node.js with server.js
railway up
```

---

## 💾 Environment Variables Summary

### Frontend (.env)
```env
VITE_API_URL=http://localhost:5000  # Backend URL
```

### Backend (.env)
```env
PORT=5000
NODE_ENV=development
GOOGLE_PLACES_API_KEY=your_key_here
NEARBY_SEARCH_RADIUS=5000
TRUSTED_FUEL_BRANDS=HP,IndianOil,BPCL,Shell,Reliance
CORS_ORIGIN=http://localhost:5173
LOG_LEVEL=info
```

---

## 📱 Features Overview

### NearbyFuelStations Component
**Location:** `src/components/NearbyFuelStations.tsx`

Features:
- Real-time GPS tracking integration
- Google Places API integration
- Distance calculation
- ETA estimation
- Trusted brand filtering
- Rating display with star visualization
- Operating status indicator
- Navigate button (Google Maps directions)
- Call button (phone integration)
- Error handling (no results, permission denied, API errors)
- Responsive card layout
- Auto-refresh functionality

### Distance Calculator
**Location:** `backend/utils/distanceCalculator.js`

Functions:
- `calculateDistance(lat1, lon1, lat2, lon2)` - Haversine formula
- `estimateArrivalTime(distanceKm, avgSpeed)` - ETA calculation

---

## 🔐 Security Notes

1. **API Key Protection**
   - Never commit `.env` to Git
   - Use API key restrictions (HTTP referrers)
   - Rotate keys periodically

2. **CORS Configuration**
   - Whitelist only trusted domains
   - Production: Use your actual domain only

3. **Rate Limiting** (Recommended for production)
   - Add rate limiter middleware
   - Limit requests per IP
   - Monitor Google Places API usage

4. **Input Validation**
   - All coordinates validated (lat: -90 to 90, lon: -180 to 180)
   - Radius validation
   - Backend validates all inputs

---

## 📚 Additional Resources

- [Google Places API Docs](https://developers.google.com/maps/documentation/places/web-service)
- [Google Maps Directions API](https://developers.google.com/maps/documentation/directions)
- [React Documentation](https://react.dev)
- [Express.js Documentation](https://expressjs.com)
- [Tailwind CSS Documentation](https://tailwindcss.com/docs)

---

## 🤝 Support & Troubleshooting

### Common Issues & Solutions

| Problem | Cause | Solution |
|---------|-------|----------|
| No fuel stations | Wrong coordinates | Use Delhi (28.6139, 77.2090) to test |
| CORS error | Backend not running | Start: `npm run dev` in /backend |
| API key error | Invalid/missing key | Check Google Cloud Console → Credentials |
| Location denied | Browser permission | Allow location access in browser settings |
| Slow results | API quota reached | Check Google Cloud Console → Quotas |

### Getting Help

1. Check browser console for errors: F12 → Console tab
2. Check backend logs: Look at terminal where you ran `npm run dev`
3. Verify API key: Test in [API Explorer](https://developers.google.com/maps/documentation/places/web-service)
4. Enable debug logging: Set `LOG_LEVEL=debug` in .env

---

## 📝 License

ISC License - Use freely for personal and commercial projects

---

## 🎉 You're All Set!

Now you can:
1. ✅ Track user location in real-time
2. ✅ Fetch nearby fuel stations from Google
3. ✅ Display stations with all details
4. ✅ Navigate to stations
5. ✅ Call stations directly

Happy coding! 🚀
