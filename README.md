<img src="https://file.cdn.minimax.io/public/MMX.png" alt="MiniMax" width="100%" />

<p align="center">
  <strong>The official CLI for the MiniMax AI Platform</strong><br>
  Built for AI agents. Generate text, images, video, speech, and music — from any agent or terminal.
</p>

<p align="center">
  <a href="https://www.npmjs.com/package/mmx-cli"><img src="https://img.shields.io/npm/v/mmx-cli.svg" alt="npm version" /></a>
  <a href="https://opensource.org/licenses/MIT"><img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="License: MIT" /></a>
  <a href="https://nodejs.org"><img src="https://img.shields.io/badge/node-%3E%3D18-brightgreen.svg" alt="Node.js >= 18" /></a>
</p>

<p align="center">
  <a href="README_CN.md">中文文档</a> · <a href="https://platform.minimax.io">Global Platform</a> · <a href="https://platform.minimaxi.com">CN Platform</a>
</p>

## Features

- **Text** — Multi-turn chat, streaming, system prompts, JSON output
- **Image** — Text-to-image with aspect ratio and batch controls
- **Video** — Async video generation with progress tracking
- **Speech** — TTS with 30+ voices, speed control, streaming playback
- **Music** — Text-to-music with lyrics, instrumental mode, auto lyrics, and cover generation from reference audio
- **Vision** — Image understanding and description
- **Search** — Web search powered by MiniMax
- **Dual Region** — Seamless Global (`api.minimax.io`) and CN (`api.minimaxi.com`) support

<img src="https://file.cdn.minimax.io/public/MMX-CLI.png" alt="MiniMax" width="100%" />

---

## Setup for AI Agents

This CLI is designed to be called programmatically by AI agents (Claude Code, Cursor, OpenClaw, etc.).

### Method 1 — npm global install (recommended for agents)

```bash
npm install -g mmx-cli
```

