# Strichliste – Getränke pro Tag

Web-App für den Verein:
- Getränke werden pro Ausfahrt-Tag eingeladen.
- Mitglieder buchen sie per Barcode mit ihrem eigenen Konto.
- Am Ende gleicht der Admin mit dem gezählten Rest ab.

Alle Handys teilen sich eine Firebase-Datenbank. Scannen funktioniert auch ohne Netz; die Buchungen werden gesendet, sobald wieder Verbindung besteht.

## Dateien im GitHub-Repository

| Datei | Zweck |
|---|---|
| `index.html` | die App |
| `sw.js` | hält die App offline startbar |
| `index_min.js` | Barcode-Erkennung (ZXing) |
| `firestore.rules` | Sicherheitsregeln – werden in Firebase eingefügt, nicht auf GitHub gebraucht |

## Einrichtung (einmalig, ca. 15 Minuten)

Die Firebase-Konsole hat links zwei Bereiche: **Projektverknüpfungen** (zuletzt benutzte Dienste) und **Produktkategorien** (alle Dienste).

### 1. Firebase-Projekt anlegen
1. <https://console.firebase.google.com> öffnen, mit dem Google-Konto des Vereins anmelden.
2. **Projekt erstellen**, Namen vergeben. Google Analytics kann aus bleiben.
3. Der kostenlose **Spark**-Tarif reicht. Keine Kreditkarte nötig.

### 2. Anmeldung einschalten
1. Links **Authentication** öffnen. Steht es nicht unter „Projektverknüpfungen“, oben über **Nach Produkten suchen** „Authentication“ eingeben. Falls ein Knopf **Jetzt starten** erscheint, ihn anklicken.
2. Reiter **Anmeldemethode** (bei schmalem Fenster ggf. die Reiterleiste nach rechts scrollen):
   - **E-Mail/Passwort** aktivieren. Die Option „E-Mail-Link (Anmeldung ohne Passwort)“ bleibt aus.
   - Falls **Anonym** aktiv ist: deaktivieren. Wird nicht mehr gebraucht.
3. Reiter **Einstellungen → Autorisierte Domains → Domain hinzufügen**: `DEIN-NAME.github.io` eintragen.

### 3. Datenbank anlegen
1. Links **Firestore** öffnen. Steht es nicht unter „Projektverknüpfungen“, über **Produktkategorien → Datenbanken und Speicher** oder die Suche oben.
2. **Datenbank erstellen**, Standort in Europa wählen (z. B. `europe-west3` Frankfurt). Der Standort lässt sich später nicht ändern.
3. **Im Produktionsmodus starten**.
4. Reiter **Regeln**: den kompletten Inhalt von `firestore.rules` einfügen und **Veröffentlichen**. Die Admin-ID trägst du in Schritt 6 nach.

### 4. Zugangsdaten in die App eintragen
1. Links **Einstellungen** (Zahnrad) → **Allgemein**.
2. Ganz nach unten zu **Meine Apps** scrollen, auf das Web-Symbol **`</>`** klicken.
3. App-Namen vergeben (z. B. „Strichliste“). *Firebase Hosting* **nicht** anhaken, dann **App registrieren**.
4. Den angezeigten Block `const firebaseConfig = { … }` kopieren. Später findest du ihn wieder unter Einstellungen → Allgemein → Meine Apps → Option „Config“.
5. In `index.html` nach `KONFIGURATION` suchen (ca. Zeile 200). Den Platzhalter-Block `const firebaseConfig = { … };` komplett durch den kopierten ersetzen.

Die Werte in `firebaseConfig` sind keine Geheimnisse und dürfen im öffentlichen Repository stehen. Geschützt werden die Daten durch die Sicherheitsregeln.

### 5. Auf GitHub Pages hochladen
`index.html`, `sw.js` und `index_min.js` ins Repository legen (alle im selben Ordner). Nach ein bis zwei Minuten ist die Seite unter `https://DEIN-NAME.github.io/REPO/` erreichbar.

### 6. Admin festlegen
1. Die Seite öffnen, **Registrieren** wählen und dein eigenes Konto anlegen (Name, E-Mail, Passwort).
2. Oben auf **Admin** tippen. Es erscheint „Keine Admin-Rechte“. Darunter „Für die Einrichtung: Nutzer-ID“ aufklappen und die ID kopieren.
   - Alternativ steht die ID in der Firebase-Konsole unter Authentication → Nutzer → Spalte „Nutzer-UID“.
