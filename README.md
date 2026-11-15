# Colombia MBTiles Generator

GitHub Action that builds an OpenMapTiles-compatible `colombia.mbtiles` every Monday with [Planetiler](https://github.com/onthegomap/planetiler).

The latest build is always available at a fixed URL:

```
https://github.com/ColombiaTransit/colombia-mbtiles/releases/download/planetiler-latest/colombia.mbtiles
```

with a matching `colombia.mbtiles.sha256` next to it. It is served by [tileserver-docker](https://github.com/ColombiaTransit/tileserver-docker).

### Coverage
- Covers Colombia, zoom 0–14 (OpenMapTiles schema; clients overzoom beyond 14)
- Names in English and Spanish (`--languages=en,es`)

## Release assets

The workflow publishes one rolling release, [`planetiler-latest`](https://github.com/ColombiaTransit/colombia-mbtiles/releases/tag/planetiler-latest), and replaces its assets on every build:

| Asset | Description |
|---|---|
| `colombia.mbtiles` | Vector tiles (~430 MB) |
| `colombia.mbtiles.sha256` | SHA-256 of the file; consumers compare this to decide whether to download |
| `build-log.txt` | Planetiler log of the build |

The release notes list the build time, the OSM data timestamp, the zoom range, size and tile count.

## Build pipeline

[`.github/workflows/planetiler-mbtiles.yml`](.github/workflows/planetiler-mbtiles.yml) runs every Monday at 05:25 UTC and can be started manually (*Actions → planetiler-mbtiles → Run workflow*).

1. **OSM extract:** one attempt at Geofabrik; on any failure (connection refused, 4xx/5xx) it moves straight on to the `download.openstreetmap.fr` mirror instead of retrying.
2. **Water polygons** (`water-polygons-split-3857.zip`, ~890 MB), because `osmdata.openstreetmap.de` is often unreachable:
   1. GitHub Actions cache of the current month → used as is;
   2. otherwise a fresh download from osmdata, which also updates the cache and the [`sources-mirror`](https://github.com/ColombiaTransit/colombia-mbtiles/releases/tag/sources-mirror) release;
   3. otherwise an older cache entry;
   4. otherwise the copy in the `sources-mirror` release.
3. **Planetiler** builds the MBTiles; sources already on disk are not downloaded again.
4. **Checks:** SQLite `PRAGMA quick_check` and a non-zero tile count; the release is not touched when a check fails.
5. **Publish** to `planetiler-latest` with `gh release` (the workflow needs `contents: write`).

## Consumers

- [tileserver-docker](https://github.com/ColombiaTransit/tileserver-docker) downloads `colombia.mbtiles` on container start when the `.sha256` differs from its local copy.

## Licence of outputs

Outputs include data derived from the following sources:

| Data                       | License                                                                             | Source                                   |
|----------------------------|-------------------------------------------------------------------------------------|------------------------------------------|
| OpenStreetMap data         | [ODbL-1.0](https://opendatacommons.org/licenses/odbl/)                                  | OpenStreetMap contributors, Geofabrik.de |

