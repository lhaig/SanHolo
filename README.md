
# SanHolo

An autonomous client for [replicant.space](https://replicant.space) — plays
the game on your behalf and gives you a live mission-control dashboard
to watch what it's doing.

> **Status**: pre-release binaries. Source code will be published once the
> client stabilises. Run with `--dry-run` (the default) until you trust it.

## What it does

- **Mines** the richest belt and adapts as resources deplete; opens new
  sites with survey drones; consolidates loot home with transport drones.
- **Explores** the system: scans all planets and moons, surfaces new
  resource sites, salvages, and location events as they appear.
- **Salvages** discovered wreck sites automatically.
- **Repairs** worn drones via maintenance drones before the wear costs
  you mining throughput.
- **Prints** more drones (and AMI controllers, system_hub, etc.) when
  the right resources accumulate — capped so it doesn't run away.
- **Drives in-game AMI controllers** (transport, survey) so the
  Replicant Space AI handles routine hauling and surveying for you.
- **Mission-control dashboard** at http://localhost:8080 with overview,
  fleet, mining, galaxy map, catalog, activity log, and copilot panels.
- **Optional AI copilot** (Claude CLI or local Ollama) suggests
  next-step directives — always advisory; you decide whether to act.
- **Cross-restart memory** via a local SQLite catalog of sites,
  salvages, history; queryable from the CLI (`san-holo catalog ...`).

## Download

Pre-built binaries are on the [Releases page](https://github.com/lhaig/SanHolo/releases/latest).

Pick the one that matches your machine:

| OS | Architecture | File |
|---|---|---|
| Linux | x86_64 | `san-holo-vX.Y.Z-linux-amd64` |
| Linux | ARM (Raspberry Pi, AWS Graviton) | `san-holo-vX.Y.Z-linux-arm64` |
| macOS | Intel | `san-holo-vX.Y.Z-darwin-amd64` |
| macOS | Apple Silicon (M1/M2/M3/M4) | `san-holo-vX.Y.Z-darwin-arm64` |
| Windows | x86_64 | `san-holo-vX.Y.Z-windows-amd64.exe` |

Each release also includes `SHA256SUMS.txt` — verify the download with:

```sh
sha256sum -c SHA256SUMS.txt --ignore-missing
```

## Install

### Linux / macOS

```sh
# Download (replace vX.Y.Z with the latest tag and pick your platform)
curl -L -o san-holo \
  https://github.com/lhaig/SanHolo/releases/download/vX.Y.Z/san-holo-vX.Y.Z-linux-amd64
chmod +x san-holo

# Optional: move it onto PATH
sudo mv san-holo /usr/local/bin/
```

**macOS gatekeeper**: the binary isn't signed yet, so the first launch will
warn that it's from an unidentified developer. Right-click the file → Open
once to get past the warning, or `xattr -d com.apple.quarantine san-holo`.

### Windows

Download the `.exe` and run from PowerShell. Windows SmartScreen may warn
about an unrecognized app — click "More info → Run anyway".

### Verify the install

```sh
san-holo version
# san-holo v0.1.0 (commit abcdef1)
```

## First run

1. **Register a replicant account on the game** (skip if you already
   have one):
   ```sh
   san-holo register -email you@example.com -name "Your Replicant Name" -tz UTC
   ```
   The game emails you a verification token. Then:
   ```sh
   san-holo verify -token <token-from-email>
   ```
   Credentials are saved to `~/.config/san_holo/credentials.json` (Linux),
   `~/Library/Application Support/san_holo/credentials.json` (macOS), or
   `%APPDATA%\san_holo\credentials.json` (Windows).

   **Alternative — env vars** (handy for CI, headless servers, or
   testing): if you already have an API token from elsewhere, skip the
   register/verify flow and just export:
   ```sh
   export SAN_HOLO_TOKEN="<your-api-token>"
   export SAN_HOLO_CODE="<your-replicant-code>"
   ```
   Env vars override the on-disk credentials file.

2. **Start the dashboard** (safe by default — bot is stopped, dry-run on):
   ```sh
   san-holo serve
   ```
   Open http://localhost:8080. The cockpit shows your fleet, mining
   progress, copilot advice, and lets you toggle the bot on/off.

3. **Go live** when you're comfortable: click **Start** then **Go live**
   in the cockpit, or start with `san-holo serve -live`.

## Safety defaults

- Bot starts **stopped** and in **dry-run** — you have to explicitly turn
  it on. Until then it polls your account, populates the dashboard, and
  does not send any state-changing commands.
- Every action is logged to the in-cockpit Activity panel before it fires.
- The copilot's advice is **suggestive by default** — you decide whether
  to act on it.

## Troubleshooting

- **`api.replicant.space` connection refused** — check your network /
  firewall.
- **"Invalid API token"** — re-run `san-holo recover -email <yours>` to
  get a fresh token by email, then `san-holo verify -token <token>`.
  Or temporarily set `SAN_HOLO_TOKEN` in your shell to test a specific
  token without touching the credentials file.
- **"no API token; run `san-holo register` or set SAN_HOLO_TOKEN"** —
  the credentials file is missing or your env vars aren't set. Run the
  register flow, or restore the file from a backup (see paths under
  "First run" → step 1).
- **macOS "binary is damaged" prompt** — that's the unsigned-binary
  warning; see Install → macOS gatekeeper above.
- **Dashboard shows panels but no data** — check the cockpit's command
  bar: it shows `LIVE` / `STALE` / `OFFLINE`. `OFFLINE` means the API
  poll is failing; check the binary's stdout for the actual error.

## Roadmap

- Code-signed macOS/Windows binaries
- `.deb` / `.rpm` / Homebrew tap / Scoop bucket
- Source code release once API stabilises
- Native window app (likely Wails)

## Issues and feedback

Open an issue here on the SanHolo repo. Include:
- OS + architecture
- Output of `san-holo --version`
- Reproduction steps and what the cockpit showed when things went wrong

## License

(c) 2026 Lance Haig. All rights reserved. Free to download and use for
personal play of replicant.space; no warranty of any kind; redistribution
prohibited. Source publication planned — terms may shift to a permissive
license at that point.

## Acknowledgements

Built against the [replicant.space](https://replicant.space) API.
SanHolo is an unofficial third-party client; not affiliated with or
endorsed by replicant.space.
