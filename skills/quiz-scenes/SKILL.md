---
name: quiz-scenes
description: Generate an animated, multi-style scene quiz from any workshop content.yaml. Creates human-figure scenes for language workshops, terminal scenes for IT workshops. Three rendering styles: Cinematic, Neon, Minimal.
argument-hint: <workshop-name> [--lesson 01] [--style cinematic|neon|minimal] [--preview]
allowed-tools: Read, Write, Glob, Bash, Grep
---

# Quiz Scenes — Animated Learning Cards

Erstelle ein **visuell beeindruckendes Quiz** aus einem `content.yaml` — mit animierten Szenen, die genau zum Wort/Konzept passen. Kein generisches Design — jede Antwortkarte zeigt die BEDEUTUNG.

## Was dieser Skill erzeugt

Für **Sprach-Workshops** (Spanisch, Arabisch, Englisch …):
- Animierte **Menschen in passender Atmosphäre** auf jeder Karte
- "Hola" → Person winkt auf sonnigem Platz
- "Gute Nacht" → Person schaut zu Mond und Sternen
- "Entschuldigung" → Person hängt Kopf, Regen am Fenster
- Figuren mit echten Proportionen, Augen, Mund (Lächeln/traurig), Kleidung

Für **IT-Workshops** (Linux, Python, Docker …):
- Animiertes **Terminal-Interface** mit Befehl + Bedeutung
- Code-Rain im Hintergrund, Cursor blinkt, Farbe je nach Befehlstyp

Drei **Rendering-Styles** wählbar:
| Style | Technik | Look |
|-------|---------|------|
| `cinematic` | Multi-Layer Parallax, Film-Grain, Tiefenwirkung | Atmosphärisch, filmisch |
| `neon` | Additive Blending, shadowBlur-Glow, Perspektivgitter | Cyberpunk, leuchtend |
| `minimal` | Flat Geometric Art, Bold Shapes, reduzierte Palette | Sauber, Swiss-Design |

---

## Schritt 1 — Workshop-Typ erkennen

Lese `lessons.yaml` und die ersten `content.yaml` Dateien.

**Sprach-Workshop** (→ human figures) erkennen an:
- Ordnerpfad enthält: `spanisch`, `arabisch`, `farsi`, `english`, `portugiesisch`, `japanisch`, `türkisch`
- ODER Workshop-Metadaten haben `category: language` oder `category: sprache`
- ODER Examples haben Translations-Paare (q: Wort in Fremdsprache, a: Übersetzung)

**IT-Workshop** (→ terminal scenes) erkennen an:
- Ordnerpfad enthält: `linux`, `docker`, `python`, `javascript`, `kubernetes`, `git`, `k0rdent`, `coding`, `programming`
- ODER Examples enthalten Befehle (q beginnt mit `$`, `./`, Befehlsnamen wie `ls`, `cd`, `git`, `pip`)

**Allgemeiner Workshop** (→ fallback: abstract art): Alles andere.

---

## Schritt 2 — Szenen zuweisen (Sprach-Workshop)

Lese alle `qa`-Beispiele aus der target Lektion. Weise jedem Answer-Choice eine `scene` zu:

### Semantische Mapping-Regeln

**Begrüßung / Greeting** → `plaza_day` (Person winkt, sonniger Platz)
```
Hallo, Hola, Hi, Salut, Ciao, مرحبا, سلام, Hello, Bonjour, Guten Tag
```

**Dankbarkeit / Gratitude** → `golden_room` (Person verbeugt sich, goldenes Licht)
```
Danke, Gracias, Merci, شكرًا, ممنون, Thank you, Obrigado/a
```

**Morgen / Morning** → `sunrise` (Person streckt sich, Sonnenaufgang)
```
Guten Morgen, Buenos días, Bonjour, صباح الخير, صبح بخیر, Good morning
```

**Entschuldigung / Apology** → `rainy` (Person hängt Kopf, Regen)
```
Entschuldigung, Lo siento, Désolé, آسف, ببخشید, Sorry, Desculpe
```

**Nacht / Night** → `night` (Person schaut zu Sternen, Mondlicht, Stadtsilhouette)
```
Gute Nacht, Buenas noches, Bonne nuit, تصبح على خير, شب بخیر, Good night
```

**Abschied / Farewell** → `park_sunset` (Person winkt, Sonnenuntergang, Park)
```
Tschüss, Adiós, Au revoir, مع السلامة, خداحافظ, Goodbye, Tchau
```

**Unbekannt / Default** → `plaza_day`

### Neon-Farbe pro Szene

