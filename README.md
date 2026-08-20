# Solar-Assisted Atmospheric Water Generation — Saudi Arabia

Analysis code for a study of solar-powered atmospheric water generation (AWG) across Saudi
Arabia. The work simulates hour-by-hour condensation from a power-constrained condensation
device driven by real meteorological records, optimizes airflow and energy dispatch against
that hourly climate, trains a machine-learning surrogate of the optimal control surface, and
upscales the result to a national station network.



> **Manuscript:** *Solar-Assisted Atmospheric Water Generation: Climate-Driven Simulation,
> Optimization, and Feasibility Assessment for Dammam and 33 Stations Across Saudi Arabia*
> — DWT-D-26-01134, under revision at *Desalination and Water Treatment*.
> Author: Saleh A. Aloraini, Qassim University.

---

## What's in this repository

| File | Description |
|---|---|
| `AWG_Saudi_Arabia_Full_Analysis.ipynb` | All seven working notebooks merged into one, 230 cells, outputs preserved as executed |
| `README.md` | This file |

The notebook is a **merge of seven separate notebooks** written over the course of the study,
concatenated in the order the analysis was carried out. Each part opens with a divider cell
naming the notebook it came from. Outputs are kept, so every figure and printed result is
readable without re-running anything.

> **Note on viewing:** at ~10.5 MB the notebook exceeds GitHub's in-browser rendering limit and
> will show as "too big to display." It opens normally in Jupyter, JupyterLab, VS Code and
> Colab. To read it in a browser without cloning, paste the file URL into
> [nbviewer](https://nbviewer.org/).

---

## Notebook contents

| Part | Originally | Cells | What it does |
|---|---|---|---|
| **1 — Data acquisition** | `Meteostat_Saudi_Downloader_2000_2025.ipynb` | 2 | Bulk download of hourly Meteostat records for the Saudi station network, 2000–2025 |
| **2 — Manuscript analysis** | `01_manuscript_analysis_CLEAN.ipynb` | 28 | The curated pipeline behind the paper: idealized parametric model, hour-resolved psychrometric optimization for Dammam 2024, optimal vs. fixed airflow, solar/battery dispatch, battery capacity sweep, joint T–RH–airflow behaviour, ML surrogate, national upscaling and maps. **Figures 1, 4–14; Tables 1–2** |
| **3 — Revision analyses** | `02_revision_analyses.ipynb` | 36 | Analyses added in response to peer review: component-level energy model with COP, validation against published AWG performance, dispatch optimality vs. an LP benchmark, closed-loop battery energy audit, parameter sensitivity and Sobol indices, uncertainty quantification, multi-year ERA5, surrogate cross-validation with climatic holdout, station pipeline and kriging, techno-economic assessment |
| **4 — Revision round 1** | `AWH_Revision.ipynb` | 10 | Kriging variance maps for spatial-interpolation uncertainty; battery-capacity asymptote |
| **5 — Revision round 2** | `AWG_Revison2.ipynb` | 16 | Surrogate performance by temperature–humidity regime, definition and QC of the 33-station retained network, cubic vs. kriging comparison |
| **6 — Revision round 3** | `AWG_revision3.ipynb` | 9 | Station-by-station comparison of hourly Meteostat temperature and RH against ERA5 reanalysis, 2015–2024 |
| **Appendix A** | `Completed_Water_Condensation.ipynb` | 121 | The original working notebook, preserved verbatim for provenance |

**Parts 2–6 are the authoritative analysis.** Appendix A is included so that every number in the
paper can be traced to where it was first computed, but it contains superseded parameterizations,
abandoned drafts and duplicated cells — see [Caveats](#caveats-and-known-issues) before using
anything from it.

---

## Requirements

Python 3.10 or newer.

```bash
pip install numpy pandas scipy matplotlib seaborn scikit-learn tqdm \
            meteostat geopandas shapely xarray netCDF4 requests openpyxl \
            cdsapi pykrige SALib
```

Notes on the less common ones:

- `meteostat` — station data retrieval. Parts 1, 2, 5 and 6 pin or reinstall specific versions
  in-cell; the notebook was last run against `meteostat==1.7.6`.
- `geopandas` / `shapely` — national boundary masking for the maps. These pull in GDAL; on
  macOS `conda install -c conda-forge geopandas` is usually less painful than pip.
- `xarray` + `netCDF4` — reading ERA5 NetCDF in Parts 3 and 6.
- `cdsapi` — Copernicus Climate Data Store client, Parts 3 and 6 only.
- `pykrige` — kriging interpolation and variance maps, Parts 3–5.
- `SALib` — Sobol sensitivity indices, Part 3.

---

## Data sources and credentials

No raw data is redistributed here. Everything is retrieved from the original providers at run
time.

| Source | Used for | Access |
|---|---|---|
| [Meteostat](https://meteostat.net) | Hourly T and RH for Dammam and the national station network | Python package, no key. Licensed CC BY-NC — do not redistribute the raw records |
| [NASA POWER](https://power.larc.nasa.gov) | Hourly and daily surface solar irradiance (`ALLSKY_SFC_SW_DWN`) | REST API, no key |
| [Copernicus CDS](https://cds.climate.copernicus.eu) | ERA5 reanalysis for validation and the multi-year extension | **Requires a free account and an API key** |
| [Natural Earth](https://www.naturalearthdata.com) | 1:110m Admin-0 country boundary and ocean vectors for masking | Public domain, downloaded in-cell |

### Copernicus CDS key

Parts 3 and 6 need a CDS API key. Register at the link above, then export it rather than
pasting it into a cell:

```bash
export CDSAPI_KEY="your-key-here"
export CDSAPI_URL="https://cds.climate.copernicus.eu/api"
```

The cells read `CDSAPI_KEY` from the environment. **Do not commit a key to this repository** —
notebooks are plain JSON and anything pasted into a cell is committed with it.

---

## Running the notebook

The parts were originally run as separate sessions, so the merged notebook is **not** guaranteed
to execute cleanly top to bottom in one pass. Two things to know:

1. **Order matters.** Part 1 writes the station CSVs that Part 2 reads. Part 2 writes
   `dammam_2024_opt_200W_capped.csv` and `dammam_2024_opt_200W_capped_and_fixed50.csv`, which
   Parts 3, 4 and 5 consume. Run 1 → 2 → 3 before 4–6.
2. **Imports repeat.** Each part re-imports what it needs, so you can also run a single part in
   a fresh kernel provided its input CSVs already exist in the working directory.

All file paths are relative to the notebook's working directory. A few cells in Appendix A
still reference Google Colab Drive paths (`/content/drive/MyDrive/QEC_research/...`) from when
that notebook was run in Colab; those cells will not run locally without editing the path.


---

## Citation

If you use this code, please cite the manuscript. A machine-readable `CITATION.cff` will be added
once the DOI is minted.

```bibtex
@article{Aloraini_AWG_Saudi,
  author  = {Aloraini, Saleh A.},
  title   = {Solar-Assisted Atmospheric Water Generation: Climate-Driven Simulation,
             Optimization, and Feasibility Assessment for Dammam and 33 Stations
             Across Saudi Arabia},
  journal = {Desalination and Water Treatment},
  note    = {Manuscript DWT-D-26-01134, under revision},
  year    = {2026}
}
```

## Acknowledgements

The author thanks the Deanship of Graduate Studies and Scientific Research at
[Qassim University](https://www.qu.edu.sa) for financial support (QU-APC-2026).

## License

Code in this repository is released under the MIT License. Meteorological data retrieved by
these notebooks remains under the terms of its original providers — in particular, Meteostat
records are CC BY-NC and must not be redistributed.
