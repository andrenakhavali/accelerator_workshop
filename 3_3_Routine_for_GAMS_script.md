# Run a **GAMS** job on IIASA Accelerator with `WKubeTask`

This guide shows a minimal, reproducible setup to run a **GAMS** script on Accelerator using **ACCLI**’s `WKubeTask`.

You’ll create two files and then dispatch the job:

- `main.gms` — the GAMS model (creates a CSV at `/code/output/test.csv`).
- `wkube.py` — the job configuration used by ACCLI to run `main.gms` on Accelerator.

> ✅ No input mapping required.  
> ✅ Output CSV is written to **`/code/output/test.csv`** (mapped back to Accelerator).

---

## 1) Folder layout (locally)

```
my_gams_job/
├─ main.gms
└─ wkube.py
```

> You’ll download results from Accelerator using the output mapping in `wkube.py`.

---

## 2) Prepare the files

### A) `main.gms` — GAMS script (writes a CSV)

> This example defines a small set, fills simple values for `GPP` and `NPP`, and writes them to `/code/output/test.csv` using `put`.

```gams
* main.gms
* Minimal GAMS script to produce /code/output/test.csv

$ontext
This script:
  - Ensures /code/output exists (via a shell call)
  - Creates a small data table (GPP, NPP) over a set i
  - Writes a CSV with header: i,GPP,NPP
$offtext

* Make sure the output directory exists (requires a POSIX shell in the base stack)
execute 'mkdir -p /code/output';

Sets
    i  / i1*i5 /;

Parameters
    GPP(i)  "Gross Primary Productivity (gC/m^2/yr)"
    NPP(i)  "Net Primary Productivity (gC/m^2/yr)";

* Simple, deterministic example values
GPP(i) = 1000 + ord(i)*50;  * i1..i5 => 1050, 1100, ...
NPP(i) =  500 + ord(i)*30;  * i1..i5 =>  530, 560, ...

* Write CSV
File f / '/code/output/test.csv' /;
put f;
put 'i,GPP,NPP',/;

loop(i,
  put i.tl:0, ',' , GPP(i):0:6, ',' , NPP(i):0:6, /;
);

putclose f;

display 'CSV written to /code/output/test.csv';
```

---

### B) `wkube.py` — job configuration

> Adjust `base_stack` to your **GAMS** stack name on Accelerator (e.g., `GAMS_40`, `GAMS_43`, etc.). No input mapping needed. We map `/code/output/` to an Accelerator path (`acc://out`) to collect the CSV.

```python
from accli import WKubeTask

myroutine = WKubeTask(
    name="test_gams_script",
    job_folder='./',
    base_stack='GAMS',          # <-- set to the GAMS base stack available on Accelerator
    command="gams main.gms lo=2",
    required_cores=1,
    required_ram=1024*1024*256,  # 256 MB is plenty for this example
    required_storage_local=1024*1024*2,
    required_storage_workflow=1024*1024,
    timeout=30*60,               # 30 minutes
    conf={
        # No input mappings required
        "output_mappings": "/code/output/:acc://out"
    }
)
```

---

## 3) Dispatch the job

From the folder containing `wkube.py`:
```bash
# Option A (recommended): ACCLI dispatch helper
accli dispatch . myroutine

# Option B: plain Python
python -c "from wkube import myroutine; myroutine.dispatch()"
```

The job uses the GAMS base stack and runs `gams main.gms`.

---

## 4) Retrieve the outputs

Your `wkube.py` maps `/code/output/` to `acc://out`. List or pull them:
```bash
accli ls acc://out
accli pull acc://out ./outputs_local
```

You should see `test.csv` in `./outputs_local/`.

---

## 5) Quick local dry-run (optional)

If you have GAMS locally, you can test the script without Accelerator:
```bash
# From the folder with main.gms
gams main.gms lo=2

# Check output
cat /code/output/test.csv   # if you used absolute path in the script
# or, if you change the file path to a relative one, check the local file instead
```

> If you prefer a relative path locally, change the line  
> `File f / '/code/output/test.csv' /;`  
> to  
> `File f / 'test.csv' /;`  
> and remove the `mkdir` line. On Accelerator we keep the absolute path so the output mapping picks it up.

---

## 6) Troubleshooting

- **`Cannot open /code/output/test.csv`**: Ensure your GAMS stack has a POSIX shell for the `mkdir` call. If not, either:
  - Pre-create `/code/output/` via the container image/base stack, or
  - Change the CSV path to a relative filename (e.g., `test.csv`) and map `/code/` to your output in `wkube.py`:
    ```python
    conf={"output_mappings": "/code/:acc://out"}
    ```
- **`base_stack` not found**: Use the exact GAMS stack name available on your Accelerator.
- **No file in `acc://out`**: Confirm that the CSV path in `main.gms` matches the mapped directory in `wkube.py`.

---

## 7) Summary

1. Put `main.gms` and `wkube.py` in a folder.  
2. `accli dispatch . myroutine`  
3. Download `test.csv` from `acc://out`.

Happy running! 🚀
