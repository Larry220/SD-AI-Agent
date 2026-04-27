# Backend CLI API Contract

## Endpoints

```text
GET  https://tb.sdzn.cc/asr/api/cli/health
GET  https://tb.sdzn.cc/asr/api/cli/languages
POST https://tb.sdzn.cc/asr/api/cli/transcriptions
```

## Authentication

`POST /api/cli/transcriptions` requires:

```http
Authorization: Bearer <MYVOCAL_API_KEY>
```

The Key is the user's MyVocal API Key, not the server `.env` Key.

Never pass the Key in:

- URL query string.
- Multipart form field.
- File name.
- Final answers or troubleshooting reports.

## Transcription Request

Multipart fields:

| Field | Required | Meaning |
| --- | --- | --- |
| `file` | yes | Audio/video file |
| `language_code` | no | ISO code; omit or `auto` for auto detect |
| `file_format` | no | `auto` or `pcm_s16le_16` |

The server uses fixed internal defaults:

```json
{
  "model_id": "echo_v1",
  "timestamps": "word",
  "speaker_diarization": "true",
  "audio_events": "true",
  "separate_channel": "false"
}
```

## Transcription Response

Success:

```json
{
  "ok": true,
  "request_id": "cli_...",
  "display_transcript": "...",
  "transcript_text": "...",
  "raw_response": {},
  "meta": {
    "language_code": "zho",
    "file_format": "auto",
    "original_filename": "demo.mp3",
    "file_size": 260200,
    "duration_seconds": 82.4
  }
}
```

Error:

```json
{
  "ok": false,
  "request_id": "cli_...",
  "error": {
    "code": "invalid_api_key",
    "message": "MyVocal API Key 无效或无权限"
  }
}
```

Common codes:

- `missing_api_key`
- `invalid_api_key`
- `missing_file`
- `invalid_language`
- `invalid_file_format`
- `file_too_large`
- `myvocal_bad_gateway`
- `myvocal_timeout`
- `server_busy`
- `internal_error`

## Security Boundaries

- CLI API does not use the server `.env` `MYVOCAL_API_KEY`.
- CLI API does not create `asr_jobs` records.
- CLI jobs do not appear in Web history.
- Temporary upload files are removed after request completion.
- Error responses must not include full Keys.
- `raw_response` must not include the user's full Key.
