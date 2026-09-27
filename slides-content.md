# Slide Content — Spatial Epidemiology in R: A Hands-On Tutorial

**Prepared for:** Ghana R User Community & AfreDAC Collaboration
**Author:** George K. Agyen
**Source:** the six tutorial modules (`01-setup.qmd` … `06-integration.qmd`)

---

## How to use this file

This is slide *content*, not a rendered deck. Each slide is separated by `---` and
headed with `## Slide N — Title`. Bullets are written to drop straight into
PowerPoint, Google Slides, or Quarto RevealJS (`##` becomes a slide header,
`---` a slide break).

Conventions used below:

- `[Figure: filename]` — insert the named image that already exists in the repo.
- `[Demo]` — switch to RStudio and live-code this chunk.
- `[Exercise]` — pause for the module's practical exercise.
- **Notes:** — speaker notes / talking points, not slide text.

**Deck size:** 83 slides — 10 introduction, 8–14 per module, 4 closing.
(83 total). Trim to your time budget by dropping "Concept" slides and
keeping the title, objectives, one or two key figures, pitfalls, and recap.

**Recurring theme worth stating early and repeating:** the modelling modules
(4 and 5) deliberately use *simulated, signal-free* covariates. The headline
result everywhere is a well-earned null. Frame this as a feature — the point is
that the pipeline is honest, not that it "fails".

---

# Part 0 — Introduction

## Slide 1 — Title

**Spatial Epidemiology in R: A Hands-On Tutorial**

- An R-only learning pathway for spatial epidemiology
- Ghana R User Community — R-Only Track
- Prepared for the Ghana R User Community & AfreDAC Collaboration
- George K. Agyen

[Figure: Spat_epi.png]

**Notes:** Welcome and framing. State up front that the entire workflow — import,
map, analyse, model, communicate — stays inside R, with no GIS software and no
Python.

---

## Slide 2 — Who this is for

- Designed for **beginners in spatial epidemiology**
- No prior GIS or spatial analysis experience assumed
- You *should* be comfortable with basic R:
  - installing and loading packages
  - reading data
  - `dplyr` verbs: `filter()`, `select()`, `mutate()`

**Prerequisites:** R ≥ 4.1, RStudio (recommended), basic `dplyr` + `ggplot2`

**Notes:** Reassure the audience. The barrier to entry is R basics, not mapping.

---

## Slide 3 — The R-only focus

- Every task is done **in R**: data import → preparation → mapping → analysis → modelling
- No external GIS, no Python notebooks
- One integrated toolkit across all six modules

**Notes:** This mirrors the `mlspatial` philosophy — keep spatial data handling,
mapping, diagnostics, and machine learning in a single applied workflow.

---

## Slide 4 — What you will learn (roadmap)

| Module | Title | Key skills |
|----|----|----|
| 1 | R Environment Setup and Spatial Data Import | `sf`, `mlspatial`, CRS, joins |
| 2 | Disease Mapping in R | `tmap`, `ggplot2`, interactive maps, layout |
| 3 | Spatial Autocorrelation and Cluster Analysis | `spdep`, Moran's I, LISA, Gi\* hotspots |
| 4 | Spatial Regression and Risk Factor Analysis | OLS, GWR, `GWmodel`, residual diagnostics |
| 5 | Machine Learning for Spatial Epidemiology | `mlspatial`, RF, XGBoost, SVR, cross-validation |
| 6 | Integrated Application from Data to Decision | Accessibility, reporting, ethics |

**Notes:** This is the spine of the whole workshop. Point out the narrative arc:
import → map → detect clustering → model → machine learning → integrate & decide.

---

## Slide 5 — The narrative arc

```mermaid
flowchart LR
  A[1. Import & join] --> B[2. Map]
  B --> C[3. Detect clustering]
  C --> D[4. Regression OLS + GWR]
  D --> E[5. Machine learning]
  E --> F[6. Integrate & decide]
```

- Each module is a standalone `.qmd` file with runnable code
- Modules build on one another in order (01 → 06)

**Notes:** Emphasise progression: earlier modules supply the data object
(`africa_health`) that later modules reuse.

---

## Slide 6 — How to use the tutorial

1. Clone the repo and open `Spatial Epidemiology R Tutorial.Rproj` in RStudio
2. Work through modules in order (01 → 06)
3. Each module is a self-contained `.qmd` with runnable chunks
4. Render everything: `quarto::quarto_render()`
5. Render one module: open it and press `Ctrl+Shift+K`

**Notes:** Live demo if time permits.

---

## Slide 7 — Data you will work with

- **`mlspatial::africa_shp`** — 54 African countries, `sf` polygons, EPSG:3857
- **`mlspatial::panc_incidence`** — pancreatic cancer incidence, 54 rows × 18 columns
- Joined into **`africa_health`** — the object reused in every module
- **Simulated covariates** (Modules 4–6): six lifestyle/genetic risk factors
- Bundled `terra` examples: Luxembourg cantons (`lux.shp`), elevation (`elev.tif`)
- Bundled `sf` examples: North Carolina counties in both `.gpkg` and `.shp` form

**Notes:** Stress that no external downloads are needed — everything ships with
the packages, which keeps the tutorial fully reproducible.

---

## Slide 8 — The teaching philosophy (state it now)

- Covariates in the modelling modules are **simulated**, generated independently of the outcome
- So the models *should* find nothing — and they do
- The lesson is the **pipeline and the honesty of evaluation**, not a headline result
- You will see:
  - how a perfect fit signals **data leakage**
  - why **in-sample** scores lie
  - why **AICc**, not R², judges a GWR

**Notes:** This pre-empts the "why are all the results null?" question. Repeat it at
the start of Modules 4 and 5.

---

## Slide 9 — Key reference

Azeez, A., & Noel, C. (2025). *Predictive Modelling and Spatial Distribution of
Pancreatic Cancer in Africa Using Machine Learning-Based Spatial Model.*
Zenodo. https://doi.org/10.5281/zenodo.16529986

**Notes:** The `mlspatial` package and the dataset come from this work.

---

## Slide 10 — Let's begin

- Module 1 sets up the toolkit and produces the clean `sf` object everything else depends on
- Good setup habits prevent the most common, most frustrating spatial errors

**Next:** Module 1 — R Environment Setup and Spatial Data Import

---

# Part 1 — Module 1: R Environment Setup and Spatial Data Import

## Slide 11 — Module 1 title

**R Environment Setup and Spatial Data Import**

- Establish your R spatial epidemiology toolkit
- Import, join, and inspect spatial + health data
- **Outcome:** a clean `sf` object (`africa_health`) ready for mapping and analysis

