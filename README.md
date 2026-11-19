# Wexhainis Getränke-Strichliste

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

Für das Projekt `wexhainis-getraenkestrichliste` ist das bereits erledigt; die Zugangsdaten stehen in `index.html`. Für ein anderes Projekt:

1. Links **Einstellungen** (Zahnrad) → **Allgemein**.
2. Ganz nach unten zu **Meine Apps** scrollen, auf das Web-Symbol **`</>`** klicken.
3. App-Namen vergeben (z. B. „Strichliste“). *Firebase Hosting* **nicht** anhaken, dann **App registrieren**.
4. Den angezeigten Block `const firebaseConfig = { … }` kopieren. Später findest du ihn wieder unter Einstellungen → Allgemein → Meine Apps → Option „Config“.
5. In `index.html` nach `KONFIGURATION` suchen (ca. Zeile 200). Den Platzhalter-Block `const firebaseConfig = { … };` komplett durch den kopierten ersetzen.

Die Werte in `firebaseConfig` sind keine Geheimnisse und dürfen im öffentlichen Repository stehen. Geschützt werden die Daten durch die Sicherheitsregeln.

### 5. Auf GitHub Pages hochladen
`index.html`, `sw.js` und `index_min.js` ins Repository legen (alle im selben Ordner). Nach ein bis zwei Minuten ist die Seite unter `https://DEIN-NAME.github.io/REPO/` erreichbar.

### 6. Super-Admin festlegen
1. Die Seite öffnen, **Registrieren** wählen und dein eigenes Konto anlegen (Name, E-Mail, Passwort).
2. **Fünfmal schnell auf den Titel** „Wexhainis Getränke-Strichliste“ oben tippen, das öffnet den Admin-Bereich. Es erscheint „Keine Admin-Rechte“. Darunter „Für die Einrichtung: Nutzer-ID“ aufklappen und die ID kopieren.
   - Alternativ steht die ID in der Firebase-Konsole unter Authentication → Nutzer → Spalte „Nutzer-UID“.
3. In der Firebase-Konsole unter **Firestore → Regeln** bei `superUids()` eintragen und **Veröffentlichen**:
   ```
   return ['aB3dE5fG7hJ9kL1mN3pQ5rS7tU9v'];
   ```
   Mehrere Super-Admins werden mit Komma getrennt: `['ID1', 'ID2']`. Ein zweiter Super-Admin als Vertretung ist sinnvoll.
4. Seite neu laden. Der Admin-Bereich ist jetzt offen.
5. Weitere **Admins** ernennst du direkt in der App unter **Admin → Personen**: Person antippen und bei „Rolle“ „Admin“ wählen. Die Regeln musst du dafür nicht mehr anfassen.

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
- **Passwort ändern:** angemeldet auf „Passwort ändern“ neben „Abmelden“ tippen.
- **Passwort vergessen?** E-Mail eintragen und auf „Passwort vergessen?“ tippen. Es kommt eine Mail von Firebase zum Zurücksetzen.
- Oben neben „Strichliste“ steht der Netzstatus. „offline · 3 warten“ heißt: 3 Buchungen sind gespeichert und werden gesendet, sobald Netz da ist. In dieser Zeit nicht abmelden; die App warnt davor.

## Admin-Bereich öffnen

Es gibt keinen sichtbaren Admin-Knopf. **Fünfmal schnell hintereinander auf den Titel** oben tippen (innerhalb von zwei Sekunden), dann öffnet sich der Admin-Bereich. Zurück geht es über „Zurück zum Scannen“ oben rechts. Das ist nur ein Sichtschutz – die eigentlichen Rechte prüfen die Firebase-Regeln. Wer kein Admin ist, sieht dort nur „Keine Admin-Rechte“.

## Historie (Super-Admin)

Unter Admin → **Historie** steht jede Änderung: Mitglieder (Registrierung, Name, Rolle, Sperre, Entfernen, Profilbild, Passwort), Buchungen (Rückgängig, Storno, Nachtrag), Einladen und Zählen, Getränke, Tage, Easter Eggs und System (Zähler, Import, Zurücksetzen). Datum wählen, die Bereiche lassen sich aufklappen. Mit „auch normale Scans“ erscheinen zusätzlich alle Buchungen des Datums. Die Historie kann niemand ändern oder löschen – auch der Super-Admin nicht, und auch „Statistiken zurücksetzen“ lässt sie stehen.

## Buchungen einer Person korrigieren

Unter Admin → **Buchung nachtragen** die Person wählen: Darunter stehen ihre Buchungen des Tages, pro Getränk mit „−1 stornieren“ und aufklappbar einzeln. Nachtragen und Stornieren passen die Easter-Egg-Zähler automatisch an.

## Statistiken zurücksetzen (Super-Admin)

