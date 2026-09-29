# Automated CSV Profiler

## 1. Purpose

This system takes any reasonably well-formed, single-table CSV file and automatically
produces a data profile: column types and roles, data-quality checks, descriptive
statistics, adaptive visualizations, and evidence-based narrative insights.

The design follows one rule throughout: **Python computes every statistic first; the
language model is only allowed to explain and organize results that have already been
calculated and verified.** The LLM never invents numbers, guesses column meanings, or
claims correlation implies causation.

Pipeline:

```
CSV File → Python Analysis → Verified Summary → LLM Explanation → Generated Report
```

The same code runs on any supported CSV — no dataset-specific logic. Switching datasets
means changing a file path, nothing else.

## 2. Python version

Developed and tested with **Python 3.12** (3.10+ should also work).

## 3. Required libraries

See `requirements.txt`. At minimum:

- `pandas`
- `numpy`
- `matplotlib`
- `seaborn`
- `requests` (used for the local Ollama API call)
- `jupyter` (to run the notebook)

## 4. Installation

```bash
git clone <this repo>
cd automated_csv_profiler
python -m venv .venv
source .venv/bin/activate      # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

If using the LLM component with **Ollama** (recommended — no API key required):

```bash
# Install Ollama: https://ollama.com/download
ollama pull <MODEL_NAME>       # TODO: put the exact model you used, e.g. llama3.1
ollama serve                   # starts the local server the notebook calls
```

## 5. How to run

1. Place your CSV file in the `data/` folder.
2. Open `src/profiler.ipynb` in Jupyter (`jupyter notebook src/profiler.ipynb` or via
   VS Code / JupyterLab).
3. Run all cells top to bottom (Run → Run All Cells).
4. Output is written to `output/<csv_filename_without_extension>/`, containing:
   - `report.md` — the generated report
   - `column_profile.csv` — per-column type/role/missingness table
   - `analysis_summary.json` — the full verified summary (also what's passed to the LLM)
   - `plots/` — saved chart images
   - `llm_prompt.txt` / `llm_response.txt` — the exact prompt sent and response received

## 6. How to select a CSV file

Edit the **Run configuration** cell near the bottom of the notebook — this is the only
cell that should change between datasets:

```python
CSV_PATH = "../data/dataset_a.csv"   # <- change this to your file
OUTPUT_DIR = "../output"
USE_LLM = False
```

No other code needs to change. If something needs tuning for a new dataset (e.g. the
30% missing-data threshold), that goes in the shared `CONFIG` dictionary near the top
of the notebook — it then applies to every dataset, not just one.

## 7. Enabling or disabling the LLM component

Set `USE_LLM = True` or `USE_LLM = False` in the Run configuration cell.

- `True`: the verified JSON summary is sent to the model; `llm_prompt.txt` and
  `llm_response.txt` are saved; insights are checked against the summary before being
  added to the report.
- `False`, or if the model/API is unavailable: the report still generates fully
  (overview, data quality, statistics, plots) and states plainly that the AI narrative
  was skipped.

## 8. Model used

<!-- TODO: fill in once the LLM step is finalized, e.g.:
"Ollama running <model name>, locally, via the /api/generate endpoint."
or
"OpenAI/Anthropic/Gemini API, model <name>, via <library>." -->

## 9. Known limitations

- Supports a single, flat CSV table only — no Excel, JSON, multi-sheet, or nested data.
- Type and role inference is heuristic; it can misclassify unusual columns (e.g. numeric
  codes that are really categorical, or dates in nonstandard formats) and does not infer
  the true meaning of undocumented columns.
- The sensitive-field warning is a heuristic based on column names and value patterns.
  **The absence of a warning does not mean a dataset is free of sensitive data.**
- Outlier detection uses the 1.5 × IQR rule, which is a convention, not a guarantee that
  flagged points are actually erroneous.
- Visualizations and stats are capped (e.g. top 10 categories, up to 10 plots) to keep
  the report readable, so very high-cardinality columns are summarized, not shown in full.
- LLM narrative quality depends on the model used and is checked only against the
  numbers/columns it cites, not for overall accuracy of phrasing.
- Not designed for very large files that don't fit in memory, or for streaming data.

<!-- TODO: add anything else you find while testing Dataset B -->

## 10. Dataset sources

### Dataset A

<!-- TODO fill in:
- Agency/organization:
- Dataset title:
- Source link:
- Date accessed:
-->

### Dataset B

<!-- TODO fill in:
- Agency/organization:
- Dataset title:
- Source link:
- Date accessed:
-->

## Repository structure

```
automated_csv_profiler/
├── README.md
├── requirements.txt
├── src/
│   └── profiler.ipynb
├── data/
│   └── README.md
├── output/
│   ├── dataset_a/
│   └── dataset_b/
├── video/
│   └── video_link.txt
└── logs/
    └── genai_log.md
```
