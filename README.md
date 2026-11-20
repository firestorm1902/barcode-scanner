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
| `manifest.webmanifest` | macht die Seite als App installierbar (Name, Farben, Symbole) |
| `icons/` | App-Symbole (Ordner mit allen Dateien hochladen) |
| `firestore.rules` | Sicherheitsregeln – werden in Firebase eingefügt, nicht auf GitHub gebraucht |

## Als App installieren (Android und iPhone)

Die Strichliste ist eine installierbare Web-App (PWA): Nach dem Installieren hat sie ein Wespen-Symbol auf dem Startbildschirm, startet im Vollbild ohne Adressleiste und funktioniert offline. Ein App-Store ist nicht nötig.

- **Android (Chrome):** In der App erscheint „Als App installieren“ → **Installieren**. Oder Menü **⋮** → „App installieren“. Samsung Internet: Menü ☰ → „Seite hinzufügen zu“ → „Startbildschirm“.
- **iPhone/iPad:** In **Safari** öffnen → **Teilen** → **„Zum Home-Bildschirm“** → „Hinzufügen“. Die App zeigt dafür eine Anleitung („So geht’s“, auch in der Hilfe unter **?**).
- Wurde der Link aus WhatsApp geöffnet, zuerst „Im Browser öffnen“ wählen – aus dem WhatsApp-Browser heraus lässt sich nicht installieren.
- **iPhone:** Die installierte App hat einen eigenen Speicher. Man muss sich dort **einmal neu anmelden**. Am besten installieren, bevor man offline bucht.
- **Updates:** Die installierte App prüft beim Öffnen und alle 30 Minuten auf eine neue Version. Ist eine da, erscheint unten „Neue Version da – Neu laden“.
- Lange auf das App-Symbol drücken (Android) bietet die Verknüpfung **„Getränk buchen“**, die direkt die Kamera startet.

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
3. Auf einem zweiten Handy ein Konto registrieren. Es wartet zuerst auf Freischaltung: im Admin oben unter **Neue Anmeldungen** auf „Freischalten“ tippen. Danach scannen. Die Buchung erscheint beim Admin unter „Abgleich“ und „Nach Person“.

## Für die Mitglieder

- Seite öffnen, **Registrieren** wählen und Vor- und Nachname, E-Mail und Passwort eingeben. Dafür braucht man einmal Internet.
- Danach steht „Fast geschafft – dein Konto wartet auf Freischaltung“. Sobald ein Admin freischaltet, geht die App von selbst weiter.
- Am besten zum Home-Bildschirm hinzufügen:
  - iPhone: Safari → Teilen → „Zum Home-Bildschirm“
  - Android: Chrome → Menü → „Zum Startbildschirm hinzufügen“
- Man bleibt angemeldet, auch offline. Neues Handy oder Browserdaten gelöscht? Einfach mit derselben E-Mail **anmelden**, Name und Buchungen sind wieder da.
- Nach jeder Buchung steht die Meldung mit „Rückgängig“ **10 Sekunden** lang da (Countdown), dann verschwindet sie.
- Unten rechts erscheint beim Herunterscrollen ein gelber Knopf **nach oben**.
- **Passwort ändern:** angemeldet auf „Passwort ändern“ neben „Abmelden“ tippen.
- **Passwort vergessen?** E-Mail eintragen und auf „Passwort vergessen?“ tippen. Es kommt eine Mail von Firebase zum Zurücksetzen.
- Oben neben „Strichliste“ steht der Netzstatus. „offline · 3 warten“ heißt: 3 Buchungen sind gespeichert und werden gesendet, sobald Netz da ist. In dieser Zeit nicht abmelden; die App warnt davor.

- **Ich fahre heute:** In der Scan-Ansicht auf das Auto tippen. Dann fragt die App bei jedem alkoholischen Getränk nach („Du fährst heute – trotzdem buchen?“). Admins sehen in der Tagesübersicht, wer heute fährt. Gilt nur für den einen Tag.
- **Easter Eggs für sich einstellen:** Wer Easter Eggs sehen darf, hat neben „Ich fahre heute“ den Knopf „Easter Eggs: an“. Dort wählt man *An*, *An, ohne Ton* (nur dieses Handy) oder *Aus* (für das Konto auf allen Handys: keine Popups, kein Verlauf, keine Sammelalbum-Meldungen; gezählt wird trotzdem).
- **Nachtmodus:** Oben rechts zwischen *Auto*, *Hell* und *Nacht* umschalten. *Auto* wird dunkel, wenn das Handy dunkel eingestellt ist oder zwischen 19 und 7 Uhr.

