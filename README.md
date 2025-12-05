<<<<<<< HEAD
# FlightEDA

A Julia CLI to load, clean, engineer, and plot 2024 US flight delay data. One command runs the full pipeline; config files control paths, thresholds, sampling, and logging.

## What it does
- **Phase 1 (load)**: read raw CSV, validate schema, print shape/head/tail/describe.
- **Phase 2 (clean + features)**: drop corrupted rows, fill delay/cancellation fields, convert dates, derive `hour_of_day`, `time_of_day`, `is_delayed`, `route`, and write a cleaned CSV.
- **Phase 3 (plots)**: generate PNGs (delay distribution, airline/airport volumes, cancellations, delay by hour/airline, worst routes, heatmap, facets) into `plots/`.
- **Smoke**: quick schema/feature check on sample or cleaned data.

## Prereqs
- Julia 1.10+ (ensure `julia` is in your system's PATH).
- Run once to install deps:
```bash
julia --project=. -e 'using Pkg; Pkg.instantiate()'
```

## Run commands (single CLI)
- All phases (sample config):
```bash
julia --project=. bin/flight_eda.jl --phase all --config config/eda_config_sample.toml
```
- Individual phases:
```bash
julia --project=. bin/flight_eda.jl --phase 1 --config config/eda_config.toml   # load/describe
julia --project=. bin/flight_eda.jl --phase 2 --config config/eda_config.toml   # clean/features
julia --project=. bin/flight_eda.jl --phase 3 --config config/eda_config.toml   # plots
julia --project=. bin/flight_eda.jl --phase smoke                               # smoke check
```
- Config override: set `EDA_CONFIG=/path/to/custom.toml`
- Log level override: `--log-level debug|info|warn|error`
- Interactive menu (phases, stats, plot list):
```bash
julia --project=. bin/flight_eda_menu.jl
```

## Install dependencies
- With `Project.toml`/`Manifest.toml` present, install everything with:
```bash
julia --project=. -e 'using Pkg; Pkg.instantiate()'
```
- If interactive widgets are used, ensure WebIO’s Jupyter extension is installed (run once):
```bash
julia --project=. -e 'using WebIO; WebIO.install_jupyter_nbextension()'
```

## Notebook
- Launch Jupyter from the project root (`--project=.`) so paths resolve correctly:
```bash
julia --project=. -e 'using IJulia; notebook()'
```
- Open `notebooks/flight_eda_notebook.ipynb`.
- The notebook resolves data paths relative to the project root and includes a dropdown widget; if widgets don’t render, rerun the WebIO install command above.

## Config files
- `config/eda_config.toml` — main paths, plot bounds, sample size, log level.
- `config/eda_config_sample.toml` — uses the sample CSV for fast runs.
- Plot controls: `route_min_flights` (filter low-volume routes) and `route_top_n` (how many worst routes to show).

## Outputs
- Cleaned CSV: `data/flight_data_2024_cleaned.csv` (configurable).
- Plots: PNGs in `plots/` (`1_hist_arrival_delay.png`, …, `12_facet_airline_hour.png`).

## Tests and smoke
- Full tests: `julia --project=. -e 'using Pkg; Pkg.test()'`
- Smoke shortcut: `julia --project=. test/smoke_test.jl`

## Notes
- Plotting uses Plots.jl (GR backend by default). If GR errors on your system, set `ENV["GKSwstype"]="nul"` or switch backend in `src/FlightEDA/Plotting.jl`.
- CLI checks for `Project.toml`/`Manifest.toml` and prompts to instantiate if missing.

## Data
- Expects raw `data/flight_data_2024.csv`. Sample config points to the sample file.
- Data dictionary: `data/flight_data_2024_data_dictionary.csv`.

## License / Use
Provided “as is” for educational, non-commercial use. Keep large data files out of version control. See `docs/FlightEDA_runbook.pdf` for a one-page quickstart.
#
=======
# Flight Delay EDA (Julia)


Analyzing Flight Delays in U.S. Domestic Carriers (Julia)

This project provides a descriptive analysis of flight delays among U.S. domestic carriers. The analysis was conducted using the Julia programming language.

The primary goal is to understand historical trends , rather than to perform real-time predictions. The insights gathered from this analysis are intended to provide a foundation for future work, such as building predictive models, guiding automation projects, or developing AI-based planning tools


Dataset Description:
 
The analysis is based on the Flight Delay Dataset found on Kaggle, which originates from the BTS TranStats On-Time Performance database.
Scope: The dataset contains flight-level data from numerous airlines and airports throughout 2024.
Size: It is a comprehensive dataset with over 7 million rows and 35 columns.
Key Features: Includes information on Airline, Flight Number, Origin, Destination, Scheduled & Actual Times, and Delay (Min).
Details: It provides information on scheduled and actual flight times, as well as details on delays, cancellations, diversions, and airport distances.
Dataset Source: https://www.kaggle.com/datasets/hrishitpatil/flight-data-2024 


Exploratory data analysis of 2024 US flight delays using Julia. designed as a reproducible, scripted pipeline with an interactive notebook companion. The repo includes both the **small sample** and the **full raw/cleaned datasets** via Git LFS.

## Research questions
- When are delays worst? (hour/time-of-day)
- Which airlines and routes drive the most delays?
- What causes dominate delays and cancellations?
- How much worse are the worst routes vs the network average?

## Highlights
- **Beginner-Friendly**: The project is structured as a clear, three-phase pipeline, making it easy to follow the data's journey from raw to insight.
- **Reproducible Environment**: Uses a Julia project environment (`Project.toml`) so anyone can replicate the exact setup with a single command.
- Three-phase pipeline: load/inspect → clean/engineer → plot/interpret
- Fast CSV loading + feature engineering (`hour_of_day`, `time_of_day`, `is_delayed`, `route`, etc.)
- Saved visuals (PNG) for quick review in `plots/`
- Notebook that mirrors the pipeline for interactive exploration
- Data dictionary for column definitions


## Methodology & checks
- Phase 1: load + basic profiling (shape, head/tail, describe).
- Phase 2: cleaning + features:
  - Drop rows missing essential delay data (unless the flight was cancelled).
  - Fill in missing values (`NA`s) for delay reasons and cancellation codes with sensible defaults.
  - Convert text dates into a proper `Date` format for calculations.
  - Engineer new features like `hour_of_day`, `time_of_day`, `is_delayed`, and `route`.
- Phase 3: Plot generation (PNG to `plots/`), using headless PyPlot for reliability.
- Data dictionary: `data/flight_data_2024_data_dictionary.csv` holds column definitions—reference it when interpreting plots.

## Script Walkthrough (What each phase does)
- `scripts/phase1_load_inspect.jl`: loads `data/flight_data_2024.csv`, prints shape, head/tail, and `describe()` for a quick quality scan.
- `scripts/phase2_clean_engineer.jl`: cleans the raw file, drops rows missing both dep/arr delay (unless cancelled), fills delay-reason NAs with 0, fills cancellation code with `Not_Cancelled`, parses dates, derives `hour_of_day`, `time_of_day`, `is_delayed`, `route`, `is_weekend`, then writes `data/flight_data_2024_cleaned.csv`.
- `scripts/phase3_eda_plots.jl`: reads the cleaned file and generates all PNGs into `plots/` (histograms, busiest airports, delay by hour/airline, worst routes, heatmap, facets).
- `scripts/smoke_test.jl`: miniature end-to-end run on the sample to sanity check the pipeline.


## Reproducibility
1) **Install Git LFS**: Run `git lfs install` once on your machine. This tool is needed to download the large data files (over 1 GB) stored in this repository. Without it, you'll get small, unusable pointer files.
2) **Install Dependencies**: Run the command below from the project's root folder. It tells Julia to look at `Project.toml` and download all the necessary packages (like `DataFrames` and `CSV`).
   ```bash
   ./julia-1.10.5/bin/julia --project=. -e 'using Pkg; Pkg.instantiate()'
   ```
