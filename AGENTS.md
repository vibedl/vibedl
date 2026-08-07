# VibeDL — Projektwissen

Statische Website für **VibeDL** (YouTube-Kanal zu Umwelt, Technologie und KI) sowie die
Dienstleistungen von Thomas Dietsch. Reines HTML/CSS, kein Build-Schritt, kein JavaScript.
Hosting über **GitHub Pages** (`https://github.com/vibedl/vibedl.git`).

Stand: 7. August 2026

---

## Harte Regeln

Diese Punkte nicht ohne Rücksprache ändern — sie haben rechtliche oder gestalterische Gründe.

### 1. Keine externen Requests. Niemals.
Die Seite lädt **nichts** von fremden Servern: keine CDNs, keine Google Fonts, keine
eingebetteten YouTube-Player, keine Analyse-Tools, keine Icon-Bibliotheken.

**Warum:** Die dynamische Einbindung von Google Fonts überträgt die IP jedes Besuchers ohne
Einwilligung in die USA — in Deutschland der häufigste Abmahngrund (150–500 €). Weil die Seite
keine Drittanbieter einbindet, setzt sie auch keine Cookies und braucht **kein Consent-Banner**.
Dieser Zustand ist in der Datenschutzerklärung ausdrücklich zugesichert.

Prüfbefehl (muss leer bleiben):
```bash
grep -rn "googleapis\|gstatic\|cdn\|<iframe" *.html
```

Wird je ein YouTube-Video eingebettet, fällt dieser Vorteil weg: Dann werden Cookie-Banner
und eine Anpassung der Datenschutzerklärung nötig. Videos daher **nur verlinken**.

### 2. Schriften liegen lokal
`assets/fonts/`, eingebunden per `@font-face` in `style.css`. Zwei Schriften mit klarer Rollenverteilung:

| Schrift | Rolle | Dateien |
|---|---|---|
| **Sora** (Variable, 300–700) | Headlines, Fließtext, Navigation, Buttons | `sora-latin.woff2`, `sora-latin-ext.woff2` |
| **IBM Plex Mono** (400, 600) | Timecodes, Labels, Bildunterschriften | `plex-mono-400.woff2`, `plex-mono-600.woff2` |

Beide unter SIL Open Font License 1.1 — die Lizenzdateien (`OFL-Sora.txt`, `OFL-PlexMono.txt`)
müssen mitgeliefert bleiben. **Monospace nie für Fließtext verwenden.**

Sora ersetzt seit dem Redesign vom 7. August 2026 die vorherige Hausschrift Inter. Der
zugrunde liegende Entwurf sah die Einbindung über die Google-Fonts-CDN vor; die Dateien
wurden stattdessen einmalig heruntergeladen und liegen lokal. **Nicht auf die CDN
zurückstellen** — siehe Regel 1. Alle Gewichte stecken in einer Variable-Font-Datei,
zusätzliche Schnitte sind nicht nötig.

### 3. Alle Pfade relativ
Kein führender `/`, damit die Seite sowohl unter Custom-Domain als auch unter
`vibedl.github.io/vibedl/` funktioniert.

---

## Gestaltungskonzept „Schnittraum"

Die Seite soll **nicht** wie eine generierte Landingpage aussehen. Das Konzept leitet sich vom
Beruf des Betreibers ab (Cutter/Postproduktion). Frühere Versionen wirkten generisch, weil sie
das Standardschema bedienten: Neon-Verläufe, Glow-Effekte, 4-spaltige Statistik-Leiste,
„01/02"-Kapitelnummern, überall gleiche Abstände.

**Deshalb bewusst vermieden:**
- Leuchteffekte und `box-shadow` als Deko → grundsätzlich nicht erlaubt. Es gibt genau
  **eine bewusste Ausnahme**: der grüne Hover-Schein im Hero-Kachelraster (`.hero-grid
  span:hover`, siehe unten). Er wurde dem Nutzer als Regelverstoß benannt, er wollte ihn
  trotzdem — nachbildet damit das Hover-Leuchten der ursprünglichen Spline-3D-Szene, die
  der Entwurf als Hintergrund vorsah. Jede weitere Ausnahme braucht dieselbe ausdrückliche
  Rücksprache, sonst gilt die Regel wie gehabt.
