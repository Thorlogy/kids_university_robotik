# Robotik — Kids Universität (Serie 2)

**Stand: Mai 2026 · Version mit vollständiger DE/EN-Internationalisierung**

Eine interaktive Lern-Webseite für Kinder und Jugendliche (8–15 Jahre) über Robotik, KI, Sensoren, Navigation, Algorithmen und Ethik. Alles steckt in einer einzigen HTML-Datei.

---

## 📁 Datei-Übersicht

| Datei | Zeilen | Größe | Beschreibung |
|---|---|---|---|
| `index.html` | ~9.800 | ~580 KB | Komplette Anwendung in einer Datei |
| `DOKUMENTATION.md` | dieses File | — | Diese Doku |

Keine externen Abhängigkeiten außer zwei Google Fonts (DM Sans, DM Mono) — die Seite läuft komplett offline, sobald sie geladen wurde.

---

## 🌟 Was ist neu in dieser Version?

### Vollständige Zweisprachigkeit DE ↔ EN

Die Seite kann **vollständig** zwischen Deutsch und Englisch umgeschaltet werden — per Klick auf den Sprach-Toggle (oben rechts in der Navigation).

**Was wird übersetzt:**

| Element | Anzahl Strings | Status |
|---|---|---|
| Navigation + Sprach-Toggle | ~15 | ✓ |
| Startseite (Hero, Karten, Tags) | ~40 | ✓ |
| Hero-Bereiche aller 8 Kapitel | ~50 | ✓ |
| K1 (Was ist ein Roboter?) — alle Sektionen + Loop-Steps | ~30 | ✓ |
| K2 (Bewegung) — Antriebe, Kinematik, Parcours | ~40 | ✓ |
| K3 (Sensoren) — alle 4 Sensor-Beschreibungen + Vergleich | ~35 | ✓ |
| K4 (Navigation & Karten) — SLAM, 6 Sensor-Stufen, Roomba | ~50 | ✓ |
| K5 (Entscheidungen) — If-Else, Tree, Q-Learning | ~40 | ✓ |
| K6 (KI im Roboter) — Sehen/Bewegen/Reagieren, SAE-Levels | ~60 | ✓ |
| K7 (Echte Roboter) — Mars Rover, Drohnen, Timeline, alle 9 Roboter | ~70 | ✓ |
| K8 (Mensch & Ethik) — Asimov, Trolley, Trust, Verantwortung, Alltag | ~80 | ✓ |
| Zertifikat (UI + Canvas-Text) | ~15 | ✓ |
| Alle 8 Quizze (Frage + Optionen + Feedback) | 8 × 6 = 48 | ✓ |
| **Gesamt** | **~570 Strings** | **✓** |

---

## 🏗️ Architektur

### Single-File-Struktur

```
index.html
├── <head> mit Inline-CSS (~1.500 Zeilen)
├── <body>
│   ├── <nav> + Sprach-Toggle
│   ├── 9 Pages (start, k1-k8)
│   └── <script> mit der gesamten Logik (~7.800 Zeilen)
```

### Globale Variablen (Auswahl)

| Name | Zweck |
|---|---|
| `GT` | Globaler Animations-Timer (steigt um 0.04 pro Frame) |
| `window.LANG` | Aktuelle Sprache ('de' oder 'en') |
| `window.TRANSLATIONS` | Wörterbuch `{de: {...}, en: {...}}` |
| `Scheduler` | Verwaltet aktive Page und Render-Hooks |
| `QUIZ_DATA` | Alle 8 Quizzes mit DE+EN Versionen |
| `K7_DRONE_TYPES`, `K7_ROBOTS` | K7-Datenstrukturen mit EN-Overlay |
| `k8AsimovScenarios`, `k8TrolleyScenarios`, `k8TrustItems`, `k8AlltagItems` | K8-Daten mit EN-Overlay |
| `_k8Initialized` | Lazy-init Flag für K8 |

---

## 🌐 i18n-System

### Konzept

Statt zwei parallele HTML-Versionen zu pflegen, gibt es **eine HTML-Struktur** mit `data-i18n`-Attributen — die Texte werden zur Laufzeit aus einem Wörterbuch ersetzt.

### Drei Übersetzungs-Mechanismen

**1. Statisches HTML mit `data-i18n`**

```html
<div class="sec-title" data-i18n="k1.s1.title">Was ist ein Roboter?</div>
```

`applyI18n()` durchläuft alle Elemente mit `data-i18n` und setzt `textContent` aus dem Wörterbuch.

Spezialvarianten:
- `data-i18n-html` — verwendet `innerHTML` statt `textContent` (für `<strong>`, `<em>` usw.)
- `data-i18n-placeholder` — setzt das `placeholder`-Attribut von `<input>`

**2. Dynamische JS-Inhalte via `L()`-Helper**

Für Daten, die zur Laufzeit gerendert werden (Quizzes, K8-Szenarien, K7-Robots):

