# DigiKipp 3D - Rastersystem & Geometrie Referenz

## Koordinatensystem (Three.js)

**Achsen-Orientierung:**
- **Y-Achse:** Vertikal (nach oben positiv)
- **X-Achse:** Horizontal Ost-West (+X = Ost, -X = West)
- **Z-Achse:** Tiefe Nord-Süd (+Z = Süd, -Z = Nord)

**Himmelsrichtungen:**
```
        Nord (-Z)
    West (-X) ║ Ost (+X)
         Süd (+Z)
```

---

## Projektionsebenen nach Monge

**Drei Hauptebenen:**
- **π1 (Grundriss):** XZ-Ebene bei Y=0 - horizontale Tischfläche
- **π2 (Aufriss):** XY-Ebene bei Z=0 - senkrechte Front-/Rückansicht
- **π3 (Seitenriss):** YZ-Ebene bei X=0 - senkrechte Seitenansicht

**Risskanten (Schnittlinien):**
- **x12:** X-Achse (Schnitt π1 ∩ π2) bei Z=0
- **y23:** Y-Achse (Schnitt π2 ∩ π3) bei X=0

---

## Quadranten-System im Spielbereich

**WICHTIG: Spielbereich-Mittelpunkt verschoben um Z = 0.1105**

**4 Quadranten (Würfel-Positionen):**

```
        Nord (Z < 0.1105)
    Q0      |      Q1
   X<0,Z<M  |  X>0,Z<M
   NW       |      NO
------------+------------ Zentrum Z=0.1105
    Q3      |      Q2
   X<0,Z>M  |  X>0,Z>M
   SW       |      SO
     Süd (Z > 0.1105)
```

**Quadranten-Definitionen:**
- **Q0 (Nordwest):** X<0, Z<0.1105 - Ecke bei (-3 Zellen, -3 Zellen rel.)
- **Q1 (Nordost):** X>0, Z<0.1105 - Ecke bei (+3 Zellen, -3 Zellen rel.)
- **Q2 (Südost):** X>0, Z>0.1105 - Ecke bei (+3 Zellen, +3 Zellen rel.)
- **Q3 (Südwest):** X<0, Z>0.1105 - Ecke bei (-3 Zellen, +3 Zellen rel.) - **STARTPOSITION**

---

## Rasterzellen-System

**Design-Auflösung:** 360×720 Pixel (logisch, nicht physisch)

**Grid-Einteilung:**
- Horizontal: 8 Spalten
- Vertikal: 16 Zeilen
- **1 Rasterzelle = 45px**

**Maßstab & Umrechnungen:**

| Einheit | Wert | Umrechnung |
|---------|------|------------|
| 1 Rasterzelle | 45px | 23.33mm (real) |
| 1 Rasterzelle | 0.15625 Three.js-Einheiten | - |
| 3 Rasterzellen | 135px | 70mm (real) |
| 3 Rasterzellen | 0.46875 Three.js-Einheiten | Würfelkantenlänge |
| 6 Rasterzellen | 270px | 140mm (real) |
| 6 Rasterzellen | 0.9375 Three.js-Einheiten | Spielfeld-Innenmaß |
| 8 Rasterzellen | 360px | 187mm (real) |
| 8 Rasterzellen | 1.25 Three.js-Einheiten | Spielfeld-Außenmaß |
| 16 Rasterzellen | 720px | 373mm (real) |
| 16 Rasterzellen | 2.5 Three.js-Einheiten | Volle Grid-Höhe |

**Wichtige Formel:**
```
720px = 2.5 Three.js-Einheiten
1px = 2.5/720 = 0.003472 Einheiten
1 Zelle (45px) = 45 × 0.003472 = 0.15625 Einheiten
```

---

## Wichtige Höhen & Positionen

**Y-Koordinaten (Höhen):**
- `Y = 0` → π1 (Grundriss-Ebene, Tischfläche)
- `Y = 0.001` → Grid auf π1 (minimal darüber, Z-Fighting vermeiden)
- `Y = 0.234375` → 1.5 Zellen (Wandmitte bei 3 Zellen Höhe)
- `Y = 0.46875` → 3 Zellen (Oberkante Wände)
- `Y = 2.143` → 13.71 Zellen = 320mm = Augenhöhe Spieler

**Z-Koordinaten (Tiefe) - MIT VERSCHIEBUNG:**
- `Z = 0` → x12 (Risskante, X-Achse) - **NICHT Spielbereich-Mitte!**
- `Z = 0.1105` → **Spielbereich-Mittelpunkt** (verschoben nach Süden)
- `Z = 0.109375` → 0.7 Zellen absolut = Nord-Pivot-Linie im Spielbereich
- `Z = 0.343125` → 2.2 Zellen absolut = Ost/West-Pivot-Linie (Spielbereich-Mitte Z)
- `Z = -0.359375` → Nord-Wand Innenkante (0.1105 - 0.46875)
- `Z = +0.580375` → Süd-Wand Innenkante (0.1105 + 0.46875)
- `Z = -0.376125` → Nordwand-Zentrum
- `Z = +0.597125` → Südwand-Zentrum

