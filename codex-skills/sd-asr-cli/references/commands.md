# sd-asr Command Reference

## Default Server

```text
https://tb.sdzn.cc/asr
```

## Key Management

```bash
sd-asr config set-key "<MYVOCAL_API_KEY>"
sd-asr config show
sd-asr config clear-key
```

Key priority:

```text
--key > MYVOCAL_API_KEY environment variable > local config
```

Use `--key` only for temporary debugging because it can enter shell history.

## Health and Metadata

```bash
sd-asr doctor
sd-asr languages
```

Expected:

- `doctor` prints `health: ok`.
- `languages` exits `0` and includes language codes such as `zho` and `eng`.

## Single File

```bash
sd-asr transcribe ./demo.mp3
sd-asr transcribe ./demo.mp3 --language zho
sd-asr transcribe ./demo.mp3 --language zho --output ./demo.txt
sd-asr transcribe ./demo.mp3 --language zho --output ./demo.txt --json ./demo.json
```

## Multi File

```bash
sd-asr transcribe ./a.mp3 ./b.wav --output-dir ./transcripts
sd-asr transcribe ./a.mp3 ./b.wav --output-dir ./transcripts --json-dir ./raw-json --concurrency 2
sd-asr transcribe --input-list ./files.txt --output-dir ./transcripts
sd-asr transcribe --input-list ./files.txt --output-dir ./transcripts --fail-fast
```

Concurrency:

- Default: `2`.
- Allowed: `1-8`.
- MyVocal handles QPS and upstream queueing according to the user's API Key entitlement.

Exit codes:

- `0`: all succeeded.
- `1`: all failed.
- `2`: partial success.

## Local Development Server

Use only when the user explicitly wants local testing:

```bash
sd-asr doctor --server http://127.0.0.1:18080
sd-asr languages --server http://127.0.0.1:18080
sd-asr transcribe ./demo.mp3 --server http://127.0.0.1:18080 --language zho --output ./demo.txt
```
