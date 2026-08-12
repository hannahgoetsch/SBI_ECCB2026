# SBI in Population Genetics — ECCB 2026 Tutorial

A 3.5-hour hands-on tutorial on **simulation-based inference (SBI)** in population genetics, presented at [ECCB 2026](https://eccb2026.org).

**Hosts:** Yuxin Ning · Hannah Götsch  
**Contributors:** Franz Baumdicker, Andrew Kern, Jiseon Min, Nate Pope, Lukas Tatzel, Axel Fehrenbach, Johannes Kippnich

> **Full curriculum:** [docs/CURRICULUM.md](docs/CURRICULUM.md)

---

## Tutorial outline

The tutorial runs for **3 hours 30 minutes** and is structured as follows:

| Segment | Duration |
|---------|----------|
| Opening presentation: SBI, population genetics | 20 min |
| **Part 1** — Population Genetics (msprime, coalescent, site-frequency spectrum) | 45 min |
| *Break* | 5 min |
| **Part 2** — Simulation-Based Inference (Neural posterior estimation, calibration, complex priors) | 85 min |
| *Break* | 10 min |
| **Part 3** — Snakemake workflow demo | 45 min |

See [docs/CURRICULUM.md](docs/CURRICULUM.md) for the detailed session-by-session breakdown.

---

## Repository structure

```
SBI_ECCB2026/
├── LICENSE
├── README.md
├── requirements.yaml           # Conda environment specification
├── docs/
│   ├── CURRICULUM.md           # Detailed tutorial curriculum
│   └── GLOSSARY.md             # Key terms
├── example_data/
│   └── MutRecRate/             # VCF + index + windows + popmap + ground truth
├── workflow/                   # vendored popgen-npe Snakemake pipeline
│   ├── training_workflow.smk
│   ├── prediction_workflow.smk
│   ├── common.smk
│   ├── config/                 # one YAML per experiment
│   └── scripts/                # simulators, processors, embedding nets, rules
└── notebooks/
    ├── 1_data_simulation_msprime.ipynb
    ├── 2_introduction_to_sbi.ipynb
    ├── 3_sbi_in_popgen.ipynb
    ├── 4_playground_complex_scenario.ipynb
    ├── 5_snakemake_workflow.ipynb
    └── popgen_npe_demo/        # project_dir for notebook 5
        └── MutRecRate-cnn_extract-ExchangeableCNN-42-5000-sep/
            ├── pretrain_embedding_network   # ready to use
            └── pretrain_normalizing_flow    # ready to use
```

---

## Getting started

### Installation (please do this before the tutorial!)

1. **Install Conda** (if not already available): [Miniconda](https://docs.conda.io/en/latest/miniconda.html)

2. **Clone this repository**:
   ```bash
   git clone https://github.com/ningyuxin1999/SBI_ECCB2026.git
   cd SBI_ECCB2026
   ```

3. **Create the environment**:
   ```bash
   conda env create -f requirements.yaml
   conda activate sbi-workshop
   ```
   Core dependencies: `msprime`, `tskit`, `torch`, `sbi`, `matplotlib`.

   If you run into Jupyter issues, try upgrade it manually:
   ```bash
   pip install --upgrade notebook jupyter_server jupyterlab jupyter_core traitlets
   ```

4. **Launch the notebooks**:
   ```bash
   jupyter notebook notebooks/
   ```
   Or open the `notebooks/` folder directly in VS Code.

---

## Notebook overview

### Notebook 1 — Data Simulation with msprime
Simulate genetic data under the coalescent: constant-size populations, tree sequences, genotype matrices, and the site-frequency spectrum (SFS).

### Notebook 2 — Introduction to SBI
Understand the `sbi` package and Neural Posterior Estimation (NPE) with a simple linear Gaussian example: define a `Prior`, a `Simulator`, train an `NPE` estimator, and run posterior predictive checks.

### Notebook 3 — SBI in Population Genetics
Apply NPE to jointly infer effective population size ($N_e$) and recombination rate from SFS data. Covers the full pipeline — training data generation, model training, posterior predictive checks, and simulation-based calibration (SBC).

### Notebook 4 (OPTIONAL) — Playground: Complex Demographic Scenarios
Design multi-epoch demography with custom `BoxUniform` priors. Compare six demographic scenarios (Medium, Large, Decline, Expansion, Bottleneck, Zigzag) and explore how prior choice shapes inference.

### Notebook 5 — Snakemake Workflow
Walk through the [`popgen-npe`](https://github.com/kr-colab/popgen-npe) [Soup-to-Nuts tutorial](https://popgen-npe.readthedocs.io/en/latest/tutorial.html) to infer **recombination-rate and mutation-rate landscapes** from a VCF. 

Steps 1–3 (simulator, processor, YAML) execute live; 
**Step 4 (training) is read-only — the trained checkpoint ships with this repository, so nothing is trained**; Step 5 runs `prediction_workflow.smk` live against the example VCF; Step 6 loads the per-window posteriors and plots the rate landscapes against the ground truth. Closes with pointers to cluster scaling and the popgen-npe contributor guide.

#### Notebook 5 setup (do this before the workshop)

**No training is required.** The trained checkpoints (`pretrain_embedding_network` and `pretrain_normalizing_flow`, ~2 MB) are committed to this repository under `notebooks/popgen_npe_demo/MutRecRate-cnn_extract-ExchangeableCNN-42-5000-sep/`, which is exactly where the workflow expects them. A fresh clone can run the prediction workflow immediately. Step 4 of the notebook is a read-through of what training *would* do, and its cell detects the shipped checkpoint and skips.

**The popgen-npe workflow is already in this repository** under `workflow/`: `training_workflow.smk`, `prediction_workflow.smk`, `common.smk`, `scripts/`, and `config/`. 

There is no separate repository to clone. 

The tutorial's `MutRecRate` simulator lives in `workflow/scripts/ts_simulators.py` and its config at `workflow/config/MutRecRate_cnn.yaml`. The prediction inputs are in `example_data/MutRecRate/`.

**The only setup step is the environment.** Notebook 5 needs dependencies the earlier notebooks do not (`snakemake`, `zarr`, `dinf`, `tsinfer`, `bio2zarr`, `pysam`, `lightning`, `ray`, and others). In case you built the environment incompletely:

```bash
conda env update -f requirements.yaml --prune
conda activate sbi-workshop
```

The notebook's first code cell checks every package and CLI tool (`snakemake`, `vcf2zarr`, `tabix`) and reports what is missing.

With that in place the notebook runs top to bottom:

| Step | What happens | Cost |
|------|--------------|------|
| 1–3 | Simulator, processor, and config are inspected and sanity-checked live | seconds |
| 4 | Training is explained only; the cell finds the shipped checkpoint and skips | none |
| 5 | `prediction_workflow.smk` runs live on `example_data/MutRecRate/test.vcf.gz` — 15 rules: VCF→Zarr, `tsinfer`, `cnn_extract`, posterior sampling, diagnostics | ~1 min on a laptop at `--cores 2` |
| 6 | Loads the `(30 windows, 2 parameters, 1000 draws)` posterior array and plots the rate landscapes | seconds |

Retraining is entirely optional, check [notebooks/popgen_npe_demo/README.md](notebooks/popgen_npe_demo/README.md) for the command, the output layout, and the one gotcha (delete the pruned `tensors/zarr/` first).
