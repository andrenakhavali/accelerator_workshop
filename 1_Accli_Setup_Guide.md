# 🚀 Setting Up **ACCLI** (Accelerator Terminal Client)

This guide walks you through installing, upgrading, and verifying the **IIASA Accelerator Command Line Client (ACCLI)**.

---

## 🧩 Requirements

Before you begin, ensure you have the following installed on your system:

- **Python 3.9+**
- **pip** (Python package manager)

### Check your versions

```bash
python --version
# or
python3 --version

pip --version
```

If either of these commands fails, install or update Python and pip first.

---

## ⚙️ Installation

### Install ACCLI

```bash
pip install accli
```

### Upgrade to the latest version (optional)

```bash
pip install --upgrade accli
```

---

## 🔍 Verify Installation

Once installed, confirm that **ACCLI** works correctly:

```bash
accli about
```

### Expected Output

```
This is a terminal client for Accelerator hosted on https://accelerator.iiasa.ac.at .
Please file feature requests and suggestions at https://github.com/iiasa/accli/issues .
License: The MIT License (MIT)
Version: v1.0.1
```

✅ If you see output similar to the above, your installation is complete!

---

## 💡 Quick Tips

- If `accli` isn’t recognized, try:
  ```bash
  python -m accli about
  ```
- If you’re using multiple Python versions, always specify `python3` instead of `python`.
- For permission issues on shared systems, install with:
  ```bash
  pip install --user accli
  ```

---

**ACCLI** gives you secure, direct access to IIASA’s **Accelerator Platform**, enabling you to submit jobs, upload data, and automate workflows efficiently. 🎯
