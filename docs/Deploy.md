# Deploy

These steps cover a local deployment of MoneyPrinterV2 for production-like use on macOS/Linux. The app is CLI-first and expects services (Ollama, Nano Banana 2, Firefox profile) to be ready on the host.

## Prerequisites

- Python 3.12. If missing, install from https://www.python.org/downloads/ or use pyenv.
- ImageMagick (for MoviePy subtitles). If missing on macOS, run `brew install imagemagick`; on Linux, use your package manager (for example `sudo apt-get install imagemagick`).
- Firefox profile already logged into target platforms. If you do not have one, open Firefox, log into YouTube/Twitter, then open `about:support` and click "Open Folder" next to "Profile Folder" to get the path for `firefox_profile`.
- Ollama running locally (default: http://127.0.0.1:11434). If missing, install from https://ollama.com, start it (`ollama serve`), then pull a model (for example `ollama pull llama3.2:3b`).
- Nano Banana 2 (Gemini) API key. If missing, create a Gemini API key in Google AI Studio (https://aistudio.google.com/app/apikey), then set it in `config.json` (`nanobanana2_api_key`) or export `GEMINI_API_KEY`.
- Optional: Go (only for Outreach / Google Maps scraping). If missing, install from https://go.dev/dl/ or `brew install go`.

## 1) Bootstrap the host

Clone the repo and run the setup script from the repo root:

```bash
git clone https://github.com/FujiwaraChoki/MoneyPrinterV2.git
cd MoneyPrinterV2
bash scripts/setup_local.sh
```

The script will:
- Create `config.json` from `config.example.json` if missing.
- Create and populate `venv/`.
- Try to auto-detect ImageMagick and a Firefox profile.
- Select a local Ollama model if any are installed.
- Run the local preflight check.

## 2) Configure `config.json`

Open `config.json` and fill required values:

- `firefox_profile`: path to your pre-authenticated Firefox profile
- `nanobanana2_api_key` (or set `GEMINI_API_KEY`)
- `imagemagick_path` if auto-detection failed
- Optional: `ollama_model` if you want to pin a model

See [docs/Configuration.md](Configuration.md) for the full list.

## 3) Verify services

Run preflight to validate your setup:

```bash
source venv/bin/activate
python3 scripts/preflight_local.py
```

Fix any `[FAIL]` items before proceeding.

## 4) Run the app

```bash
source venv/bin/activate
python3 src/main.py
```

## 5) Non-interactive runs (cron)

Use `src/cron.py` with a platform name and account id:

```bash
source venv/bin/activate
python3 src/cron.py youtube <account_id>
python3 src/cron.py twitter <account_id>
```

For YouTube Shorts uploads, you can also use:

```bash
bash scripts/upload_video.sh
```

## Notes

- Always run commands from the repo root.
- The app writes state and temp files to `.mp/` in the repo root.
- For Post Bridge cross-posting, set `post_bridge.enabled` and API key (or `POST_BRIDGE_API_KEY`).
