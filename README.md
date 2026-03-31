# dreamina-cli

> Guide, one-click install, and Agent Skill for the official Dreamina（即梦）CLI.

**[中文文档 →](README.zh-CN.md)**

Dreamina is ByteDance's AIGC platform supporting text-to-image, text-to-video, image-to-video, multimodal video, and more. This repo contains installation instructions, a command reference, and a Skill file for AI Agents.

---

## One-click Install

```bash
curl -fsSL https://jimeng.jianying.com/cli | bash
```

The binary `dreamina` is installed to `~/.local/bin/` by default.

---

## Login

### Option 1: QR Code via Headless Browser (recommended)

```bash
dreamina login --headless
```

Scan the QR code with the **Douyin (TikTok) App** and confirm authorization on your phone.

### Option 2: Manual JSON Import

1. Log in to Dreamina in your browser: `https://jimeng.jianying.com/ai-tool/login`
2. Visit the following URL and copy the full JSON response:
   ```
   https://jimeng.jianying.com/dreamina/cli/v1/dreamina_cli_login
   ```
3. Save it as a file and import:
   ```bash
   dreamina import_login_response --file /path/to/dreamina-login.json
   ```

---

## Command Reference

### Account

| Command | Description |
|---------|-------------|
| `dreamina login` | Login via browser callback |
| `dreamina login --headless` | Login via headless browser + QR code (Agent-friendly) |
| `dreamina logout` | Clear local login session |
| `dreamina relogin` | Force a fresh login |
| `dreamina user_credit` | Check remaining credit balance |

### Task Management

| Command | Description |
|---------|-------------|
| `dreamina list_task` | List saved tasks |
| `dreamina list_task --gen_status=success` | Filter by successful tasks |
| `dreamina query_result --submit_id=<id>` | Query a single async task |

### Image Generation

| Command | Description |
|---------|-------------|
| `dreamina text2image --prompt="..." --ratio=1:1` | Text to image |
| `dreamina image2image --images ./input.png --prompt="..."` | Image to image |
| `dreamina image_upscale --image ./input.png --resolution_type=4k` | Upscale image |

### Video Generation

| Command | Description |
|---------|-------------|
| `dreamina text2video --prompt="..." --duration=5` | Text to video |
| `dreamina image2video --image ./first.png --prompt="..."` | Single image to video |
| `dreamina frames2video --first ./a.png --last ./b.png --prompt="..."` | First-last frames to video |
| `dreamina multiframe2video --images ./a.png,./b.png --prompt="..."` | Multi-image story video |
| `dreamina multimodal2video --image ./input.png --audio ./music.mp3` | Flagship multimodal video |

---

## Examples

```bash
# Text to image (default model, 1:1, 2K)
dreamina text2image --prompt="cyberpunk cityscape at night, neon reflections" --ratio=1:1 --resolution_type=2k

# Text to video (5s, 16:9)
dreamina text2video --prompt="a cat running through a meadow" --duration=5 --ratio=16:9

# Image to video with 30s polling
dreamina image2video --image ./photo.png --prompt="slow camera push in" --poll=30

# Query async result and download
dreamina query_result --submit_id=3f6eb41f425d23a3 --download_dir=./output

# Check credit balance
dreamina user_credit
```

---

## Model Reference

### Text-to-Image / Image-to-Image

| model_version | resolution_type | Notes |
|---------------|-----------------|-------|
| 3.0 / 3.1 | 1k, 2k | - |
| 4.0 / 4.1 / 4.5 / 4.6 / 5.0 | 2k, 4k | - |
| lab | 2k, 4k | VIP required |

### Video Generation

| model_version | Duration | Resolution | Commands |
|---------------|----------|------------|----------|
| seedance2.0 | 4-15s | 720p | text2video / image2video / frames2video / multimodal2video |
| seedance2.0fast | 4-15s | 720p | Same as above, faster |
| 3.5pro | 4-12s | 720p/1080p | image2video / frames2video |

> Always run `dreamina <subcommand> -h` to confirm the exact supported flags before use.

---

## For AI Agents (Auto-Install Instructions)

If you are an AI Agent, follow these steps:

### Step 1: Install the CLI

```bash
curl -fsSL https://jimeng.jianying.com/cli | bash
```

### Step 2: Install the Skill

After installation, the Skill file is automatically saved to:

```
~/.dreamina_cli/dreamina/SKILL.md
```

Copy it to your Agent's skills directory (example for Claude Code):

```bash
cp ~/.dreamina_cli/dreamina/SKILL.md ~/.claude/skills/dreamina-cli.md
```

Or fetch it directly from this repo:

```bash
curl -fsSL https://raw.githubusercontent.com/simonjiang99/dreamina-cli/main/skill/SKILL.md \
  -o ~/.claude/skills/dreamina-cli.md
```

### Step 3: Login (Agent-driven)

In headless or remote environments, use `--headless` to generate a QR code for the user to scan:

```bash
dreamina login --headless
```

Or, after the user completes web login and provides the JSON file:

```bash
dreamina import_login_response --file /path/to/dreamina-login.json
```

### Important Notes

- All generation commands **consume credits** — warn the user before running them
- Many generation tasks are async; use `query_result` after submitting
- Some models require a one-time web-side authorization on Dreamina before first use
- Always treat `dreamina <subcommand> -h` as the authoritative reference, not this document

---

## Links

- Dreamina website: [jimeng.jianying.com](https://jimeng.jianying.com)
- Install: `curl -fsSL https://jimeng.jianying.com/cli | bash`