3) Choose data:
   - **Sample (fast)**: To run the pipeline quickly on a small subset of data, copy the sample file to the name the script expects:
     ```bash
     cp data/flight_data_2024_sample.csv data/flight_data_2024.csv
     ```
   - **Full**: use the LFS-fetched `data/flight_data_2024.csv`
4) Run scripts in order (Phase 1 → 2 → 3) from repo root.
5) View outputs in `plots/` and re-run as needed after data changes.


## Dataset
- Included: `data/flight_data_2024_sample.csv` (small slice), `data/flight_data_2024_data_dictionary.csv`, **and full files** `flight_data_2024.csv` and `flight_data_2024_cleaned.csv` stored via Git LFS.
- After cloning, run `git lfs pull` to download the large data files.

## Repository Layout
- `scripts/phase1_load_inspect.jl` — load and profile the raw CSV
- `scripts/phase2_clean_engineer.jl` — cleaning + feature engineering, writes cleaned CSV
- `scripts/phase3_eda_plots.jl` — generates PNG plots into `plots/`
- `scripts/smoke_test.jl` — tiny end-to-end check using the sample
- `notebooks/flight_eda_notebook.ipynb` — interactive version of the pipeline
- `plots/` — saved figures
- `data/` — sample CSV + data dictionary (add full data here if available)

