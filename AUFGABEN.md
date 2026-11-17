# Offene Aufgaben

Erledigte Aufgaben abhaken (`[x]`) und kurz notieren, was umgesetzt wurde. Die Regeln aus `CLAUDE.md` gelten für alles hier.

Alle drei Aufgaben erst **nach dem Turnier**, in dieser Reihenfolge.

## [ ] Design-Schritte von `testdaten` nach `main` bringen

Erst wenn der Nutzer sie freigibt. Auf `testdaten` fertig und getestet, auf `main` fehlen noch:
- `13ce435` Einstellungen mit Steppern, Segmenten und Schalter
- `d5b81e0` Hell/Dunkel-Schalter in der Kopfzeile (System/Hell/Dunkel, `localStorage` `mk-theme`)
- `c088b2c` Karteneditor fürs Glücksrad
- der Commit, der diese Aufgabenliste ergänzt hat (`AUFGABEN.md`)

Per Cherry-Pick nach `main` holen, nicht `testdaten` mergen (sonst kommen `testdaten.html`, `muster.html` und die befüllte `ruhmeshalle.json` mit). `fonts/` ist schon auf `main`. Danach `main` wieder in `testdaten` mergen.

## [ ] Eine HTML-Datei statt drei

`mario-kart-turnier.html`, `-bayern.html` und `-sunset.html` unterscheiden sich nur in `data-design`, `<title>` und dem Theme-Stylesheet. Das Design soll stattdessen eine Einstellung sein, wie Hell/Dunkel.

- Eine Seite `mario-kart-turnier.html`. Das Design kommt aus `localStorage` `mk-design` (gibt es schon), optional überschreibbar per `?design=bayern`.
- Ein Inline-Skript im `<head>` setzt `data-design` und das Theme-Stylesheet vor dem ersten Zeichnen, genau wie heute schon `data-theme`. Kein Flackern.
- Design-Wechsel ohne Neuladen. Das Beamer-Fenster wechselt live über das `storage`-Event mit, wie beim Hell/Dunkel-Schalter.
- `mario-kart-turnier-bayern.html` und `-sunset.html` bleiben als winzige Weiterleitungen: `mk-design` setzen, dann auf `mario-kart-turnier.html` mit demselben `#hash` weiter. So funktionieren alte Links, Lesezeichen und offene Beamer-Fenster.
- `index.html` (auf `testdaten`: Weiterleitung zu `testdaten.html`) und `testdaten.html` (Szenario-Knöpfe öffnen das gewählte Design) anpassen.
- Muss weiter als `file://` laufen, nur relative Pfade.
- `CLAUDE.md` (Abschnitt Struktur) danach aktualisieren.
- Testen: Design wechseln im Bearbeiten-Fenster, Beamer folgt live; alte Adressen `-bayern.html#anzeige` und `-sunset.html#tabelle` landen im richtigen Design und Tab; alle drei Designs hell und dunkel; Turnierstand bleibt unverändert.

## [ ] „Let's get ready“-Startseite mit Warm-up bis zum Turnierstart

### Ziel
Bevor das Turnier offiziell beginnt, sollen alle im Raum auf dem Beamer sehen: wie das Turnier abläuft, wie man am besten startet und fährt, und wie lange es noch bis zum Start dauert. Bis dahin läuft ein öffentliches Warm-up: Jeder darf schon frei an den Konsolen fahren.

### Was die Seite zeigen soll
1. **Countdown zum offiziellen Start**, Standard 17:00 Uhr, in den Einstellungen änderbar (Uhrzeit, optional Datum). Groß und gut lesbar auf dem Beamer, z. B. „Turnierstart in 42:13“.
   - Bis dahin: Hinweis „Warm-up läuft: freie Rennen an allen Konsolen“, gern mit Konsolenanzahl aus den Einstellungen.
   - Ist die Startzeit erreicht: Umschalten auf „Gleich geht’s los!“, bis Runde 1 ausgelost ist.
   - Ist keine Startzeit gesetzt oder liegt sie in der Vergangenheit, darf nichts kaputtgehen.