Requires [Node.js](https://nodejs.org) 18+.

### Method 2 — Clone and run locally

```bash
# Clone from fork or upstream
git clone https://github.com/MiniMax-AI/cli.git
cd cli

# Install dependencies
npm install

# Build
npm run build

# Run directly
node dist/mmx.mjs auth login --api-key YOUR_API_KEY
```

### Method 3 — Dev mode (for active development)

```bash
git clone https://github.com/MiniMax-AI/cli.git
cd cli
npm install
bun install

# Run with hot reload
bun run dev -- auth login --api-key YOUR_API_KEY
bun run dev -- text chat --message "test"
```

---

## Authentication

### Get your API key

1. Go to [platform.minimax.io](https://platform.minimax.io) (global) or [platform.minimaxi.com](https://platform.minimaxi.com) (CN)
2. Navigate to **Token Plan** → copy your API key

### Login

```bash
# Recommended — use --api-key flag (fast, no browser)
mmx auth login --api-key sk-xxxxx

# Verify authentication
mmx auth status
```

> **Note:** The OAuth browser flow (`mmx auth login` without `--api-key`) may return 404 on some MiniMax accounts. Use `--api-key` flag instead — it works reliably.

### Region

The CLI auto-detects region from your API key prefix:
- `sk-` prefix → global (`api.minimax.io`)
- `eyJ` (JWT) prefix → CN (`api.minimaxi.com`)

Or set manually:
```bash
mmx config set --key region --value cn   # for China
mmx config set --key region --value global
```

---

## Quick Start

```bash
# Authenticate
mmx auth login --api-key sk-xxxxx

# Verify
mmx auth status
mmx quota

# Text chat
mmx text chat --message "What is MiniMax?"

# Image generation
mmx image "A cat in a spacesuit"

# Speech synthesis
mmx speech synthesize --text "Hello!" --out hello.mp3

# Video generation (async)
mmx video generate --prompt "Ocean waves at sunset"

# Music generation
mmx music generate --prompt "Upbeat pop" --lyrics "[verse] La da dee" --out song.mp3

# Web search
mmx search "MiniMax AI latest news"

# Image understanding
mmx vision photo.jpg
```

---

## Using Programmatically

### From Node.js/Bun scripts

```javascript
import { spawn } from 'child_process';

const proc = spawn('mmx', ['text', 'chat', '--message', 'Hello'], {
  env: { ...process.env },
  timeout: 30000,
});

let output = '';
proc.stdout.on('data', (data) => { output += data.toString(); });
proc.on('close', (code) => console.log(output));
```

### From Python

```python
import subprocess
result = subprocess.run(
    ['mmx', 'text', 'chat', '--message', 'Hello'],
    capture_output=True, text=True
)
print(result.stdout)
```

### From another AI agent (Claude Code, etc.)

Add to your agent's skill or instructions:

```bash
# Login once
mmx auth login --api-key YOUR_KEY

# Then use any command
mmx text chat --message "..."
mmx image "prompt"
```

---

## Commands

### `mmx text`

```bash
mmx text chat --message "Write a poem"
mmx text chat --model MiniMax-M2.7-highspeed --message "Hello" --stream
mmx text chat --system "You are a coding assistant" --message "Fizzbuzz in Go"
mmx text chat --message "user:Hi" --message "assistant:Hey!" --message "How are you?"
cat messages.json | mmx text chat --messages-file - --output json
```

### `mmx image`

```bash
mmx image "A cat in a spacesuit"
mmx image generate --prompt "A cat" --n 3 --aspect-ratio 16:9
mmx image generate --prompt "Logo" --out-dir ./out/
```

### `mmx video`

```bash
mmx video generate --prompt "Ocean waves at sunset" --download sunset.mp4
mmx video generate --prompt "A robot painting" --async
mmx video task get --task-id 123456
mmx video download --file-id 176844028768320 --out video.mp4
```

### `mmx speech`

```bash
mmx speech synthesize --text "Hello!" --out hello.mp3
mmx speech synthesize --text "Stream me" --stream | mpv -
mmx speech synthesize --text "Hi" --voice English_magnetic_voiced_man --speed 1.2
echo "Breaking news" | mmx speech synthesize --text-file - --out news.mp3
mmx speech voices
```

### `mmx music`

```bash
# Generate with lyrics
mmx music generate --prompt "Upbeat pop" --lyrics "[verse] La da dee, sunny day" --out song.mp3
# Auto-generate lyrics from prompt
mmx music generate --prompt "Indie folk, melancholic, rainy night" --lyrics-optimizer --out song.mp3
# Instrumental (no vocals)
mmx music generate --prompt "Cinematic orchestral" --instrumental --out bgm.mp3
# Cover — generate a cover version from a reference audio file
mmx music cover --prompt "Jazz, piano, warm female vocal" --audio-file original.mp3 --out cover.mp3
mmx music cover --prompt "Indie folk" --audio https://example.com/song.mp3 --out cover.mp3
```

### `mmx vision`

```bash
mmx vision photo.jpg
mmx vision describe --image https://example.com/img.jpg --prompt "What breed?"
mmx vision describe --file-id file-123
```

### `mmx search`

```bash
mmx search "MiniMax AI"
mmx search query --q "latest news" --output json
```

### `mmx auth`

```bash
mmx auth login --api-key sk-xxxxx       # Recommended
mmx auth login                            # OAuth browser flow (may return 404)
mmx auth status
mmx auth refresh
mmx auth logout
```

`mmx auth status` is the canonical way to verify active authentication.
`~/.mmx/credentials.json` exists only for OAuth login. API-key login persists to
`~/.mmx/config.json` (and `--api-key` can also be passed per command).

### `mmx config` · `mmx quota`

```bash
mmx quota
mmx config show
mmx config set --key region --value cn
mmx config set --key default-text-model --value MiniMax-M2.7-highspeed
mmx config export-schema | jq .
```

### `mmx update`

```bash
mmx update
mmx update latest
```

---

## Thanks to

<a href="https://github.com/MiniMax-AI/cli/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=MiniMax-AI/cli" />
</a>

## License

[MIT](LICENSE)