**Packages:** `sf`, `terra`, `tmap`, `ggplot2`, `dplyr`, `mlspatial`

---

## Slide 12 — What you will learn

- Install and load the packages the tutorial relies on
- What a *shapefile*, a *GeoPackage*, and a *raster* are, and how to import all three (`sf`, `terra`)
- Why the GeoPackage (`.gpkg`) has largely replaced the legacy shapefile (`.shp`)
- How to preprocess (tidy) spatial data before analysis
- How to **join** health outcomes to spatial boundaries
- How to inspect and manage the **coordinate reference system (CRS)**

**Notes:** Why setup matters: blank maps, points in the wrong ocean, and joins that
silently drop countries all happen *before* any modelling.

---

## Slide 13 — Concept: install vs load

- **Install** (once per machine): `install.packages()` downloads from CRAN
- **Load** (every session): `library()` attaches the package
- You must re-load packages in each new R session

| Task | Package |
|----|----|
| Vector data | `sf` |
| Raster data | `terra` |
| Mapping | `tmap`, `ggplot2` |
| Wrangling | `dplyr`, `tidyr`, `readr` |
| Spatial statistics | `spdep` |
| Local regression | `GWmodel` |
| ML + mapping | `mlspatial` |
| ML engines | `randomForest`, `xgboost`, `e1071`, `caret` |

**Notes:** The tutorial's helper installs only missing packages (`require()` returns
FALSE instead of returning an error).

---

## Slide 14 — Concept: two spatial data models

| Model | Stores | Package | Example |
|----|----|----|----|
| **Vector** | points, lines, polygons + attributes | `sf` | admin boundaries, facilities, roads |
| **Raster** | continuous grid of cells | `terra` | elevation, temperature, rainfall |

- A **shapefile** is vector — and it is really *several* files that must travel together:
  `.shp` (geometry), `.dbf` (attributes), `.shx` (index), `.prj` (CRS)
- Move only the `.shp` and it will not open
- A **GeoPackage** (`.gpkg`) holds the same vector data in a *single* file (SQLite-based, many layers)
  - It has largely replaced the shapefile: no sidecars to lose, longer field names, true missing values

**Notes:** This is the single most common file-handling mistake for beginners. The
GeoPackage is unpacked on Slide 17.

---

## Slide 15 — Concept: the `sf` object

- An `sf` object is a **data frame + a `geometry` column**
- `class()` returns both `"sf"` and `"data.frame"`
- Each **row** = one feature (here, a country)
- Each **column** (other than geometry) = an attribute

```r
data(africa_shp, package = "mlspatial")
dim(africa_shp)               # 54 rows x 9 columns
sf::st_crs(africa_shp)$epsg   # 3857
```

**Notes:** You keep all the familiar dplyr tools *plus* spatial tools.

---

## Slide 16 — Concept: import vector and raster

```r
# Vector: st_read()
lux <- sf::st_read(system.file("ex/lux.shp", package = "terra"), quiet = TRUE)

# Raster: terra::rast()
elev <- terra::rast(system.file("ex/elev.tif", package = "terra"))
terra::res(elev)                        # cell size (~0.0083 degrees)
terra::crs(elev, describe = TRUE)$code  # 4326
```

- `st_read()` reads geometry, attributes, and CRS in one call — for `.shp`, `.gpkg`, and any other GDAL format
- `rast()` returns a `SpatRaster` with dimensions, resolution, extent, CRS

**Notes:** Elevation is a genuine epidemiological covariate — altitude shapes
temperature and vector-borne disease distributions.

---

## Slide 17 — Concept: import a GeoPackage (`.gpkg`)

A **GeoPackage** is a single-file, open (OGC/SQLite) container that can hold
several layers — the modern replacement for the shapefile.

```r
gpkg <- system.file("gpkg/nc.gpkg", package = "sf")
sf::st_layers(gpkg)                       # list layers: name, geometry, features, CRS
nc   <- sf::st_read(gpkg, quiet = TRUE)   # returns an sf object, exactly as before
names(nc)[ncol(nc)]                       # "geom" — the file names its own geometry column
```

|  | `.shp` | `.gpkg` |
|----|----|----|
| Files on disk | several (`.shp` + `.dbf` + `.shx` + `.prj`) | one |
| Layers per file | one | many |
| Field name length | truncated to 10 characters | up to 255 |
| Missing values | blank numerics read back as `0` | true `NULL` |

- Inspect the container with `st_layers()` first — the file name tells you nothing about how many layers are inside
- More than one layer? `st_read(path, layer = "...")`; omit it and you silently get the **first** layer
- The same call reads `.shp`, `.geojson`, `.gdb` — only `dsn` changes

**Notes:** Two limits bite epidemiology directly: the 10-character field limit can
truncate a join key (dropping records without warning), and shapefiles store missing
numerics as `0`. When a portal offers a choice, prefer `.gpkg`, then `.geojson`, then
a zipped `.shp`; convert with `sf::st_write(x, "out.gpkg")` — no GIS software needed.

---

## Slide 18 — Concept: tidy vector and raster data

**Tidy vector (check before analysis):**

- `st_geometry_type()` — one shared type (`MULTIPOLYGON`, `POLYGON`, …)
- `st_is_valid()` / `st_make_valid()` — find and repair broken geometries
- `st_is_empty()` — drop features with no coordinates
- Clean names: lower-case, underscores

**Preprocess raster (the three common tasks):**

- `project()` — reproject (vector equivalent: `st_transform()`)
- `crop()` then `mask()` — trim to a bounding box, then to the exact shape
- `resample()` — align rasters of differing resolution

**Notes:** Layers must share a CRS before you can overlay or stack them.

---

## Slide 19 — Concept: the health table and the join

- `panc_incidence` — 54 rows × 18 columns; key variable `NAME` (join key)
- Also: `incidence`, `female`, `male`, `agea`–`agec`, `yra`–`yre`, `TOTPOP`
- A join matches rows by a shared **key** — spelling must match exactly
- Trap: shapefile writes `"Côte d'Ivoire"`; health table writes `"Cote d'Ivoire"`
- Without fixing it, the join **silently drops** that country

```r
africa_shp <- africa_shp |>
  mutate(NAME = gsub("Côte d'Ivoire", "Cote d'Ivoire", NAME))

africa_health <- mlspatial::join_data(africa_shp, panc_incidence, by = "NAME")
```

**Notes:** `join_data()` is a thin wrapper over `dplyr::inner_join()`.
`inner_join()` keeps only matched rows; `left_join()` keeps all polygons.

---

## Slide 20 — Concept: CRS management