2. **Turnierablauf**, automatisch aus den aktuellen Einstellungen erzeugt, nicht als fester Text:
   - Anzahl Teilnehmer, Liga-Runden, Gruppen pro Runde, Konsolen, geschätzte Dauer (siehe `estimate()` und `planFor()` in `app.js`)
   - Finalmodus (direktes Finale, Top 4 oder Halbfinals) und Spaßfinale
   - kurz erklärt: Glücksrad (Top 3 Handicaps, alle anderen Glück inkl. Multiplikator, Punkteklau, Sprung nach vorne), Kopfgeld in jeder Gruppe, Stechen bei Gleichstand
   - nur Funktionen erwähnen, die in den Einstellungen gerade aktiv sind (z. B. Kopfgeld an/aus)
3. **Tipps zum Raketenstart**: Wann man Gas gibt und was passiert, wenn man zu früh drückt.
4. **Tipps zur Fahrer- und Fahrzeugauswahl**: Was die Werte bedeuten (Tempo, Beschleunigung, Gewicht, Handling, Traktion, Mini-Turbo), und eine Empfehlung für Einsteiger (Beschleunigung und Mini-Turbo sind wichtiger als Höchstgeschwindigkeit) und für Erfahrene.
5. Optional: **Teilnehmerliste** („Schon dabei: …“), weitere Fahrtipps (Drift/Mini-Turbo, Items zurückhalten, Abkürzungen mit Pilz).

### Vorgaben
- **Welches Spiel?** Bisher gehen die Glücksrad-Karten von **Mario Kart 8 Deluxe** aus (Joy-Con, Lenkhilfe, Auto-Gas). Vor dem Schreiben der Tipps beim Nutzer nachfragen, ob das stimmt. Raketenstart und Fahrzeugwerte unterscheiden sich je nach Spiel. Für MK8 Deluxe gilt: Gas genau dann drücken, wenn die „2“ des Countdowns erscheint; wer zu früh drückt, dreht beim Start durch. Tipps sachlich halten und keine Werte erfinden, die nicht sicher stimmen.
- **Tipp-Texte editierbar machen.** Mit Standardtexten wie bei den Glücksrad-Karten, damit der Nutzer sie ohne Code anpassen kann. Keine großen Textfelder untereinander (das hat der Nutzer beim alten Karten-Editor ausdrücklich nicht gemocht): eine Zeile je Tipp wie im Karteneditor (`cardEditor()` in `app.js`), mit Bearbeiten, Hinzufügen, Löschen und „Standard wiederherstellen“.
- **Startzeit** als Einstellungszeile im Stil der übrigen Einstellungen (`srow()`), nicht als loses Feld.
- **Beamer:**
  - Solange noch keine Liga-Runde ausgelost ist, zeigt `#anzeige` diese Ansichten in der Rotation: Countdown/Warm-up, Ablauf, Raketenstart, Fahrzeugauswahl.
  - Danach verschwinden sie aus der Rotation.
  - Pause/Weiter und die Pfeiltasten müssen wie bisher funktionieren.
  - Der Countdown muss jede Sekunde aktualisieren, ohne dass die Rotation oder der Fortschrittsbalken neu startet.
- **Bearbeiten-Ansicht:** Ein eigener Tab, z. B. „Los geht’s“, als Vorschau. Startzeit und Tipp-Texte stehen in den Einstellungen.
- **Gespeicherte Daten:** Neue Felder (z. B. `startTime`, Tipp-Texte) mit Standardwerten in `DEF()` anlegen. Alte gespeicherte Stände, Export/Import und der Beamer-Sync über `localStorage` müssen weiter funktionieren.
- **Design:** Passend zu allen drei Designs (`app.css` plus Theme-Variablen), hell und dunkel, auf dem Beamer groß und auf dem Handy ohne seitliches Scrollen.
- **Hosting:** Weiter statisch, nur relative Pfade, keine externen Abhängigkeiten.

### Testen
- Beamer-Fenster `#anzeige` vor Runde 1 öffnen: Countdown läuft, Ansichten rotieren, Pause funktioniert.
- Startzeit in 1 Minute setzen und warten, bis auf „Gleich geht’s los!“ umgeschaltet wird.
- Runde 1 auslosen: Die Startseiten-Ansichten verschwinden aus der Rotation.
- Einen alten Spielstand (ohne die neuen Felder) laden.
- Alle drei Designs, hell/dunkel, Handybreite.
- Im Branch `testdaten` das Szenario „Leeres Turnier“ bzw. eines vor Runde 1 nutzen. Dort ggf. ein Szenario „Warm-up, Start in 30 Minuten“ ergänzen (nur auf `testdaten`, nie nach `main` mergen).
