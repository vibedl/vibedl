# VibeDL — Projektwissen

Statische Website für **VibeDL** (YouTube-Kanal zu Umwelt, Technologie und KI) sowie die
Dienstleistungen von Thomas Dietsch. Reines HTML/CSS, kein Build-Schritt, kein JavaScript.
Hosting über **GitHub Pages** (`https://github.com/vibedl/vibedl.git`).

Stand: 1. August 2026

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
| **Inter** (Variable, 400–800) | Headlines, Fließtext | `inter-latin.woff2`, `inter-latin-ext.woff2` |
| **IBM Plex Mono** (400, 600) | Timecodes, Labels, Navigation, Buttons, Bildunterschriften | `plex-mono-400.woff2`, `plex-mono-600.woff2` |

Beide unter SIL Open Font License 1.1 — die Lizenzdateien (`OFL.txt`, `OFL-PlexMono.txt`)
müssen mitgeliefert bleiben. **Monospace nie für Fließtext verwenden.**

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
- Farbverläufe, Leuchteffekte, `box-shadow` als Deko → alle entfernt, dürfen nicht zurück
- Statistik-Leisten mit gleich großen Spalten
- Gleichförmige Abstände über alle Sektionen
- Marketing-Floskeln („Dein Hub für…", „maximales Engagement")

**Stattdessen tragende Elemente:**

| Element | Umsetzung |
|---|---|
| Timecodes | `<span class="tc">00:01:24:12</span>` statt Kapitelnummern, laufen über die Seite fortlaufend hoch |
| Metadaten-Blöcke | `.meta-list` / `.meta-row` — Label links (Mono, Versalien), Wert rechts. Ersetzt Statistik-Leisten |
| Harte Schnitte | Sektionen stoßen aneinander, getrennt durch 1px-Linie über volle Fensterbreite. Transitions `var(--cut)` = `0.1s linear` |
| Rhythmus | `.sec-pad-l` (130px) / `.sec-pad-m` (96px) / `.sec-pad-s` (64px) bewusst abwechselnd |
| Randabfallend | `.bleed` + `.still` — Cinemascope 2.39:1 über volle Breite |
| Leistungen | `.service-list` als Editorial-Liste, nicht als Karten-Grid |

### Farben (`:root` in `style.css`)
```
--bg          #222225   neutrales Schnittraum-Grau, an DaVinci Resolve orientiert
--bg-lift     #2a2a2e   leicht abgesetzte Flächen
--line        #45454d   Kanten
--line-soft   #34343a   Trennlinien
--text        #f2f2f4
--text-muted  #a6a6af
--text-faint  #8c8c96   Timecodes, Labels
--accent      #43d9a3   einziger Akzent — sparsam einsetzen
```

**Achtung bei Farbänderungen:** Alle Textfarben liegen knapp über 4,5:1 Kontrast.
`--text-faint` auf `--bg` = 4,77:1 — jede Verdunklung des Textes oder Aufhellung des
Hintergrunds bricht die Lesbarkeitsschwelle. Nach Änderungen nachrechnen.

### Weitere CSS-Eigenheiten
- `html { overflow-x: clip }` — **nicht** auf `hidden` ändern: `hidden` erzeugt einen
  Scroll-Container und bricht den `position: sticky`-Header.
- Mobiles Menü: `.nav-toggle:checked ~ nav .nav-links` — der `~ nav`-Teil ist nötig,
  weil `.nav-links` in `<nav>` verschachtelt liegt.
- Unter 640px schaltet `.still` von 2.39:1 auf 16:9 (sonst nur noch ein Schlitz).

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

- **`work-02` und `work-03`** sind noch Platzhalter (`.ph-label`). Für den Einbau genügt der
  Video-Link; Titel per `og:title` von YouTube holen, Thumbnail über
  `https://i.ytimg.com/vi/<VIDEO-ID>/maxresdefault.jpg`. Bei nur zwei Videos das Raster auf
  zwei Kacheln reduzieren, damit keine Lücke entsteht.
- **Bildunterschrift „Schnittplatz, Köln"** unter dem Hero behauptet einen Ort, den das
  generierte Bild nicht zeigt. Neutraler wäre „Postproduktion".
- **Quelldateien im Projektroot** (`Hero.png`, `ueber_mich.png`, `Work1.jpg`,
  `Man_at_editing_desk_*.jpeg`, `Man_in_video_editing_suite_*.jpeg`, ~12 MB) werden nicht
  ausgeliefert, würden aber ins Repository wandern. In `_quellen/` verschieben oder
  per `.gitignore` ausschließen.
- **Ungenutzt in `assets/img/`:** `logo.png` (3,6 MB), `logo-header.png`, `uebermich.jpeg`
  (altes Roboterbild). `favicon.png` wird weiterhin gebraucht.
- **Das Logo ist erkennbar KI-generiert** („Vibe" und „DL" unterschiedlich gerendert,
  verwaschene Icons). Es wurde von allen Seiten entfernt und lebt nur noch als Favicon fort.
  Ein rein typografischer Schriftzug wäre die nächste Verbesserung.
- **Bildmaterial ist generiert.** Ein echtes Foto von Thomas auf „Über mich" bliebe der
  wirksamste Schritt für Glaubwürdigkeit.

---

## Prüfung nach Änderungen

```bash
# 1. Keine externen Requests
grep -rn "googleapis\|gstatic\|<iframe" *.html

# 2. Keine Leucht-/Verlaufseffekte im CSS
grep -n "gradient\|glow\|box-shadow" assets/css/style.css

# 3. Alle referenzierten Bilder existieren
grep -ho 'src="assets/[^"]*"' *.html | sed 's/.*="//; s/"//' | sort -u | \
  while read a; do [ -f "$a" ] || echo "FEHLT $a"; done

# 4. Keine Platzhalter in den Rechtstexten
grep -n "\[" impressum.html datenschutz.html
```

Zusätzlich im Browser: Navigation auf allen vier Seiten identisch? Kein horizontaler Scroll
bei 375 px? Sticky Header funktioniert? Mobiles Menü öffnet?

Ein lokaler Testserver ist auf diesem Rechner nicht verfügbar (kein Python installiert) —
die Seiten lassen sich aber direkt per Doppelklick öffnen, da rein statisch.