| scene | neon_color |
|-------|-----------|
| plaza_day | `#00FF99` |
| golden_room | `#FFD700` |
| sunrise | `#FF6B9D` |
| rainy | `#4444FF` |
| night | `#AA44FF` |
| park_sunset | `#FF4444` |

---

## Schritt 3 — Szenen zuweisen (IT-Workshop)

| Befehl/Konzept | scene | color |
|----------------|-------|-------|
| ls, dir, find, tree | `tech_list` | `#00FF41` |
| cd, pwd, pushd, popd | `tech_cd` | `#00AAFF` |
| rm, del, unlink, rmdir | `tech_delete` | `#FF5555` |
| ping, curl, wget, ssh, nc | `tech_net` | `#FFAA00` |
| nano, vim, vi, code, cat | `tech_edit` | `#AA55FF` |
| mkdir, touch, cp, mv | `tech_mkdir` | `#55FFAA` |
| bash, python, node, ./run | `tech_run` | `#FF88AA` |
| git commit/push/pull | `tech_net` | `#00AAFF` |
| chmod, chown, sudo | `tech_delete` | `#FF5555` |

---

## Schritt 4 — Quiz HTML generieren

Erstelle die Datei:
```
/Users/reza/Github/openlearnapp/workshops/workshop-[name]/quiz-preview.html
```

### Template-Basis

Das vollständige funktionierende Template liegt in:
```
/Users/reza/Github/openlearnapp/experiments/18-multi-style-quiz/index.html
```

**Kopiere das Template** und ersetze nur den DATA-Block (Zeilen mit `const LANG_Q` / `const TECH_Q`).

### So generierst du den DATA-Block

Lese die `content.yaml` der Ziel-Lektion. Extrahiere alle `examples` mit type `select` oder `multiple-choice`.

**Input (content.yaml Beispiel):**
```yaml
examples:
  - id: "greet-01"
    type: select
    q: "Hola"
    a: "Hallo"
    options:
      - "Hallo"
      - "Danke"
      - "Guten Morgen"
      - "Tschüss"
```

**Output (JS DATA-Block):**
```javascript
const LANG_Q = [
  {
    q: "Hola",
    sub: "Spanisch → Deutsch",  // Workshop-Sprachen
    ans: 0,                      // Index der korrekten Antwort in choices
    choices: [
      { w: "Hallo",        s: "plaza_day"   },  // ← scene zugewiesen
      { w: "Danke",        s: "golden_room" },
      { w: "Guten Morgen", s: "sunrise"     },
      { w: "Tschüss",      s: "park_sunset" },
    ]
  },
  // ... alle weiteren Fragen
];
```

**Wichtig:** Die `choices` müssen immer 4 Einträge haben. Falls das Original weniger hat, fülle mit thematisch passenden Distraktoren auf.

---

## Schritt 5 — Output an User

```
✅ Quiz generiert für: workshop-[name] / [lektion]

📄 quiz-preview.html
   → file:///Users/reza/Github/openlearnapp/workshops/workshop-[name]/quiz-preview.html

🎨 Styles wechselbar mit den Tabs: Cinematic · Neon · Minimal
🗣  [X] Fragen aus Lektion [N]
💻  Workshop-Typ erkannt: [Sprache / IT / Allgemein]

→ /quiz-scenes [name] --style neon     anderen Style setzen
→ /quiz-scenes [name] --lesson 02      andere Lektion
```

---

## Rendering-Techniken (Dokumentation für Felix)

Diese Techniken wurden in den Experimenten `experiments/17-scene-quiz` und `experiments/18-multi-style-quiz` entwickelt und erprobt. Alles läuft **ohne externe Abhängigkeiten** außer GSAP (CDN).

### Technik 1: Cinematic (Multi-Layer Parallax)

**Wie es funktioniert:**
```javascript
// Maus-Position (normalisiert -0.5 bis +0.5) steuert Layer-Verschiebung
card.addEventListener('mousemove', e => {
  card.mx = (e.clientX - rect.left) / rect.width - 0.5;
  card.my = (e.clientY - rect.top) / rect.height - 0.5;
});

// Im Render-Loop: jede Schicht mit anderem Offset
function renderCinematic(ctx, w, h, mx, my) {
  const [ox2, oy2] = [mx * w * 0.04, my * h * 0.02]; // Hintergrund: wenig
  const [ox4, oy4] = [mx * w * 0.18, my * h * 0.07]; // Figur: viel

  // Layer 1: Himmel (kein Offset → wirkt weit entfernt)
  drawSky(ctx, w, h);

  // Layer 2: Gebäude/Bäume (kleiner Offset → mittelweit)
  ctx.save(); ctx.translate(ox2, oy2);
  drawEnvironment(ctx, w, h);
  ctx.restore();

  // Layer 4: Figur (großer Offset → nah beim User)
  ctx.save(); ctx.translate(ox4, oy4);
  drawHuman(ctx, w*0.5, h*0.86, h*0.52, pose, colors, t);
  ctx.restore();
}
```

