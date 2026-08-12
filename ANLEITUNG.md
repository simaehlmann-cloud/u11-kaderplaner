# U11 Kaderplaner — Einrichtung

Die App besteht aus statischen Dateien und läuft in jedem Browser. GitHub Pages liefert sie
aus; gespeichert wird dort **nichts**. Deshalb zwei getrennte Schritte:

1. **App veröffentlichen** (GitHub Pages) — danach hat sie eine Adresse.
2. **Gemeinsame Ablage** — erst dann sieht das ganze Trainerteam dieselben Daten.

Ohne Schritt 2 funktioniert alles, die Daten liegen dann nur auf deinem Gerät.

---

## Diese Dateien

| Datei | Zweck |
| --- | --- |
| `index.html` | Startseite |
| `app.js` | die App |
| `styles.css` | Gestaltung |
| `manifest.webmanifest`, `icon-*.png`, `apple-touch-icon.png` | App-Symbol (Vereinswappen) und „zum Startbildschirm hinzufügen" |
| `wappen.png` | Wappen in der Kopfzeile der App |
| `.nojekyll` | verhindert, dass GitHub die Dateien umbaut |
| `ANLEITUNG.md` | diese Datei |

Alle Dateien gehören zusammen in **einen** Ordner.

---

## Schritt 1 · GitHub Pages

