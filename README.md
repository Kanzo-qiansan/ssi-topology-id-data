# Distribution Network Topology Identification — Benchmark Data

This repository provides the network models, candidate topology libraries, and
sensor (monitored-line) placements used in our study on **distribution-network
topology identification via the Structural Similarity Index (SSI)**. The data let
others reproduce the topology-set construction and the sensor configurations used
in the experiments.

Four distribution systems are included. For each system we provide:

1. the electrical network model (MATPOWER case file),
2. the **candidate topology library**: every feasible radial operating topology
   that satisfies the radiality, connectivity, and security constraints, and
3. the **sensor placement**: the set of monitored lines (current sensors; for a
   hybrid device, the sending-bus voltage is also available at the same site).

All topology libraries have been verified: **every topology is a radial spanning
tree (closed branches = buses − 1, fully connected) and converges in an AC power
flow** (MATPOWER, Newton–Raphson).

---

## 1. Networks at a glance

| Network | Buses | Branches | Tie switches | Candidate topologies *N* | Sensors | Density (sensors / branches) |
|---|---:|---:|---:|---:|---:|---:|
| IEEE 33-bus  | 33  | 37  | 5  | 25  | 5  | 13.5% |
| IEEE 69-bus  | 69  | 73  | 5  | 47  | 10 | 13.7% |
| IEEE 118-bus | 118 | 132 | 15 | 81  | 15 | 11.4% |
| 533-bus (Kraftringen) | 533 | 577 | 45 | 445 | 50 | 8.7% |

The 533-bus system is a **real medium-voltage distribution network** of the Swedish
DSO Kraftringen, publicly released by Malmer and Thorin (see *Provenance*). The
IEEE 33-, 69-, and 118-bus systems are standard distribution benchmarks.

---

## 2. Repository contents

```
release/
├── README.md                         (this file)
├── cases/                            MATPOWER case files (electrical model)
│   ├── case33bw.m
│   ├── case69gai.m
│   ├── case118zh.m
│   └── case533mt_hi.m
├── topology_libraries/               candidate topology sets (one row = one topology)
│   ├── case33_topologies_N25.csv   / .xlsx
│   ├── case69_topologies_N47.csv   / .xlsx
│   ├── case118_topologies_N81.csv  / .xlsx
│   └── case533_topologies_N445.csv / .xlsx
├── branch_reference/                 branch index → endpoints (decode the columns above)
│   ├── case33_branches.csv
│   ├── case69_branches.csv
│   ├── case118_branches.csv
│   └── case533_branches.csv
└── sensor_placement/                 monitored-line sets
    ├── sensor_placement.csv          (machine-readable, with bus endpoints)
    └── sensor_placement.txt          (ready to paste into the MATLAB driver)
```

---

## 3. File formats

### 3.1 `cases/*.m` — network model
Standard **MATPOWER** case files. Each returns an `mpc` struct with `mpc.bus`,
`mpc.branch`, `mpc.gen`, etc. The branch order in `mpc.branch` defines the **branch
index** used everywhere else in this dataset. Requires MATPOWER on the MATLAB path.

```matlab
mpc = case33bw;        % or loadcase('case33bw')
```

### 3.2 `topology_libraries/*` — candidate topology sets
Each file lists the feasible operating topologies of one network.

- **Rows** = topologies (`N` rows). The CSV has a leading `topology_id` column (1…N).
- **Columns** `branch_001 … branch_0NN` = the on/off state of each branch, in the
  branch order of the corresponding case file:
  - `1` = branch **closed / in service**,
  - `0` = branch **open** (the switch is open in that configuration).
- Each row contains exactly `buses − 1` ones and forms a connected, loop-free
  (radial) spanning tree.

CSV and XLSX hold identical numbers; the CSV is the portable, human-readable form
and the XLSX (sheet `topologies`) is convenient for MATLAB/Excel.

### 3.3 `branch_reference/*.csv` — how to read a branch index
This is the key that makes the topology columns and the sensor indices meaningful.

| Column | Meaning |
|---|---|
| `branch_id` | branch index (matches `branch_00k` columns and the sensor lists) |
| `from_bus`, `to_bus` | the two buses the branch connects |
| `nominal_status_1closed_0open` | the branch state in the original case file |
| `switch_type` | `sectionalizing` (normally closed) or `tie` (normally open) |

Example: in `case33_branches.csv`, `branch_id = 9` is the line from bus 9 to bus 10.
Therefore column `branch_009` in `case33_topologies_N25.csv` is the on/off state of
that line, and a sensor listed as branch 9 monitors that line.

### 3.4 `sensor_placement/*` — monitored lines
`sensor_placement.csv` columns: `network, n_sensors, density_percent, branch_id,
from_bus, to_bus, switch_type`. `sensor_placement.txt` gives the same sets as
MATLAB vectors (`cfg.pmu = [...]`) ready to paste into the experiment driver.

---

## 4. How the pieces fit together

To reconstruct configuration *i* of a network:

1. take row *i* of its topology file → a 0/1 vector over the branches;
2. set each branch in/out of service accordingly (`mpc.branch(:,11) = row'` in MATPOWER);
3. the open branches are exactly the open switches of that operating topology.

To know which physical lines are measured, read the sensor `branch_id`s and look up
their `from_bus`/`to_bus` in the branch reference.

---

## 5. Loading the data

### MATLAB
```matlab
% topology library (rows = topologies, columns = branch states)
T   = readmatrix('topology_libraries/case33_topologies_N25.csv');
ids = T(:,1);            % topology_id
TP  = T(:,2:end);        % N x branches, 0/1
% apply topology i to the network
mpc = case33bw;  mpc.branch(:,11) = TP(i,:).';
% sensors
cfg.pmu = [9 14 20 33 37];   % from sensor_placement.txt
```

### Python
```python
import pandas as pd
topo = pd.read_csv('topology_libraries/case33_topologies_N25.csv')
states = topo.filter(like='branch_').to_numpy()   # N x branches, 0/1
branches = pd.read_csv('branch_reference/case33_branches.csv')
sensors  = pd.read_csv('sensor_placement/sensor_placement.csv')
```

---

## 6. Provenance and validation

- **533-bus (Kraftringen).** Real medium-voltage distribution network of the Swedish
  DSO Kraftringen, serving roughly 30,000 inhabitants and an industrial area over a
  20 km × 30 km territory, with a net load from about −5 MW to 50 MW. The network and
  its data were publicly released by **Malmer and Thorin** *(add full citation /
  DOI)*.
- **IEEE 33-, 69-, 118-bus.** Standard distribution benchmarks derived from real
  distribution feeders.
- **Validation.** Each topology in every library was checked to be a radial spanning
  tree and to converge in an AC power flow (MATPOWER). All sensor indices are valid
  branch indices of their network.

---

## 7. Citation, license, and code availability

- **Citation.** If you use these data, please cite our paper *(add full reference
  once available)* and the Kraftringen data source (Malmer and Thorin) for the
  533-bus network.
- **License.** *(choose one before publishing, e.g. CC BY 4.0 for data, MIT for any
  scripts; add a `LICENSE` file.)*
- **Code availability.** The source code that reproduces the topology-identification
  results will be made publicly available in this repository **upon acceptance of the
  paper**.
