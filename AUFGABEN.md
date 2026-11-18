# Offene Aufgaben

Erledigte Aufgaben abhaken (`[x]`) und kurz notieren, was umgesetzt wurde. Die Regeln aus `CLAUDE.md` gelten für alles hier.

Die beiden offenen Aufgaben erst **nach dem Turnier**, in dieser Reihenfolge. Danach kommt der **Online-Ausbau** (unten) in drei Etappen.

## [x] Design-Schritte von `testdaten` nach `main` bringen

Vom Nutzer nach eigener Prüfung in der Preview vorgezogen und noch vor dem Turnier freigegeben. Per Cherry-Pick nach `main` gebracht (Einstellungen mit Steppern, Hell/Dunkel-Schalter, Karteneditor, diese Aufgabenliste), danach `main` in `testdaten` gemergt. Die App-Dateien auf beiden Branches sind identisch.

## [ ] Eine HTML-Datei statt drei

`mario-kart-turnier.html`, `-bayern.html`, `-sunset.html` und `-itdlz.html` unterscheiden sich nur in `data-design`, `<title>` und dem Theme-Stylesheet. Das Design soll stattdessen eine Einstellung sein, wie Hell/Dunkel.

- Eine Seite `mario-kart-turnier.html`. Das Design kommt aus `localStorage` `mk-design` (gibt es schon), optional überschreibbar per `?design=bayern`.
- Ein Inline-Skript im `<head>` setzt `data-design` und das Theme-Stylesheet vor dem ersten Zeichnen, genau wie heute schon `data-theme`. Kein Flackern.
- Design-Wechsel ohne Neuladen. Das Beamer-Fenster wechselt live über das `storage`-Event mit, wie beim Hell/Dunkel-Schalter.
- `mario-kart-turnier-bayern.html`, `-sunset.html` und `-itdlz.html` bleiben als winzige Weiterleitungen: `mk-design` setzen, dann auf `mario-kart-turnier.html` mit demselben `#hash` weiter. So funktionieren alte Links, Lesezeichen und offene Beamer-Fenster.
- `index.html` (auf `testdaten`: Weiterleitung zu `testdaten.html`) und `testdaten.html` (Szenario-Knöpfe öffnen das gewählte Design) anpassen.
- Muss weiter als `file://` laufen, nur relative Pfade.
- `CLAUDE.md` (Abschnitt Struktur) danach aktualisieren.
- Der Schriftzug je Design (`BRAND_TEXT` in `app.js`, z. B. „IT-DLZ Mario Kart Cup“) und der Seitentitel müssen beim Wechsel ohne Neuladen mitwechseln.
- Testen: Design wechseln im Bearbeiten-Fenster, Beamer folgt live; alte Adressen `-bayern.html#anzeige`, `-sunset.html#tabelle` und `-itdlz.html#liga` landen im richtigen Design und Tab; alle vier Designs hell und dunkel; Turnierstand bleibt unverändert.

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

---

# Online-Ausbau: öffentliche Live-Ansicht, Admin-Bereich, später mehrere Standorte

Erst nach den beiden Aufgaben oben angehen, die Etappen in Reihenfolge. Jede Etappe ist für sich nutzbar. Die Regeln dazu stehen in `CLAUDE.md` (Abschnitt „Server“).

**Ziel:** Jeder im Raum kann den laufenden Turnierstand per Link oder QR-Code am Handy mitverfolgen. Der Turnierleiter arbeitet in einem geschützten Admin-Bereich. Später können andere Standorte eigene Turniere und eine eigene Ruhmeshalle führen.

**Technik:** Cloudflare Worker (läuft schon für die statischen Dateien) plus D1 (SQL) für die Daten. Admin-Schutz über Cloudflare Access. Live-Aktualisierung zunächst per Nachfragen alle 5–10 Sekunden, keine WebSockets.

### Was der Nutzer selbst im Cloudflare-Dashboard einrichten muss
Vorher mit dem Nutzer durchgehen, nicht raten:
- D1-Datenbanken anlegen, je eine für Produktion und eine für Previews, damit `testdaten` nie echte Daten schreibt. Mit `wrangler d1 create …` oder im Dashboard; die IDs kommen in `wrangler.jsonc`.
- Zero Trust → Access → Application für den Admin-Pfad, mit den erlaubten E-Mail-Adressen.
- Prüfen, ob die D1-Bindung auch in Branch-Previews ankommt. Der `previews`-Block übernimmt laut Berichten nicht alle Einstellungen der obersten Ebene; ggf. Bindungen dort extra eintragen.

## [ ] Etappe 1: Live-Ansicht für alle, Admin-Bereich geschützt

Die Spiellogik bleibt im Browser des Admins. Der Server speichert nur den Turnierstand (`S`, wie in `localStorage` `mk-turnier-v2`) und gibt ihn öffentlich lesbar aus. Das ist der kleinste Umbau mit dem größten Effekt.

