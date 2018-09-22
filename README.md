# WIP Code für diese Seite

Der Code aus diesem Repository ist zu sehen unter https://tordans.github.io/fahrradkilometer.berlin/Autobahn/index.html

Es dauert eine Weile, bis die Daten im Hintergrund geladen wurden!

Die Seite basiert auf https://github.com/dktue/autobahnkilometer.ch

# Daten vom OpenStreetMap Hackweekend Sept 2018

## Daten von Lars aus QGIS

- Tabelle https://twitter.com/mapwebbing/status/1041288158153977856
- GeoJSON https://t.co/QlsZrgatKC
- Bild https://twitter.com/mapwebbing/status/1041296195447140352

## http://overpass-turbo.eu/ Query

- Im Output (Reiter) sieht man ganz am Ende die km-Zahlen

- Namen für das Attribut `name`

  > Charlottenburg-Wilmersdorf
    Friedrichshain-Kreuzberg
    Lichtenberg-Hohenschönhausen
    Marzahn-Hellersdorf
    Mitte
    Neukölln
    Pankow
    Reinickendorf
    Spandau
    Steglitz-Zehlendorf
    Tempelhof-Schöneberg
    Treptow-Köpenick

- Known issues
  - Enthält keine Wege in Parks wie Tempelhofer Feld. Auf der [OpenCycleMap](http://www.opencyclemap.org/?zoom=15&lat=52.47399&lon=13.40763&layers=B0000) werden diese mit ausgegeben
  - Die Abfrage zählt die Strecke aller Wege, die Teil der Area sind. Auch die Teile des Weges, die eigentlich außerhalb der Area sind. Das geht scheinbar mit Overpass nicht anders. Beispiel Neukölln aus der Overpass-Abfrage 126.695 km. Und mit "Clip" aus QGIS 116.779 km. Also 9.916 km zuviel bei Overpass.
  - Das Fahrrad-Tagging von OSM hat bestimmt noch Fallstricke, die hier durchdacht werden müssten https://wiki.openstreetmap.org/wiki/DE:Key:cycleway
  - Radstreifen links / rechts vom Weg wird nicht beachtet bisher.

```
( area[name="Neukölln"][admin_level=9]; )->.searchArea;
(
  way[highway=cycleway](area.searchArea);
  way[highway=path][bicycle=designated](area.searchArea);
  way[highway][cycleway]["cycleway"!~"no|opposite"](area.searchArea);
  way[highway]["cycleway:left"!=no]["cycleway:left"](area.searchArea);
  way[highway]["cycleway:right"!=no]["cycleway:right"](area.searchArea);
);
out geom;
make stats length=sum(length()),section_lengths=set(length());
out;
```
