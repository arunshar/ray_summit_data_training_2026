# Ray Summit 2026: Multimodal Data Processing Pipelines for AI Systems

Course material for the Ray Summit 2026 training "Multimodal Data Pipelines for AI Systems". The course teaches large-scale data processing and multimodal inference with Ray Data through an end-to-end e-commerce scenario.

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

- Planned class compute: one m5.2xlarge head node plus two g5.2xlarge workers (one NVIDIA A10G with 24 GB each).
- Minimum: A10G or better acceleration, 48 GB total GPU RAM.
- Labs 1 and 2 are pure Ray Core on synthetic data and need no GPU.
- The jobs demo YAML requests eight g5.2xlarge workers, but the script sizes its actor pool to whatever GPU count Ray reports, so smaller allocations work with proportionally longer runtime.

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

## Provenance

This is Ray Summit 2026 Anyscale course material, republished with permission for personal study. Original content copyright 2026 Anyscale.
