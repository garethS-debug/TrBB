# Virtual Environment Setup

This project uses a local Python virtual environment at `.venv/` in the repo root.
VS Code is configured to activate it automatically in every new integrated terminal.

## Requirements

- **Python 3.12** (OpenBB supports 3.10–3.12; avoid 3.13+/3.14 as some dependencies lack wheels)
- Windows: the `py` launcher (installed with Python from python.org)

Check what you have:

```powershell
py -0
```

## 1. Create the venv

From the repo root:

```powershell
py -3.12 -m venv .venv
```

## 2. Activate it manually (outside VS Code)

| Shell              | Command                              |
| ------------------ | ------------------------------------ |
| PowerShell         | `.\.venv\Scripts\Activate.ps1`       |
| Command Prompt     | `.venv\Scripts\activate.bat`         |
| Git Bash           | `source .venv/Scripts/activate`      |

If PowerShell blocks the script ("running scripts is disabled"), either run it for the
current session only:

```powershell
powershell -ExecutionPolicy Bypass -NoExit -Command ". .\.venv\Scripts\Activate.ps1"
```

or allow local scripts for your user permanently:

```powershell
Set-ExecutionPolicy -Scope CurrentUser RemoteSigned
```

Deactivate with `deactivate`.

## 3. Install dependencies

With the venv active:

```powershell
python -m pip install --upgrade pip
python openbb_platform/dev_install.py        # core + default extensions, editable
# or, for everything (all providers/extensions):
python openbb_platform/dev_install.py -e
```

To add the CLI as well:

```powershell
pip install -e ./cli
```

### SSL certificate errors

If pip fails with `CERTIFICATE_VERIFY_FAILED` (common behind corporate proxies or
antivirus HTTPS scanning), point pip at the system certificate store:

```powershell
python -m pip install pip-system-certs --trusted-host pypi.org --trusted-host files.pythonhosted.org
```

After that, normal `pip install` commands should work.

## 4. VS Code auto-activation

`.vscode/settings.json` configures:

- **Interpreter:** `.venv\Scripts\python.exe` is the workspace Python interpreter.
- **Terminal profiles** that activate the venv on launch:
  - `PowerShell (venv)` is the default. It uses `-ExecutionPolicy Bypass` for that
    terminal process only, so no system policy change is needed.
  - `Command Prompt (venv)`
  - `Git Bash (venv)`, which loads `~/.bashrc` and then activates via `.vscode/venv-bashrc.sh`.

Open a terminal with `` Ctrl+` ``. The prompt should begin with `(.venv)`. To use
another shell, click the **˅** next to the **+** in the terminal panel and pick a
`(venv)` profile.

Verify:

```powershell
python -c "import sys; print(sys.executable)"
# -> ...\TrBB\.venv\Scripts\python.exe
```

> Note: `.venv/` and `.vscode/` are both git-ignored, so this setup is local to your
> machine. If you clone the repo elsewhere, repeat steps 1 and 3; to get auto-activation,
> copy `.vscode/settings.json` and `.vscode/venv-bashrc.sh` across.

## Recreating the venv

```powershell
Remove-Item -Recurse -Force .venv
py -3.12 -m venv .venv
```

Then reinstall dependencies (step 3).