**X-Koordinaten (Breite):**
- `X = 0` → y23 (Risskante, Mittellinie)
- `X = ±0.234375` → ±1.5 Zellen
- `X = ±0.46875` → ±3 Zellen (West-/Ost-Wandkanten-Innenmaß)
- `X = ±0.48550` → ±3.1 Zellen (Seitenwand-Positionen)

**KRITISCH:** Spielbereich ist **nach Süden verschoben um 0.1105 Einheiten (≈0.7 Zellen)**

---

## Würfel-Spezifikation

**Größe:** 3×3×3 Zellen = 70×70×70mm (real)

**In Three.js:**
```javascript
const cubeSize = 0.46875; // 3 Zellen
const offset = cubeSize / 2; // 0.234375 = 1.5 Zellen
```

**Startposition (Quadrant 3 - Südwest):**
```javascript
cube.position.set(
  -offset,              // X: -1.5 Zellen (West)
  offset,               // Y: +1.5 Zellen (auf π1 stehend)
  0.109375 + offset     // Z: 0.7 + 1.5 = 2.2 Zellen absolut
);
// = (-0.234375, 0.234375, 0.343125)
```

**Würfel-Zentrum bei Start:**
- X = -0.234375 (-1.5 Zellen - West)
- Y = 0.234375 (+1.5 Zellen - auf π1 stehend)
- Z = 0.343125 (+2.2 Zellen absolut = +1.5 Zellen relativ zum Spielbereich-Zentrum)

**Nord-Bodenkante des Würfels:**
- Liegt auf Z = 0.109375 (Nord-Pivot-Linie)
- Diese Linie ist 0.7 Zellen absolut = Nord-seitig im Spielbereich

---

## Wände-Spezifikation

**Nord-/Süd-Wände:**
- Breite: 6 Zellen = 0.9375 Einheiten
- Höhe: 3 Zellen = 0.46875 Einheiten
- Dicke: 5mm = 0.0335 Einheiten
- Material: Silbergrau (0xc0c0c0), metallic

**West-/Ost-Wände:**
- Tiefe: 6.45 Zellen = 1.00675 Einheiten (schließt Nord-/Süd-Wanddicken ein)
- Höhe: 3 Zellen = 0.46875 Einheiten
- Dicke: 5mm = 0.0335 Einheiten

**Wandpositionen (mit Süd-Verschiebung):**
```javascript
// Südwand
position: (0, 0.234375, 0.597125)
// Mitte bei Z = 0.597125

// Nordwand
position: (0, 0.234375, -0.376125)
// Mitte bei Z = -0.376125

// Westwand
position: (-0.48550, 0.234375, 0.1105)
// Bei X = -0.48550, zentriert auf Spielbereich-Mitte Z = 0.1105

// Ostwand
position: (0.48550, 0.234375, 0.1105)
// Bei X = +0.48550, zentriert auf Spielbereich-Mitte Z = 0.1105
```

**Innenkanten (Spielbereich 6×6 Zellen):**
- Nord: Z = -0.359375 (0.1105 - 0.46875)
- Süd: Z = +0.580375 (0.1105 + 0.46875)
- West: X = -0.46875 (-3 Zellen)
- Ost: X = +0.46875 (+3 Zellen)

**Spielbereich-Zentrum:**
- X = 0
- Z = 0.1105 (verschoben nach Süden!)

---

## Kamera-Einstellungen

**Augenhöhe (Spieler):**
```javascript
const baseY = 2.143; // 320mm = 13.71 Zellen
```

**Perspektive:**
```javascript
const camera = new THREE.PerspectiveCamera(
  50,                                   // FOV
  containerWidth / containerHeight,     // Aspect (360/720 = 0.5)
  0.01,                                 // Near
  100                                   // Far
);
```

**Startposition:**
```javascript
const verticalAngle = -1.0;
const horizontal = 0;
const radius = 1.6;

camera.position.set(
  0 + Math.sin(horizontal) * 0.5,
  baseY + radius * Math.sin(-verticalAngle),
  radius * Math.cos(verticalAngle) + Math.cos(horizontal) * 0.3
);
```

**LookAt-Punkt (variiert mit Kamerawinkel):**
```javascript
const lookAtBlend = Math.max(0, Math.min(1, (verticalAngle + 0.7) / 0.4));
const lookAtY = 0.234375 + (0.46875 - 0.234375) * (1 - lookAtBlend);
const lookAtZ = 0.7 + (0.421875 - 0.7) * lookAtBlend;

camera.lookAt(0, lookAtY, lookAtZ);
```

