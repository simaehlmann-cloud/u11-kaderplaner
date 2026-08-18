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

## Schritt 4 · Android-App (APK) bauen — optional

Auf dem Handy reicht eigentlich „Zum Startbildschirm hinzufügen". Wer trotzdem eine echte
Installationsdatei möchte, kann sie GitHub bauen lassen — ohne eigenen Rechner, ohne
Android Studio. Die App verhält sich genauso und synchronisiert weiterhin über eure Ablage.

1. Im Repository auf den Reiter **Actions**. Beim ersten Mal fragt GitHub nach — mit
   **„I understand my workflows, go ahead and enable them"** bestätigen.
2. Links **Android-App bauen** anklicken, rechts **Run workflow** → **Run workflow**.
3. Nach etwa fünf Minuten den fertigen Lauf öffnen. Unten unter **Artifacts** liegt
   `u11-kaderplaner-apk` als ZIP zum Herunterladen; darin steckt `app-debug.apk`.
4. Die APK auf das Handy kopieren und öffnen. Android fragt, ob Apps aus dieser Quelle
   installiert werden dürfen — einmal erlauben.

Dazu drei ehrliche Hinweise:

- Die APK ist **Debug-signiert**. Sie lässt sich installieren, aber nicht über den Play Store
  verteilen. Für den internen Gebrauch im Trainerteam ist das in Ordnung.
- iPhones können keine APK installieren. Dort bleibt es beim Startbildschirm-Symbol — das
  funktioniert genauso gut.
- In der App gibt es keine Web-Adresse zum Teilen. Der Knopf zeigt dann statt eines Links die
  **Adresse der Ablage**, die die anderen einmal unter *Optionen* eintragen.

Ändert sich die App später, einfach den Workflow erneut starten — die neue APK enthält den
aktuellen Stand.

### Adresse der Ablage fest in die APK einbauen — mit Bedacht

Wer die Adresse nicht bei jeder Installation eintippen will, kann sie beim Bauen hinterlegen:

1. Im Repository auf **Settings → Secrets and variables → Actions → New repository secret**.
2. Name: `SYNC_URL`. Wert: die vollständige Adresse eurer Ablage.
3. Workflow neu starten. Die fertige APK ist dann sofort verbunden.

Das Secret steht **nicht** im Repository und landet auch nicht in der Web-Fassung auf GitHub
Pages — nur in der APK.

**Aber:** Bei einem öffentlichen Repository kann jeder, der die Actions-Seite aufruft, die
gebaute APK herunterladen. Wer sie auseinandernimmt, findet die Adresse darin. Damit wäre
sie faktisch öffentlich — und wer die Adresse hat, kann alle Daten lesen und ändern.

Deshalb die ehrliche Empfehlung: **Adresse lieber einmal pro Gerät eintippen.** Das dauert
zehn Sekunden und passiert genau einmal. Wenn ihr die Adresse doch einbauen wollt, dann nur
mit einem privaten Repository (dort sind auch die Artefakte geschützt) — und in jedem Fall
mit sparsamen Daten: Vorname und abgekürzter Nachname, sonst nichts.

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
- **Mannschaften.** Unter *Optionen → Mannschaften* stehen Name, Spielform (Feldspieler ohne
  Torwart) und Kadergröße je Mannschaft. Die App rechnet daraus die Zahl auf dem Feld und die
  Auswechselspieler aus. So lässt sich die App später auf 7+1, 9+1 oder 10+1 umstellen, ohne
  dass etwas neu gebaut werden muss.
- **Spieltag-Infos.** Heim oder auswärts, Gegner, Treffen an der ZSA, Treffen am Spielort
  (nur auswärts), Anstoß und die Adresse des Spielorts. Aus der Adresse baut die App einen
  Google-Maps-Link; ein fertiger Maps-Link darf auch direkt eingefügt werden. Der Knopf
  **Teilen** erzeugt daraus eine fertige Nachricht mit allen Zeiten, Navigation und Kader.
- **Zurück-Taste.** Sie schließt erst die geöffnete Ansicht und fragt auf der Startseite
  nach, bevor die App zugeht. Im Browser greift das ab der ersten Berührung des Bildschirms —
  vorher lässt Chrome keinen eigenen Verlaufseintrag zu.
- **Blick nach vorn.** Beim Aufstellen der oberen Mannschaft prüft die App, wie viele Kinder
  beim nächsten Pflichtspiel der unteren noch spielberechtigt wären. Wird es dort zu eng für
  eine Mannschaft plus zwei Auswechselspieler, warnt sie und nennt die Kinder, an denen es
  liegt.
- **Zusagen.** Drei Zustände je Kind: Haken (dabei), Fragezeichen (Eltern wissen es noch nicht),
  Kreuz (nicht dabei). Für den Kadervorschlag zählen nur Haken. Bleiben weniger als zwei
  Auswechselspieler übrig, weist die App darauf hin, bei welchen Kindern eine Nachfrage
  lohnt.
- **Gleichzeitiges Arbeiten.** Zusagen werden einzeln pro Spieler zusammengeführt, da könnt
  ihr euch nicht überschreiben. Nur wenn zwei im selben Moment dieselbe Aufstellung ändern,
  gewinnt die spätere Eingabe.
- **Ohne Internet** läuft die App weiter und gleicht ab, sobald wieder Netz da ist.
- **Sicherung.** In der App unter **Optionen → Sicherung** lädst du mit einem Tipp eine Datei
  mit allen Spielern, Spieltagen, Zusagen und Kadern herunter. Über **Einspielen** kommt sie
  zurück — der aktuelle Stand wird dabei ersetzt, bei gemeinsamer Ablage für das ganze
  Trainerteam. Empfehlung: einmal vor der Winterpause und vor größeren Umbauten.