## Mehr: Bestenliste, Album, Mein Konto, Rückblick

Unter der Liste „Deine Getränke heute“ gibt es bis zu vier Reiter. Welche jemand sieht, legt der Super-Admin unter Einstellungen fest (Standard: nur „Mein Konto“).

| Reiter | Inhalt |
|---|---|
| **Bestenliste** | heute oder gesamt, Personen und Gruppen. Wonach gewertet wird, legt der Super-Admin fest (Standard: alkoholfreie Getränke). |
| **Sammelalbum** | alle Easter Eggs als Abzeichen. Antippen zeigt, wie man es bekommt, und bei gesammelten wie oft und wann zuletzt. Minderjährige sehen nur die alkoholfreien. |
| **Mein Konto** | eigene Getränke und Kosten der Saison (11.11. bis Aschermittwoch) pro Tag und Getränk, als Excel herunterladbar. |
| **Rückblick** | die eigene Saison wie ein Jahresrückblick: Getränke, Tage, Lieblingsgetränk, stärkster Tag, Easter Eggs, Platz der Gruppe und ein Fasnets-Titel. **Als Bild teilen** erzeugt ein Bild für WhatsApp oder Instagram. Ab Aschermittwoch erscheint oben ein Hinweis. |

## Einstellungen (Super-Admin)

Unter Admin → **Einstellungen**:
- **Kontakt:** Name, WhatsApp-Nummer (oder Link) und Telefon. Erscheint überall, wo man sich melden soll: „Konto wartet auf Freischaltung durch …“, „gesperrt“, „keine Rechte“ und in der Hilfe – mit Knöpfen „WhatsApp an …“ und „Anrufen“.
- **Wer sieht was?** Für Easter Eggs, Bestenliste, Sammelalbum und Saison-Rückblick jeweils: *niemand (nur Super-Admin)*, *Admins und Vorstand*, *alle Mitglieder* oder *ausgewählte Personen*. **Standard: ausgeblendet.** Gezählt wird trotzdem, beim Einschalten sind die Daten also da. „Mein Konto“ sehen immer alle.
- **Bestenliste – Regeln:** wonach Personen gewertet werden (alkoholfreie Getränke, alle Getränke oder Bier), Überschrift, Anzahl der Plätze, Gruppen-Wertung an/aus und wonach die Gruppen sortiert werden (Getränke gesamt, pro Kopf, alkoholfreie, Anteil alkoholfrei), Startansicht heute/gesamt.

Außerdem zeigt **Neu registriert** die letzten 25 Registrierungen mit Status (wartet, aktiv, abgelehnt, gesperrt); „Als gesehen markieren“ setzt die „neu“-Markierung zurück.

## Datenschutz

- Bei der **Registrierung** muss man den Datenschutzhinweis bestätigen und kann gleich seine **Gruppe** wählen.
- Der Hinweis steht unten auf jeder Seite („Datenschutz“) und in der Hilfe: wer verantwortlich ist, was gespeichert wird, wofür, wer was sieht (mit den aktuellen Einstellungen), wo die Daten liegen, wie lange und welche Rechte man hat.
- Bestehende Konten sehen einmal den Hinweis „Bitte lies kurz …“ mit „Gelesen und einverstanden“.
- **Konto löschen:** Mitglieder beantragen es im Datenschutzhinweis. Der Super-Admin sieht oben im Admin „Löschanträge“ und entfernt das Konto (auf Wunsch mit Buchungen); das Login löscht er in der Firebase-Konsole.
- Verein, Aufbewahrungsdauer und einen Zusatztext stellt der Super-Admin unter Einstellungen → Datenschutzhinweis ein. **Bitte den Text vom Vorstand bzw. Datenschutz-Verantwortlichen prüfen lassen** – er ist eine Vorlage, keine Rechtsberatung.
- Gruppen, Kontakt und Datenschutz-Angaben sind schon vor der Anmeldung lesbar (für die Registrierung).

