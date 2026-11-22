# Projektkontext für Claude Code

Antworte und kommentiere auf **Deutsch**. Die Oberfläche der Seite ist deutsch.

## Was das ist
Wanderwege-Filter: eine Webseite, auf der man markierte Wanderwege auf einer Karte sucht und filtert, nach Schwierigkeit, Länge, Höhenmetern, Rundweg und Kategorie. Daten live von Waymarked Trails (OpenStreetMap).
Live: https://spundekees.github.io/wanderwege/ (GitHub Pages, Branch `main`, Ordner `/`).

## Aufbau
- **Alles steckt in `index.html`** (HTML + CSS + JS). Kein Build, keine Abhängigkeiten außer Leaflet 1.9.4 von unpkg.
- `README.md` beschreibt die Seite für Nutzer. Bei neuen Funktionen bitte mit anpassen.
- Die Seite muss am Computer **und auf dem iPhone** (Safari) gut funktionieren. Unter 800 px Breite gilt das Handy-Layout: Karte oben (sticky), Filter einklappbar, Details als Bottom-Sheet.

## Externe Dienste (alle ohne API-Schlüssel, CORS erlaubt)
- **Waymarked Trails API** `https://hiking.waymarkedtrails.org/api/v1`
  - `list/by_area?bbox=x0,y0,x1,y1&limit=100`: bbox in **EPSG:3857 (Web Mercator)**, max. 100 Treffer → der Code viertelt den Ausschnitt rekursiv (`listByArea`)
  - `list/search?query=…&limit=…`: eigenwillig, liefert je nach `limit` unterschiedlich viele Treffer, keine 2. Seite → `searchByName` fragt mehrere Limits ab und führt zusammen
  - `details/relation/{id}`: Tags, Länge, Geometrie (`route.main[…].ways[].geometry`, verschachtelt, siehe `collectLines`)
  - `details/relation/{id}/way-elevation?simplify=21`: Höhen pro Weg-Abschnitt → Auf-/Abstieg (`ascentFromElevation`, Abschnitte nach Geometrie ausrichten)
  - `symbols/id/{symbol_id}.svg`: Wegsymbol
  - Es gibt **keinen** GPX-Endpunkt. GPX wird im Browser erzeugt (`buildGPX`).
  - Die Wegliste enthält vorab nur Name/Kategorie/Symbol. Länge, Höhenmeter, Rundweg und Schwierigkeit erst nach `details`. Deshalb Vorfilter nur nach Kategorie und Zwischenspeicher (`preMatches`), max. 50 Wege pro Ladevorgang (`MAX_LOAD`, Rest über „weitere laden“).
- **Kartenkacheln:** OpenTopoMap (Standard, `maxNativeZoom:17`), Esri World Topo/Street, Overlay `tile.waymarkedtrails.org/hiking`.
  ⚠️ `tile.openstreetmap.org` blockiert lokal geöffnete Dateien (403), CARTO verlangt inzwischen einen API-Key. Beide nicht verwenden.
- **Ortssuche:** Photon (`photon.komoot.io`). Nominatim nicht verwenden.

## Fachliche Regeln
- Schwierigkeit: SAC-Skala der Abschnitte (T1 leicht, T2 mittel, ab T3 schwer), sonst `km + Hm/100` (<12 leicht, <22 mittel, sonst schwer).
- Rundweg: Tag `roundtrip`, sonst Start≈Ziel < 300 m (mit * markiert).
- Gehzeit nach DIN 33466 (4 km/h, 300 Hm/h ↑, 500 Hm/h ↓).
- Ortssuche zoomt auf die größte Stufe mit ≤ 50 Wegen (`zoomForMaxRoutes`) und lädt automatisch.
- Karte zeigt standardmäßig alle gefilterten Wege in Pink (`#ff00a8`), Namen ab Zoom 12, und passt sich bei Filteränderung ohne Herumspringen an.
- Schieberegler (Doppelregler) bekommen ihren Bereich von den geladenen Wegen. Länge auf 0,1 km genau, Höhenmeter auf 1 m. Regler am Rand = keine Grenze.
- Zwischenspeicher der Wegdetails in `localStorage` (`wmt-filter-cache-v2`, ohne Geometrie). Bei neuen Feldern Schlüssel hochzählen.

## Offene Punkte
- **OsmAnd auf dem iPhone: gelöst.** Test mit `gpx-test.html` (Chrome auf iOS): Per Web Share (`navigator.share({files})`) erscheint OsmAnd bei **keinem** Dateityp (ohne, gpx+xml, octet-stream, xml, text/xml, text/plain) im Teilen-Menü. Bei einer **heruntergeladenen** Datei über „Öffnen in …“ erscheint OsmAnd. Deshalb auf iOS immer Download (`gpxShareFile` liefert dort `null`). Als Home-Bildschirm-App sind keine Downloads möglich → Hinweis, die Seite im Browser zu öffnen.
- Android/Chrome erlaubt das Teilen von `.gpx` per Web Share vermutlich nicht → Download als Rückfall.

## Arbeitsweise
- Nach Änderungen JavaScript prüfen, z. B. Skriptblock extrahieren und `node --check`.
- Veröffentlichen: committen und auf `main` pushen. GitHub Pages ist nach 1–2 Minuten aktualisiert. Bisher hat der Nutzer über GitHub Desktop committet.
