# Claude Code Prompt — Enhjørningi

## Instruksjoner
Kopier alt under "---" og lim inn som én prompt i Claude Code (desktop-appen). Sørg for at PLAN.md og CLAUDE.md ligger i prosjektmappen.

---

Les PLAN.md og CLAUDE.md grundig først. Bygg deretter spillet som beskrevet — én enkelt `index.html`-fil uten noen eksterne avhengigheter.

## Kjernekrav

Du bygger "Enhjørningi" — et whack-a-mole-spill for en seksåring. Enhjørninger dukker opp fra regnbueskyer, barnet tapper for å "klappe" dem. Alt positivt framing — ingen vold. **Landscape-orientering er standard.**

### Tekniske krav
- **Én HTML-fil**, alt inline (SVG, CSS, JS). Null CDN, null bilder, null eksterne filer.
- **Må fungere på iPad ~2017** (Safari, iOS 15/16): Kun CSS transforms/opacity for animasjoner. Ingen backdrop-filter, ingen blur, ingen canvas. touch-action: manipulation på html-elementet.
- **Touchstart**, ikke click-events (unngå 300ms delay).
- **Forhåndsopprett alle DOM-elementer** — hull med enhjørninger ferdig i DOM. Toggle CSS-klasser for å vise/skjule, aldri opprett/fjern elementer under spill.
- **Landscape-layout** er standard og prioritert.

### Enhjørning-animasjon (VIKTIG — les nøye)
Enhjørningene skal ha **livaktig bevegelse med animerte kroppsdeler**, ikke bare flyttes som en boks. Bygg hver enhjørning som en SVG med nestede `<g>`-grupper for kroppsdeler. Bruk ordentlige bezier-kurver (`<path>` med C/Q-kommandoer) for kropp og hode — IKKE enkle ellipser og rektangler.

Kropp (torso), 4 bein, hode+nakke, horn, man (2-3 bølgende deler), hale, øyne, øre.

Animasjoner (alle CSS @keyframes):
1. **Pop-up:** translateY fra 115% til 0% med spring-overshoot. 0.5s cubic-bezier.
2. **Bein-spark:** Under pop-up roterer beinene. Fremre/bakre bein med ulik delay og retning. transform-origin: top. 0.7s, 1 gang.
3. **Man-bølge:** Kontinuerlig mens synlig. Staggered delay per del. 2s infinite.
4. **Hale-vift:** Kontinuerlig pendul med scaleX-flip. 1.6s infinite.
5. **Hode-bob:** Subtil translateY + rotate. 3s infinite.
6. **Øre-twitch:** Liten rotate hvert 4. sekund.
7. **Klapp-reaksjon:** scale(1.18) + translateY bounce. 0.4s, 1 gang.
8. **Sink ned:** translateY(115%). 0.3s ease-in via CSS transition.

3 fargevarianter (rosa/lilla, blå/turkis, gull/oransje) + sjelden gull-enhjørning (10%, gir 3 poeng). Hver variant har egen linearGradient med unik ID.

### Spillmekanikk
- Konfigurerbart grid: **3×2** (6 skyer, standard) eller **4×3** (12 skyer). Valg i innstillinger via dropdown.
- Regnbueskyer som "hull" med overflow:hidden og pastell-puffer på toppen (pseudo-elementer).
- 60 sekunder per runde (konfigurerbart).
- **Avslutt-knapp (✕)** oppe til høyre i HUD — alltid synlig under spill. Tar direkte tilbake til meny uten bekreftelsesdialog.
- Tre vanskelighetsgrader (Lett 🌸 / Medium ⭐ / Vanskelig 🔥):
  - Lett: 3000ms synlig, 2000ms spawn-intervall, maks 1 samtidig
  - Medium: 2000ms synlig, 1400ms spawn-intervall, maks 2 samtidig
  - Vanskelig: 1200ms synlig, 900ms spawn-intervall, maks 3 samtidig
- Innstillinger-panel med slidere for synlighetstid (500-5000ms), spawn-intervall (500-4000ms), maks samtidige (1-5), spilltid (30-120s), og dropdown for grid (3×2 / 4×3). Lagre i localStorage.
- Ingen negative poeng. Vanlig = +1, gull = +3.

### Effekter
- Ved klapp: 5 emoji-partikler (❤️💖⭐✨🌟) burst fra tap-punkt med CSS-animasjon og tilfeldige retninger via CSS custom properties. "+1"/"+3" tekst flyter opp og fader.
- Streak-bonus: 3→"Flott!", 5→"Fantastisk!", 10→"MAGISK! ✨". Nullstilles ved timeout.
- Forhåndsopprett partikkel-pool (20 elementer), gjenbruk med klasse-toggling.

### Lyd (Web Audio API)
Synth-generert, ingen filer:
- Plop (dukker opp): Sine 200→600Hz, 150ms
- Pling (klapp): Triangle 800→1200Hz, kort
- Gull-pling: Arpeggio C-E-G-C, 300ms
- Slutt-fanfare: Stigende melodi, 1.5s
- Mute-toggle i hjørnet, state i localStorage
- AudioContext opprettes ved første touchstart (iOS policy)

### Skjermer
1. **Meny:** Tittel "Enhjørningi 🦄", vanskelighetsvalg (3 knapper), "Innstillinger"-knapp
2. **Spill:** Grid med skyer, HUD (poeng venstre, streak midt, tid høyre, ✕-knapp helt til høyre), gressbakgrunn
3. **Slutt:** "Bra jobba! 🌟", poeng, stjerner (1-3), "Spill igjen"-knapp, "Meny"-knapp

### PWA
- apple-mobile-web-app-capable: yes
- apple-mobile-web-app-status-bar-style: black-translucent
- apple-mobile-web-app-title: Enhjørningi
- viewport: width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no

### Responsivt (landscape-først)
- Cell-size: min(20vw, 28vh) for landscape
- Safe area insets
- Fungerer i portrait og landscape, optimalisert for landscape

### Ytelse
- will-change: transform på aktive elementer
- Passive event listeners
- Forhåndsopprett alle hull i DOM
- Partikkel-pool med 20 gjenbrukbare elementer

## Output
Skriv alt til én fil: `index.html`. Filen skal være komplett og kjørbar — bare åpne i Safari.