---

## Grid & Achsen

**Grid-Positionen:**
```javascript
// Grid auf π1
gridHelper.position.y = 0.001;
gridHelper.position.z = 0.421875; // Verschoben mit Spielbereich

// Grid auf Wandhöhe (falls vorhanden)
gridHelper2.position.y = 0.46875;
gridHelper2.position.z = 0.421875; // Verschoben mit Spielbereich
```

**Achsen:**
- X-Achse: Verläuft entlang X bei verschiedenen Y-Höhen
- Z-Achse: Verläuft entlang Z bei verschiedenen Y-Höhen

---

## Kipp-Mechanik (Pivot-Punkte)

**Alle Pivot-Punkte liegen auf π1 (Y=0) an Würfelkanten:**

**Nord-/Süd-Kippen (X-Achse Rotation):**
```javascript
// Nord-Kipp (Q3 → Q0) oder Süd-Kipp (Q0 → Q3)
pivotPoint: (-0.234375, 0, 0.109375)
// West-seitig (-1.5 Zellen X), auf Nord-Pivot-Linie (0.7 Zellen Z)

// Süd-Kipp von Q1 (Q1 → Q2) oder Nord-Kipp von Q2 (Q2 → Q1)
pivotPoint: (0.234375, 0, 0.109375)
// Ost-seitig (+1.5 Zellen X), auf Nord-Pivot-Linie (0.7 Zellen Z)
```

**Ost-/West-Kippen (Z-Achse Rotation):**
```javascript
// Ost-Kipp (Q0 → Q1) oder West-Kipp (Q1 → Q0)
// Ost-Kipp von Q3 (Q3 → Q2) oder West-Kipp von Q2 (Q2 → Q3)
pivotPoint: (0, 0, 0.343125)
// Auf y23 (X=0), auf Spielbereich-Zentrum-Z (2.2 Zellen Z)
```

**Pivot-Logik:**
- **X-Achse Kippen:** Pivot bei Z = 0.109375 (Nord-Pivot-Linie)
- **Z-Achse Kippen:** Pivot bei Z = 0.343125 (Spielbereich-Mitte)
- Pivot X = -0.234375 (West-Würfelkante) oder +0.234375 (Ost-Würfelkante) oder 0 (Mitte)

**Rotationsrichtungen:**
- Achse X: Nord/Süd-Kippen (targetRotation ±Math.PI/2)
- Achse Z: Ost/West-Kippen (targetRotation ±Math.PI/2)

---

## Schnellreferenz: Häufige Umrechnungen

**Pixel → Three.js Einheiten:**
```javascript
Einheiten = Pixel × (2.5 / 720)
```

**Zellen → Three.js Einheiten:**
```javascript
Einheiten = Zellen × 0.15625
```

**Millimeter → Three.js Einheiten:**
```javascript
// 1 Zelle = 23.33mm
Einheiten = (mm / 23.33) × 0.15625
```

**Beispiele:**
- 1 Zelle = 45px = 23.33mm = 0.15625 Einheiten
- 3 Zellen = 135px = 70mm = 0.46875 Einheiten
- 6 Zellen = 270px = 140mm = 0.9375 Einheiten
- 8 Zellen = 360px = 187mm = 1.25 Einheiten

**Spielbereich-Verschiebung:**
- Z-Verschiebung = 0.1105 Einheiten = ~0.7 Zellen nach Süden
- Spielbereich-Zentrum Z = 0.1105 (nicht Z = 0!)

---

## Verwendung dieser Referenz

**In anderen Chats:**
1. Diese Datei hochladen
2. Sagen: "Nutze dieses Rastersystem mit der Z-Verschiebung von 0.1105"
3. Der neue Chat versteht sofort die Koordinaten

**Für Entwicklung:**
- Spielbereich-Zentrum ist bei Z = 0.1105 (nicht bei 0!)
- Alle Maße in Rasterzellen denken
- Mit 0.15625-Multiplikator in Three.js umrechnen
- π1 als Basis-Ebene für alle Positionierungen

**Koordinaten-Kommunikation:**
- "3 Zellen über π1" = Y = 0.46875
- "Bei -3X/+3Z relativ" = Position relativ zum Spielbereich-Zentrum (0, Y, 0.1105)
- "Auf x12" = Z = 0 (NICHT Spielbereich-Mitte!)
- "Spielbereich-Zentrum" = (0, Y, 0.1105)
- "Q0" = Nordwest-Quadrant (X<0, Z<0.1105)

---

*Erstellt: 2026-01-31*  
*Projekt: DigiKipp 3D Memory Game*  
*Entwickler: Peter Lenhart*  
*Version: Exakt auf funktionierende Original-Datei angepasst*
