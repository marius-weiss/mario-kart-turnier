# Mario Kart Turnier

Statische Web-App für ein Mario-Kart-Turnier (Liga mit wechselnden Gruppen, danach Finale) mit Beamer-Anzeige. Oberfläche und Texte auf Deutsch.

## Struktur
- `app.js`: die komplette Logik für alle Designs (eine Datei, kein Build-Schritt, keine Abhängigkeiten).
- `app.css`: Basis-Design. `theme-bayern.css` und `theme-sunset.css` enthalten nur die Abweichungen und werden danach geladen.
- `mario-kart-turnier.html`, `-bayern.html`, `-sunset.html`: nur Markup. Sie unterscheiden sich ausschließlich in `data-design`, `<title>` und dem Theme-Stylesheet.
- Farben von Siegerlinie und Konfetti kommen aus den CSS-Variablen `--wire-stops` und `--confetti`.
- `index.html`: leitet auf das zuletzt gewählte Design weiter (`localStorage` `mk-design`).
- `ruhmeshalle.json`: Online-Stand der Ruhmeshalle, wird per Download aus der App ersetzt und committet.
- `_headers`, `robots.txt`, `wrangler.jsonc`, `.assetsignore`: Hosting auf Cloudflare (Workers mit statischen Assets). `wrangler.jsonc` braucht den Block `"previews": {}` für Branch-Previews.

## Regeln
- Reine statische Seite: kein Backend, keine Datenbank, keine Cloudflare Functions. Nur relative Pfade, damit ein Umzug auf einen eigenen Server ohne Änderungen geht.
- Alles muss auch als lokale Datei (`file://`) laufen; dann ist nur der Ruhmeshallen-Download gesperrt.
- Turnierstand in `localStorage` `mk-turnier-v2`, Ruhmeshalle lokal in `mk-ruhm-v1`. Alte gespeicherte Stände und Exporte müssen weiter laden: neue Felder immer mit Standardwert in `DEF()` bzw. `migrate()` ergänzen, nie umbenennen.
- Das Beamer-Fenster (`#anzeige`) wird über das `storage`-Event synchronisiert.
- Punkte-Boni (Kopfgeld, Glücksrad) werden immer berechnet und nie in die eingetragenen Rohpunkte gemischt.
- Kein `*.json` in `.gitignore`, sonst wird `ruhmeshalle.json` nicht committet.

## Offene Aufgaben
Stehen in `AUFGABEN.md`. Zu Beginn einer Session dort nachsehen und erledigte Punkte abhaken.

## Branches
- `main`: Produktion (Cloudflare).
- `testdaten`: Preview mit Testdaten (`testdaten.html`, befüllte `ruhmeshalle.json`). Nie nach `main` mergen, nur `main` dort hineinmergen.

## Testen
Im Browser durchspielen: Teilnehmer → Liga-Runden mit Glücksrad → Tabelle mit Stechen → Turnierbaum → Ruhmeshalle, dazu ein zweites Fenster mit `#anzeige`. Alle drei Designs, hell und dunkel, und Handybreite prüfen.