## Schutz der Zähler und der Bestenliste

- Mitglieder dürfen einen Zähler pro Schreibvorgang nur um genau 1 ändern und in der Bestenliste nur ihren eigenen Eintrag und ihre eigene Gruppe (Firebase-Regeln).
- Admin-Handys gleichen alle 5 Minuten die Zähler des aktiven Tages mit den echten Buchungen ab und korrigieren sie. Größere Abweichungen (mehr als 2) landen in der Historie.
- Das Handy des Super-Admins berechnet einmal am Tag alle Zähler „seit Beginn“ neu.

## Einkaufsliste

Admin → **Einkaufsliste**: Einkauf für wie viele Ausflüge, Basis (letzte wie viele Ausflüge) und Puffer eintragen, unter „Lagerbestand“ die Flaschen im Lager (oder „Lager = Übrig vom …“ übernehmen), dann **Einkaufsliste berechnen**. Verbrauch pro Ausflug = gebucht, oder mitgenommen − übrig, falls das mehr ist. Ergebnis in Kästen (größtes Gebinde), teilbar per WhatsApp oder kopierbar.

## Hilfe

Der **?**-Knopf oben erklärt Mitgliedern, wofür die App ist und wie Buchen, Offline, Mein Konto, Fahrer-Modus und Profilbild funktionieren – ohne Easter Eggs und Extras.

## Freischaltung neuer Konten

- Neue Konten können erst buchen, wenn ein **Admin** sie freischaltet. Admins sehen im Admin-Bereich ganz oben **Neue Anmeldungen** mit „Freischalten“ und „Ablehnen“ und bekommen eine kurze Meldung, wenn sich jemand registriert.
- Beim Freischalten lassen sich gleich **Gruppe** und **unter 18** setzen.
- Konten, die es vor Version 15 schon gab, bleiben aktiv. Der Super-Admin schaltet sich selbst automatisch frei.

## Jugendschutz

- Unter Admin → Personen (oder beim Freischalten) **unter 18** ankreuzen.
- Minderjährige sehen nur alkoholfreie Getränke. Bier, Radler/Mischbier, Wein/Sekt und Schnaps lassen sich für sie nicht buchen – das prüfen auch die Firebase-Regeln, also nicht nur die App. Admins können für sie ebenfalls nichts Alkoholisches nachtragen.
- Sie bekommen keine Easter Eggs mit Alkoholbezug, auch nicht die gemeinsamen der Gruppe.
- Wichtig dafür ist die richtige **Sorte** jedes Getränks (Admin → Getränke): *Bier, Radler/Mischbier, Alkoholfrei, Wein/Sekt, Wasser, Softdrink, Schnaps, Sonstiges*. Fehlt sie, setzt die App sie beim nächsten Öffnen durch einen Admin automatisch anhand des Namens; bitte einmal kontrollieren.

## Gruppen (Narrenfiguren)

- Der **Super-Admin** legt unter Admin → **Gruppen** die Gruppen an (z. B. Wilde Wefzg, Hofgoischd), Admins ordnen Personen unter Admin → Personen zu.
- Die Gruppe steht neben dem Namen, in der Bestenliste und im Rückblick.
- Gruppen-Easter-Eggs: „30 Getränke für die Gruppe“ und „Gruppe bleibt vernünftig“ (10 alkoholfreie). Sie erscheinen auf allen Handys.

## Admin-Bereich öffnen

Es gibt keinen sichtbaren Admin-Knopf. **Fünfmal schnell hintereinander auf den Titel** oben tippen (innerhalb von zwei Sekunden), dann öffnet sich der Admin-Bereich. Zurück geht es über „Zurück zum Scannen“ oben rechts. Oben steht eine **Navigation** mit allen Bereichen (antippen springt hin). Jeder Bereich lässt sich über seine Überschrift **ein- und ausklappen**, „Alle auf“ / „Alle zu“ schaltet alle; das Handy merkt sich die Auswahl. Das ist nur ein Sichtschutz – die eigentlichen Rechte prüfen die Firebase-Regeln. Wer kein Admin ist, sieht dort nur „Keine Admin-Rechte“.

