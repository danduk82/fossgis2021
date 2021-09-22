# Prepare the data

## import OSM data

### prepare OSM optimized indexes

```bash
baremaps execute \
 --database 'jdbc:postgresql://localhost:5432/baremaps?&user=baremaps&password=baremaps' \
 --file 'res://osm_create_extensions.sql' \
 --file 'res://osm_drop_tables.sql' \
 --file 'res://osm_create_tables.sql' \
 --file 'res://osm_create_gist_indexes.sql' \
 --file 'res://osm_create_gin_indexes.sql'
```

### import switzerland from geofabrik

(this takes ~30 minutes)

```bash
baremaps import \
  --database 'jdbc:postgresql://localhost:5432/baremaps?&user=baremaps&password=baremaps' \
  --file 'https://download.geofabrik.de/europe/switzerland-latest.osm.pbf' \
  --enable-http
```

```bash
baremaps import \
  --database 'jdbc:postgresql://localhost:5432/baremaps?&user=baremaps&password=baremaps' \
  --file 'https://download.geofabrik.de/europe/liechtenstein-latest.osm.pbf' \
  --enable-http
```

## check data and style

```bash
baremaps edit --log-level DEBUG \
  --database 'jdbc:postgresql://localhost:5432/baremaps?user=baremaps&password=baremaps' \
  --tileset osm/tileset-osmvecto.json \
  --style osm/style-osmvecto.json
```

## materialized views for OSM

the files are run in the input order

```bash
baremaps execute --database 'jdbc:postgresql://localhost:5432/baremaps?&user=baremaps&password=baremaps' --file 'queries/osm_create_views.sql' --file 'queries/osm_create_gist_indexes.sql'
```

## create a custom dataset

here, we create a postgis table for points, with only the attributes "name" and "importance". use the tool of your choice to create a few points

## import the custom dataset

to import the dataset, simply add a section that specifies and ID (should be unique), and the SQL statement used to retrive the data, like so:

```json
{
  "id": "custom",
  "queries": [
    {
      "minZoom": 0,
      "maxZoom": 20,
      "sql": "select id, hstore('importance', importance::text) || hstore('name', name::text), geom from custom.custom"
    }
  ]
}
```

for labels

```json
{
  "id": "customlabel",
  "type": "symbol",
  "source": "baremaps",
  "source-layer": "custom",
  "filter": ["all"],
  "layout": {
    "symbol-placement": "point",
    "symbol-spacing": 350,
    "symbol-z-order": "auto",
    "text-allow-overlap": false,
    "text-field": "{name}",
    "text-font": ["Frutiger Neue Italic"],
    "text-ignore-placement": false,
    "text-keep-upright": true,
    "text-letter-spacing": 0.05,
    "text-max-width": 1000,
    "text-pitch-alignment": "map",
    "text-rotation-alignment": "viewport",
    "text-size": 16,
    "visibility": "visible"
  },
  "paint": {
    "text-color": "rgba(20, 136, 205, 1)",
    "text-halo-blur": 1,
    "text-halo-color": "rgba(242, 242, 242, 0.7)",
    "text-halo-width": 1
  }
}
```

## (optional) additional preparation

it might be useful to add indexes, simplified geometries for other zoom levels, etc

## update the tileset JSON with custom bounds

for Lausanne:

```json
"bounds": [6.36, 46.070, 6.53, 46.53946],
```

## export the tiles

```bash
baremaps export --database 'jdbc:postgresql://localhost:5432/baremaps?user=baremaps&password=baremaps' --tileset presentation/osm/tileset-osmvecto.json --repository tiles/
```

copy the content of the directory `tiles/` to your server so that they can be accessed, e.g. at the following URL : https://your.domain.com/some/url/tiles/{z}/{x}/{y}.mvt

## copy and adapt both the style and tileset

in the style.json adapt the sources so that the URL points to your server

```bash
curl localhost:9000/style.json | jq '' > /tmp/style.json
```

```json
  "sources": {
    "baremaps": {
      "url": "http://localhost:9000/tiles.json",
      "type": "vector"
    }
  },
```

should become:

```json
  "sources": {
    "baremaps": {
      "url": "https://your.domain.com/some/url/tiles.json",
      "type": "vector"
    }
  },
```

Do the same in the tileset.json

```bash
curl localhost:9000/tiles.json | jq '' > /tmp/tiles.json
```

you should modify:

```json
  "tiles": [
    "http://localhost:9000/tiles/{z}/{x}/{y}.mvt"
  ],

```

to

```json
  "tiles": [
    "https://your.domain.com/some/url/tiles/{z}/{x}/{y}.mvt"
  ],

```

## setup of the server

then copy these files on your server accordingly.

It is important that when the browser retrieves the tiles, they are served with the correct headers. These are headers that work in most of the cases:

```
access-control-allow-origin: *
content-encoding: gzip
content-type: application/vnd.mapbox-vector-tile
```

Ensure that your server serves them with the correct content-type
