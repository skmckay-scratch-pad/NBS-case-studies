# NBS Case Studies: Plan

**Status:** Draft for review. Nothing below has been run.

This plan was written without opening either website, so the number and type of sources are unknown. Step 1 will show whether anything here needs to change.

## Goal

Compile every nature-based solutions (NBS) case study from the documents and resources listed on the [NBS Manual of Practice case study resources page](https://skmckay.github.io/NBS.Manual.Of.Practice/case-study-resources.html). Store the result as a table in this repo, and build an R Shiny web app that shows it on a map, using the [DamHealth viewer](https://crumplabsandbox.github.io/DamHealth/) as the example.

## Data table

The database is `data/case_studies.csv`, with one row per project.

| Column | Contents |
|---|---|
| `id` | Stable identifier used by the web app |
| `project_name` | Name of the project |
| `description` | One-sentence description, written in our own words |
| `locations` | Place names, separated by `;` when there is more than one site |
| `latitude` | Decimal degrees, separated by `;` in the same order as `locations` |
| `longitude` | Decimal degrees, separated by `;` in the same order as `locations` |
| `location_precision` | `exact`, `city`, `county`, `state`, `region` or `country`, per location |
| `source_resources` | Every resource on the site where this case study appears |
| `original_documentation_links` | Links to the documentation where the resource originally found the case study |

Rules:

- If a project appears in more than one resource, it keeps one row and lists every source.
- If a resource gives no original documentation, the link field stays blank. Links are never guessed.
- A missing location stays blank, and the project is listed in the app's table but not on the map.

## Steps

### 1. Inventory the sources

- Read the resources page and record every linked document, database and site in `data/sources.csv`, with its name, URL, type and processing status.
- Report anything that can't be reached, such as paywalled, broken or sign-in-only sources.

### 2. Extract the case studies

- Go through each source in full and pull out each case study, along with where it appears in the source (page or section).
- Use a repeatable script for structured sources, such as searchable databases, tables and web listings. Read long-form reports directly.
- Downloaded files stay in a git-ignored folder, so third-party documents are not republished from this repo.
- Respect each site's access rules and rate limits. If a large database can't be collected this way, report that rather than including partial results silently.

### 3. Remove duplicates

- Match projects across sources by normalized name and location.
- Merge clear matches into one row. List uncertain matches in `data/review_notes.md` for a person to decide, and leave them unmerged.

### 4. Add coordinates

- Use coordinates from the source when it gives them.
- Otherwise geocode the place name with a public geocoder, within its usage limits, and record the precision honestly.
- Check that each point falls in the stated country or state.

### 5. Check quality

- Confirm that required fields are filled, identifiers are unique and coordinates are valid.
- Test every link in the table and list the broken ones in `data/review_notes.md`.
- Spot-check a random sample of rows from each source against the original text.
- Report the per-source counts and the number of rows without coordinates.

### 6. Build the web app

- Study the DamHealth viewer first, and follow its general layout and interaction patterns while building this app's own design.
- Write the app in R Shiny, using the `shiny`, `bslib`, `leaflet` and `DT` packages. The map would use OpenStreetMap tiles.
- The app reads `data/case_studies.csv` when it starts, so the CSV remains the single source of truth.
- Record package versions with `renv` so the app can be reproduced.

Planned features:

- Map markers that cluster when zoomed out.
- A popup for each project with its name, description, locations, sources and links.
- Text search, plus filters by source and location.
- A results table linked to the map, including projects without coordinates.
- A visible difference between exact and approximate locations.
- A CSV download of the data.
- A layout that works on phones.

Run the app locally and test it in a browser, including search, filters, popups and links.

## Repo layout

```text
data/case_studies.csv       Main database
data/sources.csv            Inventory of sources and their status
data/review_notes.md        Uncertain duplicates, broken links, gaps
scripts/                    Extraction, geocoding, checks
app.R                       R Shiny map viewer
renv.lock                   R package versions
downloads/                  Source files, git-ignored
```

## Deliverables

1. The compiled database and source inventory in this repo.
2. The working map viewer in this repo.
3. A short report of per-source counts, gaps and items that need a person's decision.

## Decisions for review

1. **What counts as a case study?** Proposed: a specific, named project or site that the source describes. A passing mention or a general technique would be left out.
2. **Where should the app be hosted?** A standard Shiny app needs an R server, so it can't run directly on GitHub Pages. Proposed: build and test it locally, then after your approval publish it to GitHub Pages with Shinylive, which runs R in the visitor's browser and needs no server. If it loads too slowly or a package doesn't work, the fallback is shinyapps.io, which needs an account. The repo is public, so anything pushed will be publicly visible either way.
3. **Should the work be committed and pushed as it goes?** Proposed: commit to a working branch and merge to `main` after your review.
