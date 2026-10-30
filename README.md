# COMP6704 Group 15 — LASSO Regression via Convex Optimization

Code, frozen datasets, saved solutions, experiment records and figures for **A Certified Comparison of Four Algorithms**, COMP6704, PolyU, Fall 2026.

[Report on Overleaf](https://www.overleaf.com/project/6ae42b362cf96194f201aa5b)

## Team

| Member | Student ID | Implementation |
|---|---|---|
| CUI Chengming | 26026126r | Cyclic coordinate descent (CD) |
| LIU Haoming | 26023011r | ADMM |
| SUN Kexin | 24152919r | ISTA |
| ZHAO Zixin | 26104541r | FISTA |

## Problem and results

We minimize `P(beta) = ||X beta - y||² / (2n) + lambda ||beta||₁`. Features and response are centered using training statistics; feature scales also come from training data. All solvers start from zero and use the same independently evaluated relative primal–dual gap tolerance, `1e-6`.

The report uses **192 original runs** (12 configurations × 4 algorithms × one trace and three timing repeats) and **32 same-machine runs** (E01/E02 × 4 algorithms × four runs). The stored records contain **224 converged solutions and 7,721 unique checkpoints**. Saved-model verification recomputes certificates and prediction/support metrics from the actual coefficients and arrays.

| Same-machine median total time (ms) | ISTA | FISTA | CD | ADMM |
|---|---:|---:|---:|---:|
| E01: Diabetes, 10 features | 2.458 | 2.971 | **0.912** | 1.185 |
| E02: synthetic, 200 features | 3.988 | 4.694 | 9.481 | **2.663** |

These times include cold solver setup and online diagnostics. Original runs used different machines; cross-method runtime comparisons use only the common-machine supplement. Trace mode checks every update; timing mode checks every five updates. A CD update is a full coordinate sweep. Timings from new runs will vary with hardware and runtime conditions.

## Download

[Download the complete reproducibility package](Group15_Reproducibility.zip). It contains all source files, frozen arrays, saved models, result CSVs, figures and manifests in their original relative directory structure. The SHA-256 checksum is in `SHA256SUMS.txt`. Extract the ZIP before running the commands below.

## Quick start: reproduce the report analysis

Use **Python 3.12.x**. From a terminal:

```bash
git clone https://github.com/CCM0111/COMP6704_Group15.git
cd COMP6704_Group15
python3.12 -m zipfile -e Group15_Reproducibility.zip .
cd final_presentation
python3.12 -m venv .venv
.venv/bin/python -m pip install -r reproduce/requirements.lock
.venv/bin/python reproduce/integrate_results.py
.venv/bin/python reproduce/write_analysis.py
.venv/bin/python reproduce/make_experiment_figures.py
```

On Windows, create the environment with `py -3.12 -m venv .venv` and replace `.venv/bin/python` with `.venv\Scripts\python.exe`.

The six numerical/plotting dependencies are pinned to the versions used in the report. The frozen data are included: no dataset download, original ZIP archive, external working directory or presentation tool is required.

Expected integration output:

```json
{
  "total_summary_rows": 224,
  "original_rows": 192,
  "same_machine_supplement_rows": 32,
  "convergence_rows": 7721,
  "statuses": {"converged": 224},
  "all_saved_models_independently_recomputed": true
}
```

Integration checks source hashes and recomputes all saved models; it does not rerun optimization. It rebuilds `analysis/Merged_Results.csv`, `analysis/Convergence_Results.csv`, `analysis/results_summary.json` and `analysis/model_recomputation.json`. The next commands rebuild analysis notes and 12 PNG/SVG figure pairs in `slides/figures/experiment_figures/`. The `slides/` directory name is retained to match the original plotting script; this repository contains the report's computational materials.

## Run the solvers again

From `final_presentation/`, after installing the dependencies:

```bash
.venv/bin/python reproduce/run_comparison.py --output analysis/supplement-rerun
.venv/bin/python reproduce/integrate_results.py --supplement analysis/supplement-rerun
.venv/bin/python reproduce/write_analysis.py
.venv/bin/python reproduce/make_experiment_figures.py
```

This performs 32 fresh E01/E02 runs using byte-preserved copies of all four solvers and a common runner. Choose a new output name for each rerun: existing output directories are rejected. It records models, trace/timing CSVs, execution order, environment information and verification results. The original `analysis/supplement/` and `analysis/source_data/` remain available. The merge and plotting commands above use the new supplement, so the regenerated timings then describe your machine.

To restore the report's recorded analysis, run the three quick-start analysis commands again without `--supplement`. The original 192 runs are supplied as immutable records; the provided new-run command reproduces the 32-run focused comparison.

## Repository map

All paths below are relative to `final_presentation/`.

| Path | Contents |
|---|---|
| `reproduce/inputs/solvers/` | Original ISTA, FISTA, CD and ADMM implementations |
| `reproduce/library/common/` | Shared runner, certificate, metrics, data loading and environment capture |
| `reproduce/inputs/dev_assets/` | DIA, S100, S200, S400 arrays; preprocessing metadata; 12 configurations and reference solutions |
| `reproduce/input_manifest.json` | SHA-256 identities of frozen inputs |
| `analysis/source_data/` | 192 original result rows, trajectories, saved coefficients, configurations and environment records |
| `analysis/source_data/source_catalog.json` | Original evidence file hashes |
| `analysis/supplement/` | Report's 32-run same-machine comparison |
| `analysis/Merged_Results.csv` | Combined per-run metrics with evidence scope and source paths |
| `analysis/Convergence_Results.csv` | All recorded convergence checkpoints |
| `analysis/model_recomputation.json` | Independent saved-model checks |
| `analysis/Results_Summary.md` | Detailed numerical summary |
| `slides/figures/experiment_figures/plot_sources.json` | Figure source hashes, filters and run identifiers |

Historical environment and audit files retain their original path strings as provenance. Execution uses repository-relative inputs. Virtual environments and installed dependencies are not included.

## Report figure mapping

The report uses these files from `final_presentation/slides/figures/experiment_figures/`:

- `fig01_convergence_outer.png`: relative primal–dual gaps by outer update.
- `fig04_fista_oscillation.png`: FISTA objective oscillation.
- `fig05_shared_trace_time_E02.png`: same-machine E02 objective-reference difference over time.
- `fig06_shared_timing_E01_E02.png`: median timings and observed ranges.
- `fig07_regularization_S200.png`: prediction and support recovery over regularization.
- `fig09_within_host_dimension_scaling.png`: original within-host dimension scaling.
- `fig10_admm_residuals_and_gap.png`: ADMM residuals versus the common certificate.

The original data split, fixed lambda grid and solver parameters are preserved. The report studies optimization and regularization effects; the test set is not used to select a deployed model.