## Quick Start (sample data)
1) Install Julia 1.10+ (portable binary included at `./julia-1.10.5/` if you prefer).
2) **Install Project Dependencies**: Open a terminal in the project's root directory and run this command. It tells Julia's package manager (`Pkg`) to find the `Project.toml` file and install all the listed packages. You only need to do this once.
```bash
./julia-1.10.5/bin/julia --project=. -e 'using Pkg; Pkg.instantiate()'
```
3) Use the sample dataset by copying it to the expected raw name:
```bash
cp data/flight_data_2024_sample.csv data/flight_data_2024.csv
```
4) Run the pipeline from the repo root:
```bash
./julia-1.10.5/bin/julia --project=. scripts/phase1_load_inspect.jl
./julia-1.10.5/bin/julia --project=. scripts/phase2_clean_engineer.jl
./julia-1.10.5/bin/julia --project=. scripts/phase3_eda_plots.jl
```
For a fast check, use: `./julia-1.10.5/bin/julia --project=. scripts/smoke_test.jl`.

## Notebooks
- `notebooks/flight_eda_notebook.ipynb` mirrors the scripted phases and defaults to the sample data for speed. Run `Pkg.instantiate()` once inside, then execute cells in order. Update the file paths in the first code cell if you have the full dataset.

## Plot guide
- `1_hist_arrival_delay.png` — arrival delay distribution; report on-time share and tail behavior.
- `2_flights_by_airline.png` — volume by airline; note which carriers dominate traffic.
- `3_busiest_airports.png` — top airports by departures/arrivals.
- `4_cancellation_reasons.png` — mix of cancellation codes.
- `5_delay_by_hour.png` — average delay vs scheduled hour; identify peak windows.
- `6_delay_by_airline.png` — mean delays by carrier.
- `7_boxplot_by_airline.png` — distribution spread per carrier; highlight outliers.
- `8_scatter_dep_arr.png` — departure vs arrival delay relationship.
- `9_stacked_delay_reasons.png` — delay composition over time or category.
- `10_worst_routes.png` — worst-performing routes; great for “actionable” summary.
- `11_heatmap.png` — correlations between delay types; point out the strongest links.
- `12_facet_airline_hour.png` — delay by hour faceted by airline; compare patterns.

Use the saved plots to craft a short results paragraph in your report. Update with numbers from your full-data run if available.


## Results (What the plots show)
Saved PNGs in `plots/` include:
- Distribution of arrival delays and on-time performance
- Flights by airline and busiest airports
- Cancellation reasons and delay breakdowns (carrier, weather, NAS, security, late aircraft)
- Delay patterns by hour of day and by airline
- Route-level delay severity (worst routes), correlation heatmap, and scatter/facet views

## Limitations & notes
- Sample data is illustrative; use the full dataset for final numbers.
- No causal claims—visual EDA only. Join with weather/operational data if you need causal insight.


## Reproducibility Notes
- All plots are deterministic for the same input file. Re-run scripts or the notebook to regenerate figures after data changes.
>>>>>>> 7c0fcdc95f78aedb642bec9f3d29933d450b335c
