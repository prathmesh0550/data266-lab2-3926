# AI-Use Appendix - Prathmesh Mankar

## Part 2 — Stable Diffusion LoRA Fine-Tuning

1. **Which parts did you use an assistant for, and which did you write yourself?**

   **Ans:** I selected the base model, dataset, student-ID settings, training
   configuration, prompt groups, evaluation criteria, and final model-selection
   process. I ran the dataset cleaning, train/test split, LoRA training,
   generation, metric calculations, safety checks, memorization checks, and
   human-evaluation preparation. I used the AI assistant to organize the
   notebook into sequential cells, create initial Diffusers and PEFT training
   code, explain LoRA and the evaluation metrics, generate comparison tables
   and plots, troubleshoot output paths, and prepare the documentation.

2. **Give one specific thing it produced that was wrong — a tensor shape error,
   a deprecated API, a loss function that trained but was wrong, or a
   plausible-looking metric computed incorrectly. Paste the wrong output.**

   **Ans:** The assistant initially created the training configuration with
   half-precision tensors even when the notebook was running on CPU. The
   problematic configuration was:

   ```python
   DEVICE = "cpu"
   DTYPE = torch.float16
   pipe = StableDiffusionPipeline.from_pretrained(
       BASE_MODEL, torch_dtype=DTYPE
   ).to(DEVICE)
   ```

   The model-loading cell failed with:

   ```text
   RuntimeError: "slow_conv2d_cpu" not implemented for 'Half'
   ```

   This was a model/runtime error because the CPU execution path did not
   support all of the required float16 convolution operations.

3. **How did you find out? What did the failure look like?**

   **Ans:** I found the failure when the same notebook was tested in a CPU-only
   runtime. Stable Diffusion attempted to execute a float16 convolution on the
   CPU and stopped during model inference. The error showed that the precision
   setting had to depend on the available device.

4. **What did you change, and why does your version work?**

   **Ans:** I changed the precision selection to use float16 only on CUDA and
   float32 on CPU:

   ```python
   DEVICE = "cuda" if torch.cuda.is_available() else "cpu"
   DTYPE = torch.float16 if DEVICE == "cuda" else torch.float32
   ```

   I also kept both runs on the same prepared dataset and comparable training
   steps, generated the same prompt set for each model, and retained the KID,
   CLIP, LPIPS, safety, memorization, and human-evaluation checks. The corrected
   version works because the model uses a precision supported by the runtime.