- A CRS = a **datum** (Earth model) + a **projection** (flattening), identified by EPSG code
- **Geographic** (EPSG:4326, WGS 84) — degrees; `st_is_longlat()` is TRUE
  - degrees are *not* a consistent distance unit → bad for distances/areas
- **Projected** (EPSG:3857, UTM zones) — metres; distances and areas are meaningful

```r
sf::st_crs(africa_health)          # EPSG:3857
sf::st_is_longlat(africa_health)   # FALSE
```

**Notes:** Every projection distorts *something*. EPSG:3857 is fine for
visualisation but distorts area, worsening away from the equator.

---

## Slide 21 — Common pitfalls (Module 1)

- **Mismatched CRS** — the most common source of silent spatial errors
- **Join key spelling** — accented vs unaccented names drop countries silently
- **Invalid geometry** — self-intersections cause cryptic downstream errors
- **Shapefile file set** — `.shp`, `.dbf`, `.shx`, `.prj` must stay together
- **Legacy format** — prefer `.gpkg` over `.shp`: 10-character field names can truncate join keys, and blank numerics arrive as `0`
- Always `summary()` / `sum(is.na(...))` after a join to confirm nothing was lost

**Notes:** `incidence` here is numeric, ranges roughly 1.8–4950, median near 185,
with **no missing values** — so no imputation is needed in this dataset.

---

## Slide 22 — Practical exercise (Module 1)

1. Load `africa_shp` and report class, dimensions, geometry type, CRS code
2. Import the Luxembourg shapefile — how many cantons, what geometry type?
3. Import the elevation raster — report resolution, extent, CRS
4. Reproject that raster to EPSG:3857 and confirm the new CRS
5. Recreate the join with `inner_join()` — how many rows?
6. Read the `sf` GeoPackage (`gpkg/nc.gpkg`): use `st_layers()` to report the layer's feature count and geometry type, and name its geometry column. Then read the equivalent shapefile (`shape/nc.shp`) and explain why both objects behave identically
7. *(Challenge)* Report how many countries have invalid geometries; repair them

[Exercise]

---

## Slide 23 — Module 1 recap / checklist

- [ ] All packages installed and loaded
- [ ] `africa_shp` imported successfully
- [ ] Can import an external shapefile with `st_read()`
- [ ] Can read a GeoPackage (`st_layers()` + `st_read()`) and explain why `.gpkg` supersedes `.shp`
- [ ] Can import and preprocess a raster with `terra`
- [ ] `panc_incidence` loaded and inspected
- [ ] `africa_health` created via `join_data()`
- [ ] CRS verified and understood

**Next:** Module 2 — Disease Mapping in R

---

# Part 2 — Module 2: Disease Mapping in R

## Slide 24 — Module 2 title

**Disease Mapping in R**

- Publication-quality thematic maps with `mlspatial`, `tmap`, and `ggplot2`
- Static maps, interactive maps, multi-layer overlays, and export

**Notes:** A **choropleth** colours each region by a data value (here, disease
incidence). It is the most common way to show spatial variation in health outcomes.

---

## Slide 25 — What you will learn

- Build a choropleth in **one line** with `mlspatial`
- How `tmap` assembles a map from stacked **layers**
- Reproduce and customise maps with `ggplot2`
- Switch between static and interactive (Leaflet) maps
- Overlay point data on polygons and export multi-panel figures

---

## Slide 26 — Concept: the one-line map

```r
mlspatial::plot_single_map(
  sf_data = africa_health,
  var = "incidence",
  title = "Incidence (per 100,000)",
  palette = "yl_or_rd"
)
```

- `var` must be **quoted** (passed as a string)
- `palette` accepts a palette name in lower-case with underscores
- Under the hood: `tm_fill()` with a **quantile** scale → 5 equal-count bins
- Quantile bins are robust to skewed data

[Figure: africa_disease_labelled.png]

---

## Slide 27 — Concept: the `tmap` layer grammar

`tmap` builds maps the way `ggplot2` builds plots — by adding layers with `+`:

| Layer | Adds |
|----|----|
| `tm_shape(data)` | declares the data (draws nothing itself) |
| `tm_fill("incidence")` | colours polygons by a variable |
| `tm_borders()` | outlines |
| `tm_text("NAME")` | labels at centroids |
| `tm_title()`, `tm_compass()`, `tm_scalebar()`, `tm_layout()` | map furniture |

- `tmap` v4 splits each aesthetic into:
  - `*.scale` — **how values map to visuals** (palette, breaks)
  - `*.legend` — **how the legend looks** (title, position)

**Notes:** e.g. `fill.scale = tm_scale(values = "brewer.yl_or_rd")` sets the
palette; `fill.legend = tm_legend(title = "Rate per 100k")` sets the title.

---

## Slide 28 — Concept: the same map in `ggplot2`

```r
ggplot(africa_health) +
  geom_sf(aes(fill = incidence), colour = "white", linewidth = 0.2) +
  scale_fill_distiller(palette = "YlOrRd", direction = 1, name = "Incidence") +
  geom_sf_text(aes(label = stringr::str_wrap(NAME, 8)),
               size = 3.2, check_overlap = TRUE) +
  theme_minimal() +
  labs(title = "Pancreatic Cancer Incidence in Africa")
```

- `ggplot(data)` → `geom_sf()` → `aes(fill = ...)`
- Values set **inside** `aes()` are mapped to data; **outside**, they are fixed
- `scale_fill_distiller()` — perceptually uniform, colour-blind safe, prints in greyscale
- `check_overlap = TRUE` drops colliding labels

[Figure: africa_incidence_ggplot.pdf]

---

## Slide 29 — Concept: static vs interactive

```r
tmap_mode("view")   # Leaflet widget: pan, zoom, click, popups
tmap_mode("plot")   # static image for documents and print
```

- The map code is **identical** in both modes — only the mode changes
- `popup.vars = c("incidence", "female", "male")` lists click-popup columns
- Interactive maps are for exploration; static maps are for publication

**Notes:** The interactive chunk is `eval: false` in the book so the widget isn't
embedded — run it live in RStudio.

---

## Slide 30 — Concept: overlaying layers

```r
set.seed(4607)
facility_points <- st_sample(africa_health, size = 70, type = "random") |>
  st_as_sf()

tm_shape(africa_health) +
  tm_polygons("incidence", ...) +
  tm_shape(facility_points) +          # second tm_shape switches the active data
  tm_dots(fill = "type", size = 0.5)
```

- `st_sample()` places random points **inside** the polygons
- Each `tm_shape()` switches which data the following layers draw
- `tm_scale_categorical()` for a categorical variable (single colour, not a ramp)

[Figure: africa_disease_panel.png]

---

## Slide 31 — Concept: arranging and exporting

