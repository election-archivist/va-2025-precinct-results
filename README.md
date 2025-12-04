# Virginia 2025 Statewide Elections: Precinct GeoPackage

<p align="left">
  <img src="VA%202025%20Data%20Preview.png"
       alt="Virginia 2025 statewide precinct results"
       width="300">
</p>

## File and Layer

- **File:** `VA_precincts_2025_statewide_standardized.gpkg`  
- **Layer:** `precincts_2025`

## Download

The GeoPackage is published as a release asset:

https://github.com/election-archivist/va-2025-precinct-results/releases/download/v1.0.0/VA_precincts_2025_statewide_standardized.gpkg

## Overview

This GeoPackage combines Virginia's 2025 **statewide** election results  
(Governor, Lieutenant Governor, Attorney General) with a cleaned and  
standardized precinct geometry layer.

Each feature represents a **regular voting precinct** as of 2025 with  
joined vote totals and percentages for the three statewide contests.

---

## Identity Fields

The following fields form the standard geographic identity schema for
precincts and are intended for reuse across future states and years.

- **state**  
  Two-letter postal code. For this dataset always `"VA"`.

- **county_name**  
  County or independent city name in title case, including “County”  
  or “City” where applicable (for example `Appomattox County`,  
  `Richmond County`, `Richmond City`).

- **county_fips**  
  Five-character county FIPS code as text.  
  **Placeholder only in this version** and left `NULL`.  
  This can be filled from an external FIPS reference and reused  
  for all elections.

- **precinct_id**  
  Local numeric precinct identifier as used by the state in its  
  reporting (for example `101`, `502`, `6`). Stored as integer.

- **precinct_code**  
  Zero-padded text version of the precinct ID, currently padded to  
  four digits (for example `"0101"`, `"0502"`, `"0006"`).

- **precinct_name**  
  Short precinct name only, in uppercase, without the numeric prefix  
  (for example `CHINCOTEAGUE`, `CHAPEL`, `ETLAN`).

- **precinct_label**  
  Human-facing label in the form `"NNN – NAME"`, combining the  
  numeric ID (three digits) with the precinct name  
  (for example `101 – CHINCOTEAGUE`, `006 – ETLAN`).  
  Intended for direct use on maps.

- **precinct_geoid**  
  Precinct-level identifier. When the source shapefile provided a  
  GEOID, that value is preserved here. When it did not, a fallback  
  placeholder of the form `"51XXX####"` is used and should **not**  
  be treated as authoritative.

- **precinct_uid**  
  Machine-friendly unique ID for joins and cross-files. Pattern:

  `VA_[countyfips]_[precinct_code]`

  In this version `CountyFIPS` from the source shapefile is used  
  when available. When absent, a placeholder `"XXX"` is used.

---

## Election Result Fields

All election fields follow the naming grammar

`VA_[OFFICE][YY]_[CANDLAST]_[PARTY]_[MEASURE]`

and for totals:

`VA_[OFFICE][YY]_TOTAL_[MEASURE]`

Where:

- `STATE` is implicitly `VA` and included literally.  
- `OFFICE` is one of:
  - `GOV`  (Governor)
  - `LG`   (Lieutenant Governor)
  - `AG`   (Attorney General)
- `YY` is the last two digits of the election year: `25` for 2025.  
- `CANDLAST` is the uppercased candidate surname with no spaces.  
- `PARTY` is a single-letter party code:
  - `D` = Democratic
  - `R` = Republican  
- `MEASURE` is one of:
  - `VOTES`   = raw vote count
  - `PERCENT` = share of the office total, 0–100, rounded to two decimals

### Governor

- `VA_GOV25_SPANBERGER_D_VOTES`  
- `VA_GOV25_SPANBERGER_D_PERCENT`  
- `VA_GOV25_EARLESEARS_R_VOTES`  
- `VA_GOV25_EARLESEARS_R_PERCENT`  
- `VA_GOV25_TOTAL_VOTES`

### Lieutenant Governor