**Server (Worker):**
- `src/worker.js` beantwortet `/api/*`, alles andere liefern weiter die statischen Dateien. In `wrangler.jsonc`: `main`, Assets-Binding und `run_worker_first` nur für `/api/*`. Die genaue Syntax in der aktuellen Wrangler-Doku prüfen.
- D1-Tabelle `turniere`: `id` (zufällig, nicht erratbar), `name`, `state` (JSON), `version` (Zahl, für Konflikte), `oeffentlich` (an/aus), `aktualisiert`. Schema als Migration unter `migrations/`.
- `GET /api/turnier/:id`: öffentlich, nur wenn `oeffentlich` an ist. Gibt nur aus, was die öffentliche Ansicht braucht: kein Logo-Upload, keine Admin-Einstellungen, keine Ruhmeshallen-Entwürfe.
- `PUT /api/turnier/:id` mit `version`: nur Admin. Ist die gespeicherte Version neuer, antwortet der Server mit einem Konflikt statt zu überschreiben.
- Admin-Prüfung: Cloudflare Access schützt den Admin-Pfad. Der Worker prüft zusätzlich das Access-JWT (Header `Cf-Access-Jwt-Assertion`) gegen die Zertifikate des Teams. Ein vorhandener Header allein reicht nicht.
- `src/`, `migrations/` und `package.json` (falls nötig) in `.assetsignore` eintragen.

**Admin-Seite:**
- Die bisherige Bearbeiten-Ansicht, erreichbar unter dem geschützten Pfad.
- Neues Turnier „online stellen“ → bekommt eine `id`. Danach wird nach jedem `persist()` gebündelt gespeichert, höchstens etwa alle 2 Sekunden.
- Sichtbarer Status in der Kopfzeile: „Online gespeichert“, „Offline, wird nachgereicht“ oder „Konflikt“.
- **Offline-fähig:** `localStorage` bleibt die Hauptquelle am Turniertag. Fällt das Netz aus, läuft alles weiter und wird später nachgeschickt.
- Schalter „Öffentliche Ansicht an/aus“.
- Link und QR-Code zur öffentlichen Ansicht. QR-Code ohne externe Dienste: entweder eine kleine eigene Implementierung oder eine Bibliothek, die als lokale Datei im Repo liegt.

**Öffentliche Ansicht:**
- Eigene Seite, z. B. `live.html?t=<id>`, nur lesend, ohne Eingabefelder und ohne Admin-Knöpfe.
- Nutzt dieselben Bausteine wie die Beamer-Anzeige: Gruppen, Tabelle mit 💰/🍀/🦝, Turnierbaum, Glücksrad-Ergebnisse, Siegerehrung. Fürs Handy als Tabs statt Rotation, mit eigenem Vollbild-Modus für den Beamer.
- Fragt alle 5–10 Sekunden nach und aktualisiert nur, wenn sich `version` geändert hat. Ohne Netz bleibt der letzte Stand sichtbar, mit dem Hinweis „Stand von 17:42“.
- Sinnvolle Meldungen für unbekannte `id` und für ausgeschaltete öffentliche Ansicht.

**Weiter wie bisher:**
- Ohne Server, also lokal als Datei oder ohne Netz, funktioniert die App wie heute. Nur die Online-Funktionen sind dann aus.
- Das Beamer-Fenster im selben Browser wird weiter über das `storage`-Event synchronisiert.
- Alte Spielstände und Export/Import laden weiter.

**Testen:**
- Mit `wrangler dev` und lokaler D1 (`wrangler d1 migrations apply … --local`).
- Admin und öffentliche Ansicht in zwei Browsern: Punkte eintragen, die öffentliche Ansicht folgt in wenigen Sekunden.
- Netz trennen und wiederherstellen; Konflikt mit zwei Admin-Fenstern.
- Ohne Login darf `PUT` nicht durchgehen.
- Handybreite, alle Designs, hell und dunkel. Dann die Preview auf `testdaten` mit der Preview-Datenbank.

## [ ] Etappe 2: Ruhmeshalle in die Datenbank

- D1-Tabelle für Ruhmeshallen-Einträge, gleiche Felder wie in `ruhmeshalle.json`: `id`, `datum`, `titel`, `teilnehmer`, `platzierung`.
- Den Inhalt der heutigen `ruhmeshalle.json` einmalig importieren.
- `GET` ist öffentlich. Eintragen, Korrigieren und Löschen nur als Admin.
- Ablauf danach: „Eintragen“ nach der Siegerehrung speichert direkt online. Download und Git-Commit entfallen.
- Der Button „ruhmeshalle.json herunterladen“ bleibt als Export und Backup.
- Ohne Server fällt die App auf die statische `ruhmeshalle.json` zurück, wie heute.
- Die bisherige Sicherung gegen Datenverlust beibehalten: nichts überschreiben, was nicht vorher erfolgreich geladen wurde.
- Ewige Tabelle und Beamer-Ansicht bleiben gleich.

## [ ] Etappe 3: Mehrere Standorte und Turniere

**Erst angehen, wenn ein anderer Standort es wirklich nutzen will.** Vorher mit dem Nutzer klären, wie Standorte und Admins verwaltet werden sollen.

- Tabelle `standorte`. Turniere und Ruhmeshallen-Einträge gehören zu einem Standort.
- Admins je Standort: entweder per Access-Gruppe oder per E-Mail-Liste in D1, die der Worker gegen die E-Mail aus dem Access-JWT prüft.
- Öffentliche Startseite je Standort: laufende und vergangene Turniere, Ruhmeshalle des Standorts.
- Glücksrad-Karten und Einstellungen je Standort als Vorlage für neue Turniere.
- Datenschutz: In der öffentlichen Ansicht stehen Namen. Pro Standort einstellbar machen, ob öffentlich angezeigt wird. Hinweis an den Nutzer, das mit dem Standort abzuklären.
