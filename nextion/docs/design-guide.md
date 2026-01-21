# Nextion Design Guide

**Design-Prinzipien und Best Practices für SmartEBL Display**

---

## Design-Philosophie

### Inspiration: Luftfahrt-Cockpits

Das Design ist inspiriert von **Boeing/Airbus Cockpit-Systemen**:

✅ **Klarheit** - Informationen auf einen Blick
✅ **Priorisierung** - Wichtiges zuerst (Master Warning/Caution)
✅ **Konsistenz** - Gleiche Elemente, gleiche Positionen
✅ **Minimalismus** - Nur relevante Informationen
✅ **Fehlertoleranz** - Eindeutige Bedienung

---

## Transparentes Design

### Warum transparent?

- **Flexibel**: Funktioniert mit jedem Hintergrund/Wallpaper
- **Modern**: Glassmorphism-Trend
- **Ressourcen-schonend**: Keine großen Bilder nötig
- **Anpassbar**: User können eigene Hintergründe nutzen

### Implementierung

**Alle Komponenten:**
```
bco=65535    // Background Color = Transparent
pco=65535    // Text Color = Weiß (Standard)
pco2=65535   // Press Color 2 = Transparent
```

**Nur Text sichtbar, kein "Button-Rahmen"**

---

## Farbsystem

### Primärfarben

| Farbe | Hex | Decimal | RGB | Verwendung |
|-------|-----|---------|-----|------------|
| 🔴 Rot | `#F800` | 63488 | (248,0,0) | Master WARNING, kritische Alarme |
| 🟠 Orange | `#FC00` | 64512 | (252,0,0) | Master CAUTION, Warnungen |
| 🟢 Grün | `#07E0` | 2016 | (0,124,0) | Normal, OK, Aktiv |
| ⚪ Weiß | `#FFFF` | 65535 | (255,255,255) | Text, Inaktiv |
| 🔘 Grau | `#C618` | 50712 | (198,24,192) | Deaktiviert |

### Farblogik

**Buttons:**
- Weiß (65535): Standard / Inaktiv
- Grün (2016): Aktive Sektion

**Alarme:**
- Grün (2016): OK
- Orange (64512): Warnung (Caution)
- Rot (63488): Kritisch (Warning)

**Lauftext:**
- Grün: "✓ ALL SYSTEMS NORMAL"
- Orange: "⚡ MASTER CAUTION"
- Rot: "⚠️ MASTER WARNING"

---

## Layout-Prinzipien

### Grid-System (800x480)

```
┌────────────────────────────────────────────────────────┐
│ 0─────────────────── Header (50px) ─────────────────800│
│                                                         │
│ 0  Menu   │                                             │
│    (150)  │         Content Area                        │
│           │         (650x430)                           │
│           │                                             │
│           │                                             │
│           │                                             │
│           │                                             │
│           │                                             │
│ 480       │                                             │
└───────────┴─────────────────────────────────────────────┘
    0     150                                           800
```

### Zonen

1. **Header (y=0-50)**
   - Lauftext links
   - Seitenzähler rechts

2. **Menu (x=0-150, y=50-480)**
   - 5 Buttons vertikal
   - Je 80px hoch, 10px Abstand

3. **Content (x=150-800, y=50-480)**
   - Frei gestaltbar
   - Empfohlen: 20px Padding

---

## Typografie

### Font-Hierarchie

| Font ID | Größe | Verwendung | Beispiel |
|---------|-------|------------|----------|
| Font 0 | 12pt | Feinschrift, Labels | "Voltage:" |
| Font 1 | 16pt | Standard Text | "12.8V" |
| Font 2 | 20pt | Buttons, wichtiger Text | "Electric" |
| Font 3 | 24pt | Lauftext, Überschriften | "MASTER WARNING" |
| Font 4 | 32pt | Große Werte, Zahlen | "78%" |

### Font-Empfehlungen

**Im Nextion Editor:**
1. File → Font Generator
2. Wähle Font: **Roboto** oder **Arial** (gut lesbar)
3. Erstelle Größen: 12, 16, 20, 24, 32pt
4. **Anti-Aliasing**: Aktiviert (für glatte Kanten)
5. **Format**: 8-bit für Farbe

---

## Komponentengrößen

### Buttons

**Menu Buttons (vertikal):**
- w=150, h=80
- Abstand: 10px