## Excel-Auswertung (Admins)

Unter Admin → **Excel-Auswertung** einen Zeitraum wählen (oder „Gesamter Zeitraum“, „Dieser Monat“, „Gewählter Tag“) und **Excel erstellen** tippen. Die Datei enthält:

| Blatt | Inhalt |
|---|---|
| Übersicht | Zeitraum, Anzahl Tage und Personen, Getränke, Wert, Fehlmengen, Liste der Tage |
| Pro Person | jedes Mitglied mit Anzahl je Getränk, Getränke gesamt und **Kosten**, darunter die Summen |
| Person pro Tag | wer an welchem Tag was getrunken hat, mit Preis und Summe |
| Abgleich pro Tag | mitgenommen, gebucht, Soll-Rest, gezählt, Differenz und Werte |
| Buchungen | jede einzelne Buchung mit Datum, Uhrzeit, Person, Getränk und Art |
| Getränke | Stammdaten mit Sorte, Preis, Gebinde und Barcodes |

Summen und Kosten stehen als Formeln drin, Kopfzeilen sind fixiert und mit Filter versehen. Die Kosten werden mit den **aktuellen** Preisen berechnet. Die Datei entsteht direkt im Browser (ohne zusätzliche Bibliothek); dafür wird nur Internet für das Laden der Buchungen gebraucht.

## Historie (Super-Admin)

Unter Admin → **Historie** steht jede Änderung: Mitglieder (Registrierung, Name, Rolle, Sperre, Entfernen, Profilbild, Passwort), Buchungen (Rückgängig, Storno, Nachtrag), Einladen und Zählen, Getränke, Tage, Easter Eggs und System (Zähler, Import, Zurücksetzen). Datum wählen, die Bereiche lassen sich aufklappen. Mit „auch normale Scans“ erscheinen zusätzlich alle Buchungen des Datums. Die Historie kann niemand ändern oder löschen – auch der Super-Admin nicht, und auch „Statistiken zurücksetzen“ lässt sie stehen.

## Buchungen einer Person korrigieren

Unter Admin → **Buchung nachtragen** die Person wählen: Darunter stehen ihre Buchungen des Tages, pro Getränk mit „−1 stornieren“ und aufklappbar einzeln. Nachtragen und Stornieren passen die Easter-Egg-Zähler automatisch an.

## Statistiken zurücksetzen (Super-Admin)

Admin → Daten → **Statistiken zurücksetzen**, dann das eigene Passwort eingeben. Gelöscht werden alle Easter-Egg-Zähler (Gruppe und Personen, heute und seit Beginn) und alle Easter-Egg-Verläufe; danach können alle Easter Eggs neu auslösen. Mit gelöscht werden Bestenliste, Sammelalben und Fahrer-Einträge. Optional lassen sich zusätzlich **alle Buchungen und Tage** löschen. Getränke, Personen, Easter-Egg-Regeln und die Historie bleiben erhalten.

## Rollen

Rollen vergibt der Super-Admin unter Admin → Personen → Rolle.

| | Mitglied | Vorstand | Getränke-Admin | Super-Admin |
|---|:-:|:-:|:-:|:-:|
| Getränke für sich buchen, eigene Buchung zurücknehmen, Passwort ändern | ✓ | ✓ | ✓ | ✓ |
| Tag, Abgleich, Nach Person, Buchungen, Getränke, Personen **ansehen** | | ✓ | ✓ | ✓ |
| Excel-Auswertung erstellen | | ✓ | ✓ | ✓ |
| Tage, Getränke, Einladen, Zählen, Abgleich **ändern**, CSV, Backup speichern | | | ✓ | ✓ |
| Buchungen stornieren und nachtragen, Namen korrigieren | | | ✓ | ✓ |
| Neue Konten freischalten, unter 18 markieren, Gruppe zuordnen | | | ✓ | ✓ |
| Einstellungen: Kontakt, wer sieht was, Bestenliste-Regeln | | | | ✓ |
| Easter Eggs verwalten und an/aus, Zähler neu berechnen, Gruppen anlegen | | | | ✓ |
| Neu registrierte Mitglieder, Historie, Statistiken zurücksetzen | | | | ✓ |
| Tag löschen, Backup importieren | | | | ✓ |
| Rollen vergeben, Konten sperren, entfernen, Passwort-Mail | | | | ✓ |

