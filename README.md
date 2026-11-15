# Colombia MBTiles Generator

GitHub Action that builds an OpenMapTiles-compatible `colombia.mbtiles` every Monday with [Planetiler](https://github.com/onthegomap/planetiler).

The latest build is always available at a fixed URL:

```
https://github.com/ColombiaTransit/colombia-mbtiles/releases/download/planetiler-latest/colombia.mbtiles
```

with a matching `colombia.mbtiles.sha256` next to it. It is served by [tileserver-docker](https://github.com/ColombiaTransit/tileserver-docker).

### Coverage
- Covers Colombia

## Licence of outputs

Outputs include data derived from the following sources:

| Data                       | License                                                                             | Source                                   |
|----------------------------|-------------------------------------------------------------------------------------|------------------------------------------|
| OpenStreetMap data         | [ODbL-1.0](https://opendatacommons.org/licenses/odbl/)                                  | OpenStreetMap contributors, Geofabrik.de |