**Action Buttons (z.B. "Next"):**
- w=120, h=50
- Position: Rechts unten (x=650, y=400)

**Touch-Fläche**: Mindestens 60x60px für gute Bedienbarkeit

### Text-Komponenten

**Lauftext:**
- h=50px (für 24pt Font + Padding)
- Volle Breite (w=800) oder mit Reserve für Seitenzähler (w=650)

**Werte-Anzeige:**
- h=40-50px
- w=je nach Bedarf

**Labels:**
- h=30px
- Font 0 oder 1

---

## Navigation-Pattern

### Horizontale Navigation (zwischen Sektionen)

```
Electric → Water → Climate → Status → Power
   ↓         ↓         ↓         ↓       ↓
Electric_1 Water_1  Climate_1  Status_1 Power_1
```

**Immer über Menu-Buttons links**

### Vertikale Navigation (innerhalb Sektion)

```
Electric_1
    ↓ (btn_next)
Electric_2
    ↓ (btn_next oder btn_electric)
Electric_1
```

**Via:**
- "Next →" Button
- Erneutes Klicken auf Sektions-Button

---

## Seiten-Templates

### Template 1: Standard Unterseite

```
┌────────────────────────────────────────────────────────┐
│ ✓ ALL SYSTEMS NORMAL                           1/2     │
├───────────┬─────────────────────────────────────────────┤
│           │                                             │
│ Electric  │  [TITEL]                                    │
│           │                                             │
│ Water     │  Label1: Value1                             │
│           │  Label2: Value2                             │
│ Climate   │  Label3: Value3                             │
│           │                                             │
│ Status    │                                             │
│           │                                             │
│ Power     │                          [Next →]           │
│           │                                             │
└───────────┴─────────────────────────────────────────────┘
```

### Template 2: Status-Overview (Grid)

```
┌────────────────────────────────────────────────────────┐
│ ⚠️ MASTER WARNING                              1/2     │
├───────────┬─────────────────────────────────────────────┤
│           │  🔴 Sich.1   🟢 Sich.2   🟢 Sich.3         │
│ Electric  │  🟢 Sich.4   🔴 Sich.5   🟢 Sich.6         │
│           │  🟢 Sich.7   🟢 Sich.8   🟢 Sich.9         │
│ Water     │                                             │
│           │  TANK LEVELS                                │
│ Climate   │  Fresh: 45L (60%) 🟢                        │
│           │  Gray:  28L (40%) 🟢                        │
│ Status    │  Black: 12L (20%) 🔴                        │
│           │  LPG:   3.2kg (40%) 🟢                      │
│ Power     │                          [Next →]           │
│           │                                             │
└───────────┴─────────────────────────────────────────────┘
```

---

## Icons & Symbole

### Unicode Symbole (funktionieren in Nextion)

```
✓ Check Mark (OK)         U+2713
⚠️ Warning                U+26A0
⚡ Caution/Lightning      U+26A1
🔴 Red Circle            U+1F534
🟢 Green Circle          U+1F7E2
🟠 Orange Circle         U+1F7E0
→ Arrow Right            U+2192
↓ Arrow Down             U+2193
```

### Verwendung

```
txt_alert.txt="✓ ALL SYSTEMS NORMAL"
txt_alert.txt="⚠️ MASTER WARNING"
txt_status.txt="🟢 OK"
```

**Tipp**: Teste im Nextion Editor ob Font die Symbole unterstützt!

---

## Animation & Feedback

### Touch-Feedback

**Standard:**
- Press Color (pco2) = Gleiche Farbe wie pco (kein Feedback)
- Oder: pco2 = Leicht heller für subtiles Feedback

**Alternative:**
- Sound-Feedback via `click` Befehl im Touch Event

### Scrolling Text

```
dis=1        // Scrolling aktiviert
tim=50       // Speed (50 = medium)
dir=0        // Left-to-right
```

**Best Practice:**
- Nur für Lauftext (Alarme)
- Nicht für normale Werte (verwirrt User)

---

## Responsive Content

### Verschiedene Display-Größen

Dieses Design ist für **7" (800x480)**.

**Für andere Größen:**

| Display | Resolution | Anpassungen |
|---------|------------|-------------|
| 3.5" | 480x320 | Menu schmaler (100px), Font kleiner |
| 5" | 800x480 | 1:1 verwendbar |
| 7" | 800x480 | Perfekt (Original) |
| 10.1" | 1024x600 | Content Area größer, Fonts größer |

