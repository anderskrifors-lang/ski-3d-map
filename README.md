# 🎿 Ski 3D Map - Komplett Skidguide

En webbapp som kombinerar **3D-terräng**, **skidpister**, **väder** och **serviceinformation** för att planera den perfekta skiddagen.

## 🎯 Funktioner

### 🗺️ 3D-karta
- **Cesium World Terrain** med fotorealistiska satellitbilder
- **Skidpister** färgkodade efter svårighet (grön/blå/röd/svart)
- **Liftar** visas som streckade gula linjer (gondol 🚡 / lift 🚟)
- **POIs** på kartan: Restauranger, parkeringar, stugor, skiduthyrning

### ☀️🏔️ SOL PÅ TOPPEN!
- **Höjdbaserat väder** - separat prognos för dal och topp
- **Solpositionsberäkning** - realtid solvinkel över horisonten
- **Smart indikator**: "SOL PÅ TOPPEN!" / "SNÖFALL!" / "Mulet" / "Natt"
- **Soluppgång/solnedgång** - planera dagen rätt

### ❄️ Snöförhållanden
- **Snödjup** på toppen
- **Nysnö 24h och 7 dagar**
- **Snökvalitetsbedömning**: POWDER ALERT! / Utmärkt / Vårskidåkning / Slask

### ⚠️ Lavinfara
- **Faronivå** (1-5 skala, europeisk standard)
- **Länk till officiell lavinrapport** för varje region
- Stödjer: lavinprognoser.se, avalanche.report, SLF.ch, Météo-France

### 🧭 Pisternas riktning
- **Aspect-analys** - hur pisterna är vända
- Norrvända = bättre snökvalitet (mindre sol)
- Sydvända = tidigare sol, mjukare snö

### 📸 Webcams
- **Direktlänkar** till skidortens webcams
- **Windy.com** integration
- **Snow-forecast.com** för prognos

### 📍 POIs (Points of Interest)
- 🍽️ Restauranger & Cafés
- 🏠 Fjällstugor
- 🅿️ Parkeringar
- ⛽ Bensinstationer
- 🎿 Skiduthyrning
- 🚻 Toaletter

### 📏 Statistik
- Antal pister och liftar
- Höjdmeter (vertikal drop)

## 🚀 Snabbstart

### Enklaste sättet
```bash
cd /home/ubuntu/clawd/projects/ski-3d-map
python3 -m http.server 8080
```
Öppna sedan: http://localhost:8080

### Alternativ
Öppna `index.html` direkt i webbläsaren (fungerar men kan ha CORS-begränsningar).

## 🛠️ Teknisk arkitektur

### Stack
- **CesiumJS 1.124** - 3D-globvisualisering (Apache 2.0, gratis)
- **Cesium Ion** - Terrängdata och satellitbilder (gratis tier)
- **Overpass API** - Live OSM-data för pister (gratis, ingen nyckel)

### Dataflöde
```
1. Användare väljer skidort
2. Kameran flyger till koordinaterna
3. Overpass API hämtar pister inom bounding box
4. Pister renderas som polylines på terrängen
```

## 📡 API:er

### Cesium Ion (terräng + bilder)
- **Asset 1**: Cesium World Terrain
- **Asset 3**: Bing Maps Aerial imagery
- **Gratis tier**: 500K tiles/månad
- **Skapa konto**: https://cesium.com/ion/signup

### Overpass API (pister)
- **Endpoint**: https://overpass-api.de/api/interpreter
- **Kostnad**: Gratis, ingen API-nyckel
- **Begränsning**: Rate limiting vid för många requests

### Open-Meteo (väder) ✅
- **Endpoint**: https://api.open-meteo.com/v1/forecast
- **Kostnad**: Helt gratis, ingen API-nyckel!
- **Data**: Temperatur, vind, nederbörd, snöfall, snödjup, vädertyp
- **Täckning**: Global

