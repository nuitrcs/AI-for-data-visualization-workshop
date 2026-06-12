# Workshop Datasets

This folder holds the example datasets for our workshop, along with a starter prompt and source links for each.

Remember the workflow:

> **Draft** (prompt → figure) → **Code** (ask for the standalone script) → **Run** (execute it yourself) → **Refine** (iterate to publication quality)

- **Parts 1–3** use a single shared dataset: **`CPS_merged.csv`** (the scatter plot).
- **Part 4** is open — pick any dataset below that's closer to your own work, or bring your own data.

You can use whichever LLM you like (Claude, ChatGPT, Gemini, Copilot — free tiers are fine). We demo in **Python**, but the same steps work in **R**: just ask the model for a "standalone R script" instead.


## Getting the data into your code

Once the LLM hands you a script, you need to point it at the data. Three ways:

**1. Local Python** — keep the data file next to your script:
```python
import pandas as pd
df = pd.read_csv("CPS_merged.csv")
```

**2. Google Colab** — upload via the Files panel, or read straight from a URL:
```python
import pandas as pd
df = pd.read_csv("https://raw.githubusercontent.com/<user>/<repo>/main/data/CPS_merged.csv")
```

**3. Let the LLM wire it up** — just tell it where the data lives:
> The data is at this raw GitHub URL: `<url>`. Load it directly from there.

