# Ray Summit 2026: Multimodal Data Processing Pipelines for AI Systems

Course material for the Ray Summit 2026 training "Multimodal Data Pipelines for AI Systems". The course teaches large-scale data processing and multimodal inference with Ray Data through an end-to-end e-commerce scenario.

## Provenance

This is Ray Summit 2026 Anyscale course material, republished with permission for personal study. Original content copyright 2026 Anyscale.

## Layout

Two instructor sessions plus bonus labs and a jobs demo:

- `1_Intro.ipynb` - Session 1: Ray, Anyscale, and the AI libraries. An end-to-end product category prediction demo: load and split data with Ray Data, train with Ray Train, tune with Ray Tune, run batch inference, and serve online predictions with Ray Serve. `intro.py` holds the training loop and the TF-IDF vectorizer actor this notebook calls.
- `2_Data.ipynb` - Session 2: a deep treatment of Ray Data. Ingest and transformation of a product catalog, filtering with the expression language, embeddings for semantic search, performance and parallelism, multimodal catalog copy with Gemma 3, AI-assisted pipeline development, and accelerated inference with vLLM through `ray.data.llm`.
- `bonus/labs/` - Four hands-on labs. Labs 1 and 2 cover Ray Core with synthetic data and run on a CPU-only laptop. Lab 3 covers Ray Data fundamentals. Lab 4 builds an AI-assisted multimodal pipeline and needs a GPU.
- `bonus/solutions/` - Worked solutions for all four labs.
- `jobs_demo/` - Operationalizing the pipeline as an Anyscale Job. `1_Job.ipynb` walks the submit flow, `create_extended_desc_job.py` is the entrypoint, and `create_extended_desc_job.yaml` is the job config.
- `offline_slides/` - The course deck as HTML and PDF.
- `outline-compute.md` - Full course outline and the planned compute config.
- `Dockerfile` - The course image: `anyscale/ray-llm:2.55.1-py311-cu128` plus pinned Python packages.
- `requirements.txt` - The same package pins as a plain pip file, with Ray pinned at 2.55.1.

## Compute expectations

From `outline-compute.md`:

- Planned class compute: one m5.2xlarge head node plus two g5.2xlarge workers.
- Minimum: A10G or better acceleration, 48 GB total GPU RAM across those workers.
- Labs 1 and 2 are pure Ray Core on synthetic data and need no GPU.
- The jobs demo YAML requests eight g5.2xlarge workers, but the script sizes its actor pool to whatever GPU count Ray reports, so smaller allocations work with proportionally longer runtime.

`outline-compute.md` gives the instance types and the 48 GB total. It records no per-GPU memory figure, so none is stated here.

### Tested configuration

The configuration this material targets. Values come from `Dockerfile` unless the row says otherwise.

| Component | Version |
| --- | --- |
| Base image | `anyscale/ray-llm:2.55.1-py311-cu128` |
| Python | 3.11, from the `py311` segment of the base image tag |
| CUDA | 12.8, from the `cu128` segment of the base image tag |
| `ray[data,train,tune,serve,llm]` | 2.55.1, from `requirements.txt` |
| `torch` | 2.10.0 |
| `torchvision` | 0.25.0 |
| `transformers` | 4.57.4 |
| `vllm[runai]` | 0.18.0 |
| `sentence-transformers` | 5.2.2 |
| `diffusers` | 0.32.2 |
| `accelerate` | 1.5.2 |
| `datasets` | 3.5.0 |
| `evaluate` | 0.4.3 |
| `scikit-learn` | 1.6.1 |
| `xgboost` | 2.1.4 |
| `pytorch-lightning` | 2.5.1 |
| `pandas` | 3.0.3 |
| `pyarrow` | 19.0.1 |
| `matplotlib` | 3.10.1 |
| `tensorboard` | 2.19.0 |
| `torch-tb-profiler` | 0.4.3 |
| `textdistance` | 4.6.3 |
| Head node | m5.2xlarge, from `outline-compute.md` |
| Worker nodes | 2 x g5.2xlarge, from `outline-compute.md` |
| Minimum acceleration | A10G or better, 48 GB total GPU RAM, from `outline-compute.md` |