- Farbverläufe als Deko → dürfen nicht zurück. Es gibt genau **eine erlaubte Ausnahme**:
  den Hero-Scrim (`.hero-scrim`), rein funktional und im CSS kommentiert. Er sichert die
  Lesbarkeit heller Schrift auf dem Foto, er schmückt nicht. Neue Verläufe brauchen
  dieselbe Rechtfertigung. (Das Aufblenden der Kopfzeile, `@keyframes header-lift`,
  arbeitet ohne Verlauf — es animiert nur die Deckkraft der Hintergrundfarbe.)
- Statistik-Leisten mit gleich großen Spalten
- Gleichförmige Abstände über alle Sektionen
- Marketing-Floskeln („Dein Hub für…", „maximales Engagement")

**Stattdessen tragende Elemente:**

| Element | Umsetzung |
|---|---|
| Vollbild-Hero | `.hero` — `min-height: 100svh`, Inhalt unten links, Standbild randabfallend im Hintergrund |
| Kachelraster | `.hero-grid` — 240 leere `<span>` über dem Foto, feine Trennlinien, grünes Hover-Leuchten (siehe Ausnahme oben) |
| Wortmarke | `<h1>Vibe<span class="accent">DL</span></h1>` — rein typografisch, Versalien, `clamp(3rem, 8vw, 6rem)` |
| Timecodes | `<span class="tc">00:01:24:12</span>` statt Kapitelnummern, laufen über die Seite fortlaufend hoch |
| Metadaten-Blöcke | `.meta-list` / `.meta-row` — Label links (Mono, Versalien), Wert rechts. Ersetzt Statistik-Leisten |
| Harte Schnitte | Sektionen stoßen aneinander, getrennt durch 1px-Linie über volle Fensterbreite. Transitions `var(--cut)` = `0.1s linear` |
| Rhythmus | `.sec-pad-l` (130px) / `.sec-pad-m` (96px) / `.sec-pad-s` (64px) bewusst abwechselnd |
| Leistungen | `.service-list` als Editorial-Liste, nicht als Karten-Grid |
| Einblenden | `.animate-fade-up` mit gestaffeltem `animation-delay` — **nur im Hero**, weil ohne JavaScript kein Scroll-Trigger möglich ist |

Der randabfallende Cinemascope-Streifen (`.bleed` / `.still`) ist mit dem Redesign entfallen —
das Standbild trägt jetzt den Hero. Klassen und CSS wurden entfernt.

### Hero-Kachelraster (`.hero-grid`)
Ersetzt die interaktive Spline-3D-Szene, die der ursprüngliche Entwurf als Hero-Hintergrund
vorsah. Der Prompt beschrieb nur die Einbettung (`@splinetool/react-spline`, feste Szenen-URL),
nicht deren Inhalt — das Würfelraster mit grünem Hover steckte in der 3D-Szene selbst. Ein
Indiz dafür lieferte der Entwurf trotzdem: Er setzte `pointer-events-none` auf den gesamten
Textblock, „damit Klicks zur Spline-Szene durchgehen" — diesen Aufwand betreibt man nur für
einen Hintergrund, der auf den Zeiger reagiert.

Technisch rein in CSS, kein JavaScript:
- **240 leere `<span>`** in `index.html`, weil jede Rasterzelle einzeln auf `:hover` reagieren
  muss. Ein gezeichneter Hintergrund (`repeating-linear-gradient`) kann Linien zeichnen, aber
  keine einzelne Zelle ansprechen. Die Zahl 240 ist bewusst gewählt: durch 20, 12, 8 und 30
  teilbar, jede der drei Rasteraufteilungen unten geht ohne Rest auf.