Admin → Daten → **Statistiken zurücksetzen**, dann das eigene Passwort eingeben. Gelöscht werden alle Easter-Egg-Zähler (Gruppe und Personen, heute und seit Beginn) und alle Easter-Egg-Verläufe; danach können alle Easter Eggs neu auslösen. Optional lassen sich zusätzlich **alle Buchungen und Tage** löschen. Getränke, Personen, Easter-Egg-Regeln und die Historie bleiben erhalten.

## Rollen

| | Mitglied | Admin | Super-Admin |
|---|:-:|:-:|:-:|
| Getränke für sich buchen, eigene Buchung zurücknehmen | ✓ | ✓ | ✓ |
| Eigenes Passwort ändern | ✓ | ✓ | ✓ |
| Tage, Getränke, Einladen, Zählen, Abgleich, Exporte | | ✓ | ✓ |
| Buchungen stornieren und für andere nachtragen | | ✓ | ✓ |
| Namen korrigieren | | ✓ | ✓ |
| Anstehende Easter Eggs sehen | | ✓ | ✓ |
| Easter Eggs verwalten, Historie, Statistiken zurücksetzen | | | ✓ |
| Admins ernennen und abberufen | | | ✓ |
| Konten sperren, entsperren, entfernen, wiederherstellen | | | ✓ |
| Passwort-Mail an eine Person schicken | | | ✓ |

Die Rechte prüfen die Firebase-Regeln, nicht nur die App.
- **Sperren:** wirkt sofort. Die Person sieht „Dein Konto ist gesperrt“ und kann nichts mehr buchen.
- **Entfernen:** sperrt das Konto und nimmt es aus der Liste. Auf Wunsch werden dabei alle Buchungen der Person gelöscht. Unter „Entfernte Konten“ lässt sich das rückgängig machen.
- **Login endgültig löschen:** In der Firebase-Konsole unter Authentication → Nutzer, über das Menü „⋮“ der Person. Die App verlinkt dorthin. Direkt aus der App geht das im kostenlosen Tarif nicht. Fremde Passwörter setzen geht ebenfalls nicht; dafür gibt es die Passwort-Mail.

## Profilbilder

- Jedes Mitglied tippt in der Scan-Ansicht auf den runden Kreis neben „Gebucht als“ und wählt ein Foto (Kamera oder Galerie). Das Bild wird auf dem Handy quadratisch zugeschnitten und auf 160×160 Pixel verkleinert (etwa 10 KB).
- Das Bild erscheint groß im Easter-Egg-Popup und klein im Verlauf „Easter Eggs heute“, bei Gruppen-Easter-Eggs immer das Bild der Person, die es ausgelöst hat. Ohne Bild erscheinen die Initialen.
- „Bild entfernen“ steht direkt darunter. **Admins** sehen die Bilder unter Admin → Personen und können unpassende entfernen.
- Die Bilder liegen in der Datenbank (nicht in Firebase Storage, das für neue Projekte den kostenpflichtigen Tarif bräuchte). Andere Handys laden ein Bild einmal pro Sitzung und merken es sich.

## Namen

- Der Name wird bei der Registrierung festgelegt und hängt am Konto. Mitglieder können ihn nicht ändern, das verhindern die Sicherheitsregeln.
- Admins und Super-Admins können einen Namen unter **Admin → Personen** korrigieren, z. B. bei Tippfehlern. Dort stehen auch die E-Mail-Adressen. Gleiche Namen werden als „doppelt“ markiert.
- Bei älteren Konten fehlt die E-Mail in der Liste noch. Sie wird automatisch ergänzt, sobald die Person die App das nächste Mal öffnet.
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

## Warnung, wenn Getränke zu Ende gehen

- Unter **Admin → Getränke** bei jedem Getränk **„Warnen ab Rest (Flaschen)“** eintragen, z. B. 6. Ohne Wert gibt es für dieses Getränk keine Warnung.
- Sinkt am aktiven Tag der Soll-Rest (mitgenommen minus gebucht) auf diesen Wert, bekommen **alle Admins** unten eine rot-schwarze Hinweisleiste mit Ton und Vibration – egal, ob sie gerade scannen oder im Admin-Bereich sind. Bei „leer“ kommt ein zweiter Hinweis.
- **Einladen** in der Leiste öffnet direkt das Nachladen per Scan. Wird nachgeladen, ist die Warnung wieder scharf.
- In der **Tagesübersicht** stehen „Leer“ und „Geht zu Ende“ ganz oben, im **Abgleich** sind die Zeilen markiert.
- **Hinweis:** Die Warnung kommt nur, solange die App auf dem Admin-Handy geöffnet ist. Mit „Warnungen aufs Handy erlauben“ (Admin → Daten) erscheint sie zusätzlich als Handy-Benachrichtigung, wenn die App im Hintergrund offen ist – auf dem iPhone nur, wenn die App über „Zum Home-Bildschirm“ installiert ist. Benachrichtigungen bei ganz geschlossener App bräuchten einen eigenen Server (Firebase Blaze-Tarif).

## Easter Eggs

