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
  --file 'https://download.geofabrik.de/europe/switzerland-latest.osm.pbf'
```

## check data and style

```bash
baremaps edit \
  --database 'jdbc:postgresql://localhost:5432/baremaps?user=baremaps&password=baremaps' \
  --tileset 'tileset.json' \
  --style 'style.json'
```

## create a custom dataset

```bash

```

## import the custom dataset

```bash

```

## prepare it

```bash

```
