# Glossary

A list of abbreviations and terms used throughout this workshop, grouped by topic.

## Bayesian & Machine Learning

| Term / Abbreviation | Description |
|---------------------|-------------|
| Amortization | Reusing a trained inference network to evaluate many observations without retraining. |
| Bayesian Inference | The process of updating beliefs about parameters given observed data via Bayes' theorem. |
| Bayesian Method | An approach to statistics based on probability distributions over parameters. |
| Checkpoint | A saved snapshot of a model's state during training, enabling resumption or evaluation. |
| Diagnostics | Checks and metrics used to assess model correctness and performance. |
| Embedding Network | A neural network that compresses raw/high-dimensional data into informative summary features. |
| Features | Input variables/characteristics used by a model (may be raw data or summary statistics). |
| Likelihood | The probability of observing the data given specific parameter values. |
| Linear-Gaussian Model | A model with linear relationships and Gaussian noise; often used as a test/benchmark case. |
| MCMC (Markov Chain Monte Carlo) | A class of algorithms for sampling from probability distributions, often used in Bayesian inference. |
| Neural Density Estimator | A neural network trained to approximate a probability distribution. |
| NLE (Neural Likelihood Estimation) | An SBI method that uses neural networks to approximate the likelihood. |
| NPE (Neural Posterior Estimation) | An SBI method that uses neural networks to directly approximate the posterior. |
| NRE (Neural Ratio Estimation) | An SBI method that estimates likelihood-to-evidence ratios using neural networks. |
| Posterior | Updated distribution of parameters *after* observing data. |
| PPC (Posterior Predictive Check) | Comparing data simulated from the posterior against observed data. |
| Prior | Distribution representing beliefs about parameters *before* observing data. |
| SBC (Simulation-Based Calibration) | Diagnostic to validate the correctness of a Bayesian inference procedure. |
| SBI (Simulation-Based Inference) | Inferring parameters of models defined only through a simulator (no explicit likelihood). |
| Simulator | A model that generates synthetic data given parameters; central to SBI. |
| Summary Statistics | Compressed quantities describing key aspects of the data (e.g. the SFS). |
| Test Data | Held-out data used to evaluate model performance after training. |
| Trainer | A component/object that manages the model training loop. |
| Training Data | Data used to fit/train a model. |

---

## Computing

| Term / Abbreviation | Description |
|---------------------|-------------|
| Cache | Stored intermediate results to speed up repeated computations. |
| Cluster | A group of connected computers/nodes used for large-scale parallel computation. |
| CPU (Central Processing Unit) | General-purpose processor for computation. |
| DAG (Directed Acyclic Graph) | A graph with directed edges and no cycles, used to define workflow dependencies. |
| Environment | A defined set of software dependencies and versions for reproducibility. |
| GPU (Graphics Processing Unit) | Hardware optimized for parallel computation, used to accelerate training. |
| Logging | Recording events, metrics, and messages during a run for monitoring and debugging. |
| Pipeline | A sequence of connected processing steps forming an automated workflow. |
| Processor | A component that transforms or prepares data within the pipeline for the next step. |
| SVG (Scalable Vector Graphics) | An XML-based vector image format for scalable graphics. |
| Tensor | A multi-dimensional array; the core data structure in deep-learning frameworks. |
| Workflow System | Software that manages and automates multi-step pipelines (e.g. Snakemake, Nextflow). |
| YAML Config | A configuration file in YAML format (human-readable data-serialization format) specifying parameters and settings. |

---

## Population Genetics

| Term / Abbreviation | Description |
|---------------------|-------------|
| Ancestral Allele | The original allele at a locus, before a mutation occurred. |
| Coalescent | A model describing how sampled lineages merge (coalesce) back in time to common ancestors. |
| Derived Allele | The new allele at a locus that arose via mutation from the ancestral allele. |
| Genealogy | The ancestral relationships among sampled sequences, often depicted as a tree. |
| Genetic Drift | Random change in allele frequencies across generations due to finite population size. |
| LD (Linkage Disequilibrium) | Non-random association of alleles at different loci. |
| Linkage | The tendency of alleles at nearby loci to be inherited together. |
| Minor Allele | The less frequent of two alleles at a polymorphic site. |
| N_e (Effective Population Size) | Size of an idealized population that drifts at the same rate as the observed one. |
| Recombination | The exchange of genetic material between chromosomes, breaking up linkage. |
| Segregating Site | A position in the genome that is polymorphic (varies) within a sample. |
| SFS (Site-Frequency Spectrum) | Distribution of allele counts across segregating sites. |
| SNP (Single Nucleotide Polymorphism) | A variation at a single position in the DNA sequence. |
| Tree Sequence | A compact data structure encoding the genealogies along a genome, including recombination. |
| VCF (Variant Call Format) | Standard text format for storing genetic sequence variations. |