Bei bestimmten Buchungen erscheint für ein paar Sekunden ein Vollbild mit Wespe, Konfetti, Ton und Vibration. Antippen schließt es. Jedes Easter Egg kommt pro Person und Tag nur einmal, auch nach „Rückgängig“.

**Nur auf dem eigenen Handy**
- 1., 3., 5., 7., 8., 9. und 10. Bier am Tag, danach bei jedem weiteren Bier (mit zunehmendem Augenzwinkern Richtung Heimfahrt)
- erstes Wasser nach mindestens 3 Bier
- Schnapszahl bei den eigenen Getränken (11, 22, 33 …): heute und seit Beginn
- Scan um 11:11 Uhr und am 11.11.

**Auf allen Handys** („Max Meier hat … ausgelöst“)
- Anstich: erster Scan des Tages
- 10., 20. und 30. Bier der ganzen Gruppe am Tag
- Schnapszahl bei allen Getränken der Gruppe: heute und seit Beginn

Hinweise:
- **Was als Bier zählt**, legt die **Sorte** des Getränks fest (Admin → Getränke). Ohne Angabe rät die App aus dem Namen. Radler und Alkoholfreies zählen nicht als Bier.
- Die **Gruppen-Easter-Eggs** brauchen Netz. Offline wird weiter mitgezählt, aber ohne Meldung.
- Nach Stornos, Nachträgen oder einem Import unter **Admin → Daten → Zähler neu berechnen** tippen, damit die Zahlen wieder stimmen.
- **Easter Eggs: an/aus** schaltet alles für alle Handys ab.
- **Verlauf für alle:** In der Scan-Ansicht steht unter „Deine Getränke heute“ der Abschnitt „Easter Eggs heute“ mit zwei Reitern: *Für alle* (alle gemeinsamen Easter Eggs des Tages mit Uhrzeit und Auslöser) und *Deine* (die eigenen). Die eigenen sind in der Datenbank gespeichert und bleiben auch bei einem Handywechsel erhalten.
- **Vorschau für Admins:** Im Admin-Bereich zeigt „Anstehende Easter Eggs“ für den aktiven Tag, was als Nächstes kommt – für die Gruppe (Anstich, nächstes Gruppen-Bier, nächste Schnapszahl, 11:11 und 11.11.) und pro Person, sortiert danach, wer am nächsten dran ist.
- **Verwalten (Super-Admin):** Unter Admin → **Easter Eggs verwalten** lässt sich jedes Easter Egg an- und ausschalten, ändern, kopieren, löschen und als Vorschau anzeigen; „Neues Easter Egg“ legt eins an, „Standard wiederherstellen“ setzt alles auf die mitgelieferten zurück. Einstellbar sind:
  - **Für:** jede Person für sich oder alle gemeinsam
  - **Wann:** genau bei einer Anzahl, bei jeder n-ten, ab einer Anzahl jedes Mal, bei Schnapszahl, „erstes … nach mindestens n …“, zu einer Uhrzeit, an einem Datum
  - **Getränk** (Sorte oder alle) und **Zeitraum** (heute oder seit Beginn)
  - **Texte:** eine Zeile pro Variante im Format `Titel | Text`; mehrere Zeilen wechseln sich ab. `{n}` wird durch die Zahl ersetzt, `{name}` durch die Person.
- Die mitgelieferten Easter Eggs stehen als `DEFAULT_RULES` in `index.html`. Sobald der Super-Admin etwas ändert, gelten die gespeicherten Regeln aus der Datenbank.

## Kosten und Grenzen (kostenloser Spark-Tarif)

- **Datenbank:** 50.000 Lesevorgänge und 20.000 Schreibvorgänge pro Tag. Jede Buchung kostet mit Easter-Egg-Zählern etwa vier Schreib- und zwei Lesevorgänge; Mitglieder lesen nur ihre eigenen Buchungen. Für 20–30 Handys und bis zu einigen tausend Getränken am Tag reicht das deutlich.
- **Anmeldung:** bis zu 100 neue Konten pro Stunde und Internetanschluss. Das genügt auch, wenn sich alle gleichzeitig im selben WLAN registrieren.
- **E-Mails:** 150 „Passwort vergessen“-Mails pro Tag.

## Update der App

Nach Änderungen zwei Zahlen gleich hochzählen (z. B. von 13 auf 14):
- in `index.html`: `const APP_VERSION = '13';`
- in `sw.js`: `const VERSION = 'strichliste-v13';`

Dann holen sich alle Handys beim nächsten Öffnen mit Netz die neue Version. Ganz unten auf der Seite steht „Version 13“. Daran siehst du, ob das Update auf einem Handy angekommen ist. Falls nicht: die Seite schließen und neu öffnen, beim zweiten Öffnen ist die neue Version aktiv.

## Daten aus der Offline-Version übernehmen

1. In der alten Version unter **Admin → Daten → Backup speichern** eine JSON-Datei erzeugen.
2. In der neuen Version unter **Admin → Daten → Backup importieren** diese Datei auswählen.

Die alten Buchungen haben keinen Personennamen.