### OpenWeatherMap (väder-tiles)
- **Tiles**: `https://tile.openweathermap.org/map/{layer}/{z}/{x}/{y}.png`
- **Layers**: precipitation_new, clouds_new, temp_new, wind_new, snow
- **Kostnad**: Gratis tier (60 calls/min, 1M calls/månad)
- **API-nyckel**: Krävs - skapa på openweathermap.org/api

### OpenSkiMap GeoJSON (alternativ)
- **URL**: https://tiles.openskimap.org/geojson/runs.geojson
- **Format**: GeoJSON med alla pister globalt
- **Storlek**: ~200MB (hela världen)

## 🔑 API-nycklar

### Nuvarande setup
Appen använder en demo Cesium Ion token som kan ha begränsningar.

### För produktion
1. Skapa gratis konto på https://cesium.com/ion/signup
2. Kopiera din access token
3. Ersätt `CESIUM_ION_TOKEN` i index.html

### Google Photorealistic 3D Tiles (upgrade)
För ännu bättre 3D-visualisering:
1. Skapa Google Cloud projekt
2. Aktivera Map Tiles API
3. Lägg till i Cesium via:
```javascript
const tileset = await Cesium.createGooglePhotorealistic3DTileset();
viewer.scene.primitives.add(tileset);
```

## 🗺️ Skidorter inkluderade

| Ort | Land | Dal → Topp | Höjdmeter |
|-----|------|------------|-----------|
| Åre | 🇸🇪 Sverige | 380m → 1420m | 1040m |
| Sälen/Lindvallen | 🇸🇪 Sverige | 440m → 750m | 310m |
| Chamonix | 🇫🇷 Frankrike | 1035m → 3842m | 2807m |
| Val Gardena | 🇮🇹 Italien | 1236m → 2518m | 1282m |
| Nordkette, Innsbruck | 🇦🇹 Österrike | 860m → 2334m | 1474m |
| St. Anton | 🇦🇹 Österrike | 1304m → 2811m | 1507m |
| Zermatt | 🇨🇭 Schweiz | 1620m → 3883m | 2263m |
| Verbier | 🇨🇭 Schweiz | 1500m → 3330m | 1830m |

## 🎨 Svårighetsgrader

| Färg | Nivå | OSM-tagg |
|------|------|----------|
| 🟢 Grön | Lätt | novice, easy |
| 🔵 Blå | Medel | intermediate |
| 🔴 Röd | Svår | advanced |
| ⬛ Svart | Expert | expert |
| 🟣 Lila | Freeride | freeride |
| 🟠 Orange | Okänd | unknown |

## 📋 TODO / Framtida förbättringar

### ✅ Implementerat
- [x] Liftar (gondol/stolift/kabinbana)
- [x] Vädervisning (dal + topp)
- [x] Sol på toppen
- [x] Soluppgång/solnedgång
- [x] Snödjup & nysnö
- [x] Snökvalitetsbedömning
- [x] Lavinfara med länkar
- [x] POIs (restauranger, parkering, etc)
- [x] Webcam-länkar
- [x] Pisternas riktning (aspect)
- [x] Fler skidorter (8 st)

### 🔜 Framtida förbättringar
- [ ] Sök efter skidort
- [ ] Höjdprofil för pister
- [ ] Live liftstatus (kräver skidorters API)
- [ ] Google Photorealistic 3D Tiles
- [ ] Mobilvänlig UI
- [ ] Offline-läge
- [ ] Prisinfo (liftkort)
- [ ] Anpassad route-planering

## 📄 Licenser

- **Cesium JS**: Apache 2.0
- **OpenStreetMap data**: ODbL
- **OpenSkiMap**: ODbL
- **Denna app**: MIT

## 🔗 Referenser

- [CesiumJS Docs](https://cesium.com/learn/cesiumjs-learn/)
- [OpenSkiMap](https://openskimap.org/)
- [OpenSkiStats](https://openskistats.org/)
- [Overpass API](https://wiki.openstreetmap.org/wiki/Overpass_API)
- [OSM Piste tagging](https://wiki.openstreetmap.org/wiki/Piste_Maps)

---

*Skapad 2026-02-25 av Lea*
