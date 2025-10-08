# Accelerator → GitHub → Zenodo (DOI) — Step‑by‑Step Guide

This guide walks you through publishing data stored on **IIASA Accelerator** to **GitHub** (as metadata) and minting a **Zenodo DOI** linked to that GitHub release.  
Your actual data stay on Accelerator; GitHub hosts the **README/metadata**, and Zenodo mints the DOI for the GitHub **release**.

> **Prerequisite**: You’ve already uploaded your files to Accelerator. If not, follow **`2_ACCLI_environment_guide.md`** first.

---

## Overview

1) **Accelerator**: Upload files (done) → make a chosen folder **Public** (“Release”).  
2) **README.md**: Create a detailed README describing the folder structure, files, units, and how to cite.  
3) **GitHub**: Create a repo and upload the README with a **link** to the released (public) Accelerator folder.  
4) **Zenodo**: Connect GitHub ↔ Zenodo and **publish a GitHub release** to mint a DOI.  
5) **Add DOI badge** back to the GitHub README.

---

## 1) Publish a folder on Accelerator (“Release” → Public)

1. Open **Accelerator web UI** and go to **Folder**.
2. Navigate to the working directory/folder you intend to publish (e.g., `/projects/<project>/outputs/2025-10-08-run/`).
3. Click **Release** for that folder.
4. Confirm scope (folder + subfolders) and visibility (**Public**).
5. Copy the **Public URL** that Accelerator shows for this folder. You will paste this link in your GitHub README.

> **Tip: If you update files later, create a **new dated folder** to preserve provenance.

---

## 2) Create a high‑quality `README.md` for the public folder

Create a `README.md` (locally on your machine) that fully documents your public folder. You’ll push this to GitHub in the next step. You can start from this template:

```markdown
# <Project / Dataset Title>

**Public Accelerator folder:** <PASTE THE PUBLIC URL HERE>

## Summary
Short paragraph describing what the dataset contains, why it exists, and its intended use.

## Citation
If you already have a preferred citation, put it here. After Zenodo DOI is minted, add it like:
> Author(s). (Year). *Title*. Zenodo. https://doi.org/<DOI>

## License
- **Data:** CC BY 4.0 (recommended for datasets)
- **Code (if any):** MIT (or Apache‑2.0/BSD‑3 as appropriate)

## Repository Contents (Metadata only)
This GitHub repo hosts **only** the metadata (README and badges). Data files reside on Accelerator (see link above).

## Folder Structure (on Accelerator)
```text
# Example (replace with your actual structure)
/<your-public-folder>/
├─ README.txt                  # optional readme in the data folder
├─ /inputs/                    # input data used
│  ├─ climate/                 # description of source, resolution, units
│  └─ soils/
├─ /outputs/                   # model outputs
│  ├─ monthly/                 # monthly aggregates (units)
│  ├─ annual/                  # annual summaries (units)
│  └─ figures/                 # png, pdf etc.
└─ /docs/                      # any methods notes
```
\`\`\`

## Data Dictionary
| File/Folder | Description | Format | Units | CRS/Resolution | Notes |
|---|---|---|---|---|---|
| outputs/monthly/file_YYYYMM.csv | Monthly metrics | CSV | gC m^-2 mo^-1 | 5 arc‑min | Derived from … |
| outputs/annual/file_YYYY.csv | Annual metrics | CSV | gC m^-2 yr^-1 | 5 arc‑min | QA/QC: … |

## Methods & Provenance
- Models / versions: e.g., 3PGmix vX.Y, GLEXIS v1.0.0  
- Processing scripts: path or repo reference  
- Thresholds/baselines: e.g., 1990–2010 percentiles  
- Key parameters: …  
- QA/QC checks: range checks, NA policy, reproducibility notes

## How to Access
1. Browse the **public Accelerator folder** (link at top).  
2. Download files of interest (right‑click → Download).  
3. Cite the dataset using the DOI once available (see badge below).

## Contact
- Maintainer: <Name, affiliation, email>
- Issues/Questions: GitHub Issues or email
```

**Good practice:** Include an explicit **version** (e.g., `v1.0.0`) and a **changelog** section.

---

