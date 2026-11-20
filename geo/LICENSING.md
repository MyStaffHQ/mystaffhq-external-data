# Redistribution notices - `geo/`

`docs/attribution.md` and `src/geo/boundary_attribution.py` in `MyStaffHQ/mystaffhq-api` remain the source of truth for **product-facing attribution**. This file covers the second act that vendoring adds: **redistribution**.

The three national files here are **adaptations** in the licensing sense - the geometry is reprojected to EPSG:4326 and fields are dropped. Both OGL v3.0 and Etalab v2.0 permit adaptation on the same attribution terms.

| Dataset | Licence | Redistribution permitted | Obligation when redistributing |
|---|---|---|---|
| OpenStreetMap country outlines and lakes (via Overture Maps divisions and base water, release 2026-11-18.0) | ODbL 1.0 | Yes | Attribute, and keep the licence notice below with the data |
| OpenStreetMap land polygons (osmdata.openstreetmap.de, 2026-11-18) | ODbL 1.0 | Yes | Attribute, and keep the licence notice below with the data |
| ONS LAD December 2024 UK (BGC) | OGL v3.0 | Yes, expressly (copy, publish, distribute, adapt) | Reproduce the attribution statement below |
| US Census TIGER/Line 2024 states | Public domain | Yes | None. Keep the courtesy credit |
| INSEE French regions (IGN ADMIN-EXPRESS) | Etalab Licence Ouverte v2.0 | Yes | Name the source **and the date of the last update of the reused data** |

## Notice text, verbatim

**ONS / OGL v3.0**

> Contains OS data © Crown copyright and database right 2024; Office for National Statistics licensed under the Open Government Licence v3.0

**INSEE / IGN, Etalab Licence Ouverte v2.0**

> © IGN - INSEE, Licence Ouverte v2.0, données mises à jour le \<date\>

**Etalab v2.0 requires the update date, not merely the source.** The `\<date\>` placeholder above must be filled from the upstream dataset's own last-update date at each refresh, and stamped into `manifest.json` alongside it.

The same gap exists today on the product-facing side: neither `docs/attribution.md` nor `src/geo/boundary_attribution.py` carries the update date. That is a one-line fix in each, tracked on `MyStaffHQ/mystaffhq-api#689` because it touches `src/`.

**OpenStreetMap / ODbL 1.0** (`geo/overture/`, `geo/osm/`)

> © OpenStreetMap contributors. This data is made available under the Open Database License: http://opendatacommons.org/licenses/odbl/1.0/. Any rights in individual contents of the database are licensed under the Database Contents License: http://opendatacommons.org/licenses/dbcl/1.0/

These three files are extracts of the OpenStreetMap database (two of them through Overture Maps) and stay under ODbL. They are filtered and reformatted, not altered. **Never add a repaired or merged outline here**: publishing a derived database triggers ODbL share-alike.

## Why this file is per-directory

`geo/` is the only tenant today. A future dataset under its own directory may carry different terms, so the notices live beside the data they govern rather than at the repository root.
