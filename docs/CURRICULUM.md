# ECCB Tutorial Curriculum
## Neural posterior estimation for population genetics

**Duration:** 3 hours 30 minutes, 09:00–12:30, including a 15-minute break (+ open-ended Discussions)  
**Hosts:** Yuxin Ning, Hannah Götsch  
**Contributors:** Franz Baumdicker, Andrew Kern, Jiseon Min, Nate Pope, Lukas Tatzel, Axel Fehrenbach, Johannes Kippnich

---

## Schedule Overview

| Time | Session | Duration |
|------|---------|----------|
| 09:00 | Opening Presentation | 15 min |
| 09:15 | Part 1: Population Genetics | 40 min |
| 09:55 | Part 2: Simulation-Based Inference | 35 min |
| 10:30 | *Break* | 15 min |
| 10:45 | Part 2 continued: Simulation-Based Inference | 35 min |
| 11:20 | Part 3: Snakemake Workflow | 70 min |
| 12:30 | Discussions | — |

---

## Opening Presentation (15 min)

**"SBI in Population Genetics: Motivation, Methods, and Results"**

- Introduction to population genetics and why we care about inferring demographic parameters
- Classical likelihood-based approaches and their limits
- What is simulation-based inference (SBI)?
  - Neural Posterior Estimation (NPE) and competing algorithms (e.g., Approximate Bayesian Computation)
  - The role of summary statistics as the bridge between data and inference