```r
combined <- tmap_arrange(map_inc, map_male, ncol = 2)

tmap_save(combined, filename = "africa_disease_panel.png",
          width = 12, height = 6, dpi = 300)

ggsave("africa_incidence_ggplot.pdf", plot = gg, width = 10, height = 8)
```

- `tmap_arrange()` places maps side by side; `mlspatial::plot_map_grid()` wraps it
- `tmap_save()` — inches + `dpi` (300 is the print standard)
- `ggsave()` infers format from the extension (`.png`, `.pdf`, `.svg`, `.tiff`)

---

## Slide 32 — Common pitfalls (Module 2)

- Palette names are lower-case with underscores (`"yl_or_rd"`, not `"YlOrRd"`)
- Labels collide on small countries — reduce `size`, or use `check_overlap`
- Forgetting `tm_borders()` leaves polygons hard to distinguish
- Interactive widgets won't render in a static PDF — keep `tmap_mode("plot")` for export

---

## Slide 33 — Practical exercise (Module 2)

1. Map `male` with `plot_single_map()` and the `"blues"` palette
2. Recreate it in `ggplot2` with a different palette and a subtitle
3. Build a `tmap` choropleth of `female` with borders, legend, compass, scale bar
4. *(Challenge)* Simulate 50 points and overlay them with `tm_dots()`
5. Arrange two maps side by side and save as PNG at 250 dpi

[Exercise]

---

## Slide 34 — Module 2 recap / checklist

- [ ] Created a quick map with `plot_single_map()`
- [ ] Added labels with `tm_text()`
- [ ] Built a `ggplot2` choropleth with `geom_sf()`
- [ ] Switched to interactive mode and back
- [ ] Overlaid polygon and point layers
- [ ] Exported a multi-panel figure

**Next:** Module 3 — Spatial Autocorrelation and Cluster Analysis

---

# Part 3 — Module 3: Spatial Autocorrelation and Cluster Analysis

## Slide 35 — Module 3 title

**Spatial Autocorrelation and Cluster Analysis**

- Are disease rates in one place similar to rates nearby?
- Build weights matrices → global Moran's I → local Moran's I (LISA) → Getis-Ord Gi\*
- Visualise the clusters

**Packages:** `spdep`, `tmap`, `mlspatial`

**Notes:** Quote **Tobler's First Law**: *"Everything is related to everything else,
but near things are more related than distant things."* Autocorrelation is the formal
way to test whether that holds for your data.

---

## Slide 36 — What you will learn

- What spatial autocorrelation is and why it matters in epidemiology
- How to define "neighbours" and build a spatial weights matrix
- How to test for **global** clustering with Moran's I
- How to locate **where** clustering happens with Local Moran's I (LISA)
- How to find hotspots/coldspots with **Getis-Ord Gi\***

---

## Slide 37 — Concept: why it matters

- If disease were random, each region's rate would be independent of its neighbours
- In reality, infection, environmental risk, and service access **spill across borders**
- Testing autocorrelation tells you whether resemblance is stronger than chance — and *where* clusters are
- It also **flags a violation** of the independence assumption behind ordinary regression (→ Module 4)

**Notes:** Two payoffs: it finds real hotspots, and it warns you when OLS standard
errors will be wrong.

---

## Slide 38 — Concept: spatial weights

```r
nb <- poly2nb(africa_health, row.names = africa_health$NAME,
              queen = TRUE, snap = 1000)
lw <- nb2listw(nb, style = "W", zero.policy = TRUE)
```

- **Contiguity:** neighbours touch. `queen = TRUE` = share any point/edge;
  `queen = FALSE` (rook) = share an edge only
- `snap = 1000` — tolerance (here 1000 m) for misaligned borders
- `style = "W"` row-standardises weights (`"B"` = binary, `"C"` = globally standardised)
- `zero.policy = TRUE` — **essential** for island nations with no neighbours

**Result:** 54 regions, 214 links (~4 neighbours each); **6 islands have no
neighbours** (Cabo Verde, Comoros, São Tomé & Príncipe, Madagascar, Mauritius,
Seychelles); 7 disconnected sub-graphs.

---

## Slide 39 — Concept: global Moran's I

```r
global_moran <- moran.test(africa_health$incidence, listw = lw,
                           zero.policy = TRUE, na.action = na.exclude)
```

- One number for the whole map: are similar values generally near each other?
- Range **−1 to +1**:
  - **+1** perfect clustering (high near high)
  - **0** spatial randomness
  - **−1** perfect dispersion (checkerboard)
- `moran.test()` compares the observed I to a null of spatial randomness; p < 0.05 ⇒ unlikely by chance

**Here:** I ≈ **−0.07**, p ≈ **0.75** → **not** significant. No continent-wide clustering.

**Notes:** Key teaching point — a global statistic can be **non-significant even
when local clusters exist** (a few high regions cancelling a few low ones). That is
exactly why we move to local statistics.

---

## Slide 40 — Concept: local Moran's I (LISA)

```r
local_mi <- localmoran(africa_health$incidence, listw = lw,
                       zero.policy = TRUE, na.action = na.exclude)
```

- LISA = Local Indicators of Spatial Association
- One statistic **per region**, so you can map *where* clusters are
- Output columns:
  - `Ii` — the local statistic (sign + size)
  - `E.Ii`, `Var.Ii` — expected value and variance under randomness
  - `Z.Ii` — z-score
  - `Pr(z != E(Ii))` — p-value
- The six islands get `NA` (their local statistic is undefined)

**Quadrants:** High-High (hot cluster), Low-Low (cold cluster), High-Low, Low-High

---

## Slide 41 — Concept: Getis-Ord Gi\*

```r
nb_self <- include.self(nb)
gi_star <- localG(africa_health$incidence,
                  listw = nb2listw(nb_self, style = "W", zero.policy = TRUE),
                  zero.policy = TRUE)
```

- LISA asks "is this region *correlated* with its neighbours?"
- **Gi\*** asks "is this region part of a cluster of **high** (or **low**) values?"
- `include.self()` adds each region to its own neighbour list (a region is a hotspot only if high **and** surrounded by highs)
- `localG()` returns **z-scores**; |z| > 1.96 ⇒ significant
  - z > +1.96 → hotspot; z < −1.96 → coldspot
- Exact p-values live in `attr(gi_star, "internals")`

**Here:** typically **3 hotspots, 0 coldspots, 51 not significant**.

---

## Slide 42 — Concept: visualising clusters

```r
africa_health$lisa_quadrant <- attributes(local_mi)$quadr$mean

tm_shape(africa_health) +
  tm_polygons(fill = "lisa_cluster",
              fill.scale = tm_scale_categorical(values = lisa_colours)) +
  tm_title("Local Moran's I (LISA) Cluster Map")
```