**Film-Grain** für cinematic look (einmalig pro Frame):
```javascript
function drawGrain(ctx, w, h, alpha) {
  const id = ctx.createImageData(w, h);
  for (let i = 0; i < id.data.length; i += 4) {
    const n = (Math.random() * 255) | 0;
    id.data[i] = id.data[i+1] = id.data[i+2] = n;
    id.data[i+3] = (alpha * 255) | 0;  // alpha ≈ 0.15–0.25
  }
  ctx.putImageData(id, 0, 0);
}
```

---

### Technik 2: Neon (Additive Blending + Glow)

**Additive Blending** lässt überlagerte Lichter aufleuchten:
```javascript
ctx.globalCompositeOperation = 'lighter'; // statt 'source-over'
// Mehrere halbtransparente Kreise/Linien addieren sich zu hell
ctx.strokeStyle = '#00FF99';
ctx.globalAlpha = 0.3;
ctx.beginPath(); ctx.arc(x, y, r, 0, Math.PI*2); ctx.stroke();
// → mehrere Kreise übereinander = echtes Glühen
ctx.globalCompositeOperation = 'source-over'; // zurücksetzen!
```

**shadowBlur für Glow** auf Figuren-Linien:
```javascript
ctx.shadowBlur = 15;
ctx.shadowColor = '#00FF99';
ctx.strokeStyle = '#00FF99';
ctx.lineWidth = 2;
ctx.beginPath(); ctx.moveTo(x1, y1); ctx.lineTo(x2, y2); ctx.stroke();
ctx.shadowBlur = 0; // IMMER zurücksetzen — teuer wenn vergessen!
```

**Perspektivgitter** (Vanishing-Point-Grid):
```javascript
function drawPerspGrid(ctx, w, h, col) {
  const vx = w*0.5, vy = h*0.62; // Fluchtpunkt
  // Horizontale Linien (exponentielle Abstände für Tiefe)
  for (let i = 0; i < 8; i++) {
    const y = vy + (h - vy) * ((i/8) ** 1.8);
    ctx.strokeStyle = col; ctx.globalAlpha = 0.3 * (1 - i/8);
    ctx.beginPath(); ctx.moveTo(0, y); ctx.lineTo(w, y); ctx.stroke();
  }
  // Konvergierende Vertikalen
  for (let i = -8; i <= 8; i++) {
    ctx.globalAlpha = 0.2;
    ctx.beginPath();
    ctx.moveTo(vx + i * w * 0.14, h);
    ctx.lineTo(vx, vy);
    ctx.stroke();
  }
}
```

---

### Technik 3: Minimal (Flat Geometric Art)

Flat-Design: Geometrische Formen statt fotorealistischer Szenen.
```javascript
// Sonne = einfacher Kreis
ctx.beginPath(); ctx.arc(w*0.78, h*0.2, 22, 0, Math.PI*2);
ctx.fillStyle = '#F0A500CC'; ctx.fill();

// Gebäude = einfache Rechtecke mit Transparenz
ctx.fillStyle = '#5DADE244'; // Farbe aus Sky-Palette + Alpha
BUILDINGS.forEach(b => ctx.fillRect(w*b.x, gy - h*b.h, w*b.w, h*b.h));

// Bäume = gleichschenkliges Dreieck
ctx.beginPath();
ctx.moveTo(w*0.1, gy - h*0.3);  // Spitze
ctx.lineTo(w*0.1 - w*0.08, gy); // links
ctx.lineTo(w*0.1 + w*0.08, gy); // rechts
ctx.closePath(); ctx.fill();
```

---

### Figuren-System

Die Figuren sind mit **Bezier-Kurven** gezeichnet — keine roundRect, sondern glatte, sich verjüngende Formen.

**Proportionen (7-Kopf-Figur, h = Gesamthöhe):**
```
Kopf-Mitte:    100% (oben)
Schultern:      76%
Hüfte:          51%
Knie:           31%
Boden:           0% (unten)

Schulterbreite: ±14u (u = h/160)
Hüftbreite:     ±10u
```

