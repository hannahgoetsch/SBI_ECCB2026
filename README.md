# SBI in Population Genetics — ECCB 2026 Tutorial

A 3.5-hour hands-on tutorial on **simulation-based inference (SBI)** in population genetics, presented at [ECCB 2026](https://eccb2026.org).

**Hosts:** Yuxin Ning · Hannah Götsch  
**Contributors:** Prof. Franz Baumdicker, Dr. Lukas Tatzel, Axel Fehrenbach, Johannes Kippnich

> **Full curriculum:** [docs/CURRICULUM.md](docs/CURRICULUM.md)

---

## Tutorial outline

The tutorial runs for **3 hours 30 minutes** and is structured as follows:

| Segment | Duration |
|---------|----------|
| Opening presentation: SBI, population genetics, and our study | 20 min |
| **Part 1** — Population Genetics (msprime, coalescent, AFS) | 45 min |
| *Break* | 5 min |
| **Part 2** — Simulation-Based Inference (NPE, calibration, complex priors) | 85 min |
| *Break* | 10 min |
| **Part 3** — Snakemake workflow demo | 45 min |

See [docs/CURRICULUM.md](docs/CURRICULUM.md) for the detailed session-by-session breakdown.

---

## Repository structure

```
SBI_ECCB2026/
├── LICENSE
├── README.md
├── requirements.yaml          # Conda environment specification
├── docs/
│   └── CURRICULUM.md          # Detailed tutorial curriculum
└── notebooks/
    ├── 1_data_simulation_msprime.ipynb
    ├── 2_introduction_to_sbi.ipynb
    ├── 3_sbi_in_popgen.ipynb
    ├── 4_playground_complex_scenario.ipynb
    ├── 5_snakemake_workflow.ipynb
    └── popgen_npe_demo/        # config + pre-trained checkpoint + example VCF for notebook 5
```

---

## Getting started

### Installation (do this before the tutorial — it requires significant memory)

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

   If you run into Jupyter issues, upgrade it manually:
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

### Notebook 4 — Playground: Complex Demographic Scenarios
Design multi-epoch demography with custom `BoxUniform` priors. Compare six demographic scenarios (Medium, Large, Decline, Expansion, Bottleneck, Zigzag) and explore how prior choice shapes inference.

### Notebook 5 — Snakemake Workflow
Walk through the [`popgen-npe`](https://github.com/kr-colab/popgen-npe) [Soup-to-Nuts tutorial](https://popgen-npe.readthedocs.io/en/latest/tutorial.html) to infer a **recombination rate landscape** from a VCF. Steps 1–4 (simulator, processor, YAML, training) are read-through; Step 5 runs `prediction_workflow.smk` live against a pre-trained checkpoint shipped in `notebooks/popgen_npe_demo/`; Step 6 plots the resulting per-window posterior. Closes with pointers to cluster scaling and the popgen-npe contributor guide.

#### Notebook 5 setup (extra step before the workshop)

Notebook 5 runs `snakemake --snakefile <popgen-npe>/workflow/prediction_workflow.smk`, so it needs a local clone of popgen-npe. Place it as a sibling of this repository:

```bash
cd ..              # parent of SBI_ECCB2026
git clone https://github.com/kr-colab/popgen-npe.git
pip install -e popgen-npe
```

The notebook defaults to `POPGEN_NPE_DIR = Path('../../popgen-npe')` — adjust this variable if you cloned somewhere else. The notebook also expects `notebooks/popgen_npe_demo/config.yaml`, a populated `checkpoint/`, and an `example_vcf/`; see `notebooks/popgen_npe_demo/README.md` for what each slot needs and how to generate the checkpoint.
