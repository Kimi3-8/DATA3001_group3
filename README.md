# DATA3001 Group 3 — Gulf Stream Drifter Forecasting

This repository contains [gulf_stream_eda_with_part3.ipynb](gulf_stream_eda_with_part3.ipynb), an end-to-end notebook for GDP hourly drifter data processing, exploratory analysis, baseline forecasting, and Part 3 gradient boosting.

The study window is **25–45°N, 80–50°W**. Forecasts use **24 hours of history** and predict positions **1, 24, and 168 hours** ahead. Errors are great-circle distances in kilometres.

## 1. Set up the environment

You need Python, Git (if cloning), internet access for package installation and initial data access, and a writable working directory. The full-data workflow can require substantial memory, disk space, and processing time; runtime depends on the machine and network.

Clone the repository:

```bash
git clone https://github.com/Kimi3-8/DATA3001_group3.git
cd DATA3001_group3
```

Alternatively, download and extract the repository ZIP and open a terminal in the extracted folder.

Create a virtual environment:

```bash
# macOS / Linux
python3 -m venv .venv
source .venv/bin/activate
```

```powershell
# Windows PowerShell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

Install JupyterLab and the packages imported by the notebook:

```bash
python -m pip install --upgrade pip
python -m pip install jupyterlab ipykernel clouddrift numpy pandas matplotlib pyarrow xarray scipy scikit-learn joblib
python -m ipykernel install --user --name data3001-group3 --display-name "Python (DATA3001 Group 3)"
```

Dependencies are not version-pinned in this repository, so different environments or source-data updates may produce different numerical results. Part 3 prints package versions, and the final summary records relevant versions and run metadata.

## 2. Open and run the notebook

From the repository folder, start JupyterLab:

```bash
jupyter lab gulf_stream_eda_with_part3.ipynb
```

1. Select the **Python (DATA3001 Group 3)** kernel.
2. The first code cell contains an active `%pip install ...` command. If dependencies were installed above, you may comment out that line. If you use it to install or change packages, restart the kernel before continuing.
3. Review the parameters in **Section 1: Set parameters**.
4. Run all cells **from top to bottom**, in the same kernel. In JupyterLab, use **Run → Run All Cells**, or run cells sequentially with **Shift+Enter**.
5. Wait for extraction, baseline evaluation, model training, and the final summary export to finish. Save the notebook to retain the newly generated outputs.

This is a Jupyter notebook: do not run it as a Python script with `python filename.ipynb`.

### Data access

The notebook obtains GDP hourly data through:

```python
from clouddrift.datasets import gdp1h
ds = gdp1h()
```

No input dataset is bundled in the repository. The notebook extracts the study region and creates its own Parquet files and intermediate inputs; there is no need to copy the previous repository's files.

Completed extraction blocks are cached under the current run directory and reused on subsequent runs with the same extraction configuration. Internet access may still be needed to open the source dataset.

### Settings for a complete run

Keep these defaults for the full workflow:

```python
MAX_OBSERVATIONS = None
EXPORT_FULL = True
HISTORY_HOURS = 24
HORIZONS = (1, 24, 168)
```

- `MAX_OBSERVATIONS = None` scans the full source record before regional filtering.
- `EXPORT_FULL = True` writes `gulf_stream_full.parquet`, which Part 3 requires.
- The history and forecast horizons are also assumed in later Part 3 code. Changing only the initial settings is not sufficient to change the whole experiment.

Extraction defaults to `CHUNK_SIZE = 250_000` and `MAX_WORKERS = 8`. Reducing `MAX_WORKERS` can reduce concurrent extraction pressure, but later stages still load large regional tables into memory.

For an extraction-only smoke check, you can temporarily set `MAX_OBSERVATIONS` to a positive limit. This scans a prefix of the source, writes a `partial_<signature>` directory, and may contain too few regional tracks for subsequent analysis or training. Partial runs must not be treated as full regional results. Restore `None` for the complete analysis.

## 3. Execution order

| Stage | What it produces |
| --- | --- |
| Data processing and EDA | Regional observations, quality checks, continuous segments, descriptive tables, and figures |
| Baseline forecasting | Present-velocity and spatial-mean-flow evaluation, error curves, and comparison tables |
| Initial results summary | EDA and baseline summaries retained for the final report |
| Section 15.0 | Part 3 drifter split, test origins, and future target positions |
| Sections 15.1–15.12 | Feature construction, validation, gradient-boosting models, test predictions, uncertainty estimates, and figures |
| Final code cell | Verified current-run report tables and a complete Markdown results summary |

**Do not start directly at Part 3 in a fresh kernel.** It depends on files, functions, and variables created earlier. Section 15.0 checks that its test origins and targets match the baseline evaluation.

Part 3 releases some large EDA dataframes from memory. To revisit earlier cells that use those dataframes, restart and run from the top.

## 4. Generated outputs

When launched from the repository folder, outputs are written under:

```text
member2_outputs/
└── full_<signature>/
    ├── cache/                         # Regional extraction blocks
    ├── figures/                       # EDA and baseline figures
    │   └── part3/                     # Gradient-boosting report figures
    ├── gulf_stream_full.parquet       # Required by Part 3
    ├── gulf_stream_model_sample.parquet
    ├── manifest.json
    ├── baseline_results.csv
    ├── split_drifters.csv
    ├── member3_origins.parquet
    ├── member3_targets.parquet
    ├── final_summary/                 # EDA/baseline summary
    ├── part3_outputs/                 # Models, predictions, and evaluation tables
    └── final_report_summary/
        ├── DATA3001_COMPLETE_RESULTS_SUMMARY.md
        ├── table_index.csv
        ├── table_*.csv
        └── provenance.json
```

This is a selection of the generated files. The signature is calculated from the extraction configuration and source metadata; use the `RUN_DIR` printed by your run instead of assuming a fixed folder name.

Part 3 saves model pairs as `gb_1h_east_north.joblib`, `gb_24h_east_north.joblib`, and `gb_168h_east_north.joblib`, and test predictions as `official_test_gb_predictions.parquet` inside `part3_outputs/`.

The final cell prints **“Verified and exported … tables”** after its consistency checks pass. Use the tables in `final_report_summary/` for the current run's report. The notebook's old report values are comparison references, not guaranteed results of a new run.

## 5. Troubleshooting

| Problem | What to do |
| --- | --- |
| `ModuleNotFoundError` | Install the missing dependency in the selected kernel's environment, then restart the kernel. |
| Data access or extraction fails | Check connectivity and the original exception. Rerun after resolving the issue; completed extraction blocks are retained. |
| `No regional observations in this scan` | If using a partial scan, increase the limit or restore `MAX_OBSERVATIONS = None`. |
| Part 3 cannot find `gulf_stream_full.parquet` | Keep `EXPORT_FULL = True` and rerun the preceding processing/export cells. |
| Missing split, origins, or targets | Run Section 15.0 after the preceding sections, in the same kernel. |
| Undefined variables or final-summary missing-input errors | Restart the kernel and execute the notebook sequentially from the top. |
| Kernel exits or runs out of memory | Use a machine/session with more memory. Lowering extraction workers may help extraction, but does not eliminate later memory requirements. |
| Results differ from old report values | Check data/configuration and package versions; use current-run outputs. Investigate failed assertions before reporting results. |

Part 3 defaults to `REBUILD_CACHE = True`, rebuilding its fitting/validation feature cache. Keep this enabled when changing data or feature logic.

## Validation scope

These instructions were checked against the notebook's source and file dependencies. Adding this README did not rerun the full data extraction or model training.
