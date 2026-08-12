# popgen-npe demo outputs

This is the `project_dir` for notebook `5_snakemake_workflow.ipynb`. Both Snakemake
workflows write here, as configured in `workflow/config/MutRecRate_cnn.yaml`:

```yaml
project_dir: "notebooks/popgen_npe_demo"
```

Almost everything in this folder is **generated** and gitignored. The exceptions are
committed on purpose so that a fresh clone can run notebook 5 **without training anything**:
the `pretrain_*` checkpoints, a pruned `tensors/zarr/`, and the step 6 figure `rate_landscape.png`, kept as a reference for what a correct run looks like.

## What the workflows write here

Both workflows derive their output directory from the config, using the scheme
`{simulator}-{processor}-{embedding}-{random_seed}-{n_train}-{sep|e2e}`. For our
config that is:

```
popgen_npe_demo/
├── README.md                                         [committed]
├── rate_landscape.png               # Step 6 figure  [committed]
└── MutRecRate-cnn_extract-ExchangeableCNN-42-5000-sep/
    ├── pretrain_embedding_network       # trained ExchangeableCNN  ← Step 4  [committed]
    ├── pretrain_embedding_network.ckpt  # Lightning best-epoch ckpt         [committed]
    ├── pretrain_normalizing_flow        # trained NPE              ← Step 4  [committed]
    ├── pretrain_normalizing_flow.ckpt   # Lightning best-epoch ckpt         [committed]
    ├── tensors/zarr/                    # training features + targets [pruned, committed]
    ├── trees/                           # simulated tree sequences
    ├── logs/                            # TensorBoard training logs
    ├── plots/                           # training diagnostics
    │   ├── posterior_calibration.png
    │   ├── posterior_expectation.png
    │   └── ...
    └── test.vcf.gz/                     #                          ← Step 5
        ├── vcz/                         # Zarr-encoded VCF
        ├── trees/                       # tsinfer output, one per window
        ├── tensors/zarr/predictions     # (n_windows, n_parameters, 1000)
        └── plots/
            ├── posteriors-across-windows.png
            └── tree_stats_hist.png
```

Using the model needs only two of those files: prediction loads `pretrain_embedding_network`
and `pretrain_normalizing_flow`. The `.ckpt` files are Lightning's best-epoch snapshots, from
which the training scripts derive those two; they are kept for provenance. Everything under
`test.vcf.gz/` is written by Step 5 of the notebook and is gitignored.

Changing `random_seed` or `n_train` produces a new directory rather than overwriting
the old one.

### Re-training *(optional — not needed for the workshop)*

Only relevant if you change the simulator, processor, config, or `n_train`. To retrain
from scratch, run from the repository root:

```bash
snakemake --cores 4 --configfile workflow/config/MutRecRate_cnn.yaml --snakefile workflow/training_workflow.smk
```

Roughly 15 minutes on an A100 at `n_train: 5000`, considerably longer on CPU. Simulation
is the bottleneck, so raise `n_chunk` if you have cores to spare. Because `project_dir`
already points here, nothing needs copying afterwards. The step 5 of the notebook picks the
checkpoint up automatically. 

> Note that this **overwrites the committed checkpoints** in place, since the config resolves to the same directory; bump `random_seed` or `n_train`
if you want to keep both.

> **Delete `tensors/zarr/` first.** `setup_training` declares it as a `directory()`
> output, so Snakemake treats the committed pruned copy as an already-satisfied output
> and skips regenerating it — after which training fails on the missing `features/`
> array. Either `rm -rf` the directory or pass `--forcerun setup_training`.

Before trusting any result, check `plots/posterior_calibration.png` (points on the
diagonal mean well-calibrated posteriors) and `plots/posterior_expectation.png`
(posterior means vs. true values).

## Input data

`example_data/MutRecRate/` holds the prediction inputs: a synthetic VCF with spatially
varying rates across three 5 Mb chromosomes, its tabix index, a 30-window BED file, the
population map, and `true_rates.tsv` for the Step 6 comparison.

If you swap in your own data, the constraints are: the VCF must be bgzipped and
tabix-indexed; every contig needs a `length=` in the header (otherwise `tsinfer` fails
with `sequence_length cannot be zero or less`); the sample count after applying the
population map must exactly match the simulator's `samples` config (20 diploid
individuals in population `"pop"`); and BED window sizes must equal the simulator's
`sequence_length` of 500 kb.