- `VA_LG25_HASHMI_D_VOTES`  
- `VA_LG25_HASHMI_D_PERCENT`  
- `VA_LG25_REID_R_VOTES`  
- `VA_LG25_REID_R_PERCENT`  
- `VA_LG25_TOTAL_VOTES`

### Attorney General

- `VA_AG25_JONES_D_VOTES`  
- `VA_AG25_JONES_D_PERCENT`  
- `VA_AG25_MIYARES_R_VOTES`  
- `VA_AG25_MIYARES_R_PERCENT`  
- `VA_AG25_TOTAL_VOTES`

---

## Computation Notes

- Vote totals come from the 2025 precinct-level results file  
  provided by the Virginia Department of Elections.  
- Write-in votes are included in each office’s `TOTAL_VOTES` field.  
  The candidate-specific `VOTES` fields contain the named candidates  
  only; unnamed write-ins remain aggregated in the total.  
- `PERCENT` fields are calculated as  
  `candidate_votes / office_total_votes * 100`  
  and rounded to two decimal places.

---

## Geometry Adjustments

Several targeted geometry and ID corrections align the precinct
shapefile with the 2025 reporting structure.

1. **Appomattox County – Agee and Oakville**  
   Agee precinct (`PrcnctFIPS = "000502"`) has been consolidated into  
   Oakville (`"000501"`). The two polygons are unioned into a single  
   MultiPolygon associated with Oakville.

2. **Giles County – Glen Lyn and Rich Creek**  
   Glen Lyn (`"000101"`) has been consolidated into Rich Creek  
   (`"000102"`). The geometries are unioned and associated with  
   Rich Creek.

3. **Madison County – Graves Mill, Wolftown, Etlan**  
   - Graves Mill (`"000004"`) and Wolftown (`"000006"`) are merged into  
     one precinct labelled `GRAVES MILL/WOLFTOWN` with FIPS `"000004"`.  
   - Etlan’s 2025 results are reported under precinct ID `006`.  
     To match this, Etlan’s geometry is moved into the `000006` slot,  
     labelled `ETLAN` with FIPS `"000006"`.  
     The former `ETLAN 000009` geometry is blanked.

4. **Fairfax County – Fairfax Court**  
   Precinct 700 `FAIRFAX COURT` still exists geometrically and has zero  
   registered voters in the 2025 statewide election.  
   The precinct is retained and all statewide result fields are set to  
   `0.0` rather than `NULL`.

---

## Unresolved or Special Precincts

Two precincts retain `NULL` statewide results in this dataset by design,
because the 2025 results file does not provide a clean mapping for them.

1. **Tazewell County – 107 “AMONATE”**  
   The Amonate polling place has closed and its voters have been
   reassigned, for example to a site on Adria Road.  
   The 2025 results file does not specify which new precinct line
   contains those votes.  
   The Amonate geometry is retained and all statewide election fields  
   remain `NULL`.

2. **Richmond County – 502 “PRECINCT 5-2”**  
   Historical metadata indicates a 5-2 precinct.  
   The 2025 statewide results list only precincts 1-1, 2-1, 3-1, 4-1, and 5-1.  
   There is no separate 5-2 line in the data.  
   The 5-2 geometry is retained with statewide election fields set to  
   `NULL`. It is highly likely that its voters are included in 5-1,  
   but this dataset avoids speculative assignment.

---

## Usage Notes

- Treat the identity fields  
  `state`, `county_name`, `precinct_id`, `precinct_code`,  
  `precinct_name`, `precinct_label`, `precinct_geoid`, `precinct_uid`  
  as the canonical way to refer to precincts in scripts, maps,  
  and documentation.  
- Treat all `VA_*` fields as specific to the 2025 statewide contests.  
  Future years and offices should follow the same naming grammar.  
- `county_fips` and placeholder GEOIDs can be filled or corrected from  
  an authoritative FIPS or precinct reference when this becomes a  
  formal open dataset.

---

## Version

This file represents the first standardized release of the Virginia 2025
statewide precinct dataset with a stable schema intended for reuse
across future election mapping projects.
