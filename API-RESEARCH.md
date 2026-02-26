# API Research Documentation

## OpenSkiMap Data Access

### 1. Overpass API (Implementerad ✅)
- **URL**: `https://overpass-api.de/api/interpreter`
- **Metod**: POST med Overpass QL query
- **Kostnad**: Gratis, ingen API-nyckel
- **Begränsningar**: Rate limiting, max 10MB response

**Query för pister:**
```
[out:json][timeout:30];
(
    way["piste:type"="downhill"](BBOX);
    way["piste:type"="nordic"](BBOX);
);
out body;
>;
out skel qt;
```

### 2. OpenSkiMap GeoJSON (Alternativ)
- **Runs**: `https://tiles.openskimap.org/geojson/runs.geojson`
- **Lifts**: `https://tiles.openskimap.org/geojson/lifts.geojson`
- **Ski Areas**: `https://tiles.openskimap.org/geojson/ski_areas.geojson`
- **Storlek**: ~200MB för runs (hela världen)
- **Uppdatering**: Dagligen

**Fördelar**: Komplett data, preprocessad
**Nackdelar**: Stor fil, måste filtrera klientsidan

---

## 3D Terräng APIs

### 1. Cesium Ion (Implementerad ✅)
- **Terrain**: Asset ID 1 (Cesium World Terrain)
- **Imagery**: Asset ID 3 (Bing Maps Aerial)
- **Gratis tier**: 500K tiles/månad, 3 assets
- **Signup**: https://cesium.com/ion/signup

### 2. Google Photorealistic 3D Tiles
- **Kräver**: Google Cloud projekt + Map Tiles API aktiverat
- **Kostnad**: $5 per 1000 root tileset loads (SKU: Map Tiles 3D Tileset)
- **Gratis**: $200/månad credit (första 40K loads)
- **Kvalitet**: Bästa fotorealistiska 3D-data

**Implementation:**
```javascript
const tileset = await Cesium.createGooglePhotorealistic3DTileset();
viewer.scene.primitives.add(tileset);
```

### 3. MapTiler (Alternativ)
- **Terrain**: MapTiler Terrain-v2
- **Gratis**: 100K tiles/månad
- **URL**: https://www.maptiler.com/

---

## Jämförelse: Cesium vs Google 3D

| Aspekt | Cesium Ion | Google 3D Tiles |
|--------|------------|-----------------|
| Kostnad | Gratis (500K/mån) | Gratis ($200/mån credit) |
| Kvalitet | Bra terräng | Fotorealistiskt |
| Täckning | Global | Urbana områden bäst |
| Setup | Enkel | Kräver GCP-projekt |
| API-nyckel | Ja (enkel) | Ja (kräver billing) |

---

## OSM Tagging för Pister

```
piste:type = downhill | nordic | skitour | sled | hike | sleigh
piste:difficulty = novice | easy | intermediate | advanced | expert | freeride
piste:grooming = classic | skating | mogul | backcountry
piste:name = <namn>
```

---

---

## Väder-API:er

### 1. Open-Meteo (Implementerad ✅)
- **URL**: `https://api.open-meteo.com/v1/forecast`
- **Kostnad**: Helt gratis, ingen API-nyckel
- **Rate limit**: Fair use (inga hårda gränser)
- **Data**: Temperatur, vind, nederbörd, snöfall, snödjup, vädertyp

**Exempelanrop:**
```
GET https://api.open-meteo.com/v1/forecast
  ?latitude=63.4
  &longitude=13.08
  &current=temperature_2m,relative_humidity_2m,apparent_temperature,precipitation,weather_code,wind_speed_10m,snow_depth
  &hourly=snowfall
  &forecast_days=1
  &timezone=auto
```

### 2. OpenWeatherMap Tiles (Valfri)
- **URL**: `https://tile.openweathermap.org/map/{layer}/{z}/{x}/{y}.png?appid={API_KEY}`
- **Kostnad**: Gratis tier (60 calls/min)
- **Layers**:
  - `clouds_new` - Molntäcke
  - `precipitation_new` - Nederbörd
  - `temp_new` - Temperatur
  - `wind_new` - Vind
  - `snow` - Snötäcke

### 3. Yr.no (Alternativ)
- **URL**: `https://api.met.no/weatherapi/`
- **Kostnad**: Gratis
- **Krav**: User-Agent header
- **Bäst för**: Skandinavien

---

## Rate Limits & Best Practices

### Overpass API
- Max 2 requests/sekund
- Max 10MB response
- Använd bounding box för att begränsa data

### Cesium Ion
- 500K tiles/månad (gratis)
- Övervakas via dashboard

### Rekommendation för Produktion
1. Casha pister lokalt per skidort
2. Använd egen Cesium Ion token
3. Överväg Google 3D för premium-upplevelse
