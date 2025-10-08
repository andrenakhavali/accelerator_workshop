# Accelerator Workshop

**Instructions and workflows for IIASA’s Accelerator environment**  

This repository contains step‑by‑step guides, scripts, and workflows to run modeling tasks in the IIASA Accelerator environment using R, Python, and GAMS. It also includes instructions for automating releases (GitHub → Zenodo) and setting up the execution environment.

---

## 📂 Repository Structure

| File / Directory | Description |
|-------------------|-------------|
| `1_Accli_Setup_Guide.md` | Instructions to install and set up the ACCLI (Accelerator CLI) tool. |
| `2_ACCLI_environment_guide.md` | Guide to configuring the environment (dependencies, modules, paths). |
| `3_1_Routine_for_R_script.md` | Workflow for running R scripts in the Accelerator. |
| `3_2_Routine_for_Python_script.md` | Workflow for running Python scripts in the Accelerator. |
| `3_3_Routine_for_GAMS_script.md` | Workflow for running GAMS models in the Accelerator. |
| `Accelerator_to_GitHub_to_Zenodo_DOI_Guide.md` | Guide to link GitHub releases to Zenodo for DOI assignment. |
| `Install_accli_NoAdmin_Windows.md` | Instructions for installing ACCLI on Windows without admin privileges. |
| `Run_Jupyter_Notebook_on_Accelerator.md` | How to launch Jupyter notebooks in the Accelerator environment. |
| `Run_Rstudio_on_Accelerator.md` | How to run RStudio in the Accelerator. |

---

## 🚀 Getting Started

Here’s a suggested workflow to use this repository:

1. **Clone the repository**  
   ```bash
   git clone https://github.com/andrenakhavali/accelerator_workshop.git
   cd accelerator_workshop
   ```

2. **Set up ACCLI / Accelerator environment**  
   Follow `1_Accli_Setup_Guide.md` and `2_ACCLI_environment_guide.md`.

3. **Run your scripts**  
   - For R scripts: follow `3_1_Routine_for_R_script.md`  
   - For Python scripts: follow `3_2_Routine_for_Python_script.md`  
   - For GAMS models: follow `3_3_Routine_for_GAMS_script.md`

4. **Use Jupyter or RStudio**  
   If you prefer an interactive session, use `Run_Jupyter_Notebook_on_Accelerator.md` or `Run_Rstudio_on_Accelerator.md`.

5. **Release & DOI via Zenodo**  
   Use `Accelerator_to_GitHub_to_Zenodo_DOI_Guide.md` to link your GitHub repo with Zenodo and publish releases that get DOIs.

---

## 🛠️ Prerequisites & Requirements

- You’ll need access to the **IIASA Accelerator environment** and permission to run compute jobs.
- Familiarity with **Git** and creating **GitHub releases**.
- For scripts:
  - **R** (with packages used in your modeling tasks)
  - **Python** (with necessary dependencies)
  - **GAMS** (if your models use it)
- Recommended: Basic knowledge of continuous integration / automation (for linking releases to Zenodo).

---

## 📦 Publishing and Archiving

To ensure your work is citable:

1. In your GitHub → **Settings → Integrations & services → Zenodo**, link the repository.
2. Create a **new release** (tag) on GitHub (e.g. `v1.0.0`).
3. Zenodo should automatically capture that release and assign a DOI (see `Accelerator_to_GitHub_to_Zenodo_DOI_Guide.md`).
4. In future updates, increment the version (e.g. `v1.1.0`) and repeat.

---

## ✅ Best Practices & Tips

- Use **semantic versioning** (MAJOR.MINOR.PATCH) for your releases.
- Keep your **metadata consistent**: Make sure your `description`, `README`, and LICENSE (if included) are up-to-date.
- Tag your releases with clear names and include release notes.
- Test your scripts locally (if possible) before submitting jobs to the Accelerator.
- Document any assumptions or limitations in your modeling workflows.

---

## 🏷️ License & Citation

```
Andre Nakhavali, Rupesh Singh (2025). *Accelerator Workshop: instructions for IIASA Accelerator workflows*. GitHub repository, version 1.0.0. DOI: DOI: 10.5281/zenodo.17292833
```

---

