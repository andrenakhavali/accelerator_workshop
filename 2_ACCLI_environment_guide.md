# 🌐 Setting Up and Using the **ACCLI Environment** (IIASA Accelerator)

## 1️⃣ View Available Commands

```bash
python -m accli --help
```

**Output Example:**
```
Usage: python -m accli [OPTIONS] COMMAND [ARGS]...

Options:
  --help     Show this message and exit.

Commands:
  about
  dispatch
  login
  upload
  copy
  validate
```

These are the main CLI commands you can use:
- **about** – Displays information about the Accelerator client  
- **dispatch** – Submits or runs workflows  
- **login** – Authenticates your Accelerator session  
- **upload** – Uploads local files/folders to the Accelerator  
- **copy** – Download from Accelerator to local files/folders
- **validate** – Validates any role for the given dataset


---

## 2️⃣ Login to the Accelerator

```bash
python -m accli login
```

You’ll see:
```
Welcome to Accelerator Terminal Client.
Powered by IIASA

Get authorization code on following web url:
https://accelerator.iiasa.ac.at/acli-auth-code

Enter the authorization code?:
```

➡️ Open the link above in your browser,  
➡️ Copy the **authorization code**,  
➡️ Paste it back into the terminal and press **Enter**.  

Once logged in, your session is authenticated and ready for use.

---

## 3️⃣ Upload a Folder

```bash
python -m accli upload demo test_folder xyz
```

**Explanation:**
- `upload` → Command to upload data  
- `demo` → Project space name on Accelerator  
- `test_folder` → Local folder you want to upload  
- `xyz` → Desired name of the uploaded folder on Accelerator  

💡 *Note:*  
You can also upload files directly through the **Accelerator web interface** if preferred.

---

## 4️⃣ Download a Folder

```bash
python -m accli copy demo/xyz -d .
```

**Explanation:**
- `copy` → Command to download data from Accelerator  
- `demo/xyz` → Path to the folder you want to download  
- `-d .` → Destination directory (`.` means current directory)

📁 *Optional:*  
You can replace `.` with any desired target path, e.g.:
```bash
python -m accli copy demo/xyz -d ~/Downloads/
```

👉 The `-d .` argument can be omitted if you want the folder to download into your current working directory.

---

## ✅ Quick Recap

| Action | Command | Description |
|--------|----------|-------------|
| Check CLI options | `python -m accli --help` | Lists all commands |
| Log in | `python -m accli login` | Authenticate with your IIASA account |
| Upload folder | `python -m accli upload demo test_folder xyz` | Uploads local folder |
| Download folder | `python -m accli copy demo/xyz -d .` | Downloads folder to current directory |
| About client | `python -m accli about` | Shows version and license info |