Two limits apply to that table. The Dockerfile never pins Ray, so 2.55.1 is read from the base image tag and from `requirements.txt` rather than from a pip pin in the image build. Separately, nothing in this repository was run on that image here. Those versions were read from files, not observed at runtime.

The two Ray Core labs were executed on a local machine instead. That environment:

| Component | Version |
| --- | --- |
| Machine | Apple Silicon arm64, macOS 26.7 |
| GPU | none |
| Python | 3.11.15 |
| `ray` | 2.55.1 |
| `nbconvert` | 7.17.1 |
| `ipykernel` | 7.3.0 |
| `pandas` | 3.0.5 |
| `pyarrow` | 25.0.1 |

That environment carries newer `pandas` and `pyarrow` than the 3.0.3 and 19.0.1 pinned in `requirements.txt`. Labs 1 and 2 import only `ray`, `time`, and `random`, so the drift does not reach their results. No other notebook was run there.

## Committed notebook state

Eleven notebooks ship here. As republished, every one of them was unexecuted, with zero stored outputs and no execution counts. That is still true of nine. The two Ray Core labs are the exception, and their outputs were produced on the local machine described above.

| Notebook | Code cells | Cells with outputs | Error outputs |
| --- | --- | --- | --- |
| `1_Intro.ipynb` | 31 | 0 | 0 |
| `2_Data.ipynb` | 52 | 0 | 0 |
| `bonus/labs/Lab_1_Core_Fund.ipynb` | 5 | 2 | 0 |
| `bonus/labs/Lab_2_Core_AIAssisted.ipynb` | 2 | 1 | 0 |
| `bonus/labs/Lab_3_Data_Fund.ipynb` | 6 | 0 | 0 |
| `bonus/labs/Lab_4_Data_AIAssisted.ipynb` | 3 | 0 | 0 |
| `bonus/solutions/Lab_1_solution.ipynb` | 8 | 0 | 0 |
| `bonus/solutions/Lab_2_solution.ipynb` | 4 | 0 | 0 |
| `bonus/solutions/Lab_3_solution.ipynb` | 10 | 0 | 0 |
| `bonus/solutions/Lab_4_solution.ipynb` | 6 | 0 | 0 |
| `jobs_demo/1_Job.ipynb` | 8 | 0 | 0 |

Three notes on that table:

- Lab 1 shows outputs on two of five code cells and Lab 2 on one of two. The rest are the empty TODO stubs the labs ship with, so they print nothing. Every code cell in both notebooks holds an execution count and none produced an error output. The recorded figures are the sequential baselines the exercises ask students to beat, at 5.04 s wall for Lab 1 and 12.5 s wall for Lab 2.
- One code cell in `bonus/labs/Lab_4_Data_AIAssisted.ipynb` carries execution count 1 with no output next to it. That is a leftover counter from an authoring session whose outputs were stripped, not a partial run.
- `language_info` records Python 3.11.11 for six notebooks and 3.12.13 for three, so the set was authored across at least two environments. Labs 1 and 2 now read 3.11.15 because the local run rewrote that field. Both were authored at 3.12.13.

To strip the lab outputs and return the tree to its original state:

```
git checkout bonus/labs/Lab_1_Core_Fund.ipynb bonus/labs/Lab_2_Core_AIAssisted.ipynb
```

## Running outside Anyscale

The notebooks assume the Anyscale platform. Four things break off-platform: data paths, cluster attach, AWS credentials, and the job submit flow. The Ray code itself is portable.

The credentials one is easy to miss. `intro.py` builds a plain `boto3.client('s3')` and downloads `vectorizer.pickle` and `category_index_map.pickle` at actor construction, using the default credential chain. On a machine with no AWS credentials that raises `NoCredentialsError` rather than falling back to anonymous reads, even though the bucket is public. The bucket name and keys are hardcoded at `intro.py:28-30`, so repointing the `/mnt` prefixes does not help. Either configure credentials, or edit those lines to load the two pickles from your local mirror.