- **Vorstand** sieht im Admin-Bereich (5× auf den Titel tippen) alles nur lesend, ohne Rollen der Personen. Einziger Knopf ist „Excel erstellen“.
- **Getränke-Admin** (früher „Admin“) kümmert sich um Tage, Getränke und Abgleich. Easter Eggs an/aus und „Zähler neu berechnen“ darf er nicht mehr.

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
   - **Rest zählen per Scan** oder in der Spalte **Übrig** direkt eintippen, wie viele Flaschen übrig sind.
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

Bei bestimmten Buchungen erscheint ein Vollbild mit Wespe, Animation, Ton und Vibration. Es bleibt stehen, bis man **Schließen** tippt. Mit **Teilen** lässt es sich verschicken (Bild, z. B. per WhatsApp). Wer zum ersten Mal ein Abzeichen bekommt, sieht danach ein eigenes **Sammelalbum-Popup** (helle Karte mit Medaille, „x von y Abzeichen“) – nur wenn das Sammelalbum für ihn sichtbar ist. Solange es offen ist, pausiert der 10-Sekunden-Countdown der Buchungsmeldung. Easter Eggs sieht nur, wer sie laut „Wer sieht was?“ sehen darf. Jedes Easter Egg kommt pro Person und Tag nur einmal, auch nach „Rückgängig“.

**Nur auf dem eigenen Handy**
- 1., 3., 5., 7., 8., 9. und 10. Bier am Tag, danach bei jedem weiteren Bier (mit zunehmendem Augenzwinkern Richtung Heimfahrt)
- erstes Wasser nach mindestens 3 Bier
- Schnapszahl bei den eigenen Getränken (11, 22, 33 …): heute und seit Beginn
- Scan um 11:11 Uhr, um Mitternacht und am 11.11.
- für jede Sorte etwas: erstes und fünftes/zehntes **Wasser**, erster und fünfter **Softdrink**, erstes und drittes **Alkoholfreies**, **Radler**, **Wein/Sekt** und **Schnaps**, das erste **sonstige** Getränk, „Schlau!“ (Wasser nach mindestens 2 Schnäpsen)
- 50 und 100 Getränke seit Beginn

**Auf allen Handys** („Max Meier hat … ausgelöst“)
- Anstich: erster Scan des Tages
- 10., 20. und 30. Bier der ganzen Gruppe am Tag
- 10 Wasser, 10 Softdrinks und 11 Schnäpse der ganzen Gruppe am Tag
- alle 100 Getränke seit Beginn
- Schnapszahl bei allen Getränken der Gruppe: heute und seit Beginn

**Animationen:** Konfetti, Sprudelblasen, Getränke-Regen (passendes Emoji zur Sorte), Feuerwerk, Wespenschwarm, „Dreh durch“ (die Karte wirbelt herein), Luftballons und Sternenregen – jeweils mit eigener Melodie und eigenem Vibrationsmuster. Standard ist „automatisch“: Die App wählt abwechselnd eine Animation, die zum Getränk passt. Im Editor lässt sich pro Easter Egg eine feste Animation oder „Zufall“ einstellen.

