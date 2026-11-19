# Strichliste – Getränke pro Tag

Web-App für den Verein: Getränke werden pro Ausfahrt-Tag eingeladen, von allen Handys per Barcode gebucht und am Ende mit dem gezählten Rest abgeglichen. Alle Handys teilen sich eine Firebase-Datenbank. Scannen funktioniert auch ohne Netz; die Buchungen werden gesendet, sobald wieder Verbindung besteht.

## Dateien im GitHub-Repository

| Datei | Zweck |
|---|---|
| `index.html` | die App |
| `sw.js` | hält die App offline startbar |
| `index_min.js` | Barcode-Erkennung (ZXing), unverändert aus dem bisherigen Projekt |
| `firestore.rules` | Sicherheitsregeln – werden in Firebase eingefügt, nicht auf GitHub gebraucht |

## Einrichtung (einmalig, ca. 15 Minuten)

### 1. Firebase-Projekt anlegen
1. <https://console.firebase.google.com> öffnen, mit dem Google-Konto des Vereins anmelden.
2. **Projekt erstellen**, Namen vergeben (z. B. `verein-strichliste`). Google Analytics kann aus bleiben.

### 2. Anmeldung einschalten
1. Links **Build → Authentication → Jetzt starten**.
2. Reiter **Anmeldemethode**: **Anonym** aktivieren und **E-Mail/Passwort** aktivieren.
   - *Anonym* brauchen die Scan-Handys: Sie melden sich unsichtbar im Hintergrund an.
   - *E-Mail/Passwort* ist für die Admins.
3. Reiter **Nutzer → Nutzer hinzufügen**: E-Mail und Passwort für jeden Admin anlegen.
4. In der Nutzerliste die **Nutzer-UID** jedes Admins kopieren (lange Zeichenkette).
5. Reiter **Einstellungen → Autorisierte Domains → Domain hinzufügen**: `DEIN-NAME.github.io` eintragen.

### 3. Datenbank anlegen
1. Links **Build → Firestore Database → Datenbank erstellen**.
2. Standort in Europa wählen (z. B. `europe-west3` Frankfurt). Der Standort lässt sich später nicht ändern.
3. **Im Produktionsmodus starten**.
4. Reiter **Regeln**: den kompletten Inhalt von `firestore.rules` einfügen.
5. Oben bei `adminUids()` die kopierten UIDs eintragen, z. B.
   ```
   return ['aB3dE5fG7hJ9kL1mN3pQ5rS7tU9v', 'xY2zA4bC6dE8fG0hJ2kL4mN6pQ8r'];
   ```
6. **Veröffentlichen**.

### 4. Zugangsdaten in die App eintragen
1. Links oben auf das Zahnrad → **Projekteinstellungen → Allgemein → Meine Apps → Web (`</>`)**.
2. App-Namen vergeben, *Firebase Hosting* nicht anhaken, **App registrieren**.
3. Den angezeigten Block `const firebaseConfig = { … }` kopieren.
4. In `index.html` ganz oben im Skript (Abschnitt `KONFIGURATION`) den Platzhalter-Block damit ersetzen.

Die Werte in `firebaseConfig` sind keine Geheimnisse und dürfen im öffentlichen Repository stehen. Geschützt werden die Daten durch die Sicherheitsregeln aus Schritt 3.

### 5. Auf GitHub Pages hochladen
`index.html`, `sw.js` und `index_min.js` ins Repository legen (alle im selben Ordner). Nach ein bis zwei Minuten ist die Seite unter `https://DEIN-NAME.github.io/REPO/` erreichbar.

### 6. Testen
1. Seite öffnen → **Admin** → mit Admin-E-Mail anmelden.
   - Erscheint „Keine Admin-Rechte“ mit einer Nutzer-ID: diese ID in den Regeln bei `adminUids()` eintragen und erneut veröffentlichen.
2. Neuen Tag anlegen, **Einladen per Scan** → Flasche scannen → Getränk anlegen → Kästen antippen.
3. Auf einem zweiten Handy die Seite öffnen und scannen. Die Buchung erscheint beim Admin unter „Abgleich“.

## Handys vorbereiten

- Jedes Handy muss die Seite **einmal mit Internet** öffnen. Danach startet sie auch offline.
- Am besten zum Home-Bildschirm hinzufügen:
  - iPhone: Safari → Teilen → „Zum Home-Bildschirm“
  - Android: Chrome → Menü → „Zum Startbildschirm hinzufügen“
- Oben neben „Strichliste“ steht der Netzstatus. „offline · 3 warten“ heißt: 3 Buchungen sind gespeichert und werden gesendet, sobald Netz da ist. Bitte die Seite nicht über die Browser-Einstellungen löschen, solange etwas wartet.

## Namen

- Beim ersten Öffnen muss jedes Handy einen Vor- und Nachnamen festlegen. Ohne Namen lässt sich nichts buchen.
- Der Name ist danach fest mit dem Handy verbunden. Die Datenbank-Regeln verhindern, dass er nachträglich geändert wird.
- Nur Admins können einen Namen unter **Admin → Personen** korrigieren, z. B. bei Tippfehlern. Gleiche Namen auf mehreren Handys werden dort als „doppelt“ markiert.
- Einschränkung: Wer die Website-Daten im Browser löscht oder ein anderes Handy nimmt, gilt als neues Gerät und muss wieder einen Namen festlegen. Das Gleiche passiert, wenn sich ein Admin auf seinem Handy abmeldet. Solche Fälle erkennt der Admin in der Personenliste.

## Ablauf an einem Tag

1. **Vorher (Admin):**
   - **Neuer Tag** mit Datum und Ziel anlegen. Der Tag wird automatisch der aktive Tag für alle Handys.
   - Optional „Rest vom Vortag übernehmen“ ankreuzen.
   - **Einladen per Scan:** Flasche oder Kasten scannen und „+1 Kasten“ bzw. „+1 Flasche“ antippen.
2. **Unterwegs (alle):** Getränk nehmen, Barcode scannen. Der Admin sieht eine Aufteilung nach Person und kann sie als CSV exportieren.
3. **Danach (Admin):**
   - **Rest zählen per Scan** oder die Zahlen direkt in die Tabelle tippen.
   - „Nicht Gezähltes = 0“ setzt alle leeren Getränke auf einmal.
   - Die Spalte **Diff.** zeigt, was nicht gescannt wurde.
   - **Tag abschließen**, bei Bedarf **Tag als CSV** exportieren.

## Kosten und Grenzen

Der kostenlose Firebase-Tarif erlaubt pro Tag 50.000 Lesevorgänge und 20.000 Schreibvorgänge. Die App ist darauf ausgelegt:
- Jede Buchung ist ein Schreibvorgang.
- Scan-Handys lesen nur ihre eigenen Buchungen.
- Nur das Admin-Handy liest alle Buchungen des gewählten Tages.

Für 20–30 Handys und einige hundert bis tausend Getränke am Tag reicht das deutlich.

## Update der App

Nach Änderungen an `index.html` oben in `sw.js` die Zeile `const VERSION = 'strichliste-v4'` hochzählen (z. B. `v5`). Dann holen sich alle Handys beim nächsten Öffnen mit Netz die neue Version.

## Daten aus der Offline-Version übernehmen

In der alten Version unter **Admin → Daten → Backup speichern** eine JSON-Datei erzeugen. In der neuen Version unter **Admin → Daten → Backup importieren** diese Datei auswählen.