- `quadr` holds the quadrant classification (High-High, Low-Low, …)
- Keep only significant quadrants (p ≤ 0.05); label the rest "Not significant"
- Give the `factor()` explicit `levels` so the legend order matches the colours
- `tmap_arrange()` + `tmap_save()` → two-panel cluster figure

[Figure: africa_hotspot_panel.png]

---

## Slide 43 — Common pitfalls (Module 3)

- Forgetting `zero.policy = TRUE` → errors on island nations
- Confusing **Gi\*** (needs `include.self`) with **LISA** — different questions
- Global non-significance ≠ no local clusters
- Islands return `NA` local statistics — decide how to display them
- `snap` should match your CRS units (metres here)

---

## Slide 44 — Practical exercise (Module 3)

Re-run the whole workflow for a different variable — `male`:

1. Build a **rook** neighbour list (`queen = FALSE`); compare the link count
2. Global Moran's I for `male` — significant at 0.05?
3. Local Moran's I — how many regions are significant?
4. Getis-Ord Gi\* — how many hotspots and coldspots?
5. *(Challenge)* Map the Gi\* result for `male` with your own colour scheme

[Exercise]

---

## Slide 45 — Module 3 recap / checklist

- [ ] Built a weights matrix with `poly2nb()` and `nb2listw()`
- [ ] Computed and interpreted Global Moran's I
- [ ] Computed Local Moran's I (LISA) and identified cluster types
- [ ] Calculated Getis-Ord Gi\* and classified hotspots/coldspots
- [ ] Visualised LISA and Gi\* side by side

**Next:** Module 4 — Spatial Regression and Epidemiological Risk Factor Analysis

---

# Part 4 — Module 4: Spatial Regression and Risk Factor Analysis

## Slide 46 — Module 4 title

**Spatial Regression and Epidemiological Risk Factor Analysis**

- Connect health outcomes to lifestyle/genetic drivers
- Fit a global OLS model → check residuals for spatial structure → fit GWR

**Packages:** `GWmodel`, `spdep`, `mlspatial`, `tmap`

**Notes:** Modules 2–3 mapped the disease and measured its clustering. This module
asks a *causal*-flavoured question: which factors drive the rate?

---

## Slide 47 — What you will learn

- How to assemble risk-factor covariates for a regression
- How to fit and interpret an ordinary least squares (**OLS**) model
- How to spot **data leakage** — a perfect fit that means the outcome came from its own predictors
- Why we model **rates**, not raw counts
- How to test OLS **residuals** for spatial autocorrelation
- What **Geographically Weighted Regression (GWR)** is, and how to judge whether it helps

---

## Slide 48 — Concept: simulate the covariates

```r
set.seed(898)
n <- nrow(africa_health)
africa_health <- africa_health |>
  mutate(
    smoking_prev      = round(runif(n, 8, 28), 1),
    obesity_prev      = round(runif(n, 5, 30), 1),
    diabetes_prev     = round(runif(n, 2, 11), 1),
    alcohol_per_capita= round(runif(n, 0.5, 12), 1),
    family_history_prev = round(runif(n, 3, 10), 1),
    urbanization_pct  = round(runif(n, 15, 85), 1)
  )
```

| Column | Description | Range |
|----|----|----|
| `smoking_prev` | adult smoking (%) | 8–28 |
| `obesity_prev` | adult obesity, BMI ≥ 30 (%) | 5–30 |
| `diabetes_prev` | type 2 diabetes (%) | 2–11 |
| `alcohol_per_capita` | litres pure alcohol/yr | 0.5–12 |
| `family_history_prev` | first-degree relative history (%) | 3–10 |
| `urbanization_pct` | urban population (%) | 15–85 |

**Notes:** In real research these come from WHO GHO, GLOBOCAN, IDF, national
surveys — joined by ISO code. Here they are drawn **independently of the outcome
and of each other** on purpose: it demonstrates the pipeline, not a real finding.

---

## Slide 49 — Concept: OLS (and reading `lm`)

```r
ols_model <- lm(incidence ~ smoking_prev + obesity_prev + diabetes_prev +
                  alcohol_per_capita + family_history_prev + urbanization_pct +
                  female + male + ageb + agec,
                data = africa_health)
summary(ols_model)
```

Reading the summary:

- **Estimate** — expected change in the outcome per 1-unit increase in the predictor
- **Std. Error / t / Pr(>\|t\|)** — precision and significance
- **R²** — proportion of variance explained (0–1)
- **F-statistic** — overall test that at least one predictor matters

---

## Slide 50 — Concept: data leakage (the red flag)

- The first model returns **R² = 1**, F ≈ 2.36e+31, and a warning: *"essentially perfect fit"*
- Perfect fit ⇒ the outcome is **mathematically derived from its own predictors**
- Diagnosis:
  - `female` and `male` coefficients are *identical* (t ≈ 10¹³ — an algebraic identity, not an effect)
  - `cor(incidence, female + male)` = **exactly 1**
  - `incidence / (female + male)` is the constant `0.09302326` for every country
- A systematic screen shows the leakage extends to `age*`, `yr*`, `m*`, `f*` — all parts of the same case-count calculation

**Notes:** Teach the habit: screen every numeric variable against the outcome
before trusting a model. Negative or huge t-values are tell-tale signs.

---

## Slide 51 — Concept: model a rate, not a count

```r
africa_health <- africa_health |>
  mutate(incidence_rate = (incidence / TOTPOP) * 100000)

ols_refined <- lm(incidence_rate ~ smoking_prev + obesity_prev + diabetes_prev +
                    alcohol_per_capita + family_history_prev + urbanization_pct,
                  data = africa_health)
```

- Raw counts track population size — a big country has more cases by definition
- Standardise to **cases per 100,000** so countries are comparable
- Same logic as age-standardised rates in cancer statistics
- With leaked columns removed: **R² ≈ 0.15, no significant predictors**
  - two marginal "effects" are chance and point the wrong biological way

**Notes:** Because the covariates are random noise, OLS did exactly what it should:
found nothing.

---

## Slide 52 — Concept: test the residuals

```r
residual_moran <- lm.morantest(ols_refined, listw = lw, zero.policy = TRUE)
```

- **Residual = observed − fitted** — the part the model could *not* explain
- OLS assumes residuals are randomly distributed
- `lm.morantest()` applies Moran's I to the **residuals** (Module 3 tool)
- If residuals are spatially clustered, a key spatial cause is missing → p-values unreliable
- **Here:** I = +0.126 (expected −0.024), p = 0.065 — not significant, but *borderline*
  - the observed I is positive: nearby countries *do* share similar errors
  - read it as **"weak, inconclusive spatial structure"**, not "residuals are random"