1. Auf [github.com](https://github.com) anmelden (kostenloses Konto genügt).
2. Oben rechts **+ → New repository**.
   - Name z. B. `u11-kaderplaner`
   - **Public** auswählen (Pages ist für private Repositories kostenpflichtig)
   - **Create repository**
3. Im leeren Repository auf **uploading an existing file** klicken und alle Dateien aus
   diesem Ordner hineinziehen — die Dateien selbst, nicht den Ordner.
   Falls `.nojekyll` beim Hochladen verschwindet: nicht schlimm, die App läuft auch ohne.
4. Unten **Commit changes**.
5. **Settings → Pages**:
   - *Source*: **Deploy from a branch**
   - *Branch*: **main**, Ordner **/ (root)** → **Save**
6. Nach ein bis zwei Minuten steht oben die Adresse:
   `https://DEIN-NAME.github.io/u11-kaderplaner/`

Auf dem Handy öffnen und über das Browser-Menü **„Zum Startbildschirm hinzufügen"** ablegen —
dann verhält sie sich wie eine App.

> Im Repository liegt nur Programmcode, **keine Spielerdaten**.

---

## Schritt 2 · Gemeinsame Ablage

### Der schnelle Weg (gut zum Ausprobieren)

In der App auf **Optionen → Gemeinsame Ablage → „Ablage anlegen — ohne Konto"** tippen.
Fertig. Danach erscheint der Knopf **„Link für das Trainerteam"** — diesen Link einmal an die
anderen Trainer schicken, dann sind alle verbunden.

Dahinter steckt der kostenlose Dienst **jsonblob.com**. Zwei Dinge solltest du wissen:

- Er ist eigentlich für Software-Tests gedacht, nicht für Vereinsdaten. Es gibt **keine
  Zusage**, dass er dauerhaft läuft.
- **Nicht abgerufene Ablagen werden nach 75 Tagen gelöscht.** Solange ihr die App im
  Wochenrhythmus nutzt, passiert nichts — über eine lange Winterpause aber schon.

Für eine ganze Saison würde ich deshalb auf einen der folgenden Wege wechseln. Der Umzug ist
einfach: neue Adresse eintragen, **Verbinden** — die Daten von deinem Gerät werden
automatisch hochgeladen.

### Der dauerhafte Weg · Firebase Realtime Database

Kostenlos, in der EU, unter deinem eigenen Google-Konto. Etwa fünf Minuten.

1. [console.firebase.google.com](https://console.firebase.google.com) mit Google-Konto öffnen.
2. **Projekt hinzufügen**, Name z. B. `u11-kader`. Google Analytics kannst du abwählen.
3. Links **Build → Realtime Database → Datenbank erstellen**.
   - Standort: **europe-west1**
   - **Im gesperrten Modus starten** — die Regeln setzen wir gleich selbst.
4. Reiter **Regeln**, Inhalt ersetzen durch:

   ```json
   {
     "rules": {
       ".read": true,
       ".write": true
     }
   }
   ```

   **Veröffentlichen**. (Der Testmodus wäre bequemer, läuft aber nach 30 Tagen ab — die App
   wäre dann ohne Vorwarnung stumm.)
5. Im Reiter **Daten** steht oben die Adresse, etwa
   `https://u11-kader-default-rtdb.europe-west1.firebasedatabase.app/`
6. Hänge einen **selbst ausgedachten, schwer zu ratenden Namen** an:

   ```
   https://u11-kader-default-rtdb.europe-west1.firebasedatabase.app/kader-7f3a9c2b
   ```

   Diesen Text in der App unter **Optionen** eintragen, **Testen**, dann **Verbinden**.
   Das `.json` am Ende ergänzt die App selbst.

Der kostenlose Spark-Tarif reicht für diesen Zweck um Größenordnungen.

### Alternative · Webspace des Vereins

Falls der Verein eine Homepage mit PHP hat, lege dort eine Datei `daten.php` an:

```php
<?php
$datei = __DIR__ . '/u11-daten.json';
header('Access-Control-Allow-Origin: *');
header('Access-Control-Allow-Methods: GET, PUT, OPTIONS');
header('Access-Control-Allow-Headers: Content-Type');
if ($_SERVER['REQUEST_METHOD'] === 'OPTIONS') { exit; }
header('Content-Type: application/json');
if ($_SERVER['REQUEST_METHOD'] === 'PUT') {
  file_put_contents($datei, file_get_contents('php://input'), LOCK_EX);
  echo '{"ok":true}';
  exit;
}
echo is_file($datei) ? file_get_contents($datei) : 'null';
```

In der App die volle Adresse eintragen, z. B. `https://verein.de/kader/daten.php`.
Sie muss über **https** erreichbar sein.

### Wenn etwas klemmt

Der Knopf **Testen** in der App sagt im Klartext, woran es liegt: falsche Adresse, fehlende
Schreibrechte, oder die Ablage lässt keine Zugriffe aus dem Browser zu.

---

## Was ihr wissen solltet

- **Die Adresse der Ablage ist das Passwort.** Wer sie kennt, kann alle Daten lesen und
  ändern. Nur im Trainerteam weitergeben, nicht in Eltern-Gruppen posten.
- **Datensparsamkeit.** Kinder nur mit Vorname und abgekürztem Nachnamen, keine Geburtsdaten,
  Adressen oder Telefonnummern. Dann ist auch ein versehentlich geteilter Link kein ernster
  Schaden.
- **Abgleich.** Alle 15 Sekunden, beim Zurückkehren zur App und über die Anzeige oben rechts.
  Grüner Punkt und Uhrzeit heißen: verbunden. „nur hier" heißt: keine Ablage eingerichtet.
  „Fehler" heißt: Adresse falsch oder gerade nicht erreichbar — Eingaben gehen nicht
  verloren, sie bleiben auf dem Gerät und gehen beim nächsten Abgleich hoch.
- **Gleichzeitiges Arbeiten.** Zusagen werden einzeln pro Spieler zusammengeführt, da könnt
  ihr euch nicht überschreiben. Nur wenn zwei im selben Moment dieselbe Aufstellung ändern,
  gewinnt die spätere Eingabe.
- **Ohne Internet** läuft die App weiter und gleicht ab, sobald wieder Netz da ist.
- **Sicherung.** In der App unter **Optionen → Sicherung** lädst du mit einem Tipp eine Datei
  mit allen Spielern, Spieltagen, Zusagen und Kadern herunter. Über **Einspielen** kommt sie
  zurück — der aktuelle Stand wird dabei ersetzt, bei gemeinsamer Ablage für das ganze
  Trainerteam. Empfehlung: einmal vor der Winterpause und vor größeren Umbauten.
