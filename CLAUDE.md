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
- Bisher eine reine statische Seite. Ein Server-Teil ist für den Online-Ausbau erlaubt (siehe Abschnitt „Server“ und `AUFGABEN.md`), aber nur dort, wo er gebraucht wird. Im Frontend nur relative Pfade.
- Alles muss auch ohne Server laufen, also lokal als Datei (`file://`) und ohne Netz. Dann sind nur die Online-Funktionen aus (Ruhmeshallen-Download bzw. später Live-Ansicht und Online-Speichern).
- Turnierstand in `localStorage` `mk-turnier-v2`, Ruhmeshalle lokal in `mk-ruhm-v1`. Alte gespeicherte Stände und Exporte müssen weiter laden: neue Felder immer mit Standardwert in `DEF()` bzw. `migrate()` ergänzen, nie umbenennen.
- Das Beamer-Fenster (`#anzeige`) wird über das `storage`-Event synchronisiert.
- Punkte-Boni (Kopfgeld, Glücksrad) werden immer berechnet und nie in die eingetragenen Rohpunkte gemischt.
- Kein `*.json` in `.gitignore`, sonst wird `ruhmeshalle.json` nicht committet.

## Server (für den Online-Ausbau)
- Cloudflare Worker plus D1, Admin-Schutz über Cloudflare Access. Der Worker prüft das Access-JWT selbst; der Header allein reicht nicht.
- Server-Teil dünn halten: wenige Schnittstellen unter `/api/`, die Spiellogik bleibt im Browser. Ein Umzug auf einen internen Server ist irgendwann möglich, aktuell aber nicht geplant. Er soll machbar bleiben, indem nur ein paar Schnittstellen nachgebaut werden müssen.
- Turniertag geht vor: `localStorage` bleibt die Hauptquelle des Admins, Online-Speichern wird bei Netzausfall nachgereicht.
- Branch-Previews nutzen eine eigene Preview-Datenbank, nie die Produktionsdaten.
- Öffentliche Schnittstellen geben nur aus, was die öffentliche Ansicht braucht.

## Offene Aufgaben
Stehen in `AUFGABEN.md`. Zu Beginn einer Session dort nachsehen und erledigte Punkte abhaken.

## Branches
- `main`: Produktion (Cloudflare).
- `testdaten`: Preview mit Testdaten (`testdaten.html`, befüllte `ruhmeshalle.json`). Nie nach `main` mergen, nur `main` dort hineinmergen.

## Testen
Im Browser durchspielen: Teilnehmer → Liga-Runden mit Glücksrad → Tabelle mit Stechen → Turnierbaum → Ruhmeshalle, dazu ein zweites Fenster mit `#anzeige`. Alle drei Designs, hell und dunkel, und Handybreite prüfen.
