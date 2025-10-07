# Run a **Python** job on IIASA Accelerator with `WKubeTask`

This guide shows a minimal, reproducible setup to run a **Python** script on Accelerator using **ACCLI**’s `WKubeTask`.

You’ll create three files and then dispatch the job:

- `main.py` — the Python analysis (reads **/code/input/test.csv** and writes plots/results to **/code/plots/**).
- `requirements.txt` — one‑time Python dependency spec.
- `wkube.py` — the job configuration used by ACCLI to run `main.py` on Accelerator.

> ✅ Input is expected at **`/code/input/test.csv`** (mapped from your Accelerator storage).  
> ✅ All plots and outputs are written to **`/code/plots/`** (mapped back to Accelerator).  
> ✅ When using a predefined `base_stack`, the working directory inside the job is **`/code`**. If you build a custom image, you can choose a different workdir.

---

## 1) Folder layout (locally)

```
my_py_job/
├─ main.py
├─ requirements.txt
├─ wkube.py
└─ (optional) test.csv  # only for local dry-runs
```

> You’ll upload/download data to/from Accelerator using ACCLI mappings (see steps below).

---

## 2) Prepare the three files

### A) `requirements.txt` — Python packages

```txt
pandas>=2.0
matplotlib>=3.7
```

> The official Python base stacks generally include Python and `pip`. These two packages are lightweight and widely available.

---

### B) `main.py` — analysis script

> **Important**: This script always writes to `/code/plots/`, so `output_mappings` will capture everything.

```python
# main.py
import os
import sys
import pandas as pd
import matplotlib.pyplot as plt

INPUT_ROOT = "/code/input"
OUTPUT_ROOT = "/code/plots"

os.makedirs(OUTPUT_ROOT, exist_ok=True)

input_file = os.path.join(INPUT_ROOT, "test.csv")
if not os.path.exists(input_file):
    sys.exit(f"Input file not found: {input_file}")

# Read data
df = pd.read_csv(input_file)

# Basic sanity checks
for col in ["GPP", "NPP"]:
    if col not in df.columns:
        sys.exit(f"Required column '{col}' not found in {input_file}")

# -------- Scatter: NPP vs GPP --------
plt.figure()
plt.scatter(df["GPP"], df["NPP"], s=10)
plt.title("NPP vs GPP Relationship")
plt.xlabel("GPP (gC/m²/year)")
plt.ylabel("NPP (gC/m²/year)")
plt.grid(True, linewidth=0.3)
plt.tight_layout()
plt.savefig(os.path.join(OUTPUT_ROOT, "npp_vs_gpp_scatter.png"), dpi=200)
plt.close()

# -------- Histogram: GPP --------
plt.figure()
plt.hist(df["GPP"].dropna(), bins=40)
plt.title("Distribution of GPP Values")
plt.xlabel("GPP (gC/m²/year)")
plt.ylabel("Frequency")
plt.tight_layout()
plt.savefig(os.path.join(OUTPUT_ROOT, "gpp_hist.png"), dpi=200)
plt.close()

# -------- Histogram: NPP --------
plt.figure()
plt.hist(df["NPP"].dropna(), bins=40)
plt.title("Distribution of NPP Values")
plt.xlabel("NPP (gC/m²/year)")
plt.ylabel("Frequency")
plt.tight_layout()
plt.savefig(os.path.join(OUTPUT_ROOT, "npp_hist.png"), dpi=200)
plt.close()

# -------- Boxplot: GPP vs NPP --------
plt.figure()
plt.boxplot([df["GPP"].dropna(), df["NPP"].dropna()], labels=["GPP", "NPP"])
plt.title("Distribution Comparison: GPP vs NPP")
plt.ylabel("Value (gC/m²/year)")
plt.tight_layout()
plt.savefig(os.path.join(OUTPUT_ROOT, "gpp_npp_boxplot.png"), dpi=200)
plt.close()

# -------- Correlation + data export --------
corr = df[["GPP", "NPP"]].corr().iloc[0, 1]
print(f"Correlation coefficient (GPP vs NPP): {corr:.3f}")

# Save a processed copy (example: unchanged, but validated)
df.to_csv(os.path.join(OUTPUT_ROOT, "processed_test.csv"), index=False)

print("Script execution completed successfully! Plots saved in /code/plots/")
```

---

### C) `wkube.py` — job configuration

> Adjust `base_stack` to your Python stack name on Accelerator (e.g., `Python3_11`) and update the two mappings.

```python
from accli import WKubeTask

myroutine = WKubeTask(
    name="test_python_script",
    job_folder='./',
    base_stack='Python3_7',   
    command="python main.py",
    required_cores=1,
    required_ram=1024*1024*512,
    required_storage_local=1024*1024*2,
    required_storage_workflow=1024*1024,
    timeout=60*60,
    conf={
        # Map your Accelerator bucket/folder containing test.csv to /code/input/
        "input_mappings": "acc://demo/xyz/:/code/input/",
        # Map /code/plots/ back to an Accelerator path (change 'out' to your target)
        "output_mappings": "/code/plots/:acc://out"
    }
)
```

---

## 3) Dispatch the job

From the folder containing `wkube.py`:
```
# ACCLI dispatch helper
accli dispatch demo myroutine

```

The job runs `python main.py` in the `Python3_7` base stack.

---


## 4) Troubleshooting

- **`Input file not found`**: Confirm the CSV exists in the mapped input (`accli ls <your acc path>`) and `wkube.py` points to that path.
- **No plots in `acc://out`**: Ensure `main.py` writes under `/code/plots/` (this guide already does). Check `output_mappings` in `wkube.py`.
- **Package errors**: Verify `requirements.txt` contents and that the install step runs in `command`.
- **Timeouts**: Increase `timeout` in `wkube.py` (e.g., `timeout=3*60*60`).
- **Memory**: For larger data, raise `required_ram` (e.g., `2 * 1024 * 1024 * 1024`).

---

## 5) Summary

1. Put `main.py`, `requirements.txt`, `wkube.py` in a folder.
2. Upload `test.csv` to the Accelerator path used in `input_mappings`.
3. `accli dispatch . myroutine`
4. Download results from `acc://out` (or your chosen output path).

Happy running! 🚀