```javascript
const item = { text: "Hallo", text_en: "Hello" };
console.log(L(item, 'text'));  // "Hallo" oder "Hello" je nach LANG
```

**3. EN-Overlay via `applyEnOverlay()`**

Um die Original-Datenstrukturen (`QUIZ_DATA`, `K7_ROBOTS`, …) **nicht zu duplizieren**, hängt `applyEnOverlay()` die englischen Übersetzungen als `_en`-Properties an die bestehenden Objekte:

```javascript
const obj = { name: "Spot", desc: "..." };
applyEnOverlay(obj, { name: "Spot", desc: "..." });
// Resultat: { name: "Spot", desc: "...", name_en: "Spot", desc_en: "..." }
```

### Sprach-Wechsel-Event

`setLang('en')` macht drei Dinge:
1. `window.LANG = 'en'`
2. `applyI18n()` — re-runs alle `data-i18n`-Ersetzungen
3. Dispatched `langchange` Event auf `document`

Renderer registrieren sich auf das Event und bauen ihre UI neu auf:

```javascript
document.addEventListener('langchange', () => {
  // K8 Asimov, Trolley, Trust, Alltag neu rendern
  // K7 Drone-Info, Timeline-Detail aktualisieren
});
```

---

## 📚 Kapitel-Übersicht

### Start (Landing)
Animierte Roboter-Szene, 8 Kapitel-Karten mit Tags.

### Kapitel 1 — Was ist ein Roboter?
- 3 Grundbausteine: Sensor / Steuerung / Aktor (klickbar)
- Animierter Regelkreis
- Steuerbarer Roboter mit Live-Sensor-Daten

### Kapitel 2 — Bewegung
- 4 Antriebsarten: Differential / Raupen / Beine / Omni
- Kinematik-Simulator mit zwei Slidern
- Parcours-Spiel mit Checkpoints

### Kapitel 3 — Sensoren
- 4 Sensoren: Ultraschall / Kamera / Lidar / GPS
- Live-Simulation mit ziehbaren Hindernissen
- Sensor-Vergleichsmatrix

### Kapitel 4 — Navigation & Karten
- SLAM-Konzept-Erklärung
- 6-stufiger Sensor-Aufbau
- Roomba-Live-Demo

### Kapitel 5 — Entscheidungen
- Stufe 1: If-Else mit Schwellenwert + Entscheidungsbaum
- Stufe 2: Q-Learning Grid-Welt mit Echtzeit-Lernen

### Kapitel 6 — KI im Roboter
- Teil 1 Sehen: Pixel-Inspektor + Faltungs-Werkstatt + Zeichne-KI
- Teil 2 Bewegen: RL-Arena
- Teil 3 Reagieren: Sprachparser
- Echte Roboter: Spot, SAE-Levels L0-L5, Heim-Roboter

### Kapitel 7 — Echte Roboter
- Mars Rover Perseverance mit 24-Min-Delay
- Drohnen (4 Typen)
- Humanoid-Timeline (9 Roboter, 1996-2026)

### Kapitel 8 — Mensch & Ethik
- Asimov-Konflikt-Simulator (4 Szenarien)
- Trolley-Problem (3 Dilemma)
- Echt-oder-KI? (4 Schnipsel)
- Verantwortungs-Verteilung (5 Slider)
- Roboter im Alltag (6 Karten)
- Abschluss-Quiz + Zertifikat

---

## 🎨 Design-System

### Farbpalette

```css
--navy:    #0f1b3d
--navy2:   #1a2d5a
--navy3:   #0a1428
--cream:   #f5f3ee
--accent:  #4f8ef7
--amber:   #EF9F27
--teal:    #1D9E75
--coral:   #D85A30
--purple:  #534AB7
```

### Typografie

- Body: **DM Sans** (Google Fonts, 400 und 500)
- Code/Labels/Zahlen: **DM Mono** (500)

### Strukturkomponenten

| Klasse | Verwendung |
|---|---|
| `.hero` | Dunkler Header pro Seite mit Canvas-Animation rechts |
| `.sec-label / .sec-title / .sec-sub` | Sektion-Header |
| `.dark-card` | Dunkle Karte auf Cream-Hintergrund |
| `.white-card` mit `.card-header` | "Editor-Fenster"-Stil für interaktive Elemente |
| `.fact-box` | Hervorgehobene "Wusstest du?"-Box |
| `.q-opt` | Quiz-Optionen mit `.correct` / `.wrong` |
| `.ch-nav` | Navigations-Footer pro Seite |

---

## 🛠️ Technische Details

### Canvas-Rendering & Scheduler

Anstatt jede Animation eine eigene `requestAnimationFrame`-Loop laufen zu lassen, gibt es einen **zentralen Scheduler**.

```javascript
Scheduler.setActive('k4');  // Wechselt zu Kapitel 4
```

Vorteile: CPU-schonend, Tab-im-Hintergrund pausiert, einfache Hooks.

### Quiz-System

```html
<div data-quiz="k3fb"></div>
```

