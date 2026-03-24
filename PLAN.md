# Enhjørningi: Spillplan

## Oversikt
Et whack-a-mole-spill for en seksåring der enhjørninger dukker opp fra regnbueskyer på en fargerik eng. Barnet tapper for å "klappe" enhjørningene — positivt framing, ingen vold. Enhjørningene skal bevege seg livaktig med animerte kroppsdeler (bein som sparker, man som vaier, hale som vifter).

**Leveranse:** Én enkelt HTML-fil. Ingen eksterne avhengigheter, ingen CDN, ingen bilder. Alt inline — SVG, CSS, JS. Legges på hjemskjermen som en webapp.

**Målenheter:** iPad (ca. 2017, iOS 15/16, Safari), iPhone (moderne). **Landscape-orientering er standard og prioritert.**

---

## 1. Visuell stil og estetikk

### Fargepalett
- **Himmel/bakgrunn:** Myk gradient fra lys lavendel (#E0CCFA) øverst til lys mint (#D5F5E8) nedover
- **Eng/gress:** Bølgende grønt felt nederst (~22% av skjermhøyden), gradient fra #7BC67E til #4CAF50 med bølgende lag
- **Skyer (hull):** Myke pastell-regnbueskyer med gradient (rosa→lilla→lyseblå), avrundede former med "puffer" på toppen via pseudo-elementer
- **Enhjørninger:** Hvit kropp med pastell-farget man og hale. Gullfarget horn. Store øyne med glitre-highlight.
- **UI-elementer:** Avrundede, store, barnervennlige knapper. system-ui font, stor og tydelig.

### Landscape-layout
```
┌──────────────────────────────────────────────────┐
│ [Poeng: 12]        [Flott! 5x]      [0:42]  [✕] │
│                                                    │
│    ☁️       ☁️       ☁️        (3x2 grid)         │
│         🦄                                         │
│    ☁️       ☁️       ☁️                            │
│                                                    │
│ ░░░░░░░░░░░░░░░░ GRESS ░░░░░░░░░░░░░░░░░░░░░░░░░│
└──────────────────────────────────────────────────┘
```

### Typografi
- Poeng/tid: 13-14px bold, hvit med myk bakgrunn (halvgjennomsiktig pill)
- Knapper: 14px+, hvit tekst på farget bakgrunn
- Alt tekst på norsk

---

## 2. Enhjørning-design (SVG med animerte deler)

### Anatomi — SVG-grupper
Hver enhjørning er en SVG med nestede `<g>`-grupper for kroppsdeler. Bygg med ordentlige bezier-kurver (`<path>` med C/Q-kommandoer), IKKE enkle ellipser og rektangler.

```
<g class="unicorn">
  <g class="u-body">
    <g class="u-tail">            <!-- Bølgende hale med gradient -->
    <g class="u-leg-bl/br">       <!-- Bakre bein (bak kroppen) -->
    <path class="u-torso"/>       <!-- Buet kropp (bezier) -->
    <g class="u-leg-fl/fr">       <!-- Fremre bein (foran kroppen) -->
    <g class="u-neck-head">
      <path class="u-head"/>      <!-- Hode med bezier-kurver -->
      <circle class="u-eye"/>     <!-- Stort øye med glimt -->
      <ellipse class="u-cheek"/>  <!-- Rosa kinn -->
      <path class="u-horn"/>      <!-- Gullhorn -->
      <g class="u-ear"/>          <!-- Øre med twitch -->
      <g class="u-mane"/>         <!-- Man (2-3 bølger, staggered delay) -->
    </g>
  </g>
</g>
```

### Fargevarianter
3 varianter + 1 sjelden gull:
1. **Rosa/lilla:** Man/hale gradient #FF69B4→#C850C0→#7B2FBE
2. **Blå/turkis:** Man/hale gradient #5DADE2→#48C9B0→#2ECC71
3. **Gull/oransje:** Man/hale gradient #F7DC6F→#F0B27A→#E74C3C
4. **Gull (sjelden, 10%):** Kropp #FFF8E1 med gullskimmer-overlay, man #FFD700→#FFC107→#FF9800, gir 3 poeng

Hver variant bruker en unik `<linearGradient>` med tilfeldig ID for å unngå konflikter mellom samtidige enhjørninger.

---

## 3. Animasjonssystem (CSS Keyframes)

### A. Pop-up (enhjørningen dukker opp fra sky)
```css
@keyframes unicorn-appear {
  0%   { transform: translateY(115%); }
  60%  { transform: translateY(-15%); }   /* Overshoot */
  75%  { transform: translateY(5%); }     /* Bounce tilbake */
  85%  { transform: translateY(-3%); }
  100% { transform: translateY(0%); }
}
```
Timing: 0.5s, cubic-bezier(0.34, 1.56, 0.64, 1).

### B. Bein-spark under pop-up
Fremre bein: rotate -25deg→+15deg→0, 0.7s, ease-out, kjører 1 gang.
Bakre bein: rotate +14deg→-10deg→0, 0.7s, ease-out, staggered 80ms.
transform-origin: toppen av hvert bein.

### C. Man-bølge (kontinuerlig mens synlig)
rotate ±4deg + scaleX 0.94–1.08, 2s infinite ease-in-out. Hver bølge-del har staggered delay (0s, 0.4s).

### D. Hale-vift (kontinuerlig mens synlig)
rotate ±8deg med scaleX-flip, 1.6s infinite ease-in-out. transform-origin: feste til kroppen.

### E. Hode-bob (idle)
translateY ±2px + rotate ±2deg, 3s infinite ease-in-out.

### F. Øre-twitch
Liten rotate -8deg ved 95% av en 4s syklus, ellers 0.

### G. Klapp-reaksjon (ved tap)
```css
@keyframes joyBounce {
  0%   { transform: scale(1); }
  30%  { transform: scale(1.18) translateY(-6%); }
  60%  { transform: scale(1.06); }
  100% { transform: scale(1); }
}
```
Timing: 0.4s ease-out, kjører 1 gang.

### H. Sink ned (forsvinner)
translateY(115%), 0.3s ease-in via CSS transition (toggle klasse).

---

## 4. Spillmekanikk

### Grid-layout (konfigurerbart)
- **3 × 2** = 6 skyer (standard, anbefalt for småbarn)
- **4 × 3** = 12 skyer (mer utfordrende)

Valg i innstillinger. Skyene er jevnt fordelt i landskapsmodus med god avstand. Hver sky har overflow:hidden for å skjule enhjørningen under.

### Spillflyt
1. **Meny:** Tittel "Enhjørningi 🦄", vanskelighetsvalg (Lett/Medium/Vanskelig), Innstillinger-knapp
2. **Spill:** Enhjørninger dukker opp fra tilfeldig sky. HUD med poeng (venstre), streak (midten), tid (høyre), avslutt-knapp ✕ (helt til høyre). Spilleren tapper for å klappe.
3. **Slutt:** "Bra jobba! 🌟" + poeng + stjerner (1–3 basert på poeng) + "Spill igjen"-knapp + "Meny"-knapp

### Avslutt underveis
- ✕-knapp oppe til høyre i HUD-en, alltid synlig under spill
- Ved trykk: Direkte tilbake til meny uten bekreftelsesdialog (enklere for barn)
- Spillet stopper umiddelbart, alle enhjørninger skjules

### Poengsystem
- Vanlig enhjørning: +1 poeng
- Gull-enhjørning (10% sjanse): +3 poeng, ekstra glitre-effekt
- Ingen negative poeng, ingen straff for bomming
- Visuell poeng-pop: "+1"/"+3" tekst som flyter opp og fader ut fra tap-punkt

### Spawning-logikk (JavaScript)
```
function spawnUnicorn():
  - Sjekk at antall aktive < maxSamtidige
  - Velg tilfeldig hull som ikke allerede har enhjørning og ikke er i "off"-animasjon
  - Velg tilfeldig variant (3 farger, 10% sjanse for gull)
  - Sett inn riktig SVG i hullets wrapper
  - Toggle "on"-klasse for appear-animasjon
  - Start synlighetstimer
  - Ved timeout: toggle "off"-klasse, reset streak
  
Spawn-intervall: setInterval med [spawnInterval] ms
```

### Touch-handling
```javascript
// Bruk touchstart for umiddelbar respons (unngå 300ms click-delay)
hole.addEventListener('touchstart', handler, { passive: false });
// Sett touch-action: manipulation på html-elementet
// Forhindre dobbeltklikk-zoom
```

---

## 5. Vanskelighetsgrad

### Tre predefinerte nivåer

| Parameter           | Lett (🌸) | Medium (⭐) | Vanskelig (🔥) |
|---------------------|-----------|-------------|----------------|
| Synlighetstid       | 3000ms    | 2000ms      | 1200ms         |
| Spawn-intervall     | 2000ms    | 1400ms      | 900ms          |
| Maks samtidige      | 1         | 2           | 3              |
| Spilltid            | 60s       | 60s         | 60s            |

### Finjustering (innstillinger-panel)
Innstillinger-knapp på menyskjermen OG på sluttskjermen. Panel med:
- **Slider: Synlighetstid** (500ms – 5000ms, steg 100ms)
- **Slider: Spawn-intervall** (500ms – 4000ms, steg 100ms)
- **Slider: Maks samtidige** (1 – 5, steg 1)
- **Slider: Spilltid** (30s – 120s, steg 10s)
- **Dropdown: Rutenett** — "3 × 2 (6 skyer)" eller "4 × 3 (12 skyer)"
- **Lukk-knapp**

Innstillingene lagres i localStorage.

---

## 6. Lyd (Web Audio API)

### Synth-genererte lyder (ingen filnedlasting)
- **Plop (dukker opp):** Sine 200→600Hz, 150ms
- **Pling (klapp):** Triangle 800→1200Hz, kort attack/decay
- **Gull-pling:** Arpeggio C-E-G-C, 300ms
- **Slutt-fanfare:** Kort stigende melodi, 1.5s

### Audio-regler
- AudioContext opprettes ved første touchstart (iOS autoplay-policy)
- Mute-toggle i hjørnet (høyttalerikon), state i localStorage

---

## 7. Effekter ved klapp

### Partikler
Ved klapp: 5 emoji-partikler (❤️💖⭐✨🌟) burst fra tap-punkt med CSS animasjon. Hver partikkel flyr i tilfeldig retning (CSS custom properties --dx, --dy) og fader ut over 0.65s.

### Poeng-popup
"+1" eller "+3" tekst som flyter opp 30px og fader ut over 0.8s.

### Streak-bonus
- 3+ treff på rad: "Flott!" i HUD
- 5+ treff: "Fantastisk!"
- 10+ treff: "MAGISK! ✨"
- Streak nullstilles ved timeout (enhjørning forsvinner uten å bli klappet)

---

## 8. PWA / Hjemskjerm

### Meta-tags
```html
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="apple-mobile-web-app-title" content="Enhjørningi">
```

### Landscape-orientering
Anbefal landscape via CSS og meta-tag, men tving det ikke — la det fungere i portrait også.

---

## 9. Responsivt design (landscape-først)

### Viewport-tilpasning
```css
:root {
  --cell-size: min(20vw, 28vh);  /* Landscape: bredden er stor, høyden begrenser */
}
```

### Safe area
```css
padding: env(safe-area-inset-top) env(safe-area-inset-right) env(safe-area-inset-bottom) env(safe-area-inset-left);
```

---

## 10. Ytelse — gammel iPad-optimalisering

- **CSS transforms/opacity** for alle animasjoner — GPU-akselerert
- **will-change: transform** på aktive elementer
- **Unngå box-shadow/backdrop-filter/blur**
- **Forhåndsopprett alle hull med enhjørninger i DOM** — toggle klasser, aldri opprett/fjern
- **Partikkel-pool** (20 elementer) — gjenbruk med klasse-toggling
- **Passive event listeners** der mulig
- **setInterval for spawning**, requestAnimationFrame er ikke nødvendig for denne type spill

---

## 11. Komplett filstruktur

**Én fil:** `index.html`

```
index.html
├── <head>
│   ├── Meta-tags + viewport + PWA
│   └── <style> — All CSS
│       ├── Reset + base + landscape layout
│       ├── Cloud grid (3x2 og 4x3 via klasse-toggle)
│       ├── Enhjørning-SVG styling
│       ├── Alle @keyframes
│       ├── UI (HUD, knapper, overlay, innstillinger)
│       └── Partikler + effekter
├── <body>
│   ├── Spillramme (game frame)
│   │   ├── HUD (poeng, streak, tid, ✕-knapp)
│   │   ├── Cloud-grid (forhåndsopprettede hull)
│   │   ├── Gressbakgrunn (SVG)
│   │   └── Partikkel-pool
│   ├── Meny-overlay (tittel, vanskelighetsknapper, innstillinger)
│   ├── Slutt-overlay (resultat, spill igjen, meny)
│   └── Innstillinger-panel (slidere, grid-valg, lukk)
└── <script> — All JavaScript
    ├── Konfigurasjon + difficulty presets
    ├── SVG-generering (makeUnicornSVG med fargevarianter)
    ├── Grid-bygging
    ├── Spawn/hide-logikk
    ├── Touch-handlers
    ├── HUD-oppdatering + timer
    ├── Partikkel-system
    ├── Audio-engine (Web Audio API)
    ├── Innstillinger + localStorage
    └── Meny/spill/slutt-overganger
```
