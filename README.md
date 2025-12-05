# Flight Delay EDA (Julia)

Analyzing Flight Delays in U.S. Domestic Carriers (Julia)

This project provides a descriptive analysis of flight delays among U.S. domestic carriers. The analysis was conducted using the Julia programming language.

The primary goal is to understand historical trends , rather than to perform real-time predictions. The insights gathered from this analysis are intended to provide a foundation for future work, such as building predictive models, guiding automation projects, or developing AI-based planning tools

A Julia CLI to load, clean, engineer, and plot 2024 US flight delay data. One command runs the full pipeline; config files control paths, thresholds, sampling, and logging.


Dataset Description:
 
The analysis is based on the Flight Delay Dataset found on Kaggle, which originates from the BTS TranStats On-Time Performance database.
Scope: The dataset contains flight-level data from numerous airlines and airports throughout 2024.
Size: It is a comprehensive dataset with over 7 million rows and 35 columns.
Key Features: Includes information on Airline, Flight Number, Origin, Destination, Scheduled & Actual Times, and Delay (Min).
Details: It provides information on scheduled and actual flight times, as well as details on delays, cancellations, diversions, and airport distances.
Dataset Source: https://www.kaggle.com/datasets/hrishitpatil/flight-data-2024 

The repo includes both the **small sample** and the **full raw/cleaned datasets** via Git LFS.

## Prerequisites
- Julia 1.12+ (ensure `julia` is in your system's PATH).
- To set up the environment, run:
```bash
julia --project=. -e 'using Pkg; Pkg.resolve(); Pkg.instantiate()'
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
- With `Project.toml` and `Manifest.toml` present, you can ensure the environment is correctly installed and up-to-date by running:
```bash
julia --project=. -e 'using Pkg; Pkg.resolve(); Pkg.instantiate()'
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

# Plot guide
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

  

## License / Use
Provided “as is” for educational, non-commercial use. Keep large data files out of version control.
#