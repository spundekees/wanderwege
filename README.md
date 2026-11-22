# 🥾 Wanderwege-Filter

Eine Webseite, auf der man markierte Wanderwege auf einer Karte suchen und filtern kann: nach Schwierigkeit, Länge, Höhenmetern und danach, ob es ein Rundweg ist. Die Daten kommen live von [Waymarked Trails](https://hiking.waymarkedtrails.org) und damit aus OpenStreetMap.

**➡️ Zur Seite: https://spundekees.github.io/wanderwege/**

Die Seite läuft im Browser am Computer und auf dem Handy. Man braucht weder Installation noch Anmeldung.

---

## Funktionen

- **Gebiet wählen:** Nach der Ortssuche wird der Ort mit einem roten Kreis markiert. Die Karte zoomt automatisch so, dass höchstens 50 Wege in der Ansicht liegen, und lädt sie sofort. Alternativ kann man die Wege des aktuellen Kartenausschnitts laden oder direkt nach einem Wegnamen suchen. Auf dem Button steht, wie viele Wege in der aktuellen Ansicht liegen.
- **Mehrere Gebiete sammeln:** Mit „+ hinzufügen“ kommen die Wege weiterer Kartenausschnitte dazu, ohne dass die bisherigen verloren gehen.
- **Filter:**
  - Schwierigkeit (leicht, mittel, schwer, unbekannt)
  - Länge in km und Höhenmeter im Aufstieg, jeweils per Doppelschieberegler. Der Bereich passt sich automatisch an die geladenen Wege an, von der kürzesten bis zur längsten Route bzw. vom geringsten bis zum größten Anstieg. Ein Regler am Rand bedeutet „keine Grenze“.
  - Wegform (Rundweg oder Streckenwanderung)
  - Kategorie (lokal, regional, national, international)
- **Sortierung** nach Name, Länge, Höhenmetern oder Schwierigkeit.
- **Karte passt sich automatisch an:** Alle gefilterten Wege werden in Pink eingezeichnet, ab Ortsebene mit ihren Namen. Wer einen Filter ändert, sieht sofort nur noch die passenden Wege. Mit „Wege ausblenden“ lässt sich die Anzeige abschalten.
- **Steckbrief:** Ein Klick auf einen Weg oder seinen Namen öffnet ein Fenster mit:
  - Länge, Auf- und Abstieg, niedrigstem und höchstem Punkt
  - geschätzter Gehzeit
  - Untergrund (befestigt oder naturbelassen)
  - Markierung, Betreuer und Links
- **In OsmAnd öffnen:** Übergibt den Weg als GPX-Track über das Teilen-Menü des Handys an OsmAnd. Wo das Teilen nicht geht, wird die GPX-Datei heruntergeladen und lässt sich dann mit OsmAnd öffnen.
- **GPX-Download:** Die GPX-Datei wird direkt im Browser aus dem Wegverlauf erzeugt, zum Beispiel für Komoot, Outdooractive oder Garmin.
- **Kartenstile:** OpenTopoMap (Standard), Esri Topografisch und Esri Straßenkarte, darüber die Wanderweg-Markierungen von Waymarked Trails.
- **Handy-Ansicht:** Oben die Karte, darunter Suche und Ergebnisliste. Die Filter lassen sich einklappen. Die Details öffnen sich als Fenster von unten, das man über ✕, durch Tippen daneben oder mit der Zurück-Taste schließt.

## Bedienung in Kürze

1. Einen Ort eingeben und auf **„Gehe zu“** tippen. Die Wege in der Umgebung werden automatisch geladen. Beim ersten Mal dauert das ein wenig, weil für jeden Weg Details nachgeladen werden.
2. Für ein anderes Gebiet die Karte verschieben und **„Wege im Ausschnitt laden“** klicken.
3. Filter setzen. Liste und Karte aktualisieren sich sofort.
4. Einen Weg in der Liste oder auf der Karte anklicken, um ihn hervorzuheben und den Steckbrief zu sehen.

## Wie die Werte zustande kommen

| Wert | Herkunft |
|---|---|
| **Länge** | Erfasste Länge des Weges bei Waymarked Trails, die offizielle Länge, falls in OSM eingetragen. |
| **Höhenmeter** | Aus dem Höhenprofil von Waymarked Trails berechnet (Summe aller Anstiege und Abstiege entlang des Weges). Näherungswerte. |
| **Schwierigkeit** | Haben die Wegabschnitte in OSM eine SAC-Skala, wird der schwierigste Abschnitt genommen: T1 = leicht, T2 = mittel, ab T3 = schwer. Sonst wird gerechnet: km + Höhenmeter/100. Unter 12 ist leicht, unter 22 mittel, alles darüber schwer. |
| **Rundweg** | OSM-Tag `roundtrip=yes/no`. Fehlt er, gilt ein Weg als Rundweg, wenn Start und Ziel weniger als 300 m auseinanderliegen. Solche Fälle sind mit * markiert. |
| **Gehzeit** | Nach DIN 33466: 4 km/h in der Ebene, 300 Hm/h bergauf, 500 Hm/h bergab. |
| **Untergrund** | Anteil der Weglänge mit befestigtem Belag (Asphalt, Pflaster …) bzw. naturbelassenem Belag laut OSM-Tag `surface`. |

Die Angaben sind nur so gut wie die Daten in OpenStreetMap und dienen zur groben Orientierung. Für die Tourenplanung bitte immer auch Karte, Wetter und die eigene Kondition prüfen.

## Technik

- **Eine einzige Datei:** `index.html` mit HTML, CSS und JavaScript, ohne Build-Schritt und ohne Server-Code.
- **Bibliotheken und Dienste:**
  - Karte mit [Leaflet](https://leafletjs.com) 1.9.4, eingebunden über unpkg
  - Daten über die öffentliche API von Waymarked Trails (`hiking.waymarkedtrails.org/api/v1`)
  - Ortssuche über [Photon](https://photon.komoot.io) von Komoot
- **Zwischenspeicher:** Bereits geladene Wegdetails merkt sich der Browser im `localStorage`, damit es beim nächsten Mal schneller geht.
- **Hosting:** GitHub Pages aus dem Branch `main`, Ordner `/ (root)`.

### Lokal ausprobieren

Die Datei kann am Computer per Doppelklick im Browser geöffnet werden. Eine Internetverbindung ist nötig. Auf Handys funktionieren lokal gespeicherte HTML-Dateien meist nicht, dort bitte den Link oben verwenden.

### Änderungen veröffentlichen

1. `index.html` bearbeiten.
2. In GitHub Desktop committen („Commit to main“) und hochladen („Push origin“).
3. Nach ein bis zwei Minuten ist die neue Version unter dem Link oben online. Eventuell einmal neu laden (Cmd/Strg + Shift + R).

## Bekannte Einschränkungen

- Die Namenssuche von Waymarked Trails liefert nur einige Dutzend Treffer. Für viele Wege besser über den Kartenausschnitt laden.
- Pro Abfrage nennt die API höchstens 100 Wege. Große Ausschnitte werden deshalb automatisch in Teilstücke aufgeteilt, die Zahl auf dem Button zeigt dann „100+“.
- Bei sehr langen Fernwanderwegen kann das Laden der Details etwas dauern.

## Datenquellen und Lizenzen

- **Wanderwege, Karten- und Ortsdaten:** © [OpenStreetMap-Mitwirkende](https://www.openstreetmap.org/copyright), lizenziert unter der [ODbL](https://opendatacommons.org/licenses/odbl/)
- **Wegdaten, Höhenprofile und Wegmarkierungs-Kacheln:** [Waymarked Trails](https://waymarkedtrails.org)
- **Kartenstil OpenTopoMap:** © [OpenTopoMap](https://opentopomap.org) ([CC-BY-SA](https://creativecommons.org/licenses/by-sa/3.0/)), Höhendaten SRTM
- **Kartenstile Esri:** © Esri und Partner
- **Ortssuche:** [Photon](https://photon.komoot.io) von Komoot
- **Kartenbibliothek:** [Leaflet](https://leafletjs.com) (BSD-2-Clause)
