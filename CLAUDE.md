# CLAUDE.md

## Prosjektbeskrivelse
"Enhjørningi" — et whack-a-mole-spill for en seksåring der enhjørninger dukker opp fra regnbueskyer på en fargerik eng. Barnet tapper for å "klappe" enhjørningene. Positivt framing, ingen vold. Skal fungere som en statisk nettside lagt til på hjemskjermen på iPad/iPhone.

## Teknologivalg
- Én enkelt HTML-fil — alt inline (SVG, CSS, JavaScript)
- Null eksterne avhengigheter — ingen CDN, ingen bilder, ingen npm
- SVG-enhjørninger med animerte kroppsdeler via CSS @keyframes
- Web Audio API for synth-genererte lyder
- localStorage for innstillinger og highscore

## Målenheter
- iPad ca. 2017 (Safari, iOS 15/16) — dette er primærmålet
- iPhone (moderne Safari)
- **Landscape-orientering er standard og prioritert**

## Ytelsesregler (gammel iPad)
- Kun CSS transforms og opacity for animasjoner — aldri animer top/left/width/height
- Ingen backdrop-filter, blur, eller box-shadow-animasjoner
- Forhåndsopprett alle DOM-elementer — toggle klasser, aldri opprett/fjern under spill
- Bruk touchstart, ikke click (unngå 300ms delay på gammel iOS)
- touch-action: manipulation på html-elementet
- Passive event listeners der mulig
- Partikkel-pool med gjenbrukbare elementer

## Filer
- `PLAN.md` — detaljert spillplan med all spesifikasjon
- `PROMPT.md` — prompt brukt for første generering
- `index.html` — spillfilen (eneste output)

## Kodestil
- Vanilla JavaScript, ingen frameworks
- Norske kommentarer er OK
- CSS custom properties for farger, størrelser og spillparametre
- Alle spillparametre (vanskelighetsgrad, tider) som variabler øverst i scriptet

## Regler Claude alltid skal følge
- Les PLAN.md for full spesifikasjon før du koder
- Alt skal inn i én fil: index.html
- Forklar planen din før du begynner å kode
- Test at filen er valid HTML som åpnes rett i Safari
- Ikke legg til features som ikke er beskrevet i PLAN.md
- Prioriter ytelse på gammel iPad over visuell kompleksitet
- Hvis noe må kuttes for ytelse, kutt effekter/partikler først, behold spillmekanikk og enhjørning-animasjoner
- Si ifra når du er ferdig