> **Tip:** use the **`raw.githubusercontent.com`** link (GitHub's **"Raw"** button), *not* the `github.com` page URL — the page URL returns HTML, not your data.


## 1. `CPS_merged.csv` — scatter plot *(Rounds 1–3)*

**What it is:** Chicago Public Schools data — one row per school, with many attributes per school. A real, slightly messy CSV with **~650 rows** and **many columns**.

**The figure:** a scatter plot of **school mobility rate vs. college enrollment rate**, with a fitted trend line and a 1σ confidence band.

**Source:** built by joining two CPS datasets from the prompt-engineering workshop repo — <https://github.com/ageller/promptEngineering>

**⚠️ Watch for:** several columns have very **similar names** (e.g., more than one "college enrollment" field). Tools tend to pick one **silently** — note which one yours chose, and check it against what you intended.

**Starter prompt (rough draft):**
```
I will upload a data file. Each row is a different school, and each column is a
different attribute of that school. Create a scatter plot from these data showing
the mobility percentage vs. the college enrollment percentage. Include a trendline
fit to these data, and show the 1 sigma confidence interval for that line. Provide
the figure in a PDF format that I can download and include in my research paper.
```

**Refinement prompt (Round 3 — polish toward publication quality):**
```
I'd like to make a few changes to the look of the plot:
- make the points black but with a slight transparency so I can see overlapping points
- make the fit line blue, and the 1 sigma interval a semi-transparent blue
- remove the legend
- remove the top and right axes
Please recreate the figure with these changes and provide an updated PDF to download.
```


## 2. `financial_sample.xlsx` — multi-line trend plot

**What it is:** a financial sample with products sold across several countries, segments, and discount bands over time.

**The figure:** profit over time, compared across products and broken down by country, for a chosen segment and discount bands.

**Source:** Microsoft Power BI "Financial Sample" workbook — <https://learn.microsoft.com/en-us/power-bi/create-reports/sample-financial-download>

**⚠️ Watch for:** models love to over-engineer this one — extra aggregation by month, dropping single-point series so lines vanish, or cramming everything into one unreadable legend. Compare the figure to the prompt carefully, and read the code to see what it filtered.

**Starter prompt:**
```
I will upload a data file that contains financial information about products sold in
a variety of countries. I want you to create a figure that compares the profit over
time for each product, broken down by country and showing both the high and low
discount bands and only for the Government segment. Create the figure in a PDF format
that I can download and include in my research paper.
```


## 3. `chinook.db` — bar chart from a database

**What it is:** the **Chinook** sample SQLite database — a fictional digital media store with **11 related tables** (customers, invoices, tracks, genres, etc.).

**The figure:** a bar chart comparing the **percentage of tracks sold to US customers in each genre** — which requires the model to write SQL (or use pandas) to join and aggregate across tables first.

**Source:** <https://github.com/lerocha/chinook-database/releases>
**Table reference:** <https://www.sqlitetutorial.net/sqlite-sample-database/> (note: some table names are slightly outdated)

**⚠️ Watch for:** some chat tools won't accept a `.db`/`.sqlite` upload at all. If yours refuses, that's a useful finding — try another tool, or ask it to write a script you run locally.

**Starter prompt** (paste the table summary so the model knows the schema):
```
I will upload a sqlite database file that contains information about a digital media
store. I want you to create a figure that compares the percentage of tracks sold to
US customers in each genre. Create the figure in a PDF format that I can download and
include in my research paper. Below I will copy a summary of the contents of each
table in the database file.

The database contains 11 tables, as follows:
- Employee: employee data (id, name, ...), with a ReportsTo field for hierarchy
- Customer: customer data
- Invoice & InvoiceLine: invoice header data and invoice line items
- Artist: artist id and name
- Album: albums; each album belongs to one artist (an artist can have many albums)
- MediaType: media types (e.g., MPEG audio, AAC audio)
- Genre: music genres (rock, jazz, metal, ...)
- Track: songs; each track belongs to one album
- Playlist & PlaylistTrack: playlists and the many-to-many link between playlists and tracks
```


## 4. France map — `communes.geojson` + `data_on_french_states.csv` — choropleth

**What it is:** two files that must be joined — a GeoJSON with commune geometry and a CSV with the number of restaurants per location.

**The figure:** a choropleth map of the **number of restaurants in Southern France**.

**Join key:** match the `code` column in the GeoJSON to the `depcom` column in the CSV. The value to map is `nb_equip` (number of restaurants).

**Sources:**
- GeoJSON: <https://raw.githubusercontent.com/gregoiredavid/france-geojson/master/communes.geojson>
- CSV: <https://raw.githubusercontent.com/holtzy/R-graph-gallery/master/DATA/data_on_french_states.csv>
- Reference example (in R): <https://r-graph-gallery.com/327-chloropleth-map-from-geojson-with-ggplot2.html>

**⚠️ Watch for:**
- The CSV uses a **semicolon (`;`) delimiter**, not a comma.
- The GeoJSON is **large (~tens of MB)** — it may exceed a chat tool's upload limit. If so, give the model the **raw URL** instead and let it fetch the file in code.
- You may need to **aggregate by `depcom`** before plotting (a good model will notice this on its own).
- Some tools reject a `.geojson` extension but accept the same file renamed to `.json`.

**Starter prompt (upload version):**
```
I will upload two data files that contain information about restaurants in France.
The geojson file has location information and the csv file has numerical information.
To connect the geojson and the csv file, match the "code" column from the geojson to
the "depcom" column in the csv file. I am interested in the number of restaurants,
which is in the "nb_equip" column of the csv file. I want you to create a figure that
shows the number of restaurants in Southern France on a map. Create the figure in a
PDF format that I can download and include in my research paper.
```

**Starter prompt (URL version — if the GeoJSON is too big to upload):**
```
I will upload a csv data file and I want you to access a geojson file at:
https://raw.githubusercontent.com/gregoiredavid/france-geojson/master/communes.geojson
The geojson file has location information and the csv file has numerical information.
To connect them, match the "code" column from the geojson to the "depcom" column in
the csv file. I am interested in the number of restaurants, in the "nb_equip" column
of the csv. Create a figure showing the number of restaurants in Southern France on a
map, in a PDF format I can download and include in my research paper.
```


## 5. `density.vtk` — 3D volume rendering

**What it is:** a 3D grid of density values from an astrophysics simulation snapshot (a **128 × 128 × 128** grid, ~8 MB). The clean way to render this in Python is the `pyvista` library (~5–7 lines of code).

**The figure:** a 3D volume rendering where color and opacity are driven by the density value in each cell.

**Source:** provided in this folder (originally used in ParaView workshops).

**⚠️ Watch for:** this is the **most ambitious** task and the easiest to get subtly wrong. Several tools can't render 3D at all and will instead hand you a script; some hand-roll their own ray tracing instead of using `pyvista`, producing long code that's hard to verify. Treat the output skeptically and check it carefully.

**Starter prompt:**
```
I will upload a data file that contains a 3D grid of density. I want you to create a
volume rendering of this dataset with color and opacity defined by the density value
in a given grid cell. Create the figure in a PDF format that I can download and
include in my research paper.
```

**Follow-ups (if needed):**
```
Can you create a single projected 3D (cube) volume rendering view of the data?
```
```
This view angle is strange. Please change the angle to something close to:
- elevation above horizontal = 28 degrees
- azimuth around z axis = 215 degrees
```


## Before you trust any LLM figure

1. **Does it show what you asked for?** Compare the figure to your prompt.
2. **Did it use the right data?** Check axis labels and column names. Any unexpected filtering or aggregation?
3. **Does it make intuitive sense?** Sanity-check the trend against what you know.
4. **★ Read and run the code yourself.** The most important step — re-run it to confirm it's reproducible, and read it to confirm it did what you intended.