---
name: sd-asr-cli
description: Use when Codex needs to install, configure, diagnose, validate, package, or run the 闪电智能 sd-asr CLI for MyVocal ASR transcription through https://tb.sdzn.cc/asr, including single-file and multi-file transcribe workflows, languages, doctor, API key setup, release package checks, and troubleshooting without leaking MyVocal API Keys.
---

# SD ASR CLI

## Core Rules

- Protect MyVocal API Keys. Never print, summarize, log, or include a full key in final answers.
- Do not collect full keys in screenshots, reports, logs, issue text, or test artifacts.
- Prefer `sd-asr config set-key` for long-term use. Warn that `--key` can enter shell history.
- Use the HTTPS default service `https://tb.sdzn.cc/asr` unless the user explicitly asks to test a local server.
- Treat CLI tasks as client-side workflows. The server CLI API does not write Web history and must not persist user keys.
- Before recommending Mac distribution, check the current release status; Mac package may require real macOS validation.

## Decision Flow

1. Identify the user's goal:
   - Install CLI.
   - Configure or clear Key.
   - Check health or language list.
   - Transcribe one file.
   - Transcribe multiple files or an input list.
   - Diagnose a failure.
   - Validate release packages.
2. Check whether `sd-asr` is already available:
   - Windows: `sd-asr --help` or `.\sd-asr.exe --help`.
   - macOS/Linux: `sd-asr --help`, or `"$HOME/.sd-asr-venv/bin/sd-asr" --help` for the Mac wheel installer package.
3. If install details are needed, read `references/release-status.md`.
4. If command syntax is needed, read `references/commands.md`.
5. If backend/API behavior or security boundaries are relevant, read `references/api-contract.md`.
6. Run the smallest safe verification first: `sd-asr doctor`, then `sd-asr languages`, then a short file transcribe.

## Installation Guidance

Use the package matching the user type:

- Windows ordinary user: use `sd-asr-windows-0.1.0.zip`; run `sd-asr.exe`.
- Technical user: install `asr_offline_web-0.1.0-py3-none-any.whl` into a virtual environment.
- macOS user: use `sd-asr-macos-wheel-0.1.0.zip` only after macOS real-machine validation is confirmed.

Do not include `.env`, `.env.example`, SQLite data, local venvs, pycache, smoke outputs, or real keys in user packages.

## Standard Workflows

### Configure Key

Use:

```bash
sd-asr config set-key "<MYVOCAL_API_KEY>"
sd-asr config show
```

When replying to the user, redact the key:

```text
key: configured (key_...abcd)
```

### Diagnose Service

Use:

```bash
sd-asr doctor
sd-asr languages
```

Expected healthy baseline:

```text
health: ok
```

### Single File Transcription

Use:

```bash
sd-asr transcribe ./demo.mp3 --language zho --output ./demo.txt --json ./demo.json
```

Successful output should include `display_transcript` text in the TXT output.

### Multi File Transcription

Use explicit paths:

```bash
sd-asr transcribe ./a.mp3 ./b.wav --output-dir ./transcripts --json-dir ./raw-json --concurrency 2
```

Use an input list:

```bash
sd-asr transcribe --input-list ./files.txt --output-dir ./transcripts
```

Exit codes:

- `0`: all succeeded.
- `1`: all failed.
- `2`: partial success.

MyVocal handles QPS/concurrency limits by API Key entitlement. If requests wait, explain that upstream queueing may be normal; do not treat it as a local server queue unless the service returns `server_busy`.

## Troubleshooting

Collect only safe diagnostics:

- CLI version.
- OS and shell.
- Package type.
- Command with the key removed.
- Exit code.
- `request_id`.
- Approximate timestamp.
- Error code and message.

Do not collect:

- Full MyVocal API Key.
- Raw logs containing `Authorization`.
- Audio content unless the user explicitly provides it for testing.

Common errors:

- `missing_api_key`: configure a key with `sd-asr config set-key`.
- `invalid_api_key`: the MyVocal key is invalid, expired, or lacks entitlement.
- `myvocal_bad_gateway`: upstream MyVocal issue; retry once with a short file if cost is acceptable.
- `myvocal_timeout`: long audio or upstream queue; consider increasing `--timeout`.
- `server_busy`: lower `--concurrency` or retry later.

## Validation Checklist

For release or user-support work, verify:

- `sd-asr --help` works.
- `sd-asr doctor` returns `health: ok`.
- `sd-asr languages` returns a language list that includes `zho` and `eng`.
- `config show` does not display the full key.
- Single-file transcribe succeeds on a short audio file.
- Multi-file or `--input-list` works when requested.
- TXT output is readable Chinese when the audio is Chinese.
- JSON output does not contain the full MyVocal key.
- Web page `https://tb.sdzn.cc/asr` remains reachable when relevant.
