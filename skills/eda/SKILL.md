---
name: eda
description: Performs Exploratory Data Analysis (EDA) using Polars and Seaborn. Generates scripts in py:percent format optimized for leaderboard climbing through target-variable relationship analysis and hypothesis generation.
license: MIT
compatibility: Requires polars, seaborn, matplotlib, jupytext, and nbconvert.
---

# EDA Skill

This skill guides the generation of EDA scripts designed to uncover insights that improve leaderboard rankings.

# Workflow

## Step 1: Initialize Directory
```bash
mkdir -p eda
```

## Step 2: EDA in python-repl
Clear the content of the python-repl. Use the python-repl mcp-server in a loop to do the EDA. If you find something interesting add it to the final eda-script. This way we persist interesting observations and hypothese for later experimentation phase.

## Step 3: Verify that EDA report
Generate the eda report from the python script. Make sure that your findings and hypothesis you derived during working in the REPL are actually reproducible from the eda-script

## Instructions

1.  **Naming Convention**: Use the format `description.py` (e.g., `target_skew_and_outliers.py` or `categorical_feature_interactions.py`).
2.  **Format**: Use `py:percent` format for Jupytext compatibility.
3.  **Libraries**: Use `polars` for data and `seaborn` for visuals.
4.  **Context Awareness**: Before starting a new EDA, list existing files in `eda/**/*.md` to avoid redundancy and build upon previous findings.
5.  **Focus Areas**:
    *   **Target Analysis**: Distribution, outliers, and skewness of the target variable.
    *   **Feature Correlation**: Relationship between features and the target.
    *   **Hypothesis Generation**: Document specific observations that suggest feature engineering ideas (e.g., "Feature X shows a non-linear relationship with Target Y, suggest polynomial expansion").
6.  **Hypothesis Logging**: Every EDA script must include a code block at the end that appends new hypotheses to `hypotheses.jsonl`.
7.  **Schema**: Each log entry should contain: `date`, `source_file`, `hypothesis`, `priority` (1-3), and `status` (pending/tested).

## Directory Structure
All EDA outputs must be stored in the `eda/` subdirectory of the project:
`project_root/eda/<3-digit-number-with-leading-zeros>/<descriptive_name>.py`

## Example eda-script
Create a script focusing on a specific subset of features or a specific hypothesis. In case of the first EDA, freely explore the data without restricting to a specific subset of features or a specific hypothesis.

```python
# %% [markdown]
# # EDA: [Descriptive Title]
# # Purpose: [Specific hypothesis being tested]
# %%
import polars as pl
import seaborn as sns
import matplotlib.pyplot as plt

# %%
df = pl.read_csv("train.csv")

# %% [markdown]
# ## Target Variable Analysis
# %%
# Analyze target distribution and statistics

# %% [markdown]
# ## Feature-Target Relationships
# %%
# Visualize correlations and conditional distributions

# %% [markdown]
# ## Log Hypotheses
# %%
import json
from datetime import datetime

new_hypotheses = [
    {
        "date": datetime.now().isoformat(),
        "source": "eda/004_target_analysis.py",
        "hypothesis": "Log-transforming feature 'X' will reduce skewness and improve RMSE.",
    }
]

with open("hypotheses.jsonl", "a") as f:
    for h in new_hypotheses:
        f.write(json.dumps(h) + "\n")
```

## Generating the report from the eda-script
```bash
export EDA_NAME="eda/006/feature_x_analysis"
jupytext --set-formats ipynb,py:percent ${EDA_NAME}.py
jupyter nbconvert --to markdown --execute ${EDA_NAME}.ipynb
```

## Edge Cases
- **Large Datasets**: Use `df.sample()` or `n_rows` in `read_csv` if the dataset exceeds memory.
- **Missing Values**: Use `df.null_count()` to identify gaps before plotting.
- **Filename Collisions**: Always ensure that the three-digit-number with leading zeros exists to store the eda-files.
