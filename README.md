# mystaffhq-external-data

Vendored third-party reference data, frozen on purpose and consumed **by seed and test only, never at runtime**.

Pinned by commit SHA from `MyStaffHQ/mystaffhq-api`. See **ADR-027** there for the reasoning, the rejected alternatives, and the constraints that are not reopened.

## What may live here

Four properties, all required:

1. Public and redistributable
2. Consumed by seed or test, never at runtime
3. Fetched credential-free, by SHA
4. Refreshed by a deliberate human act, never automatically

## Layout

```
geo/<source>/<file>.geojson   one directory per domain; geo/ is the only tenant today
geo/LICENSING.md              redistribution notices, per directory
manifest.json                 flat, at the root: sha256, upstream URL, conversion, drift risk
```

The name is deliberately wider than the first tenant. Renaming a repository later is cheap in git but breaks every pinned raw URL, so the wide name is chosen now. **The shape is not widened with it** - no generic multi-tenant fetcher is built for a second tenant that does not exist.

## Why the converted files, not the upstream originals

The converted files are what the seed actually eats. Freezing them is what makes a checksum mean something, and it takes `ogr2ogr` off the default fetch path entirely.

`ogr2ogr` output is a function of the local GDAL build - coordinate precision, RFC 7946 handling and field ordering have all changed across releases - so two developers on different GDAL versions produce different bytes from byte-identical input. The ONS source has no "original file" to store in any case: its artefact is a query response generated per request.

Provenance is preserved in `manifest.json` rather than by storing a second copy of everything.

## Why public

`fetch_geo_source_data` must stay credential-free. Every developer and every CI runner does an unauthenticated GET; the CI test job holds no credentials at all. A private repository or an S3 bucket would make seeding require a token or an AWS role. All four licences permit redistribution and there is nothing here worth hiding.

## Branch protection is load-bearing

The pin's guarantee rests on the SHA staying reachable. The default branch must have protection enabled with **force-push disabled**. A rewritten history silently breaks every pinned fetch.

## Refresh procedure

Refreshing is a deliberate act, never automatic.

1. In a `mystaffhq-api` checkout, run the upstream path:
   ```bash
   uv run ./manage.py fetch_geo_source_data --upstream
   ```
   This hits the original endpoints, handles the zips, runs `ogr2ogr`, and verifies download checksums for the three sources that have a stable artefact.
2. Copy the four converted files here, at the same relative paths.
3. Recompute checksums and update `manifest.json`:
   ```bash
   find geo -name '*.geojson' | sort | xargs sha256sum
   ogr2ogr --version
   ```
   Stamp the real GDAL version, the retrieval date, and - for INSEE - the upstream dataset's own last-update date, which Etalab v2.0 requires.
4. Commit and push to the default branch.
5. Update the pinned SHA in `fetch_geo_source_data` and open a PR against `mystaffhq-api`. **The seed's curated mappings are keyed to a specific vintage of all four datasets**, so a refresh is a change to review, not a chore to rubber-stamp.

### Why a refresh needs real review

The integration suite cannot catch redrawn geometry under unchanged codes: `union_children_into_parent` derives parents from children, so the whole tree moves coherently and the covering assertion still passes. The mirror is a pin first and a backup second.