- **Aufteilung nach Breite:** `20×12` ab 1024 px, `12×20` von 640–1023 px, `8×30` unter 640 px
  (Klassen in `assets/css/style.css`, Abschnitt „Responsive").
- **`@media (hover: hover)`** schaltet den Leuchteffekt für Touch-Geräte ab — sonst bliebe die
  zuletzt berührte Kachel nach dem Tippen "hängen", weil Touch keinen echten Hover-Zustand kennt.
- **`.hero-content` ist `pointer-events: none`**, genau wie im Entwurf vorgesehen. Nur die
  beiden Hero-Buttons (`.hero-actions a`) bekommen `pointer-events: auto` zurück. Preis dafür:
  der Hero-Text lässt sich nicht mehr markieren oder kopieren — das ist Absicht, kein Fehler.
  Tastaturbedienung ist unberührt, `pointer-events` wirkt nur auf den Zeiger.

### Farben (`:root` in `style.css`)
Als HSL-Tripel notiert, Verwendung über `hsl(var(--token))`. Nur Dark Mode.
```
--background        0 0%  10%   Flächen unterhalb des Heros
--hero-bg           0 0%   8%   dunkelster Ton, Hero und Body
--foreground        0 0%  96%   Fließtext, Headlines
--muted-foreground  0 0%  60%   Sekundärtext
--text-faint        0 0%  54%   Timecodes, Labels, Trust-Zeile
--primary           119 99% 46% einziger Akzent — sparsam einsetzen
--primary-foreground 0 0%   4%  Schrift auf dem Akzent
--secondary         0 0%  18%
--muted             0 0%  16%
--border            0 0%  20%   Kanten und Trennlinien
--nav-button        0 0%  18%   Button in der Kopfzeile
--radius          0.5rem        Kopfzeilen-Button, Bilder
--radius-sm     0.125rem        Hero-Buttons
```

**Achtung bei Farbänderungen:** Textfarben nach jeder Änderung nachrechnen (AA = 4,5:1
für kleinen Text). Aktuell auf `--hero-bg`: `foreground` 17,6:1 · `muted-foreground` 6,5:1 ·
`text-faint` 5,3:1 · `primary` 11,2:1 · `primary-foreground` auf `primary` 12,0:1.

`--text-faint` ist eine Zutat dieses Projekts: Der Entwurf sah für die leiseste Textstufe
`muted-foreground` bei 60 % Deckkraft vor — das ergibt nur 3,1:1 und fällt durch. **Nicht
auf eine Deckkraft-Lösung zurückbauen.**

### Weitere CSS-Eigenheiten
- `html { overflow-x: clip }` — **nicht** auf `hidden` ändern: `hidden` erzeugt einen
  Scroll-Container und bricht scroll-gesteuerte Animationen.
- Die Kopfzeile ist `position: fixed`. Grundzustand **deckend**; nur auf der Startseite
  blendet sie per `animation-timeline: scroll()` aus dem Transparenten auf. Browser ohne
  Unterstützung (derzeit Firefox) behalten den deckenden Zustand — lesbar, nur weniger
  elegant. Unterseiten tragen dauerhaft `.site-header--solid`.
- Sprungziele: `[id] { scroll-margin-top: … }` hält Anker unter der fixierten Kopfzeile frei.
  Ohne diese Regel verschwinden Überschriften beim Ansteuern hinter dem Header.
- Mobiles Menü: `.nav-toggle:checked ~ nav .nav-links` — der `~ nav`-Teil ist nötig,
  weil `.nav-links` in `<nav>` verschachtelt liegt. Die Checkbox muss im Markup **vor**
  `<nav>` stehen, das Label darf dahinter stehen (sonst rutscht der Burger nicht nach rechts).
- Der Entwurf sah auf Mobilgeräten **kein** Menü vor (Links werden ausgeblendet). Das
  Klappmenü wurde bewusst behalten — sonst wäre die Seite auf dem Handy nicht navigierbar.

---

## Bildformate

| Verwendung | Seitenverhältnis | Exportgröße | Ziel-Dateigröße |
|---|---|---|---|
| `still-hero.jpg` (Hero, randabfallend) | 2.39:1 | 2560 × 1071 | < 400 KB |
| `work-0X.jpg` (Video-Kacheln) | 16:9 | 1280 × 720 | < 250 KB |
| `ueber-mich.jpg` (Profil) | 16:9 | 1280 × 720 | < 200 KB |

Export als JPEG, Qualität ~82, sRGB, Metadaten strippen:
```bash
magick quelle.png -strip -colorspace sRGB -quality 82 -interlace Plane assets/img/ziel.jpg
```

Bei `<img>` immer `width`/`height` setzen (verhindert Layout-Sprünge beim Nachladen).
Wichtiges Motiv mittig halten — `object-fit: cover` beschneidet je nach Fenstergröße.

---

## Inhaltliche Fakten

**Person:** Thomas Dietsch, Im Salzgrund 51, 50999 Köln ·
thomas.dietsch@gmx.de · +49 151 50135071

**Positionierung:** *Content Creator & KI-Spezialist* — im Präsens, selbstbewusst.
Die 20+ Jahre Postproduktion sind das Fundament, nicht die aktuelle Tätigkeit.

**Wichtig:** Thomas ist **nicht mehr** bei Raab Entertainment angestellt. Frühere Arbeitgeber
(Raab Entertainment, ITV Studios, Redseven Entertainment) dürfen genannt werden — aber
ausschließlich als **vergangene Stationen** („zuletzt als…", „mit Stationen bei…").
Kein „seit 2024", kein Präsens.

**Links:**
- YouTube: `https://www.youtube.com/@Vibe_DL`
- LinkedIn: `https://www.linkedin.com/in/thomas-dietsch-35041698/`

**Ansprache:** Du-Form, Ich-Perspektive (Einzelperson, kein „wir").

---

## Rechtstexte

Beide Seiten sind final ausgefüllt — keine Platzhalter, keine Hinweise an den Betreiber.

**Impressum:** § 5 DDG, § 18 Abs. 2 MStV, Verbraucherstreitbeilegung (§ 36 VSBG),
Haftung für Inhalte/Links, Urheberrecht.
Kein USt-Abschnitt — **kein Gewerbe angemeldet**. Erst ergänzen, wenn eine USt-IdNr. vorliegt.

**Der Hinweis zur EU-Streitschlichtungsplattform (OS-Plattform) darf nicht zurück.**
Die ODR-Verordnung wurde durch VO (EU) 2024/3228 aufgehoben, die Plattform zum 20.07.2025
abgeschaltet. Der Link ist tot und die Nennung nicht mehr zulässig.

**Datenschutz:** benennt GitHub Inc. als Hosting-Anbieter samt Drittlandübermittlung USA
(EU-US Data Privacy Framework + Standardvertragsklauseln) und die zuständige Aufsichtsbehörde
**LDI NRW** (Kavalleriestraße 2–4, 40213 Düsseldorf). Abschnitt 5 sichert zu, dass die
Schriftarten lokal ausgeliefert werden — muss bei Schriftwechsel mitgepflegt werden.

---

## Offene Punkte

- **Das Raster zeigt derzeit zwei Videos**, die Platzhalterkachel für ein drittes wurde
  entfernt (`auto-fit` verteilt zwei Kacheln lückenlos auf die volle Breite). Für ein
  weiteres Video genügt der Link: Titel per `og:title` von YouTube holen, Thumbnail über
  `https://i.ytimg.com/vi/<VIDEO-ID>/maxresdefault.jpg` laden, lokal ablegen — **nie
  direkt von `i.ytimg.com` einbinden**, das wäre ein externer Request.

  Ohne ImageMagick geht das Herunterrechnen mit dem mitgelieferten `sips`. Achtung: dessen
  `formatOptions` ist **nicht** die JPEG-Qualität aus dem `magick`-Aufruf — Stufe 82 macht
  die Datei größer als das Original. Für 1280 × 720 landet **Stufe 60** bei rund 235 KB und
  damit unter der 250-KB-Grenze:
  ```bash
  sips -s format jpeg -s formatOptions 60 quelle.jpg --out assets/img/work-0X.jpg
  ```
- **Ungenutzt in `assets/fonts/`:** `inter-latin.woff2`, `inter-latin-ext.woff2` und `OFL.txt`
  werden seit dem Wechsel auf Sora von keiner Regel mehr referenziert. Sie liegen noch im
  Repository und können gelöscht werden — die Lizenzdatei `OFL.txt` gehört dann mit weg.
- **Quelldateien im Projektroot** (`Hero.png`, `ueber_mich.png`, `Work1.jpg`,
  `Man_at_editing_desk_*.jpeg`, `Man_in_video_editing_suite_*.jpeg`, ~12 MB) werden nicht
  ausgeliefert, würden aber ins Repository wandern. In `_quellen/` verschieben oder
  per `.gitignore` ausschließen.
- **Ungenutzt in `assets/img/`:** `logo.png` (3,6 MB), `logo-header.png`, `uebermich.jpeg`
  (altes Roboterbild). `favicon.png` wird weiterhin gebraucht.
- **Das Logo ist erkennbar KI-generiert** („Vibe" und „DL" unterschiedlich gerendert,
  verwaschene Icons). Es wurde von allen Seiten entfernt und lebt nur noch als Favicon fort.
  Der typografische Schriftzug im Hero (`Vibe` + grünes `DL`) ist der Ersatz dafür —
  bleibt offen, das Favicon ebenfalls typografisch nachzuziehen.
- **Das Hero-Standbild trägt jetzt die gesamte Startseite.** Es ist generiert und in
  2560 × 1071 (2.39:1) angelegt, wird im Hero aber auf volle Fensterhöhe beschnitten.
  Ein Austausch gegen ein Motiv im Hochformat-tauglichen Zuschnitt (mittig wichtiges
  Motiv) wäre die nächste Verbesserung, besonders für Mobilgeräte.
- **Bildmaterial ist generiert.** Ein echtes Foto von Thomas auf „Über mich" bliebe der
  wirksamste Schritt für Glaubwürdigkeit.

---

## Prüfung nach Änderungen

```bash
# 1. Keine externen Requests — muss leer bleiben
grep -rn "googleapis\|gstatic\|cdn\|<iframe\|spline" *.html

# 2. Leucht-/Verlaufseffekte im CSS — erlaubt sind genau vier Treffer:
#    das linear-gradient in .hero-scrim, sowie drei box-shadow-Treffer im
#    Hero-Kachelraster (Erklärkommentar, transition-Eigenschaft, die
#    box-shadow-Deklaration selbst). Neue Treffer außerhalb von
#    .hero-scrim und .hero-grid sind ein Regelverstoß.
grep -n "gradient\|glow\|box-shadow" assets/css/style.css

# 3. Alle referenzierten Bilder und Schriften existieren
{ grep -hoE '(src|href)="assets/[^"]*"' *.html | sed 's/.*="//; s/"//'
  grep -hoE 'url\("\.\./[^"]*"' assets/css/style.css | sed 's|url("\.\./|assets/|; s/"//'
} | sort -u | while read a; do [ -f "$a" ] || echo "FEHLT $a"; done

# 4. Keine Platzhalter in den Rechtstexten
grep -n "\[" impressum.html datenschutz.html
```

Zusätzlich im Browser: Navigation auf allen vier Seiten identisch? Kein horizontaler Scroll
bei 375 px? Blendet die Kopfzeile beim Scrollen der Startseite auf? Kollidiert nichts mit dem
Logo? Mobiles Menü öffnet? Springen `#ueber`, `#leistungen`, `#arbeiten` unter die Kopfzeile?

Ein lokaler Testserver ist möglich — macOS bringt Python mit (`/usr/bin/python3`, 3.9.6):

```bash
cd ~/Documents/vibedl && /usr/bin/python3 -m http.server 8765
```

Die Seiten lassen sich auch per Doppelklick öffnen, da rein statisch — dann greifen aber
weder `fetch` noch saubere relative Pfade in jedem Fall.
