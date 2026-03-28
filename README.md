# ALICE-Audio-SaaS

Audio processing SaaS built on the ALICE-Audio engine. Offers real-time and offline audio effects, mixing, spectral analysis, and format conversion via REST API.

## Architecture

```
Client --> API Gateway (8110) --> Core Engine (8114)
```

- **API Gateway**: Authentication, rate limiting, request proxying
- **Core Engine**: DSP pipeline, effect engine, mixer, analyzer

## Features

- 40+ real-time DSP effects (EQ, reverb, compressor, limiter, noise gate)
- Multi-track mixing with gain and pan control
- Spectral analysis (FFT, STFT, mel-spectrogram)
- Beat detection and BPM estimation
- Format conversion (WAV, FLAC, MP3, OGG, OPUS)
- Loudness normalization (ITU-R BS.1770)
- Stem separation (vocals, drums, bass, other)

## API Endpoints

### Core Engine (port 8114)

| Method | Path | Description |
|--------|------|-------------|
| GET | `/health` | Health check with uptime and stats |
| POST | `/api/v1/audio/process` | Apply effects to audio |
| GET | `/api/v1/audio/effects` | List available effects and parameters |
| POST | `/api/v1/audio/analyze` | Spectral and loudness analysis |
| POST | `/api/v1/audio/mix` | Mix multiple audio tracks |
| GET | `/api/v1/audio/stats` | Operational statistics |

### API Gateway (port 8110)

Proxies all `/api/v1/*` routes to the Core Engine with JWT/API-Key auth and token-bucket rate limiting.

## Quick Start

```bash
# Core Engine
cd services/core-engine
AUDIO_ADDR=0.0.0.0:8114 cargo run --release

# API Gateway
cd services/api-gateway
GATEWAY_ADDR=0.0.0.0:8110 CORE_ENGINE_URL=http://localhost:8114 cargo run --release
```

## Example Request

```bash
curl -X POST http://localhost:8114/api/v1/audio/process \
  -H "Content-Type: application/json" \
  -d '{"audio_b64":"...","effects":[{"name":"reverb","wet":0.3},{"name":"eq","bands":[{"freq":1000,"gain_db":3}]}]}'
```

## License

AGPL-3.0-or-later. SaaS operators must publish complete service source code under AGPL-3.0.