Hinweise:
- **Was als Bier zählt**, legt die **Sorte** des Getränks fest (Admin → Getränke). Ohne Angabe rät die App aus dem Namen. Radler und Alkoholfreies zählen nicht als Bier.
- Die **Gruppen-Easter-Eggs** brauchen Netz. Offline wird weiter mitgezählt, aber ohne Meldung.
- Nach Stornos, Nachträgen oder einem Import unter **Admin → Daten → Zähler neu berechnen** tippen, damit die Zahlen wieder stimmen.
- **Easter Eggs: an/aus** schaltet alles für alle Handys ab.
- **Verlauf für alle:** In der Scan-Ansicht steht unter „Deine Getränke heute“ der Abschnitt „Easter Eggs heute“ mit zwei Reitern: *Für alle* (alle gemeinsamen Easter Eggs des Tages mit Uhrzeit und Auslöser) und *Deine* (die eigenen). Die eigenen sind in der Datenbank gespeichert und bleiben auch bei einem Handywechsel erhalten.
- **Vorschau für Admins:** Im Admin-Bereich zeigt „Anstehende Easter Eggs“ für den aktiven Tag, was als Nächstes kommt – für die Gruppe (Anstich, nächstes Gruppen-Bier, nächste Schnapszahl, 11:11 und 11.11.) und pro Person, sortiert danach, wer am nächsten dran ist.
- **Verwalten (Super-Admin):** Unter Admin → **Easter Eggs verwalten** lässt sich jedes Easter Egg an- und ausschalten, ändern, kopieren, löschen und als Vorschau anzeigen; „Neues Easter Egg“ legt eins an, „Standard-Easter-Eggs zurückholen“ setzt nur die mitgelieferten zurück und holt gelöschte wieder – **eigene Easter Eggs bleiben erhalten**. Einstellbar sind:
  - **Für:** jede Person für sich oder alle gemeinsam
  - **Wann:** genau bei einer Anzahl, bei jeder n-ten, ab einer Anzahl jedes Mal, bei Schnapszahl, „erstes … nach mindestens n …“, zu einer Uhrzeit, an einem Datum
  - **Getränk** (Sorte oder alle) und **Zeitraum** (heute oder seit Beginn)
  - **Animation:** automatisch, fest oder Zufall
  - **Texte:** eine Zeile pro Variante im Format `Titel | Text`; mehrere Zeilen wechseln sich ab. `{n}` wird durch die Zahl ersetzt, `{name}` durch die Person.
- Die mitgelieferten Easter Eggs stehen als `DEFAULT_RULES` in `index.html`. Sobald der Super-Admin etwas ändert, wird die Liste in der Datenbank gespeichert und gilt für alle Handys. Neue mitgelieferte Easter Eggs aus späteren Updates kommen automatisch dazu – außer solche, die der Super-Admin gelöscht hat.
  - **Animation:** pro Easter Egg wählbar (siehe oben).

## Kosten und Grenzen (kostenloser Spark-Tarif)

- **Datenbank:** 50.000 Lesevorgänge und 20.000 Schreibvorgänge pro Tag. Jede Buchung kostet mit Easter-Egg-Zählern etwa vier Schreib- und zwei Lesevorgänge; Mitglieder lesen nur ihre eigenen Buchungen. Für 20–30 Handys und bis zu einigen tausend Getränken am Tag reicht das deutlich.
- **Anmeldung:** bis zu 100 neue Konten pro Stunde und Internetanschluss. Das genügt auch, wenn sich alle gleichzeitig im selben WLAN registrieren.
- **E-Mails:** 150 „Passwort vergessen“-Mails pro Tag.

## Update der App

Aktuell ist **Version 24**. Nach Änderungen zwei Zahlen gleich hochzählen (z. B. von 24 auf 25):
- in `index.html`: `const APP_VERSION = '24';`
- in `sw.js`: `const VERSION = 'strichliste-v24';`

Version 24 bringt neue Regeln (Easter Eggs pro Person aus/an): `firestore.rules` neu in die Firebase-Konsole einfügen und veröffentlichen.

Dann holen sich alle Handys beim nächsten Öffnen mit Netz die neue Version. Ganz unten auf der Seite steht „Version 24“. Daran siehst du, ob das Update auf einem Handy angekommen ist. Falls nicht: die Seite schließen und neu öffnen, beim zweiten Öffnen ist die neue Version aktiv.

## Daten aus der Offline-Version übernehmen

1. In der alten Version unter **Admin → Daten → Backup speichern** eine JSON-Datei erzeugen.
2. In der neuen Version unter **Admin → Daten → Backup importieren** diese Datei auswählen (nur der Super-Admin).

Die alten Buchungen haben keinen Personennamen.
