# Part 2: Fine-Tuning Diffusion Models for Domain-Specific Image Generation

This part fine-tunes Stable Diffusion 1.5 using LoRA adapters on the
Pokémon image-caption dataset.

Two LoRA configurations were trained using the same dataset, seed, learning
rate, batch size, gradient accumulation, resolution, and training-step budget.

The large generated image collection and trained adapter weights are hosted
on Hugging Face rather than GitHub.

Dataset
Dataset: reach-vb/pokemon-blip-captions

The dataset has Pokémon image-caption pairs and is used for non-commercial academic research. The dataset is licensed under
CC BY-NC-SA 4.0.

The cleaned dataset has 833 images:
- Training images: 750
- Test images: 83
- Split: 90/10
- Split seed: 266
- Resolution: 512×512
- Corrupted images removed: 0
- Duplicate images removed: 0
  
Models
Base model: stable-diffusion-v1-5/stable-diffusion-v1-5

LoRA runs:
- Rank 4: Shruti2498/data266-part2-lora-rank4
- Rank 16: Shruti2498/data266-part2-lora-rank16
  
The rank-16 adapter is final model because it has the lowest KID and stronger domain-specific Pokémon behavior.

Evaluation:
- KID between generated in-domain images and held-out test images
- CLIP prompt-image similarity
- LPIPS diversity across the two seeds
- Blinded comparison of 25 base-versus-final image pairs
- Memorization using nearest training-image CLIP similarity
- Safety-filter flags
- Training time and peak GPU memory
  
The fixed evaluation set:
- 10 in-domain prompts
- 5 out-of-domain prompts
- 5 compositional prompts
Each prompt was generated with seeds 0 and 1 for:
- the base model
- the rank-4 LoRA model
- the rank-16 LoRA model
This gives 120 generated images in total.

Hugging Face links
Generated image dataset:

https://huggingface.co/datasets/Shruti2498/data266-part2-pokemon-generations

Rank-4 LoRA adapter:

https://huggingface.co/Shruti2498/data266-part2-lora-rank4

Rank-16 LoRA adapter:

https://huggingface.co/Shruti2498/data266-part2-lora-rank16

How to run
1. Open notebooks/Part2_L2.ipynb in Google Colab.
2. Select a GPU runtime.
3. Add GITHUB_TOKEN and HF_TOKEN to Colab Secrets.
4. Run the notebook from the beginning.
5. Confirm that the first cell prints the Git commit, library versions,
   GPU, and random seed.
6. The fixed-prompt cell must execute before either fine-tuning run.
7. Run all the notebook cells.
8. Save the executed notebook with all outputs.
The notebook writes persistent outputs under:
/content/drive/MyDrive/data266_part2/

Reproducibility
Seed: 266
Resolution: 512×512
Training steps per run: 400
Batch size: 1
Gradient accumulation: 4
Learning rate: 0.0001
Generation seeds: 0 and 1
Inference steps: 30
Sampler: DDIM
Guidance scale: 7.5

The fixed prompts are saved in:
results/fixed_prompts.csv

The generated-image metadata is saved in:
results/generation_metadata.csv

Safety and limitations
The Stable Diffusion safety checker was used during image generation.
Safety-filter results are in safety_summary.csv.

Safety checker: screening mechanism, does not guarantee all unsafe content will be detected.

The dataset contains copyrighted character artwork. The trained models and
generated images are intended only for non-commercial academic research.
