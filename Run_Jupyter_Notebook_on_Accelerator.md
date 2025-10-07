# Run a Jupyter Notebook on IIASA Accelerator

This guide explains how to launch a **Jupyter Notebook** directly from the **Accelerator web interface** using the built-in routine **WKube Jupyter Notebooks**.

---

## 🧭 Step-by-Step Instructions

1. Go to [https://accelerator.iiasa.ac.at](https://accelerator.iiasa.ac.at)
2. In the left menu, select **Routines → Inbuilt**.
3. From the list, choose **“WKube Jupyter Notebooks.”**
4. Click **“Run Routine.”**

---

## 🧩 Fill in the Required Fields

Only **three fields** are required.  
All other fields can be left blank or collapsed.

| Field | Value | Note |
|--------|--------|------|
| **Access Token** | `gsk_************************` | ⚠️ Keep this token private and never share it. |
| **OpenAI API Base URL** | `https://api.groq.com/openai/v1` | Required for connecting to the API. |
| **Model ID** | `openai/gpt-oss-120b` | Defines the Jupyter AI model. |

---

## 💾 Data Mappings (optional)

If your notebook needs files from your workspace:
- Under **Input Mappings**, add  
  `selected_folders:/home/jovyan/work/`
- Under **Output Mappings**, you may leave it empty (default saves to `/home/jovyan/work/`).

---

## ⚙️ Resources (default)

You can keep the default values:

| Setting | Default |
|----------|----------|
| **RAM** | 1 GB |
| **Local Storage** | 1 GB |
| **CPU Cores** | 1 |
| **Timeout** | 3600 seconds |

---

## ▶️ Run the Notebook

After filling the required fields:

1. Click **“Dispatch Routine.”**  
2. The system will start your Jupyter Notebook container.  
3. Once ready, click **“Open JupyterLab”** to launch your environment.  
4. Inside JupyterLab, open or upload your `.ipynb` file and start working.

---

## 🧠 Notes

- Your notebook will automatically have access to the configured AI model (`openai/gpt-oss-120b`).
- Keep your access token secure — it authenticates your environment.
- Use the **File Browser** in JupyterLab to access `/home/jovyan/work/` where inputs and outputs are stored.
