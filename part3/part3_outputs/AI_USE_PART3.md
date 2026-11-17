# AI-Use Appendix - Prathmesh Mankar

## Part 3 — Multi-Agent Image Generation System

1. **Which parts did you use an assistant for, and which did you write yourself?**

   **Ans:** I selected the agent roles, safety requirements, evaluation
   scenarios, red-team cases, selected Part 2 model, and human-evaluation
   procedure. I ran the planner, vision, prompt, generation, and critique
   agents; checked the generated images and traces; and reviewed the Gradio
   demonstration. I used the AI assistant to organize the typed shared state,
   trace format, agent functions, critique loop, red-team tests, scenario
   evaluation, CSV outputs, and Gradio interface. I also used it to explain the
   agent workflow and prepare the documentation.

2. **Give one specific thing it produced that was wrong — a tensor shape error,
   a deprecated API, a loss function that trained but was wrong, or a
   plausible-looking metric computed incorrectly. Paste the wrong output.**

   **Ans:** The assistant initially wrote the Gradio callback with a different
   number of returned values from the number of declared output components. The
   callback returned only the image and answer text, while the interface
   expected the image, planner result, vision result, prompt, critique, trace,
   and status. The app produced this error:

   ```text
   ValueError: A function (gradio_run) didn't return enough values (expected 7,
   received 2).
   ```

3. **How did you find out? What did the failure look like?**

   **Ans:** I found the failure when launching the Gradio demonstration. The
   callback completed, but Gradio checked the returned object against the
   declared output components and raised an exception because two values were
   returned for seven outputs. As a result, the interface could not display the
   planner, vision, prompt, critique, trace, and status fields.

4. **What did you change, and why does your version work?**

   **Ans:** I changed the Gradio callback to return one value for every
   declared component, in the same order as the Gradio outputs:

   ```python
   return (
       output_image,
       planner_text,
       vision_text,
       state.prompt,
       critique_text,
       trace_text,
       state.final_status,
   )
   ```

   The corrected version works because the callback contract now matches the
   interface definition. I also retained the critique loop, scenario tests,
   red-team checks, JSON traces, and safety refusal handling.

