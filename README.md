# Data and figures for "Towards Optimal Routing Strategies for qLDPC codes on Silicon Spin Qubits"

This repository contains the data and the plotting notebook for arXiv:2611.02972, "Towards Optimal Routing Strategies for qLDPC codes on Silicon Spin Qubits". The authors are Merlin Incerti-Medici, Minh T. P. Nguyen, Stefano Bosco, James R. Wootton, and Daniel Bultrini. The paper simulates memory experiments of the [[144,12,12]] bivariate bicycle (Gross) code on spin qubit devices that shuttle qubits on a grid. It compares three shuttling strategies, two grids, many initial qubit layouts, and several shuttling noise models. With the files here, you can make the five data figures of the paper again from the simulation results. The code used for the simulations is included in hpc_code. 

## Contents

| Path | Description |
| --- | --- |
| `Paper_plots.ipynb` | Notebook that reads the data and writes the five data figures. The committed copy contains the outputs of a full run. |
| `data/` | Simulation results as CSV files. See [Data files](#data-files). |
| `figures/` | Output folder of the notebook. It contains the PDF files from the last run. |
| `hpc_code/` | Folder for `sqec.zip`, the simulation code as run on the high-performance computing (HPC) cluster. See [HPC code archive](#hpc-code-archive). |
| `pyproject.toml`, `uv.lock` | Python environment for [uv](https://docs.astral.sh/uv/). |
| `requirements.txt` | The same pinned packages for pip. |
| `.python-version` | Default Python version for uv (3.13). |

## Setup

The notebook needs Python 3.12 or 3.13. Python 3.14 also works, but then pip and uv must build numpy from source, and you need a C compiler. We pinned the package versions to the versions that made the paper figures (pandas 2.3.3, numpy 2.2.6, matplotlib 3.10.8, seaborn 0.13.2). Do not use pandas 3 or later.

### With uv

1. Install [uv](https://docs.astral.sh/uv/getting-started/installation/).
2. Clone the repository and go into it:

   ```
   git clone https://github.com/moth-quantum/SQEC.git
   cd SQEC
   ```

3. Create the environment from the lock file:

   ```
   uv sync --frozen
   ```

If Python 3.13 is not on your computer, uv downloads it.

### With pip

1. Make sure that Python 3.13 is on your computer.
2. Clone the repository and go into it (see the uv steps above).
3. Create and activate a virtual environment, then install the packages:

   ```
   python3.13 -m venv .venv
   source .venv/bin/activate
   pip install -r requirements.txt
   ```

On Windows, create the environment with `py -3.13 -m venv .venv` and activate it with `.venv\Scripts\activate`.

## Run the notebook

Run the notebook from the root folder of the repository. The notebook uses relative paths to `data/` and `figures/`.

To open the notebook in JupyterLab, use one of these commands:

```
uv run jupyter lab Paper_plots.ipynb      # with uv
jupyter lab Paper_plots.ipynb             # with pip, in the activated environment
```

Then select "Run All Cells" in the "Run" menu.

To run the notebook without a browser, use this command:

```
uv run jupyter nbconvert --to notebook --execute --inplace Paper_plots.ipynb
```

With pip, use the same command without `uv run`. A full run takes less than one minute on a laptop. The notebook writes the PDF files to `figures/`. If this folder does not exist, the notebook makes it.

## Figures

| Notebook output | Paper figure | File in the arXiv source |
| --- | --- | --- |
| `figures/Comparison_plot_main.pdf` | Figure 2 | `f/Comparison_plot_main_publication.pdf` |
| `figures/Initial_layout_main.pdf` | Figure 3 | `f/Initial_layout_main_publication.pdf` |
| `figures/Initial_layout_appendix.pdf` | Figure 7 (Appendix C) | `f/Initial_layout_appendix.pdf` |
| `figures/Comparison_plot_appendix.pdf` | Figure 8 (Appendix C) | `f/Comparison_plot_appendix.pdf` |
| `figures/Comparison_polarization_appendix.pdf` | Figure 9 (Appendix C) | `f/Comparison_polarization_appendix.pdf` |

 Figures 2 and 3 have had their legends moved manually to save space. Figures 7, 8, and 9 are directly used. Figures 1, 4, 5, and 6 of the paper are diagrams. 

The notebook also shows one extra plot that is not in the paper. This plot compares the strategies for both idling ratios. 

## Data files

All three files are CSV files with one header row. Each row is one memory experiment at one physical error rate. 

| File | Rows | Used for | Content |
| --- | --- | --- | --- |
| `data/compiled_results_complete.csv` | 7794 | Figures 2, 8, 9 | Strategy datasweep (Appendix B.2 of the paper). All runs use the optimized initial layout. |
| `data/compiled_results_baseline.csv` | 24 | Figures 2, 8, 9 | Baseline runs with gate, reset, and measurement noise only. These runs have no shuttling and no idling noise. The file has one depolarized run and one polarized run. The figures use the depolarized run. |
| `data/compiled_results_initial_positions.csv` | 10440 | Figures 3, 7 | Initial layout datasweep (Appendix B.1 of the paper). All runs use a shuttling ratio of 0.05, an idling ratio of 1.8, and depolarized noise. |


| Column | Description |
| --- | --- |
| `experiment_id` | Identifier of the simulation run. In the strategy and initial layout files, the string starts with `ID_` and two numbers that label the run. The rest of the string contains the main parameters of the run. All rows with the same identifier come from one run. One parameter set can have more than one run, with different ranges of error rates. The baseline identifiers contain a strategy name, but the baseline runs have no shuttling. |
| `Initial Layout` | Start positions of the qubits on the grid: `Tanner`, `folded Tanner`, `optimized`, `quadrants`, or `random1` to `random25`. See Section 3.3 of the paper. |
| `L`, `M` | Parameters of the bivariate bicycle code. The values 12 and 6 give the Gross code. |
| `X`, `Y` | Size of the shuttling grid (24 and 12). |
| `Code Distance` | Distance of the code (12). |
| `Strategy` | Shuttling strategy. The notebook changes the names to the names in the paper: `total_shuttling_minimized` becomes "Total Shuttling Minimized", `maximum_matching_efficient` becomes "Minimize Largest Distance", and `min_weight_matching_efficient` becomes "Combined". The baseline file uses `Baseline`. |
| `Rounds` | Number of syndrome extraction rounds (12). |
| `Physical Error Rate` | Base physical error rate p_base. When `2 Qubit Gate Error` is 1, this is the error probability of a two-qubit gate. It is the x-axis of all figures. |
| `Logical Error Rate` | Fraction of shots with a logical error: `errors / (shots - discards)`. It is the y-axis of all figures. |
| `2 Qubit Gate Error` | Two-qubit gate error as a multiple of p_base: 1 (on) or 0 (off). The notebook renames this column to "Noise Sources" with the values "Full" (1) and "Shuttling & Idling" (0). |
| `Idling Error Ratio` | Idling error per edge-traversal time as a multiple of p_base. The value is the product of `Shuttling Error Ratio` and `Idling to Error Ratio`. |
| `Shuttling Error Ratio` | Shuttling ratio S_R: the shuttling error per edge as a multiple of p_base (0.005, 0.01, 0.05, or 0.1). It is 0 in the baseline file. |
| `Measurement Error Ratio` | Measurement error as a multiple of p_base: 2 (on) or 0 (off). |
| `Reset Error Ratio` | Reset error as a multiple of p_base: 2 (on) or 0 (off). |
| `Noise Model` | Function for the accumulation of shuttling and idling noise with distance: `linear`, `exponential`, or `gaussian`. See Section 3.4 of the paper. The notebook renames this column to "Noise Dependence". |
| `t_2q`, `t_meas`, `t_1q` | Durations of a two-qubit gate, a measurement, and a one-qubit gate in the noise model. |
| `Eta Shuttling` | Bias of the shuttling noise: 0.5 for depolarized noise and 100 for polarized noise. The notebook renames this column to "Polarization" with the values "Depolarized" and "Polarized". |
| `Eta Idling`, `Eta CZ`, `Eta CNOT` | Bias values for idling noise and gate noise, with the same meaning as `Eta Shuttling`. In the strategy file, polarized runs set `Eta CZ` to 100, and `Eta Idling` and `Eta CNOT` are 0.5 in all rows. The polarized baseline run sets all four values to 100. |
| `Shuttle Bias Dir`, `Gate Bias Dir` | Pauli axis of the noise bias (`Z` in all rows). |
| `errors` | Number of shots with a logical error. |
| `shots` | Number of simulated shots. The maximum is 100000. Every row with fewer shots has at least 100 errors. |
| `discards` | Number of discarded shots (0 in all rows). |
| `Grid type` | Shuttling grid: `Square` (square grid) or `Octa` (octagrid, a square grid with diagonals). See Section 3.2 of the paper. |
| `Idling to Error Ratio` | Idling ratio I_R: the idling error relative to the shuttling error per edge (1 or 1.8). |

## HPC code archive

The folder `hpc_code/` holds `sqec.zip`. This archive contains the simulation code exactly as it ran on the HPC cluster to make the data in `data/`. We give the archive as-is, for transparency. We do not maintain this code and we do not give support for it. You do not need the archive to make the figures.

## Citation

If you use the data or the notebook, please cite the paper:

```bibtex
@misc{incertimedici2026routing,
  title         = {Towards Optimal Routing Strategies for {qLDPC} codes on Silicon Spin Qubits},
  author        = {Incerti-Medici, Merlin and Nguyen, Minh T. P. and Bosco, Stefano and Wootton, James R. and Bultrini, Daniel},
  year          = {2026},
  eprint        = {2611.02972},
  archivePrefix = {arXiv},
}
```