`mountQuizzes()` ersetzt jeden Slot durch die Quiz-Karte aus `QUIZ_DATA`. Bei Sprach-Wechsel werden nur **unbeantwortete** Quizzes neu gerendert — damit verliert man keinen Fortschritt.

### Zertifikat-Tracking

```javascript
window.quizTracker = {
  results: {},
  totalQuestions: 8,
  record(fbId, correct) { ... },
  countCorrectFirstTry() { ... }
};
```

### K8-Lazy-Init

K8 hat 5 dynamische Komponenten, die erst initialisiert werden, wenn der Nutzer die Seite betritt.

---

## 🌍 Sprach-Toggle UI

```html
<button class="nav-lang" id="langToggle" onclick="toggleLang()">EN</button>
```

- Im DE-Modus zeigt der Button "EN"
- Im EN-Modus zeigt er "DE"

### Was bleibt nicht übersetzt?

- Roboter-/Firmen-Eigennamen: "Boston Dynamics Spot", "Tesla", "ChatGPT"
- Technische Codewerte: `if`, `else`
- Programmcode-Variablennamen werden lokalisiert: "abstand" → "distance"
- Emojis und Icons

---

## 🧪 Testing

### Syntax-Check

```bash
node -e "
const fs=require('fs');
const html=fs.readFileSync('index.html','utf8');
const re=/<script(?:\\s[^>]*)?>([\\s\\S]*?)<\\/script>/g;
let m,i=0,fail=0;
while((m=re.exec(html))!==null){i++;if(!m[1].trim())continue;try{new Function(m[1]);}catch(e){fail++;}}
console.log('Scripts:',i,'Failures:',fail);
"
```

Erwartung: `Scripts: 2, Failures: 0`

### Verifizierte Funktionen

- ✓ Alle 9 Seiten (start, k1-k8) laden ohne JS-Fehler
- ✓ Sprach-Toggle hin und zurück funktioniert auf jeder Seite
- ✓ K8-Asimov/Trolley/Trust/Alltag rendern in DE und EN korrekt
- ✓ K7-Drone-Info und Robot-Timeline-Detail wechseln mit der Sprache
- ✓ Quizzes werden bei Sprach-Wechsel neu gerendert
- ✓ Quiz-Tracker zählt korrekte Erstantworten korrekt

---

## 📝 Wartung & Erweiterung

### Eine neue Übersetzung hinzufügen

**Statischer HTML-Text:**

1. HTML: `<div data-i18n="meine.id">Deutscher Text</div>`
2. DE-Dict: `'meine.id': 'Deutscher Text',`
3. EN-Dict: `'meine.id': 'English text',`

**Dynamische JS-Daten:**

1. Daten-Objekt mit DE-Strings anlegen
2. Renderer mit `L(item, 'text')` statt `item.text` verwenden
3. Am Ende der Datei `applyEnOverlay(myData, [...])` aufrufen
4. `langchange`-Listener registrieren

### Eine neue Sprache hinzufügen (z.B. Französisch)

1. `window.TRANSLATIONS.fr = {}`
2. Alle DE-Keys auch als FR liefern
3. Im Sprach-Toggle eine dritte Option hinzufügen
4. `L()`-Helper anpassen
5. Alle dynamischen Datenobjekte mit `_fr`-Properties versehen

---

## ⚙️ Browser-Kompatibilität

- **Erforderlich:** Moderne Browser mit ES2017+
- Getestet: Chrome, Firefox, Safari (Desktop und iOS), Edge
- **Mobile-First:** funktioniert ab ~360px Breite
- roundRect-Polyfill für ältere Safari-Versionen integriert

---

## 🚀 Zum Starten

Einfach die HTML-Datei im Browser öffnen — keine Build-Schritte, keine Node-Module nötig.

---

## 📊 Statistik

- **Codezeilen:** ~9.800
- **Dateigröße:** ~580 KB unkomprimiert, ~150 KB gzipped
- **Interaktive Elemente:** 25+ Canvas-Animationen, 15+ Slider, 10+ Quizzes/Tests, 1 Zertifikat-Generator
- **Übersetzte Strings:** ~570 (DE + EN parallel)
- **Kapitel:** 8 (alle vollständig)

---

## 🎯 Pädagogisches Konzept

Jedes Kapitel folgt der Struktur **Erklärung → Interaktion → Quiz**:

1. **Erklärung** in einfachem Deutsch/Englisch
2. **Interaktion** — der Lerner kann selbst experimentieren
3. **Fakt** — eine echte Anekdote aus der Robotik-Welt
4. **Quiz** — eine Verständnis-Frage mit Erklärung

Am Ende: **Abschluss-Zertifikat** mit Quiz-Auswertung.

---

## 🔖 Versionsverlauf

- **Mai 2026** — vollständige Internationalisierung DE/EN
- **Apr 2026** — Kapitel 8 (Mensch & Ethik) + Zertifikat
- **Mar 2026** — K7 Mars Rover Sensor-Ansichten + K5 Q-Learning verbessert
- **Feb 2026** — Initial-Release mit K1-K6