**Sich verjüngender Arm (Bezier):**
```javascript
function drawTaperedLimb(ctx, x1, y1, x2, y2, w1, w2, col) {
  const dx=x2-x1, dy=y2-y1, len=Math.sqrt(dx*dx+dy*dy);
  const nx=dy/len, ny=-dx/len; // Normale (senkrecht zur Richtung)

  ctx.beginPath();
  ctx.moveTo(x1 + nx*w1, y1 + ny*w1);
  // Glatte Bezier statt gerade Kante
  ctx.bezierCurveTo(
    (x1*2+x2)/3 + nx*(w1*.6+w2*.4), (y1*2+y2)/3 + ny*(w1*.6+w2*.4),
    (x1+x2*2)/3 + nx*(w1*.4+w2*.6), (y1+y2*2)/3 + ny*(w1*.4+w2*.6),
    x2 + nx*w2, y2 + ny*w2
  );
  ctx.lineTo(x2 - nx*w2, y2 - ny*w2);
  // Rückseite
  ctx.bezierCurveTo( ... );
  ctx.closePath();
  ctx.fillStyle = col; ctx.fill();
}
```

**Posen-System:**
```javascript
const POSES = {
  wave:     { aL:-82, aR:118, tor:0,   hd:6,  lL:-3, lR:3  }, // winken
  bow:      { aL:-25, aR:-25, tor:-30, hd:25, lL:0,  lR:0  }, // verbeugen
  stretch:  { aL:108, aR:108, tor:0,   hd:-18,lL:-5, lR:5  }, // strecken
  sad:      { aL:-70, aR:-70, tor:12,  hd:28, lL:0,  lR:0  }, // traurig
  gaze:     { aL:-72, aR:-72, tor:0,   hd:-40,lL:5,  lR:-5 }, // nach oben schauen
  wave2:    { aL:-80, aR:108, tor:4,   hd:8,  lL:12, lR:-3 }, // tschüss winken
};
// aL/aR = Arm-Winkel links/rechts (Grad)
// tor = Torso-Neigung (negativ = nach vorne)
// hd = Kopf-Neigung (positiv = Kinn runter)
// lL/lR = Bein-Winkel links/rechts
```

**Atemanimation + Winken:** automatisch durch `t` (Zeit in Sekunden):
```javascript
const breath = Math.sin(t * 1.1) * h * 0.004;   // 0.4% Höhe
const waveAnim = pose.wa ? Math.sin(t * 2.8) * 0.2 : 0; // Arm schwingt
```

---

## Erweiterung: Neue Szene hinzufügen

### 1. Szene definieren

In der `CFG`-Map des Templates hinzufügen:
```javascript
mein_thema: {
  sky: ['#TOP_COLOR', '#BOTTOM_COLOR'],  // Sky-Gradient (oben → unten)
  sky2: '#HORIZON_COLOR',
  ground: '#BODEN_COLOR',
  pose: 'wave',          // Figuren-Pose (aus POSES-Liste)
  neon: '#GLOW_COLOR',   // Neon-Style Glow-Farbe
  min: '#BACKGROUND',    // Minimal-Style Hintergrundfarbe
  minFig: '#FIGUR_COLOR',// Minimal-Style Figurfarbe
  fig: {                 // Figuren-Farben (Cinematic-Style)
    skin: '#F4A070',     // Hautton
    hair: '#3D2514',     // Haarfarbe
    shirt: '#4A8FD4',    // Shirt
    pants: '#2C3A50',    // Hose
    shoe: '#1A1A2E',     // Schuhe
  }
},
```

### 2. Cinematic-Hintergrund implementieren

In `drawCinemaBg(ctx, w, h, gy, key, t, c)` → neuen `key`-Zweig hinzufügen:
```javascript
else if (key === 'mein_thema') {
  // Himmel-Elemente
  // Gebäude-/Landschafts-Silhouetten
  // Lichteffekte
}
```

### 3. Semantische Zuweisung dokumentieren

Hier in diesem SKILL.md unter "Semantische Mapping-Regeln" eintragen:
```
**Mein Konzept** → `mein_thema` (kurze Beschreibung der Szene)
Schlüsselwörter: Wort1, Wort2, Wort3 …
```

---

## Was NICHT gemacht wird

- Kein Push zu Git — nur lokale `quiz-preview.html` erstellen
- Kein Ändern der `content.yaml` — Szenen werden nur zur Laufzeit gemappt
- Keine externen Assets (Bilder, Lottie) — alles ist Canvas/CSS
- Kein Three.js, WebGL, oder andere schwere Libraries — nur GSAP + Canvas 2D API

## Verwandte Skills

- `/workshop-creator` — Workshop zuerst erstellen
- `/extend-workshop` — Mehr Lektionen hinzufügen
- `/validate-workshop` — YAML-Struktur prüfen

## Experiment-Referenzen

Die Techniken wurden iterativ entwickelt:
- `experiments/17-scene-quiz/` — Erste menschliche Figuren + Atmosphären
- `experiments/18-multi-style-quiz/` — Alle 3 Styles + Parallax + Neon-Glow → **Aktuelles Template**
