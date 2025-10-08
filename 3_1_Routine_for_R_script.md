# Run an R job on IIASA Accelerator with **WKubeTask**

This guide shows a minimal, reproducible setup to run an R script on Accelerator using **ACCLI**’s `WKubeTask`.  
You’ll create three files and then dispatch the job:

- `main.R` — the R analysis (reads GPP/NPP CSV from Accelerator input, saves plots to output).
- `init.R` — one‑time package installer for R.
- `wkube.py` — the job configuration used by ACCLI to run `main.R` on Accelerator.

> ✅ Input is expected at `/code/input/test_file.csv` (mapped from your Accelerator storage).  
> ✅ All plots and outputs are written to `/code/plots/` (mapped back to Accelerator).

---

## 1) Folder layout (locally)

```
my_r_job/
├─ main.R
├─ init.R
├─ wkube.py
└─ (optional) test_file.csv  # only for local dry-runs
```

> You’ll upload/download data to/from Accelerator using ACCLI mappings (see steps below).

---

## 2) Prepare the three files

### A) `init.R` — one‑time R package install

```r

# init.R - Initialization script for R 4.4 base stack

# List of required packages
required_packages <- c(
#no library required for this practice 
)

# Function to install packages if not already installed
install_if_missing <- function(packages) {
  for (pkg in packages) {
    if (!requireNamespace(pkg, quietly = TRUE)) {
      install.packages(pkg, dependencies = TRUE)
    }
  }
}

# Install required packages
install_if_missing(required_packages)

# Verify installation
cat("Checking installed packages:\n")
for (pkg in required_packages) {
  if (requireNamespace(pkg, quietly = TRUE)) {
    cat(paste0("- ", pkg, ": installed successfully\n"))
  } else {
    cat(paste0("- ", pkg, ": installation FAILED\n"))
  }
}

cat("\nInitialization complete. All required packages are installed.\n")

```


### B) `main.R` — analysis script

> **Important fix**: This version consistently writes to `output_root <- "/code/plots/"` and uses `file.path()` so that the output mapping in `wkube.py` captures all generated files.

```r
# main.R
# --- No extra libraries needed ---

# ---------------------------
# Config (edit these two only)
# ---------------------------
input_root  <- "/code/input"
output_root <- "/code/plots/"

# Ensure output directory exists
if (!dir.exists(output_root)) {
  dir.create(output_root, recursive = TRUE, showWarnings = FALSE)
  cat("Created output root:", normalizePath(output_root), "\n")
}

# Input CSV expected at /code/input/test_file.csv
input_file <- file.path(input_root, "test_file.csv")
if (!file.exists(input_file)) {
  stop("Input file not found: ", input_file)
}

data <- read.csv(input_file)

# ------- Base scatter (with grid) -------
png(filename = file.path(output_root, "npp_vs_gpp_scatter_base.png"),
    width = 1600, height = 1200, res = 200)
plot(data$GPP, data$NPP,
     main = "NPP vs GPP Relationship",
     xlab = "GPP (gC/m²/year)",
     ylab = "NPP (gC/m²/year)",
     pch = 19, col = "blue")
grid()
dev.off()

# ------- Histogram: GPP -------
png(filename = file.path(output_root, "gpp_hist.png"),
    width = 1600, height = 1200, res = 200)
hist(data$GPP,
     main = "Distribution of GPP Values",
     xlab = "GPP (gC/m²/year)",
     col = "lightgreen",
     border = "black")
dev.off()

# ------- Histogram: NPP -------
png(filename = file.path(output_root, "npp_hist.png"),
    width = 1600, height = 1200, res = 200)
hist(data$NPP,
     main = "Distribution of NPP Values",
     xlab = "NPP (gC/m²/year)",
     col = "lightcoral",
     border = "black")
dev.off()

# ------- Boxplot: GPP vs NPP -------
png(filename = file.path(output_root, "gpp_npp_boxplot.png"),
    width = 1600, height = 1200, res = 200)
boxplot(data[, c("GPP", "NPP")],
        main = "Distribution Comparison: GPP vs NPP",
        ylab = "Value (gC/m²/year)",
        col = c("lightblue", "lightpink"))
dev.off()

# ------- Correlation + data export -------
correlation <- cor(data$GPP, data$NPP, use = "complete.obs")
message(sprintf("Correlation coefficient (GPP vs NPP): %.3f", correlation))

write.csv(data, file.path(output_root, "processed_npp_gpp_data.csv"), row.names = FALSE)

message("Script execution completed successfully! Plots saved in /code/plots/")

```

---

### C) `wkube.py` — job configuration (as provided)

```python
from accli import WKubeTask

myroutine = WKubeTask(
    name="test_rscript",
    job_folder='./',
    base_stack='R4_4',
    command="Rscript main.R",
    required_cores=1,
    required_ram=1024*1024*512,
    required_storage_local=1024*1024*2,
    required_storage_workflow=1024*1024,
    timeout=60*60,
    conf={
        "input_mappings": "acc://demo/xyz/:/code/input/",
        "output_mappings": "/code/plots/:acc://out"
    }
)
```
> This is your uploaded `wkube.py`. Adjust the two mappings to your actual Accelerator buckets/paths:
>
> - `input_mappings`: where `test_file.csv` resides on Accelerator (e.g., `acc://(porject name space)/(input folder on accelerator)/`).
> - `output_mappings`: where you want plots/results to land on Accelerator (e.g., `acc://(output folder)/`).
> - `/code`: When using a predefined base_stack, the default working directory is always set to /code. If you are building a custom Docker image, you can define any working directory you prefer in your environment settings. 
---


## 4) Dispatch the job

From the folder containing `wkube.py`:
```bash
#Use ACCLI’s dispatch helper
accli dispatch demo myroutine
`

> The job uses the `R4_4` base stack and runs `Rscript main.R` with the mappings you set.

---


## 5) Troubleshooting

- **`Input file not found`**: Confirm the CSV path exists in the mapped input (`accli ls <your acc path>`) and that `wkube.py` points to that path.
- **No plots in `acc://out`**: Ensure `main.R` writes under `/code/plots/` (this guide already does). Check `output_mappings` in `wkube.py`.
- **Package errors**: Run `Rscript init.R` inside your stack or add the packages to your base image. The public CRAN mirror is set in `init.R`.
- **Timeouts**: Increase `timeout` in `wkube.py` (e.g., `timeout=3*60*60`).
- **Memory**: For larger data, raise `required_ram` (e.g., `2 * 1024 * 1024 * 1024`).

---

## 6) Summary

1. Put `main.R`, `init.R`, `wkube.py` in a folder.
2. Upload `test_file.csv` to the Accelerator path used in `input_mappings`.
3. `accli dispatch demo myroutine`
4. Download results from `acc://out` (or your chosen output path).

Happy running! 🚀