## 3) Create a GitHub repository (metadata‑only)

You can do this via web UI or command line.

### Option A — Web UI
1. Go to **GitHub → New Repository**.
2. Name it (e.g., `project-dataset-metadata`), add description, set **Public**.
3. Create repository, then click **Add file → Upload files**.
4. Upload your `README.md` (from step 2).  
5. Commit changes.

### Option B — Command line
```bash
# In an empty folder containing README.md
git init
git add README.md
git commit -m "Add metadata README linking to Accelerator public folder"
git branch -M main
git remote add origin https://github.com/<USER>/<REPO>.git
git push -u origin main
```

> **Note:** Keep only metadata in this repo; the data stay on Accelerator. You can optionally add `LICENSE`, `CITATION.cff`, and a `CHANGELOG.md`.

---

## 4) Connect GitHub to Zenodo and mint a DOI

1. Sign in to **Zenodo** with your **GitHub** account (OAuth).
2. In Zenodo, open your **GitHub** settings tab and **enable** the repository you created above.
3. Back on **GitHub**, **create a release** (this is what Zenodo archives):
   - Go to **Releases → Draft a new release**.
   - **Tag version**: `v1.0.0` (or your versioning scheme), **Release title**: `v1.0.0`.
   - Release notes: briefly describe what this version documents (dataset on Accelerator).
   - Click **Publish release**.
4. Zenodo will archive that release and mint:
   - A **version‑specific DOI** (e.g., `10.5281/zenodo.1234567`), and
   - A **concept DOI** that remains constant across versions (e.g., `10.5281/zenodo.12345`).

5. In Zenodo, **edit metadata** if prompted:
   - **Title**, **Authors/ORCID**, **Description** (copy summary from README),  
   - **Keywords**, **Funding**, **Related identifiers** (add the Accelerator public URL as “isSupplementedBy”),  
   - **License** (choose appropriate data license, e.g., CC BY 4.0).  
   - Save/Publish to finalize.

6. Copy the **DOI badge** Zenodo provides and add it to your GitHub `README.md`:
```markdown
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.XXXXXXX.svg)](https://doi.org/10.5281/zenodo.XXXXXXX)
```
Commit the README update.

> **Result:** Your DOI resolves to the GitHub **release** which documents (and links to) the **public Accelerator folder** containing the files.

---

## 5) Versioning & updates

- When you update the **data on Accelerator**, create a **new dated public folder**.
- Update the GitHub README (folder link, changelog) and **publish a new GitHub release** (`v1.1.0`, `v2.0.0`, etc.).  
- Zenodo will mint a **new version DOI** while the **concept DOI** stays the same.

---

## 6) Suggested extras

- **LICENSE files**  
  - Data: `LICENSE` with **CC BY 4.0** text.  
  - Code (if any): `LICENSE` with **MIT** (or chosen) text.
- **CITATION.cff** (example):
```yaml
cff-version: 1.2.0
message: "If you use this dataset, please cite as below."
title: "<Dataset Title>"
authors:
  - family-names: "<Surname>"
    given-names: "<Given>"
    orcid: "https://orcid.org/<ORCID>"
version: "v1.0.0"
doi: "10.5281/zenodo.XXXXXXX"
date-released: "2025-10-08"
url: "https://github.com/<USER>/<REPO>"
```

- **Reproducibility note**: Add methods, parameter files, and environment info (e.g., `sessionInfo()`, `conda env export`) to the README or a `docs/` folder.

---

## Quick checklist

- [ ] Public **Accelerator folder** (released) and URL copied
- [ ] Detailed **README.md** with structure, units, methods, and **Accelerator link**
- [ ] **GitHub repo** created and README pushed
- [ ] GitHub repo **enabled** in Zenodo
- [ ] **GitHub release** `v1.0.0` published → Zenodo DOI minted
- [ ] **DOI badge** added to README
- [ ] **Licenses** set (data/code), **CITATION.cff** added (optional)

---

### Notes on longevity
- The DOI will resolve to your **GitHub release**; ensure the README contains a stable **Accelerator public link**.  
- If you ever need the data to live **inside** the DOI record, upload the files directly to **Zenodo** (instead of or in addition to linking Accelerator).
