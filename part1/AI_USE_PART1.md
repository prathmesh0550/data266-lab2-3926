# AI-Use Appendix - Prathmesh Mankar

## Part 1 — RAG and Document Question Answering

1. **Which parts did you use an assistant for, and which did you write yourself?**

   **Ans:** I selected the input documents, student-ID configuration, retrieval
   requirements, output columns, safety behavior, and validation procedure. I
   ran the notebook, checked the retrieved evidence and generated answers, and
   verified the final submission file. I used the AI assistant to organize the
   RAG workflow, create initial code for PDF extraction, OCR, image captioning,
   ChromaDB indexing, BM25 retrieval, question answering, conversational
   memory, citation formatting, timing, and validation. I also used it to
   explain the assignment requirements and prepare the documentation.

2. **Give one specific thing it produced that was wrong — a tensor shape error,
   a deprecated API, a loss function that trained but was wrong, or a
   plausible-looking metric computed incorrectly. Paste the wrong output.**

   **Ans:** The assistant initially produced BLIP image-captioning code that
   created the model on the GPU but left the processor tensors on the CPU. The
   code was:

   ```python
   inputs = figure_processor(images=image, return_tensors="pt")
   output = figure_model.generate(**inputs)
   ```

   The notebook then produced this runtime error:

   ```text
   RuntimeError: Expected all tensors to be on the same device, but found
   tensors on cpu and cuda:0.
   ```

3. **How did you find out? What did the failure look like?**

   **Ans:** I found the failure when the BLIP captioning cell was executed on a
   CUDA runtime. The model was on cuda:0, while the processor-generated input
   tensors remained on the CPU. The error occurred before caption generation,
   so no visual captions were added to the knowledge base.

4. **What did you change, and why does your version work?**

   **Ans:** I moved the processor inputs to the same device as the BLIP model
   before calling generate:

   ```python
   inputs = {
       key: value.to(figure_device)
       for key, value in inputs.items()
   }
   output = figure_model.generate(**inputs)
   ```

   I also retained the visual-label lookup, hybrid retrieval, citation checks,
   and official validation. The corrected version works because both the model
   and its input tensors use the same device.

