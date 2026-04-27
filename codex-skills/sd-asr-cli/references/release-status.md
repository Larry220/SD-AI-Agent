# Release Status Reference

Source status date: 2026-04-27.

## Current Packages

Package directory in the source project:

```text
D:\闪电智能\asr-offline-web\项目文档\发布包
```

Artifacts:

```text
asr_offline_web-0.1.0-py3-none-any.whl
sd-asr-windows-0.1.0.zip
sd-asr-macos-wheel-0.1.0.zip
sd-asr-wheel-0.1.0.zip
SHA256SUMS.txt
```

## Release Status

```text
Windows package: passed
Technical user wheel package: passed
Mac package: blocked until real macOS validation
```

Windows and technical wheel packages can be used for limited trial if the first batch excludes Mac users.

Do not recommend distributing the Mac package until these checks pass on a real macOS machine:

1. `bash install.sh`.
2. `"$HOME/.sd-asr-venv/bin/sd-asr" --help`.
3. `config set-key/show/clear-key`.
4. `doctor`.
5. `languages`.
6. Short audio `transcribe`.
7. Chinese path and path with spaces.
8. Invalid input handling.
9. Output/log scan for full Key leakage.

## Install Patterns

Windows ordinary user:

```powershell
Expand-Archive .\sd-asr-windows-0.1.0.zip .\sd-asr-windows-0.1.0
cd .\sd-asr-windows-0.1.0
.\sd-asr.exe --help
.\sd-asr.exe doctor
```

Technical user:

```bash
python -m venv .venv
source .venv/bin/activate
python -m pip install ./asr_offline_web-0.1.0-py3-none-any.whl
sd-asr doctor
```

PowerShell technical user:

```powershell
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install .\asr_offline_web-0.1.0-py3-none-any.whl
.\.venv\Scripts\sd-asr.exe doctor
```

Mac user after validation:

```bash
bash install.sh
"$HOME/.sd-asr-venv/bin/sd-asr" doctor
```

## Package Safety

Release packages must not contain:

- `.env`
- real MyVocal API Keys
- SQLite databases
- local virtual environments
- pycache or pyc files
- smoke output files
- full source workspace dumps