### Skalierungsfaktoren

```
Menu Width:     800x480 → 150px  |  1024x600 → 200px
Font Basis:     800x480 → 16pt   |  1024x600 → 20pt
Button Height:  800x480 → 80px   |  1024x600 → 100px
```

---

## Accessibility

### Lesbarkeit

✅ **Kontrast**: Weiß auf Schwarz/Dunkel = Optimal
✅ **Font-Größe**: Min. 16pt für Lesbarkeit bei Tageslicht
✅ **Touch-Ziele**: Min. 60x60px für Finger-Bedienung

### Farbblindheit

**Protanopie/Deuteranopie (Rot-Grün-Schwäche):**

Problem: Rot/Grün nicht unterscheidbar

**Lösung:**
- Zusätzlich Symbole nutzen: ⚠️ + 🔴
- Text-Labels: "WARNING" statt nur Farbe

---

## Performance-Tipps

### Optimierung

**Weniger Updates = Bessere Performance**

❌ **Schlecht:**
```c
// Im Timer (jede Sekunde)
txt_value.txt="12.8"
```

✅ **Gut:**
```c
// Nur bei Änderung (via ESPHome)
if(new_value!=old_value)
{
  txt_value.txt=new_value
}
```

### Bild-Dateien vermeiden

**Transparent-Design = Keine großen Hintergrundbilder**

→ Schnellere Ladezeiten
→ Weniger Flash-Speicher

---

## Konsistenz-Checkliste

Beim Erstellen neuer Seiten:

- [ ] Lauftext an gleicher Position (y=0)
- [ ] Menu-Buttons an gleicher Position (x=0, y=50-480)
- [ ] Seitenzähler an gleicher Position (x=700, y=10)
- [ ] Gleiche Fonts für gleiche Elemente
- [ ] Transparente Hintergründe (bco=65535)
- [ ] Touch Events folgen gleichem Muster
- [ ] Page Preinit setzt Button-Highlighting

---

## Common Mistakes (Häufige Fehler)

### ❌ Fehler 1: Zu viel auf einer Seite

**Problem**: User überfordert mit 20+ Werten auf einer Seite

**Lösung**: Mehrere Unterseiten, logisch gruppiert

---

### ❌ Fehler 2: Inkonsistente Farben

**Problem**: Rot bedeutet mal "Alarm", mal "Aktiv"

**Lösung**: Farbschema einhalten (siehe oben)

---

### ❌ Fehler 3: Zu kleine Touch-Ziele

**Problem**: Buttons schwer zu treffen beim Fahren

**Lösung**: Min. 60x60px, besser 80x80px

---

### ❌ Fehler 4: Fehlende Rückmeldung

**Problem**: User weiß nicht ob Touch registriert wurde

**Lösung**: Page-Wechsel als Feedback oder Sound

---

## Design-Workflow

### Empfohlene Reihenfolge

1. **Wireframe** (Papier/Whiteboard)
   - Skizziere Layout
   - Definiere Navigation

2. **Prototyp** (Nextion Editor)
   - Erstelle Page 0 (Home)
   - Test Navigation-Konzept

3. **Template erstellen**
   - Perfektioniere eine Unterseite
   - Kopiere als Template

4. **Iterieren**
   - Fülle alle Seiten
   - Teste auf echtem Display

5. **ESPHome Integration**
   - Verbinde mit echten Daten

6. **Feedback & Anpassen**

---

## Weitere Ressourcen

- **Nextion Instruction Set**: [https://nextion.tech/instruction-set/](https://nextion.tech/instruction-set/)
- **Nextion Forum**: [https://forum.nextion.tech/](https://forum.nextion.tech/)
- **ESPHome Nextion Docs**: [https://esphome.io/components/display/nextion.html](https://esphome.io/components/display/nextion.html)

---

## Zusammenfassung

**5 Kern-Prinzipien:**

1. 🎨 **Transparent & Minimalistisch**
2. 🚨 **Klare Alarm-Hierarchie**
3. 🎯 **Konsistente Navigation**
4. 📏 **Grid-basiertes Layout**
5. ♿ **Accessibility beachten**

**Befolge diese Prinzipien → Professionelles, nutzbares Display!**