- `zero.policy = TRUE` keeps the 6 islands with no neighbours in the test
  (Cabo Verde, Comoros, Sao Tome and Principe, Madagascar, Mauritius, Seychelles)

**Notes:** In real epidemiology, significant residual autocorrelation is common and
is the signal that a spatial model is needed. Here the signal is weak rather than
clean — positive I, but it misses the 5% threshold only narrowly.

---

## Slide 53 — Concept: Geographically Weighted Regression

```r
bw_adapt <- bw.gwr(incidence_rate ~ ..., data = africa_health,
                   approach = "CV", kernel = "bisquare", adaptive = TRUE)

gwr_model <- gwr.basic(incidence_rate ~ ..., data = africa_health,
                       bw = bw_adapt, kernel = "bisquare", adaptive = TRUE)
```

- OLS fits **one** equation for the whole continent
- GWR fits a weighted regression at **every** location → one coefficient set per country
- **Bandwidth** = size of the local window; `bw.gwr()` picks it by cross-validation
  - `adaptive = TRUE` → bandwidth in **number of neighbours** (good for unequal sizes)
- **Kernel** = weighting function; `"bisquare"` drops smoothly to zero at the edge

**Here:** optimal bandwidth = **35 neighbours** — more than half of the 54 countries.

---

## Slide 54 — Concept: judging whether GWR is worth it

- GWR almost always **inflates R²** because it fits many more parameters
- Judge it by **AICc** (lower is better), not R²
- **Here:** AICc **rose** from 276.4 (OLS) to **294.7** (GWR) → GWR is **worse**
- Yet GWR shows `diabetes_prev` ranging ≈ −0.64 to +0.23 — apparently flipping sign
- With simulated noise, the model is chasing nothing; the "variation" is an artefact
- The verdict is **specific to this six-covariate spec** — the residual test was only
  *borderline* (I = 0.126, p = 0.065), not a clean "no clustering"
- Let AICc decide per specification — the two-covariate exercise model leaves much
  stronger residual clustering (I = 0.217, p = 0.007), exactly what GWR is for

**Notes:** This is a deliberate, valuable **negative result** — a lesson in resisting
a flattering R². But it is not a blanket ruling against spatial regression: change
the covariate set and the balance can shift.

---

## Slide 55 — Concept: visualising GWR outputs

```r
gwr_sf <- gwr_model$SDF

tm_shape(gwr_sf) +
  tm_polygons(
    fill = "diabetes_prev",
    fill.scale = tm_scale_continuous(values = "-brewer.rd_bu",
                                     midpoint = 0),
    fill.legend = tm_legend(title = "Local Coefficient (Diabetes)"),
    col = "black", col_alpha = 0.5, lwd = 0.5
  ) +
  tm_title("GWR: Spatially Varying Effect of Diabetes")
```

- `gwr_model$SDF` — one row per country, a column per local coefficient + `Local_R2`
- Diverging palette with `midpoint = 0` makes the **sign** instantly readable
- A second map of `Local_R2` (viridis) shows where the model fits best
- Smooth colour transitions look convincing — but they contradict the AICc

**Notes:** A beautiful map of a pattern that isn't real. Always cross-check with a
diagnostic.

---

## Slide 56 — Common pitfalls (Module 4)

- **Data leakage**: R² = 1 means an identity was reversed, not a discovery
- Modelling **counts** instead of **rates** — population dominates
- Judging GWR by **R²** instead of **AICc**
- GWR with too few observations / huge bandwidth → unstable local estimates
- Forgetting the residual test before reaching for a spatial model
- Building weights over islands — neighbourless units need `zero.policy = TRUE` (or to be dropped)

---

## Slide 57 — Practical exercise (Module 4)

1. Confirm the leakage: `cor(incidence, female + male)` — report the value
2. Fit an OLS on `incidence_rate` using only `smoking_prev` and `obesity_prev` — report R²
3. Run `lm.morantest()` on that model's residuals — significant autocorrelation?
4. *(Challenge)* Re-run `bw.gwr()` with `kernel = "gaussian"` — report the bandwidth and whether AICc improved

**Presenter reference:** Gaussian kernel → bandwidth **24**, AICc **279.7** — beats the bisquare GWR (294.7) but stays above the six-covariate OLS (276.4).

[Exercise]

---

## Slide 58 — Module 4 recap / checklist

- [ ] Simulated risk-factor covariates
- [ ] Built and interpreted an OLS model
- [ ] Diagnosed data leakage from a perfect fit
- [ ] Modelled a rate rather than a count
- [ ] Tested OLS residuals for spatial autocorrelation
- [ ] Fitted a GWR with adaptive bandwidth and judged it by AICc
- [ ] Mapped local coefficient surfaces

**Next:** Module 5 — Machine Learning for Spatial Epidemiology

---

# Part 5 — Module 5: Machine Learning for Spatial Epidemiology

## Slide 59 — Module 5 title

**Machine Learning for Spatial Epidemiology**

- Showcasing the `mlspatial` package
- Train Random Forest, XGBoost, and Support Vector Regression
- Evaluate honestly, then map predictions to look for spatial risk patterns

**Packages:** `mlspatial`, `randomForest`, `xgboost`, `e1071`, `caret`

---

## Slide 60 — What you will learn

- How to turn an `sf` object into a clean table ready for ML
- What Random Forest, XGBoost, and SVR are — in plain terms
- The difference between **in-sample** fit and **held-out** (cross-validated) performance
- How to run honest, cross-validated evaluation with `caret` and `xgboost`
- How to map model predictions back onto the map

---

## Slide 61 — Concept: what `mlspatial` offers

- Traditional workflow scatters across GIS, R scripts, and Python notebooks
- `mlspatial` bundles: data handling (`sf`), mapping (`tmap`), autocorrelation (`spdep`), and ML (`randomForest`, `xgboost`, `e1071`) into one workflow
- `train_*()` functions are thin wrappers — same output as the underlying package:

| Function | Wraps |
|----|----|
| `train_rf()` | `randomForest::randomForest(..., importance = TRUE)` |
| `train_xgb()` | `xgboost::xgb.train()` (squared-error objective) |
| `train_svr()` | `e1071::svm(type = "eps-regression")` with radial kernel |

---

## Slide 62 — Concept: prepare the data

```r
ml_data <- africa_health |>
  st_drop_geometry() |>
  select(incidence_rate, smoking_prev, obesity_prev, diabetes_prev,
         alcohol_per_capita, family_history_prev, urbanization_pct, TOTPOP) |>
  drop_na()
```