3. In der Firebase-Konsole unter **Firestore → Regeln** bei `adminUids()` eintragen und **Veröffentlichen**:
   ```
   return ['aB3dE5fG7hJ9kL1mN3pQ5rS7tU9v'];
   ```
   Mehrere Admins werden mit Komma getrennt: `['ID1', 'ID2']`.
4. Seite neu laden. Der Admin-Bereich ist jetzt offen.

### 7. Testen
1. Im Admin einen **neuen Tag** anlegen.
2. **Einladen per Scan** wählen, eine Flasche scannen, das Getränk anlegen und Kästen antippen.
3. Auf einem zweiten Handy ein Konto registrieren und scannen. Die Buchung erscheint beim Admin unter „Abgleich“ und „Nach Person“.

## Für die Mitglieder

- Seite öffnen, **Registrieren** wählen und Vor- und Nachname, E-Mail und Passwort eingeben. Dafür braucht man einmal Internet.
- Am besten zum Home-Bildschirm hinzufügen:
  - iPhone: Safari → Teilen → „Zum Home-Bildschirm“
  - Android: Chrome → Menü → „Zum Startbildschirm hinzufügen“
- Man bleibt angemeldet, auch offline. Neues Handy oder Browserdaten gelöscht? Einfach mit derselben E-Mail **anmelden**, Name und Buchungen sind wieder da.
- **Passwort vergessen?** E-Mail eintragen und auf „Passwort vergessen?“ tippen. Es kommt eine Mail von Firebase zum Zurücksetzen.
- Oben neben „Strichliste“ steht der Netzstatus. „offline · 3 warten“ heißt: 3 Buchungen sind gespeichert und werden gesendet, sobald Netz da ist. In dieser Zeit nicht abmelden; die App warnt davor.

## Namen

- Der Name wird bei der Registrierung festgelegt und hängt am Konto. Mitglieder können ihn nicht ändern, das verhindern die Sicherheitsregeln.
- Nur Admins können einen Namen unter **Admin → Personen** korrigieren, z. B. bei Tippfehlern. Dort stehen auch die E-Mail-Adressen. Gleiche Namen werden als „doppelt“ markiert.
- Wer mit einer zweiten E-Mail-Adresse ein weiteres Konto anlegt, taucht in der Personenliste als eigener Eintrag auf.

## Ablauf an einem Tag

1. **Vorher (Admin):**
   - **Neuer Tag** mit Datum und Ziel anlegen. Er wird automatisch der aktive Tag für alle Handys.
   - Optional „Rest vom Vortag übernehmen“ ankreuzen.
   - **Einladen per Scan:** Flasche oder Kasten scannen und „+1 Kasten“ bzw. „+1 Flasche“ antippen.
2. **Unterwegs (alle):** Getränk nehmen, Barcode scannen.
3. **Danach (Admin):**
   - **Rest zählen per Scan** oder die Zahlen direkt in die Tabelle tippen.
   - Die Spalte **Diff.** zeigt, was nicht gescannt wurde.
   - Mit **Nach Person als CSV** gibt es die Abrechnung pro Mitglied.
   - **Tag abschließen**.

## Kosten und Grenzen (kostenloser Spark-Tarif)

- **Datenbank:** 50.000 Lesevorgänge und 20.000 Schreibvorgänge pro Tag. Jede Buchung ist ein Schreibvorgang; Mitglieder lesen nur ihre eigenen Buchungen. Für 20–30 Handys und bis zu einigen tausend Getränken am Tag reicht das deutlich.
- **Anmeldung:** bis zu 100 neue Konten pro Stunde und Internetanschluss. Das genügt auch, wenn sich alle gleichzeitig im selben WLAN registrieren.
- **E-Mails:** 150 „Passwort vergessen“-Mails pro Tag.

## Update der App

Nach Änderungen zwei Zahlen gleich hochzählen (z. B. von 5 auf 6):
- in `index.html`: `const APP_VERSION = '5';`
- in `sw.js`: `const VERSION = 'strichliste-v5';`

Dann holen sich alle Handys beim nächsten Öffnen mit Netz die neue Version. Ganz unten auf der Seite steht „Version 5“. Daran siehst du, ob das Update auf einem Handy angekommen ist. Falls nicht: die Seite schließen und neu öffnen, beim zweiten Öffnen ist die neue Version aktiv.

## Daten aus der Offline-Version übernehmen

1. In der alten Version unter **Admin → Daten → Backup speichern** eine JSON-Datei erzeugen.
2. In der neuen Version unter **Admin → Daten → Backup importieren** diese Datei auswählen.

Die alten Buchungen haben keinen Personennamen.
