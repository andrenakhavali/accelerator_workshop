# 🧠 How to Install and Run `accli` on Windows Without Admin Rights

> This guide shows how to install **Python** and the **Accelerator CLI (ACCLI)** using **Scoop**, a no-admin package manager for Windows.  
> Works even on restricted systems (no administrator password required).

---

## 🪟 1. Open Windows Terminal (PowerShell)
- Press **Start → type “Terminal” → hit Enter**  
- Or open **PowerShell** directly (no need for Admin mode)

---

## ⚙️ 2. Allow Script Execution for Your User
Copy-paste and run:
```powershell
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
```

---

## 📦 3. Install Scoop
Scoop installs applications into your user folder (`%USERPROFILE%\scoop`).

Run:
```powershell
iwr -useb get.scoop.sh | iex
```

Add optional buckets:
```powershell
scoop bucket add main
scoop bucket add extras
```

---

## 🐍 4. Install Python (User-Level)
```powershell
scoop install python
```

Confirm installation:
```powershell
python --version
pip --version
```

---

## 🚀 5. Install ACCLI (Accelerator Client)
```powershell
pip install --user accli
```

If `pip` gives a warning about PATH, ignore it — you can always call:
```powershell
python -m accli --help
```

---

## ✅ 6. Verify Installation
Test that `accli` works:
```powershell
accli --help
```

You should see:
```
This is a terminal client for Accelerator hosted on https://accelerator.iiasa.ac.at
Usage: accli [OPTIONS] COMMAND [ARGS]...
```

If `accli` is not recognized, run:
```powershell
python -m accli --help
```

---

## 🔄 7. (Optional) Update ACCLI
To upgrade later:
```powershell
pip install --user --upgrade accli
```

---

## 🧰 8. Common Commands
| Command | Description |
|----------|--------------|
| `accli about` | Show version & host information |
| `accli dispatch <project> <routine>` | Run a routine on Accelerator |
| `accli --help` | Show all commands and options |

---

### 📝 Notes
- Scoop keeps everything under your user account → **no admin rights needed**.
- Works in corporate or student environments where software installs are blocked.
- To uninstall later:
  ```powershell
  scoop uninstall python
  scoop uninstall scoop
  ```

---