- `st_drop_geometry()` — ML needs a plain rectangular table
- **Row order is preserved**, so predictions can be pasted back onto the map later
- `select()` — keep the outcome + predictors
- `drop_na()` — XGBoost and SVR error on `NA`

**Scaling:**

- **Trees** (RF, XGBoost) are scale-invariant → no scaling needed
- **SVR** is distance-based → scale predictors (`scale()`), done per CV fold

---

## Slide 63 — Concept: Random Forest

```r
rf_model <- mlspatial::train_rf(data = ml_data, formula = rf_formula,
                                ntree = 500, seed = 222)
```

- A **decision tree** splits data on predictors; a single tree is unstable
- A **forest** grows many trees on random samples + random predictor subsets, then averages → less variance, more robust
- `ntree = 500` — more trees = less variance, more compute
- Output: **MSE** and **% variance explained** — the latter is **out-of-bag** (an honest, built-in estimate)
- **Here:** % variance explained ≈ **1.3%** — essentially zero (as expected)

**Notes:** Variable importance via `caret::varImp()`; a **negative** importance means
shuffling that variable *improved* the model — i.e. it was noise.

---

## Slide 64 — Concept: XGBoost

```r
xgb_model <- train_xgb(data = ml_data, formula = rf_formula,
                       nrounds = 100, max_depth = 4, learning_rate = 0.1)
```

- **Gradient boosting** grows trees **sequentially** — each new tree fits the errors of the previous ones
- Hyperparameters:
  - `nrounds` — number of boosting rounds
  - `max_depth` — tree depth (smaller = less overfitting)
  - `learning_rate` (η) — shrinkage per tree (typical 0.01–0.3)
- `xgb.importance()` ranks by:
  - **Gain** — average loss improvement per split (headline)
  - **Cover** — observations passing through splits
  - **Frequency** — how often used
- Gain is never negative — unlike random-forest importance

---

## Slide 65 — Concept: Support Vector Regression

```r
ml_data_scaled <- ml_data |>
  mutate(across(all_of(predictors), ~ as.numeric(scale(.x))))

svr_model <- train_svr(data = ml_data, formula = rf_formula)
```

- SVR fits a function that is **flat** within a margin (ε) — only points outside the margin (the **support vectors**) matter
- The **radial (RBF) kernel** captures non-linear relationships via a higher-dimensional mapping
- Predictors must be **scaled** (mean 0, SD 1) because the kernel is distance-based
- **Here:** ≈ 49 of 54 rows are support vectors — typical for small, noisy data
- Tune `cost` and `gamma` by cross-validation for production models

---

## Slide 66 — Concept: the three metrics (and why in-sample ≠ real)

```r
rf_metrics <- postResample(pred = rf_preds, obs = ml_data$incidence_rate)
```

| Metric | Meaning |
|----|----|
| **RMSE** | root mean squared error — penalises large errors; outcome units |
| **MAE** | mean absolute error — average error size |
| **R²** | proportion of variance explained |

- These are **in-sample** metrics — evaluated on the training data
- A high in-sample R² means the model **memorised**, not learned
- Tree models (especially XGBoost) can fit noise almost perfectly
- **A high in-sample R² is not evidence of a real relationship**

---

## Slide 67 — Concept: honest evaluation with cross-validation

```r
cv_control <- trainControl(method = "repeatedcv", number = 5, repeats = 5,
                           savePredictions = "final")
rf_cv  <- train(rf_formula, data = ml_data, method = "rf", trControl = cv_control)
svr_cv <- train(rf_formula, data = ml_data, method = "svmRadial",
                trControl = cv_control, preProcess = c("center", "scale"))

xgb_cv <- xgb.cv(data = xgb_matrix_full, params = list(
  objective = "reg:squarederror", max_depth = 4, eta = 0.1),
  nfold = 5, early_stopping_rounds = 10, ...)
```

- **k-fold CV** trains on k−1 parts and evaluates on the held-out part, so every row is predicted by a model that never saw it
- `repeatedcv, number = 5, repeats = 5` → 25 fits per candidate, less noise
- `preProcess = c("center","scale")` scales **within each fold** (no leakage)
- Current `xgboost` breaks with `caret::train`, so XGBoost is cross-validated with `xgb.cv()`

**The honest result:** CV R² collapses toward zero — RF ≈ 0.15, SVR ≈ 0.07, XGB ≈ 0.
**That collapse is the lesson.**

---

## Slide 68 — Concept: map predictions to find patterns

```r
africa_health <- africa_health |>
  mutate(pred_rf = rf_oof$pred_rf, pred_xgb = xgb_oof$pred_xgb,
         pred_svr = svr_oof$pred_svr)

plot_obs_vs_pred(observed = africa_health$incidence_rate,
                 predicted = africa_health$pred_rf)
```

- **Out-of-fold predictions** are made on each row while it was held out
- Average over repeated folds, re-attach by **row order** (the `st_drop_geometry` trick)
- `plot_obs_vs_pred()` draws a 1:1 line — points hugging it would mean a good model
- Four maps: observed vs RF vs XGB vs SVR predictions
- **Here:** points scatter widely; predictions don't reproduce the observed map

**Notes:** The consistent finding across all three algorithms: the six simulated
covariates carry no real signal. That is the intended result of a teaching dataset —
a correctly run pipeline producing honestly interpreted null results.

---

## Slide 69 — Common pitfalls (Module 5)

- Trusting **in-sample** R² — always cross-validate
- Forgetting `drop_na()` — XGBoost and SVR error on missing values
- Not scaling predictors for **SVR** (or scaling before the CV split — leakage)
- Expecting a high % variance explained on signal-free simulated data
- The `xgboost` ↔ `caret` compatibility issue — use `xgb.cv()`

---

## Slide 70 — Practical exercise (Module 5)

1. Train a Random Forest with `ntree = 200` and a different seed — report % variance explained
2. List the **top three** predictors by Gain with `xgb.importance()`
3. Compute the in-sample R² of the SVR model with `postResample()`
4. *(Challenge)* Re-run the `caret` RF cross-validation with `number = 10` and compare the best RMSE with the 5-fold result

[Exercise]

---

## Slide 71 — Module 5 recap / checklist

- [ ] Prepared clean ML data with `st_drop_geometry()`
- [ ] Trained Random Forest, XGBoost, and SVR
- [ ] Evaluated all three with `postResample()`
- [ ] Compared RMSE, MAE, and R²
- [ ] Re-evaluated honestly with cross-validation
- [ ] Mapped predictions back onto the map

**Next:** Module 6 — Integrated Application from Data to Decision

