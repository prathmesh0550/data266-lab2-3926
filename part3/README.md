# DATA 266 Lab 2 — Part 3 (Shruti)

## Multimodal Multi-Agent System

Part 3 implements a five-agent image understanding and generation system:

Planner → Vision Understanding → Prompt Engineering → Image Generation → Critique

The system supports captioning, visual questions, style changes, enhancements, Pokémon-style domain generation, safety refusal, broken-input handling, and critique-based revisions.

## How to run

1. Open `notebooks/Part3_L2.ipynb` in Google Colab.
2. Select a GPU runtime.
3. Add the following Colab Secrets:
   
Because the repository is private and the models are hosted on Hugging Face, the notebook requires credentials from the person running it.

In Google Colab, open the Secrets panel and add:

- `GITHUB_TOKEN`: a GitHub token with read access to this private repository
- `HF_TOKEN`: a Hugging Face read token
4. Run the notebook using **Runtime → Restart session and run all**.
5. The notebook mounts Google Drive and creates:     /content/drive/MyDrive/data266_part3/
6. The notebook loads the Part 2 adapter from Hugging Face:  Shruti2498/data266-part2-lora-rank16
7. The notebook runs:- the five-agent pipeline
  - baseline and final scenario evaluations
  - red-team safety tests
  - human-evaluation metrics
  - the combined results table
  - the Gradio demonstration app

 Part 3 outputs are saved under:
/content/drive/MyDrive/data266_part3/outputs/

Important outputs include:
- part3_results_table.csv
- human_metrics.csv
- red_team/red_team_results.csv
- evaluation/baseline_scenario_results.csv
- evaluation/final_scenario_results.csv
- fixed_test_scenarios.json
- JSON request traces under traces/
  
Generated images and model weights are stored on Google Drive or Hugging Face rather than committed to GitHub.

Reproducibility:
The first notebook cell records:
- Git commit
- Python version
- library versions
- GPU
- random seed
  


 



