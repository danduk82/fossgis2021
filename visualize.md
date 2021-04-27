# visualize the data in a browser using geo.admin.ch

An easy way to test your style, is to use the beta viewer of swisstopo with the parameter `bgLayer_styleUrl`
pointing to your local style (or any that you want):

```
https://test.map.geo.admin.ch/?bgLayer_styleUrl=http://localhost:9000/style.json&lon=6.43058&lat=46.50897&zoom=8.87


https://test.map.geo.admin.ch/?bgLayer_styleUrl=https://fossgis-mvt-test.s3.eu-central-1.amazonaws.com/tiles/style.json&lon=6.43058&lat=46.50897&zoom=8.87


```

use fonts from [openmaptiles](https://github.com/openmaptiles/fonts)

```json
    "glyphs": [
        "http://fonts.openmaptiles.org/{fontstack}/{range}.pbf"
    ],
```

```bash
aws s3 cp \
       s3://fossgis-mvt-test/ \
       s3://fossgis-mvt-test/ \
       --exclude '*' \
       --include '*.pbf' \
       --no-guess-mime-type \
       --content-type="application/vnd.mapbox-vector-tile" \
       --content-encoding=gzip \
       --metadata-directive="REPLACE" \
       --recursive
```

S3 URL:

```
https://fossgis-mvt-test.s3.eu-central-1.amazonaws.com/
```
