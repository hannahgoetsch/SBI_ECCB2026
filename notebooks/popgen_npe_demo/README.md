# popgen-npe demo assets

This folder holds everything notebook `5_snakemake_workflow.ipynb` needs to run the
prediction step of the [popgen-npe Soup-to-Nuts tutorial](https://popgen-npe.readthedocs.io/en/latest/tutorial.html)
live during the workshop.

Training is **not** done during the session — we ship a checkpoint instead, so the
notebook only has to run `prediction_workflow.smk`.

## Layout

```
popgen_npe_demo/
├── README.md              # this file
├── config.yaml            # the YAML used to train and to predict (Step 3 of the tutorial)
├── checkpoint/            # output of `training_workflow.smk` — pre-generated
│   ├── ...                # trained embedding + flow weights, normalizer stats, etc.
│   └── predictions.npz    # populated by Step 5 at runtime
├── example_vcf/           # input data the prediction step consumes
│   ├── example.vcf.gz
│   ├── example.vcf.gz.tbi
│   ├── windows.bed        # genomic windows to score
│   └── ancestral.fa       # optional, if the simulator uses polarised alleles
└── expected_outputs/
    └── rate_landscape.png # cached figure for fallback if the live run is skipped
```

## What's missing right now

1. **`config.yaml`**
2. **`checkpoint/`** 
3. **`example_vcf/`** 
4. **`expected_outputs/rate_landscape.png`**

## Reproducibility

If you regenerate the checkpoint, also bump `config.yaml`'s `random_seed` field
(or commit the existing one) so attendees who rerun training off-session get the
same numbers.