1. Mirror the data first. Every dataset and model weight lives in one public S3 prefix that Anyscale can retire at any time:

   ```
   aws s3 sync s3://anyscale-public-materials-use2/ecom <shared_dir>/ecom --no-sign-request
   ```

   The prefix holds the intro CSV and pickles, the product catalog, the catalog images, both Gemma model snapshots, and the derived embeddings dataset.

2. Repoint storage paths. The notebooks hardcode `/mnt/cluster_storage`, and the jobs demo hardcodes `/mnt/shared_storage` and `/mnt/user_storage`. Replace these prefixes with a directory that every Ray node mounts, not a driver-local one. Workers open model weights, images, and checkpoints directly.

3. Attach to your cluster. `1_Intro.ipynb` and `2_Data.ipynb` never call `ray.init()`, and the labs call bare `ray.init()`. Off Anyscale that silently starts a private single-node Ray with no cluster GPUs. Add `ray.init(address="auto")` as the first cell and run the kernel on the head node, or export `RAY_ADDRESS`.

4. Watch GPU pool sizes. `2_Data.ipynb` hardcodes actor pools of size 2 with `num_gpus=1` in several cells. On a single-GPU allocation these hang waiting for resources instead of erroring. Drop the pools to 1 or request two GPUs.

5. Pin Ray at exactly 2.55.1. The Dockerfile never pins Ray. The base image supplied 2.55.1, and the notebooks use version-sensitive symbols such as `ray.data.llm.build_processor` and `ray.data.SaveMode.OVERWRITE`. The `requirements.txt` in this repo pins it.

The jobs demo YAML uses the Anyscale Jobs schema, and `jobs_demo/1_Job.ipynb` submits it with `anyscale job submit`. On plain Ray, edit the path constants in `jobs_demo/create_extended_desc_job.py` and run it with `ray job submit` instead.

## A note on scope

This is study material, not a reproducible pipeline. It carries no datasets, no model weights, and no lockfile. `requirements.txt` holds version pins without hashes or transitive pins, so it does not rebuild the course image byte for byte.

What runs on a plain CPU machine:

- `bonus/labs/Lab_1_Core_Fund.ipynb` and `bonus/labs/Lab_2_Core_AIAssisted.ipynb`. Both are pure Ray Core over synthetic data behind a bare `ray.init()`. Both were executed here and their outputs are committed.
- `bonus/solutions/Lab_1_solution.ipynb` and `bonus/solutions/Lab_2_solution.ipynb` import the same three modules and name no external path, so they look CPU-clean by the same test. Neither was executed here, and both still ship without outputs.

What needs more than a CPU:

- `2_Data.ipynb` needs the full image even for its CPU half. The first code cell reads `from ray.data.llm import vLLMEngineProcessorConfig, build_processor` next to `torch`, `transformers`, and `sentence_transformers`. Ingest, filtering, and the expression language are ordinary tabular work that a plain `ray[data]` install would handle, but that import runs before any of it. Moving it down into the section that uses it would free the tabular half.
- `1_Intro.ipynb` calls into `intro.py`, which builds a `boto3.client('s3')` and pulls two pickles at actor construction. It needs AWS credentials.
- `bonus/labs/Lab_3_Data_Fund.ipynb` and `bonus/solutions/Lab_3_solution.ipynb` read `s3://anyscale-public-materials-use2/ecom/catalog` and write under `/mnt/cluster_storage`. The compute is CPU-sized, but both paths have to exist.
- `bonus/labs/Lab_4_Data_AIAssisted.ipynb` and `bonus/solutions/Lab_4_solution.ipynb` need a GPU and the Gemma 3 snapshot under `/mnt/cluster_storage/hf_cache`.
- `jobs_demo/` needs the Anyscale Jobs control plane. Its YAML also points at `anyscale/image/c26:5`, an Anyscale-internal image that outside accounts cannot pull.

The portability section above covers the data paths, the storage prefixes, and the cluster attach. It does not make the GPU sections run without GPUs.
