# Numerical results (figures and tables of the response letter)

This folder holds the **numerical data** behind the figures and tables of our
point-by-point response to reviewers, made public here as noted in the response.
Each file is named after the figure/table it provides.

Conventions (same as the response letter):

- **mn** = measurement-noise level; **lf** = load fluctuation. They appear as
  percentages in names (e.g. `mn_5`, `lf_10` = 5% and 10%) and as fractions inside
  some sheets (e.g. mn `0.05` = 5%, lf `1` = 100%).
- A column header such as `lf_10_mn_5` (or `mn_5_lf_10`) denotes one **paired
  (load-fluctuation, measurement-noise) setting**.
- Row labels `missing_NN` = **NN% data-missing rate** (entries randomly dropped
  before the dimension-preserving temporal-average imputation).
- Each value is the identification accuracy (fraction of feasible topologies
  correctly identified, every topology as ground truth; branch-current magnitude,
  maximum-SSI criterion), **averaged over ten independent random sensor placements**
  (seed 2025).

## Files and their contents

### `Fig2Identification accuracy for different networks…Fig. 3…33- and 69-node networks..xlsx`
Covers **Fig. 2** and **Fig. 3**.
- Sheets `33`, `69`, `118`, `533` — **Fig. 2**. Rows = data-missing rate
  (`missing_0`…`missing_90`), columns = five paired settings
  `lf_10_mn_5`, `lf_20_mn_10`, `lf_50_mn_20`, `lf_80_mn_50`, `lf_100_mn_80`.
- Sheets `fig34-33`, `fig34-69` — **Fig. 3** (33- and 69-node networks). Same row
  meaning, columns = six milder paired settings `lf_5_mn_1` … `lf_50_mn_10`.

### `FigV-2_load_fluctuation_and_noise.xlsx` — **Fig. V-2**
One sheet `Sheet1`: columns `Network, Topologies, Sensors, lf_10, lf_20, lf_50,
lf_80, lf_100`; rows = the four networks (33/69/118/533). The `lf_*` cells are the
ten-placement mean accuracy at **fixed mn = 20% and 50% data missing**.

### `Table V-1 Identification accuracy under measurement noise and load fluctuation for 118-node network.xlsx` — **Table V-1**
- Sheet `mean`: the 118-node mn × lf accuracy grid (rows = mn `0.05`…`0.8`,
  columns = lf `0.1`…`1`), 15 sensors, **0% data missing**.
- Sheet `sensor_sets`: the ten random 15-sensor placements (branch indices).

### `Table V-2 Identification accuracy for unbalanced network..xlsx` — **Table V-2**
- Sheet `mean`: the unbalanced 3-phase 33-node mn × lf accuracy grid (rows = mn,
  columns = lf), 5 sensors (each providing its branch's three-phase current = 15
  channels).
- Sheet `sensor_sets`: the ten random 5-branch placements.

### `Table I-1 SSI term decomposition identification accuracy comparison using single SSI term alone..xlsx` — see note below
Sheets `33_metrics`, `118_metrics`, `533_metrics`. In each, rows = similarity/distance
method — `SSI (proposed)`, `Pearson correlation`, `neg. Euclidean (MSE/RMSE)`,
`neg. L1 (MAE)`, `histogram intersection` — and columns = five paired settings
`mn_5_lf_10` … `mn_80_lf_100`, with no data missing. Cells = identification accuracy.

> Note: this file compares the **full SSI against general similarity/distance
> metrics**; it is the *metric-comparison* data, not the luminance/contrast/structure
> *term decomposition* implied by the name "SSI term decomposition." See the open item.

## Reproducibility

Each file was produced by the corresponding MATLAB driver (MATPOWER for power flow,
Image Processing Toolbox for the SSI) with fixed random seeds for the sensor
placements, load fluctuation, measurement noise, and missing-data masks. The drivers
will be released in this repository upon acceptance of the paper.
