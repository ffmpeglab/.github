# 🎬 FFmpegLab

**The FFmpeg runtime — run entirely in your browser & then scale to your servers.**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![GitHub Stars](https://img.shields.io/github/stars/ffmpeglab/.github)](https://github.com/ffmpeglab/.github/stargazers)

---

## 📖 Table of Contents

- [What is FFmpegLab?](#what-is-ffmpeglab)
- [Quick Start](#quick-start)
- [Architecture](#architecture)
- [Deployment Options](#deployment-options)
- [Features](#features)
- [Environment Variables](#environment-variables)
- [Documentation](#documentation)
- [Project Structure](#project-structure)
- [Open Source Community](#open-source-community)
- [License](#license)

---

## What is FFmpegLab?

FFmpegLab is a **complete ecosystem for video processing** — from a browser‑based IDE to a scalable, self‑hostable rendering backend.

| Component | Description |
|-----------|-------------|
| **[ffmpeglab/webapp](https://github.com/ffmpeglab/webapp)** | Browser‑based IDE running FFmpeg via WebAssembly |
| **[ffmpeglab/server](https://github.com/ffmpeglab/server)** | API server with PostgreSQL, pgmq, and Supabase Storage |
| **[ffmpeglab/ffmpeglab.com](https://github.com/ffmpeglab/ffmpeglab.com)** | Documentation, guides, and deployment scripts |

> **Everything runs on your own infrastructure. Open source. Self‑hosted. Privacy‑first.**

---

## Quick Start

### 🌐 Try FFmpegLab in Your Browser

The fastest way to try FFmpegLab is to open the webapp:

👉 **[webapp.ffmpeglab.com/webapp](https://webapp.ffmpeglab.com/webapp)**

No account, no download, no installation. Just open the link and start editing.

### 🏠 One‑Line Install (Self‑Hosted Supabase)

To deploy the complete FFmpegLab stack with self‑hosted Supabase:

```bash
curl -sSL https://ffmpeglab.com/sh/install.sh | bash
```

This script will:
1. Clone and configure a self‑hosted Supabase instance
2. Enable pgmq and create the `rendering_queue`
3. Create storage buckets with RLS policies
4. Deploy FFmpegLab Server with Docker Compose
5. Generate an API key for authentication

### 📦 Deploy with GitHub Pages

Fork and deploy the webapp for free:

1. **Fork** [ffmpeglab/webapp](https://github.com/ffmpeglab/webapp)
2. **Enable GitHub Pages** — Settings → Pages → `main` branch → `/(root)`
3. **Access** your instance at `https://yourusername.github.io/webapp`

---

## Architecture

FFmpegLab uses a modern, privacy‑first architecture:

```
┌─────────────────────────────────────────────────────────────────────┐
│                    FFmpegLab Client (WebAssembly)                   │
│  ┌─────────────────────────────────────────────────────────────────┐│
│  │                         Browser IDE                             ││
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐         ││
│  │  │ Visual   │  │ Real‑time│  │ Filter   │  │ Linting  │         ││
│  │  │ Editor   │  │ Preview  │  │ Graph    │  │ & Debug  │         ││
│  │  └──────────┘  └──────────┘  └──────────┘  └──────────┘         ││
│  └─────────────────────────────────────────────────────────────────┘│
└─────────────────────────────────────────────────────────────────────┘
                               │
                               │ HTTPS / WebSocket
                               ▼
┌─────────────────────────────────────────────────────────────────────┐
│                     FFmpegLab Server (Self‑Hosted)                  │
│  ┌─────────────────────────────────────────────────────────────────┐│
│  │                     REST API (Express)                          ││
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐         ││
│  │  │ Renders  │  │ Projects │  │ Users    │  │ API Keys │         ││
│  │  │ API      │  │ API      │  │ API      │  │ API      │         ││
│  │  └──────────┘  └──────────┘  └──────────┘  └──────────┘         ││
│  └─────────────────────────────────────────────────────────────────┘│
│                               │                                     │
│                               ▼                                     │
│  ┌─────────────────────────────────────────────────────────────────┐│
│  │                    Supabase Backend                             ││
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐         ││
│  │  │PostgreSQL│  │   pgmq   │  │ Storage  │  │ Auth     │         ││
│  │  │(Render,  │  │ (Job     │  │ (S3‑     │  │(JWT, RLS)│         ││
│  │  │ Projects)│  │ Queue)   │  │compatible)│ │          │         ││
│  │  └──────────┘  └──────────┘  └──────────┘  └──────────┘         ││
│  └─────────────────────────────────────────────────────────────────┘│
└─────────────────────────────────────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────────────┐
│                    Scalable Runners                                 │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐             │
│  │ Render   │  │ File     │  │ Logs     │  │ DNN      │             │
│  │ Runner   │  │ Runner   │  │ Runner   │  │ Runner   │             │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘             │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Deployment Options

### ✅ GitHub Pages (Free, Zero‑Server)

| Step | Command |
|------|---------|
| **1. Fork** | `https://github.com/ffmpeglab/webapp` |
| **2. Enable Pages** | Settings → Pages → `main` branch → `/(root)` |
| **3. Access** | `https://yourusername.github.io/webapp` |

### ✅ Docker Compose (Minimal Server+GUI)

```yaml
services:
  ffmpeglab:
    image: ffmpeglab/ide
    ports: ["8080:8080"]
    environment:
      - FFMPEGLAB_HOST=http://localhost:8080/webapp/
      - FFMPEGLAB_API=http://localhost:3000
      - EVOLU_DB_HOST=http://localhost:4000
      - EVOLU_DB_NAME=ffmpeglab
      - PUBLIC_HOST=https://localhost:8080/
      - PEXELS_API_HOST=https://pexels.starpy.me/
      - SUPABASE_HOST=https://office.starpy.me
      - SUPABASE_ANON_KEY=123456789qwerty
  api:
    restart: unless-stopped
    image: ffmpeglab/server
    ports:
      - "3000:3000"
    environment:
      - DB_HOST=${DB_HOST}
      - DB_USER=${DB_USER}
      - DB_PORT=${DB_PORT}
      - DB_PASSWORD=${DB_PASSWORD}
      - DB_NAME=${DB_NAME}
      - DB_MIGRATION_ENABLED=true
      - S3_BUCKET_ID=${S3_BUCKET_ID}
      - S3_ACCESS_KEY=${S3_ACCESS_KEY}
      - S3_SECRET_KEY=${S3_SECRET_KEY}
      - S3_REGION=${S3_REGION}
      - S3_ENDPOINT=${S3_ENDPOINT}
      - IS_FILE_RUNNER=true
      - IS_RENDER_RUNNER=true
      - IS_LOGS_RUNNER=true
  evolu:
    image: evoluhq/evolu:pr-5
    ports: ["4000:4000"]
```

---

## Features

### 🎨 Visual Editor

- **Live preview** — see edits in real time
- **30+ video operations** — crop, trim, scale, filter, transition
- **Visual filtergraph editor** — build complex pipelines without the command line
- **Real‑time linting** — catch syntax errors before they fail

### 🚀 Scalable Backend

- **PostgreSQL + pgmq** — durable job queuing with transactional guarantees
- **S3‑compatible storage** — media assets and rendered output
- **Three runner types** — render, file, and logs — each with specific responsibilities
- **Hardware acceleration** — NVENC, QSV, VAAPI support (5‑10× faster)

### 🤖 AI & DNN

- **Super‑resolution** — upscale videos 2×, 3×, or 4×
- **Object detection** — detect faces, objects, and scenes
- **Speech‑to‑text** — Whisper AI subtitle generation
- **Video labeling** — automatic metadata generation

### 👥 Collaboration

- **Live cursor** — see where teammates are working
- **Comments** — leave feedback on specific frames
- **Version history** — every render is reproducible and scriptable
- **Self‑hosted** — complete control over your data

### 🔒 Privacy & Security

- **0 bytes uploaded** — all processing happens in your browser
- **Self‑hostable** — own your entire pipeline
- **RLS policies** — per‑user data isolation
- **Open source** — audit the code yourself

### 🧩 Developer Experience

- **Environment variables** — parameterize renders
- **Scalable runners** — distribute encoding across workers
- **YAML to SQL transpiler** — define pipelines declaratively
- **TypeScript SDK** — full API coverage
- **SQL pipeline templates** — ready‑to‑use Supabase migrations

---

## Environment Variables

| Variable | Description | Required |
|----------|-------------|----------|
| `DATABASE_URL` | PostgreSQL connection string | ✅ Yes |
| `SUPABASE_URL` | Supabase project URL | ✅ Yes |
| `SUPABASE_ANON_KEY` | Supabase anonymous key | ✅ Yes |
| `SUPABASE_SERVICE_ROLE_KEY` | Supabase service role key | ⚠️ For server/runner |
| `QUEUE_NAME` | pgmq queue name for rendering jobs | ⚠️ For runner |
| `S3_BUCKET_ID` | Storage bucket name | ⚠️ For file-runner |
| `S3_ACCESS_KEY` | S3 access key | ⚠️ For file-runner |
| `S3_SECRET_KEY` | S3 secret key | ⚠️ For file-runner |

---

## Documentation

| Guide | Description |
|-------|-------------|
| [Agent‑First Setup](https://ffmpeglab.com/articles/agent-first-setup-ffmpeglab-server.html) | Complete setup guide with self‑hosted Supabase |
| [Debugging & Troubleshooting](https://www.ffmpeglab.com/articles/ffmpeg-debugging-troubleshooting-guide.html) | Master `-loglevel` and filtergraph errors |
| [Dynamic Runtime Commands](https://www.ffmpeglab.com/articles/ffmpeg-dynamic-runtime-commands.html) | `sendcmd` and `zmq` for real‑time changes |
| [Video Denoising](https://www.ffmpeglab.com/articles/ffmpeg-video-denoising.html) | Remove noise with `hqdn3d`, `nlmeans`, `bm3d` |

### SQL Pipeline Templates

| Pipeline | Description |
|----------|-------------|
| [Video Onboarding](https://www.ffmpeglab.com/articles/ultimate-video-onboarding-pipeline-sql-triggers-supabase.html)| Thumbnails, resized videos, real‑time notifications |
| [Audio Processing](https://www.ffmpeglab.com/articles/audio-processing-podcast-pipeline-sql-triggers-supabase.html) | Loudness normalization, noise reduction, waveforms |
| [Subtitle Generation](https://www.ffmpeglab.com/articles/automated-subtitle-generation-pipeline-sql-triggers-supabase.html)| Whisper AI transcription and burn‑in |

### Guides for Enterprise & Teams

| Guide | Description |
|-------|-------------|
| [Figma for Video](https://www.ffmpeglab.com/articles/figma-for-video-collaborative-editing.html) | Collaborative video editing with templates |
| [Environment Variables & Runners](https://www.ffmpeglab.com/articles/ffmpeg-env-vars-scalable-runners.html)) | Scale your rendering pipeline |

---

## Project Structure

```
ffmpeglab/
├── .github/
│   └── README.md                    # This file
├── webapp/                          # Browser IDE
│   └── README.md
├── server/                          # API backend
│   ├── src/
│   │   ├── models/                  # TypeORM entities
│   ├── sdk/                         # SDK source
│   │   ├── ts/                      # TypeScript SDK
├── ffmpeglab.com/                   # Documentation
│   ├── articles/                    # Guides and tutorials
│   └── sh/                          # Deployment scripts
```

---

## Open Source Community

FFmpegLab is open source and community‑driven. We welcome contributions of all kinds:

- 🐛 **Bug reports** — [Open an issue](https://github.com/ffmpeglab/webapp/issues)
- 💡 **Feature requests** — [Start a discussion](https://github.com/ffmpeglab/webapp/discussions)
- 📝 **Documentation** — [Improve our guides](https://github.com/ffmpeglab/ffmpeglab.com)
- 🔧 **Code contributions** — [Submit a PR](https://github.com/ffmpeglab/webapp/pulls)

### How to Contribute

1. **Fork** the repository
2. **Create a feature branch** — `git checkout -b feature/amazing-feature`
3. **Make your changes** — add your improvements
4. **Submit a pull request** — describe what you changed and why

### Join the Community

- **Discord**: [discord.gg/ffmpeglab](https://discord.gg/ffmpeglab)
- **Twitter**: [@ffmpeglab](https://twitter.com/ffmpeglab)
- **GitHub Discussions**: [github.com/ffmpeglab/webapp/discussions](https://github.com/ffmpeglab/webapp/discussions)

---

## License

FFmpegLab is open source and available under the [MIT License](LICENSE).

---

## Links

- **Website**: [ffmpeglab.com](https://ffmpeglab.com)
- **Documentation**: [ffmpeglab.com/articles](https://ffmpeglab.com/#docs)
- **GitHub**: [github.com/ffmpeglab](https://github.com/ffmpeglab)
- **Web App**: [ffmpeglab.com/webapp](https://ffmpeglab.com/webapp)
- **Self‑hosting Guide**: [ffmpeglab.com/articles/agent-first-setup](https://ffmpeglab.com/articles/agent-first-setup-ffmpeglab-server.html)

---

**Run the whole editing pipeline on your own infrastructure. Open source and offline-first. Free in your browser to start.**

[![Star on GitHub](https://img.shields.io/github/stars/ffmpeglab/.github?style=social)](https://github.com/ffmpeglab/.github)