---

# Part 6 — Module 6: Integrated Application from Data to Decision

## Slide 72 — Module 6 title

**Integrated Application from Data to Decision**

- Bring it all together: overlay disease burden with service availability
- Quantify accessibility, generate reproducible reports, reflect on ethics

**Notes:** This is the payoff. Earlier modules taught *how* to import, map, model,
and predict; this one shows how the pieces become a decision-ready deliverable.

---

## Slide 73 — What you will learn

- Combine a disease-burden map with service locations to spot underserved areas
- Compute the distance from each region to its nearest facility
- Turn your analysis into a **reproducible report**
- The **ethical obligations** that come with mapping people and disease

---

## Slide 74 — Concept: overlay disease burden and services

```r
africa_health <- africa_health |>
  mutate(burden_class = case_when(
    incidence_rate >= quantile(incidence_rate, 0.67, na.rm = TRUE) ~ "High",
    incidence_rate >= quantile(incidence_rate, 0.33, na.rm = TRUE) ~ "Medium",
    TRUE ~ "Low")) |>
  mutate(burden_class = factor(burden_class, levels = c("High", "Medium", "Low")))
```

- Tertiles (0.67 / 0.33) → three groups of 18 countries
- Fix `factor` levels so "High" leads the legend
- Overlay 70 simulated facility points (`st_sample`, `set.seed(4607)`)
- **Red polygons with no nearby blue dots = priority areas**

---

## Slide 75 — Concept: distance / accessibility

```r
africa_health_geo <- st_transform(africa_health, crs = 4326)
facility_points_geo <- st_transform(facility_points, crs = 4326)
africa_centroids <- st_centroid(africa_health_geo, of_largest_polygon = TRUE)

dist_matrix <- st_distance(africa_centroids, facility_points_geo)
africa_health$dist_to_facility_km <- as.numeric(apply(dist_matrix, 1, min)) / 1000
```

- `st_centroid(of_largest_polygon = TRUE)` — one point per country (handles multi-part nations)
- `st_distance()` returns a **54 × 70 matrix** — cell (i, j) = country *i* to facility *j*
- `apply(..., 1, min)` — distance to the **nearest** facility; ÷1000 → km
- **Here:** median ≈ **346 km** (range ≈ 29–2380 km)

**Notes:** CRS matters. In EPSG:4326, `sf` uses spherical maths for true geodesic
distances. For highly local analysis (e.g. Ghana districts), reproject to a metric
CRS such as UTM Zone 30N (EPSG:32630).

---

## Slide 76 — Concept: reproducible reports

- A **reproducible report** generates text, numbers, tables, and figures from code — re-run it, get identical results
- R Markdown (`.Rmd`) and Quarto (`.qmd`) are the gold standards

```markdown
---
title: "Spatial Epidemiology Report: Pancreatic Cancer in Africa"
format: html
---
## Introduction
## Data
## Results
## Conclusion
```

Three ingredients:

- **Code chunks** — run at render time, regenerating every figure/table
- **Inline code** — embed R expressions in sentences so numbers update automatically
- **Parameterised reports** — a `params:` block produces one report per country from one template

---

## Slide 77 — Concept: ethical considerations

- **Privacy protection** — aggregate data protect identity; never map exact household locations or counts < 5 without privacy protection; combining coordinates with demographics risks re-identification
- **Modifiable Areal Unit Problem (MAUP)** — results depend on the boundaries chosen; test sensitivity across scales
- **Ecological fallacy** — aggregate relationships need not hold for individuals; say "areas with higher X are associated with higher Y", not "X causes Y"
- **Spatial inequality & resource allocation** — maps are political; share with local officials first, show uncertainty, pair burden maps with resource maps

**Notes:** Spatial epidemiology maps *people*, not just polygons. Pause before
publishing.

---

## Slide 78 — Practical exercise (Module 6)

1. Classify `incidence_rate` into **quartiles** (Q1–Q4); how many countries per group?
2. Recompute nearest-facility distance; report the **median** and **maximum** in km
3. *(Challenge)* Which country is farthest from a facility? Use `which.max()` and print its name

[Exercise]

---

## Slide 79 — Module 6 recap / checklist

- [ ] Overlaid disease burden with facility locations
- [ ] Classified burden into meaningful groups
- [ ] Computed nearest-facility distances
- [ ] Mapped accessibility
- [ ] Sketched a reproducible report
- [ ] Considered privacy, MAUP, ecological fallacy, and equity

---

# Closing

## Slide 80 — The full workflow, end to end

You have completed a full spatial epidemiology workflow **in R only**:

1. Imported and joined spatial and health data
2. Created static, interactive, and multi-layer thematic maps
3. Detected global and local spatial autocorrelation
4. Built OLS and Geographically Weighted Regression models
5. Trained and evaluated Random Forest, XGBoost, and SVR via `mlspatial`
6. Integrated disease burden with service availability and generated reproducible reports

---

## Slide 81 — The honest bottom line

- Every modelling result in this tutorial is a **null** — by design
- The covariates were simulated noise, and the pipeline correctly found nothing
- What you learned is the *standard of scrutiny*:
  - screen for **data leakage**
  - model **rates**, not counts
  - judge spatial models by **AICc**
  - never trust **in-sample** scores
- Apply real data with this same discipline

---

## Slide 82 — Next steps

- Replace the built-in Africa data with **Ghana Health Service district-level data**
- Download real WorldClim rasters via `geodata::worldclim_global()`
- Explore the `mlspatial` vignettes: `vignette("mlspatial")`
- Combine modules into a parameterised district report

---

## Slide 83 — Reference and thanks

Azeez, A., & Noel, C. (2025). *Predictive Modelling and Spatial Distribution of
Pancreatic Cancer in Africa Using Machine Learning-Based Spatial Model.*
Zenodo. https://doi.org/10.5281/zenodo.16529986

Thank you — Ghana R User Community & AfreDAC Collaboration

---

# Appendix — suggested figure inventory

These images already exist in the repo and map onto specific slides:

| Figure | Used on |
|----|----|
| `Spat_epi.png` | Slide 1 (title / cover) |
| `africa_disease_labelled.png` | Slide 25 (quick thematic map) |
| `africa_incidence_ggplot.pdf` | Slide 27 (ggplot choropleth) |
| `africa_disease_panel.png` | Slide 29 (overlay / multi-panel) |
| `africa_hotspot_panel.png` | Slide 41 (LISA + Gi\* clusters) |

Additional figures to generate live (not yet saved): GWR coefficient map and
`Local_R2` (Module 4), variable-importance plots and the observed-vs-predicted
diagnostic (Module 5), and the accessibility map (Module 6).