- Showcase: how we implemented and evaluated SBI for inferring $N_e$ and recombination rates
  - Data pipeline: msprime coalescent simulations + summary statistics (Site frequency spectrum, Linkage disequilibrium)
  - Training strategy and validation
  - Key results in our preprint [Neural posterior estimation for population genetics;
Jiseon Min, Yuxin Ning, Nathaniel S. Pope, Franz Baumdicker, Andrew D. Kern;
bioRxiv 2025.12.01.691638; doi: https://doi.org/10.64898/2025.12.01.691638](https://doi.org/10.64898/2025.12.01.691638)

---

## Part 1: Population Genetics (40 min)

**Showcase in Notebook: `1_data_simulation_msprime.ipynb`**

*Goal: build intuition about coalescent simulations and summary statistics before touching any inference machinery.*

### 1.1 The coalescent model (10 min)
- Genealogies, effective population size $N_e$, mutation and recombination rates
- How `msprime` encodes these as tree sequences

### 1.2 Simulating a constant-size population (15 min)
- Running `msprime.sim_ancestry()` and `msprime.sim_mutations()`
- Inspecting tree sequences and genotype matrices

### 1.3 Summary statistics: the site-frequency spectrum (SFS) (10 min)
- Extracting the SFS with `tskit`
- Folded vs. unfolded SFS
- Visualising and interpreting SFS shape under different $N_e$ values

### 1.4 Q&A / buffer (5 min)

---

## Part 2: Simulation-Based Inference (70 min, in two 35-min blocks either side of the break)

**Showcase in Notebooks: `2_introduction_to_sbi.ipynb`, `3_sbi_in_popgen.ipynb`, `4_playground_complex_scenario.ipynb`**

*Goal: understand NPE from a simple toy example up to a realistic popgen application, and appreciate how prior design shapes results.*

### 2a. Introduction to SBI (20 min) — `2_introduction_to_sbi.ipynb`

#### 2a.1 Why SBI? (5 min)
- Intractable likelihoods and the role of simulators
- The focus of this workshop: Neural Posterior Estimation

#### 2a.2 The `sbi` toolkit: linear Gaussian example (10 min)
- Defining a `Prior` and a `Simulator`
- Training a neural density estimator with `NPE`
- Drawing posterior samples and visualising with `pairplot`

#### 2a.3 Posterior predictive checks (PPC) (5 min)
- Comparing simulated data from posterior draws to the observed data
- Reading a PPC plot: what good and bad coverage look like

---

### 2b. SBI in Population Genetics (30 min core, split by the break; 2b.4 optional) — `3_sbi_in_popgen.ipynb`

#### 2b.1 Applying NPE to popgen (10 min)
- The full pipeline: msprime simulator → SFS summary statistic → prior → NPE
- Jointly inferring two epoch-specific $N_e$ values ($N_{e,1}$, $N_{e,2}$) with a log-uniform prior; recombination and mutation rates stay fixed in the simulator. 

> NPE infers exactly the parameters that are randomized during simulation.

#### 2b.2 Q&A / buffer (5 min)

---

## *Break (15 min)* — 10:30–10:45

---

#### 2b.3 Training and evaluation (15 min)
- Generating $(\theta, x)$ training pairs
- Training the density estimator and monitoring convergence
- Posterior predictive checks on held-out test data

#### 2b.4 Simulation-based calibration (SBC) (5 min) — *optional*
- Why calibration matters for scientific credibility
- Running SBC and interpreting rank histograms

---

### 2c. Playground — Complex Demographic Scenarios (20 min) — *optional* — `4_playground_complex_scenario.ipynb`

#### 2c.1 Multi-epoch demography (8 min)
- Designing a `BoxUniform` prior over epoch-specific $N_e$ values and recombination rate
- Building piecewise-constant demography in `msprime`

#### 2c.2 Comparing demographic scenarios (7 min)
- Medium, Large, Decline, Expansion, Bottleneck, Zigzag
- How SFS shape encodes demographic history — and where it becomes ambiguous

#### 2c.3 Why your prior matters (5 min)
- Coverage, identifiability, and pathological priors
- Open exploration: feel free to modify priors and observe the effects on the simulated summaries

---

## Part 3: Snakemake Workflow (70 min)

**Demo / Walkthrough | Notebook: `5_snakemake_workflow.ipynb` + [`popgen-npe`](https://github.com/kr-colab/popgen-npe) pipeline**

*Goal: see how a research-grade SBI workflow is structured for reproducibility and scaling, and walk through the popgen-npe [Soup-to-Nuts tutorial](https://popgen-npe.readthedocs.io/en/latest/tutorial.html) to infer a recombination rate landscape from a Variant Call Format (VCF) file.*

### 3.1 Motivation: from notebook to pipeline (5 min)
- Why interactive notebooks don't scale: memory, parallelism, reproducibility
- What Snakemake brings: rule-based directed acyclic graph (DAG), checkpointing, cluster integration

### 3.2 Pipeline architecture (10 min)
- Repository structure of [`popgen-npe`](https://github.com/kr-colab/popgen-npe): `workflow/training_workflow.smk`, `workflow/prediction_workflow.smk`, `workflow/config/`
- The simulator → processor → embedding network → normalising flow pipeline
- One YAML drives both training and prediction (see `workflow/config/MutRecRate_cnn.yaml`)

### 3.3 Live demo: running the pipeline (40 min)
- Walk through the Soup-to-Nuts tutorial's six steps inside `5_snakemake_workflow.ipynb`
- Steps 1–3 (simulator, processor, YAML): read the config that produced the shipped checkpoint
- Step 4 (training): explain, **do not** re-run live — the checkpoint ships with the repository at `notebooks/popgen_npe_demo/MutRecRate-cnn_extract-ExchangeableCNN-42-5000-sep/`, so no training is required before or during the session
- Step 5 (prediction): live run of `snakemake --snakefile workflow/prediction_workflow.smk` against the bundled example VCF
- Step 6 (interpret): load per-window posteriors, plot the rate landscape

### 3.4 Scaling to real data (10 min)
- Customising the YAML's `cpu_resources` / `gpu_resources` and SLURM options for cluster runs
- Swapping simulators or processors without changing pipeline code
- Tutorial "Next steps" and the popgen-npe [contributor guide](https://popgen-npe.readthedocs.io/en/latest/contributing.html)

### 3.5 Q&A and wrap-up (5 min)
- Key resources and follow-up reading
- How to adapt the pipeline to your own simulator and summary statistics
