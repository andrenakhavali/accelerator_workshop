# Run a **GAMS** job on IIASA Accelerator with `WKubeTask`

This guide shows a minimal yet realistic setup to run a **GAMS** script on Accelerator using **ACCLI**’s `WKubeTask`.  
You’ll create two files and dispatch the job directly to the Accelerator.

The exercise simulates a **mock GLOBIOM-style model output**: it defines regions, commodities, scenarios, and years; computes production, area, yield, prices, and emissions; and writes the results into a CSV file.

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

### A) `main.gms` — GAMS script (generates a mock GLOBIOM-style CSV)

> This example creates multi-dimensional data for regions, commodities, and scenarios, then calculates derived indicators and writes them to `/code/output/globiom_mock.csv`.

```gams
execute 'mkdir -p /code/output';

set r /EU27 USA BRA CHN/;
set c /Wheat Maize Soy/;
set s /Baseline Mitigation/;
set y /y2020 y2030 y2040/;

parameter YearVal(y);
YearVal(y) = 2020 + 10*(ord(y)-1);

scalar baseProd /2000/;
scalar baseArea /500/;
scalar basePrice /250/;
scalar baseEmis /30/;

parameter rMult(r);
parameter cMult(c);
parameter sMult(s);
parameter yMult(y);

rMult(r) = 1.00 + 0.05*(ord(r)-1);
cMult(c) = 1.00 + 0.10*(ord(c)-1);
sMult(s) = 1.00 - 0.03*(ord(s)-1);
yMult(y) = 1.00 + 0.10*(ord(y)-1);

parameter Production(r,c,s,y);
parameter Area(r,c,s,y);
parameter Yield(r,c,s,y);
parameter Price(r,c,s,y);
parameter Emissions(r,c,s,y);
parameter EI(r,c,s,y);

Production(r,c,s,y) = baseProd  * rMult(r) * cMult(c) * sMult(s) * yMult(y);
Area(r,c,s,y)       = baseArea  * rMult(r) * cMult(c) * sMult(s) * (0.90 + 0.05*ord(y));
Yield(r,c,s,y)      = Production(r,c,s,y) / Area(r,c,s,y);
Price(r,c,s,y)      = basePrice * (0.95 + 0.02*ord(y)) * (1 - 0.03*(ord(s)-1));
Emissions(r,c,s,y)  = baseEmis  * rMult(r) * cMult(c) * (1 - 0.10*(ord(s)-1)) * (0.98 + 0.01*ord(y));
EI(r,c,s,y)         = (Emissions(r,c,s,y) * 1e6) / (Production(r,c,s,y) * 1e3);

file f /'/code/output/globiom_mock.csv'/;
f.nd = 3;
put f;
put 'region,commodity,scenario,year,production_kT,area_kha,yield_t_per_ha,price_USD_per_t,emissions_MtCO2e,emiss_intensity_tCO2e_per_t'/;

loop(r,
  loop(c,
    loop(s,
      loop(y,
        put r.tl:0, ',';
        put c.tl:0, ',';
        put s.tl:0, ',';
        put YearVal(y):0:0, ',';
        put Production(r,c,s,y):0:3, ',';
        put Area(r,c,s,y):0:3, ',';
        put Yield(r,c,s,y):0:3, ',';
        put Price(r,c,s,y):0:3, ',';
        put Emissions(r,c,s,y):0:3, ',';
        put EI(r,c,s,y):0:3;
        put /;
      )
    )
  )
);

putclose f;
```

---

### B) `wkube.py` — job configuration

> Adjust `base_stack` to your **GAMS** stack name on Accelerator (e.g., `GAMS_40`, `GAMS_43`, etc.).  
> Output mapping ensures `/code/output/` content is saved to your Accelerator storage.

```python
from accli import WKubeTask

myroutine = WKubeTask(
    name="globiom_mock_gams",
    job_folder='./',
    base_stack='GAMS40_1__R4_0',          
    command="gams main.gms lo=2",
    required_cores=1,
    required_ram=1024*1024*256,  # 256 MB
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
```
# ACCLI dispatch helper
accli dispatch demo myroutine

```

The job uses the GAMS base stack and runs `gams main.gms`.

---

## 4) Troubleshooting

- **`base_stack not found`**  
  → Verify the exact stack name available on your Accelerator instance.

- **No output in `acc://out`**  
  → Check that the CSV path inside `main.gms` matches the mapped directory in `wkube.py`.

---

## 5) Summary

1. Create `main.gms` and `wkube.py`.  
2. Dispatch with `accli dispatch demo myroutine`.  
3. Retrieve `globiom_mock.csv` from `acc://out`.  

This example demonstrates how to **run and export structured GAMS model outputs** on IIASA Accelerator — an ideal pattern for small verification models, data generators, or reproducible simulation runs. 

Happy running! 🚀
