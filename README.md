# DATA 266 Lab 2

This repository contains the three notebook parts of the lab. Run the parts in
the following order:

1. `p1_rag.ipynb` — document RAG and knowledge-base question answering.
2. `p2_finetune(3).ipynb` — Stable Diffusion LoRA fine-tuning and evaluation.
3. `part3_agents.ipynb` — vision-and-generation agents, safety tests, traces,
   and the Gradio demonstration.

Part 3 uses the selected LoRA model from Part 2, so Part 2 must be completed
before Part 3.


## Environment

The notebooks are designed for a CUDA-enabled Kaggle or Linux GPU runtime.
CPU execution is possible for some preparation and validation cells, but model
loading, LoRA training, image generation, CLIP evaluation, and the agent demo
are intended for a GPU. Free GPU memory before loading multiple diffusion or
vision models.

Use Python 3.10 or newer when possible. Install the packages in the first cell
of each notebook, or install a matching `requirements.txt` once:

```bash
pip install -r requirements.txt
```

The notebooks download Hugging Face models and datasets on first use. Internet
access is therefore required during setup and model loading.

## Part 1 — RAG and document QA

Open `part1/p1_rag.ipynb` and run the cells from top to bottom.

### Inputs

The notebook searches `/kaggle/input` for files whose names contain:

- `Document.pdf`
- `Document2`
- `Document3`
- `kb_manifest.json`
- `Questions.csv`
- the official validator file

The notebook uses the student ID in the configuration cell to derive the
personalized seed, document slice, classes, and other assignment values.

### Workflow

1. Install PyMuPDF, BM25, ChromaDB, Sentence Transformers, Transformers,
   `bitsandbytes`, OCR, and related packages.
2. Extract text and figures from the three PDF documents.
3. Generate captions and OCR text for visual items.
4. Build dense embeddings with `BAAI/bge-small-en-v1.5` and a persistent
   Chroma collection.
5. Combine dense retrieval with BM25 lexical retrieval.
6. Answer questions with Qwen 7B in 4-bit mode, with a 3B fallback.
7. Apply citations, status labels, conversational memory, safety checks, and
   visual-reference validation.
8. Write and validate the final submission.

### Main output

```text
part1/submission.csv
part1/part1_artifacts_019142539/submission_with_timing.csv
```

The submission is checked for the required columns, row order, valid statuses,
valid citations, and official-validator success. The notebook also records
runtime information and a `run_config.json` artifact.

## Part 2 — Stable Diffusion LoRA fine-tuning

Open `part2/p2_finetune.ipynb` and run all cells in order.

### Data and model

- Base model: `runwayml/stable-diffusion-v1-5`
- Dataset: `lambdalabs/naruto-blip-captions`
- Resolution: `512 x 512`
- Split: approximately 90% training and 10% test after cleaning
- Training seed: `266`
- Personalized slice: derived from `SID4`
- Hyperparameter arm: selected from `HP_ARMS` using `HP_ID`

The notebook verifies image readability, removes duplicates, creates the split,
resizes images, and writes `metadata.jsonl` files before training.

### Experimental runs

Two LoRA runs are trained with the same prepared data and number of steps but
different LoRA ranks. The notebook then compares:

- training time and GPU memory
- KID and CLIP quality metrics
- LPIPS seed diversity
- NSFW filtering
- memorization and closest-training-image checks
- blinded human evaluation
- failure-mode examples

The notebook generates 20 prompts and 40 images per model where required by the
assignment, then selects a final model using the documented ranking criteria.

### Main output

```text
part2/part2_outputs/
├── prepared/train/
├── prepared/test/
├── run_a_lora_rank4/
├── run_b_lora_rank16/
├── generated/{base,run_a,run_b}/
├── evaluation/
└── grids/
```

Important evaluation files include:

- `evaluation/training_comparison_table.csv`
- `evaluation/automatic_metrics.csv`
- `evaluation/nsfw_filter_report.csv`
- `evaluation/part2_results_table.csv`
- `evaluation/memorization_check.csv`
- `evaluation/failure_mode_examples.csv`
- human-evaluation CSV files and summaries

The notebook can optionally upload LoRA adapters and generated images to
Hugging Face. Set the username and authentication token only in the runtime;
never commit credentials.

## Part 3 — Multi-agent image system

Open `part3/part3_agents.ipynb` after Part 2 has completed.

Part 3 loads the selected Part 2 LoRA model. The current notebook configuration
selects `run_a`; update `FINAL_MODEL` only if the Part 2 selection changes.

### Agent workflow

1. **Planner** checks the request, selects a route, and applies safety rules.
2. **Vision Understanding** produces captions, object information, OCR text,
   and visual-question answers when an input image is provided.
3. **Prompt Engineering** converts the request and visual evidence into a
   generation prompt while preserving user intent.
4. **Image Generation** uses Stable Diffusion text-to-image or image-to-image
   generation with the selected LoRA.
5. **Critique** checks CLIP alignment, safety, and output quality, then may
   request up to two revisions.

All agents share a typed `RequestState`. Each request records timing, inputs,
outputs, errors, revisions, and final status in a JSON trace.

### Evaluation and demo

The notebook runs:

- 15 predefined scenarios
- comparison with and without the critique loop
- 10 red-team cases
- human-evaluation template and agreement calculation
- a Gradio interactive demonstration

Run the Gradio cell last. It accepts an optional input image and a user request,
then displays the generated image, agent outputs, critique information, trace,
and final status.

### Main output

```text
part3/part3_outputs/
├── generated/
├── evaluation/
│   ├── scenario_results_without_critique.csv
│   ├── scenario_results_with_critique.csv
│   ├── critique_comparison.csv
│   ├── red_team_results.csv
│   ├── red_team_summary.csv
│   ├── human_evaluation_template.csv
│   └── human_evaluation_summary.csv
├── red_team/
└── traces/
```

## Reproducibility checklist

- Run notebooks from top to bottom in Parts 1, 2, 3 order.
- Keep the configured student ID and seeds unchanged for the submitted run.
- Record the commit, Python version, PyTorch version, GPU, and model versions.
- Keep the same base model, dataset, prompt list, and evaluation settings when
  comparing runs.
- Review generated images and complete the human-evaluation files before
  claiming final human results.
- Confirm that no secrets, private data, or large generated directories are
  committed.

## Troubleshooting

- **CUDA out-of-memory:** restart the runtime, use a GPU with more VRAM, close
  unused pipelines, and execute model cells one section at a time.
- **Missing Kaggle files:** attach or mount the required input dataset and
  verify that its filenames contain the patterns used in Part 1.
- **Missing Part 2 model in Part 3:** finish Part 2 or update the selected
  Hugging Face repository in the Part 3 configuration cell.
- **Hugging Face authentication failure:** log in in the runtime and keep the
  token out of notebooks and Git.
- **Gradio does not start:** run the notebook cell in a supported notebook
  runtime and use the displayed local/share URL.

