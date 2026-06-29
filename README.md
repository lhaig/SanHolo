
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
- **Scouts** nearby stars with the home vessel once your account is mature
  enough to support it safely. It now prefers the best nearby unexplored
  system, not just the nearest, and always returns home after the scan-and-
  survey leg. Fresh accounts keep `scout` off by default; developed anchored
  empires auto-enable it unless you've explicitly chosen another stored scout
  setting.
- **Expands frontier worlds** more deliberately: when nearby discovery pressure
  subsides and the best next move is to anchor a world you've already scouted,
  SanHolo can now arm the normal `colonise` flow automatically toward that
  anchor-chain target instead of leaving expansion stuck at the recommendation
  layer.
- **Salvages** discovered wreck sites automatically.
- **Repairs** worn drones via maintenance drones before the wear costs
  you mining throughput.
- **Prints** more drones (and AMI controllers, system_hub, etc.) when
  the right resources accumulate — capped so it doesn't run away. When
  discovery is the strongest climb available, unattended printing now
  biases toward survey capacity before raw miner count. It now also notices
  obvious idle slack in the fleet, so it stops assuming that "print more of
  the thing already sitting idle" is the best default growth move. Big idle
  transport pools are now explicitly demoted so the bot stops inflating a role
  that is already underused.
- **Re-arms logistics** when a real build goal is short on resources and a
  cached remote anchored stockpile looks useful, preferring the best haul
  source instead of waiting only for the last manually-armed supply line. When
  discovery pressure is low, the Progress view can now promote that haul move
  directly as the best near-term climb.
- **Diverts incoming asteroids** before impact: prints propulsors,
  deploys + activates them at the asteroid, drops a beacon at the
  impact target once diverted.
- **Drives in-game AMI controllers** (transport, survey, mining) so the
  Replicant Space AI handles routine hauling, surveying, and resource
  gathering for you. If home-system mining or survey drones get stranded
  under a remote AMI controller, SanHolo now revisits that controller's
  system, lets the release path run, then returns home so local surveying
  and mining recover automatically.
- **Mining network planner** keeps a relay-aware per-system mining summary
  (staffing, anchors, known resources, freshness). The bot can now return from
  weak spokes when home is the better-managed mining world, and it only
  auto-arms remote miner ferries for anchored worlds with clearly better,
  fresher resource prospects. Persisted catalog site intel now triggers a
  validation visit too, so promising stale remote worlds are revisited and
  rescanned before miners get sent there. Fully depleted remote worlds are now
  treated as “finish the recovery/validation leg, then come home”.
- **Galaxy-first cockpit** at http://localhost:8080 — a pannable, zoomable
  star/system map is the home screen, and a guided **Play** menu translates
  your current game state into a simple early-game loop for manual pilots.
  Click any planet, belt, star, or device to open an inspector with its
  detail and controls; a top menu bar (**Play**, **Engine**, **Fleet**,
  **Build**, **Intel**, **System**) holds the guided flow, bot controls,
  fleet roster, blueprints, leaderboards/catalog, and settings; the Intel
  menu now includes a **Progress** view that turns XP pace, build readiness,
  fleet growth, and leaderboard movement into a clearer “what helps next?”
  summary; leaderboard panels call out your visible rank plus recent up/down
  movement; the Build and Mining surfaces now render in tighter content shells
  with scroll wrappers on the truly wide tables, so they stop sprawling
  sideways; the guided Play surface now explains live travel intent more
  plainly, including remote AMI orphan-recovery trips and stale-intel
  validation legs, and its automation guidance now adapts to new-player,
  dry-run, and already-live advanced-account states. The guided Play loop now
  also carries its own per-step actions, so scanning, fleet inspection, and
  build follow-through are linked directly from the guided cards instead of
  forcing menu hunting. Play and Progress now also use a stronger guide-layer
  visual treatment, with clearer kickers, card depth, and section hierarchy so
  the “what matters next?” surfaces stand apart from the denser ops panels; the Progress surface now
  also calls out the current **Frontier** target so distance pushes and scout
  goals are visible instead of implicit, and adds a clearer fleet-utilization
  readout so underused capacity is visible without digging through raw device
  tables. It now also surfaces a **Logistics** hint when a remote anchored
  stockpile looks worth hauling and the freighter is sitting idle. The larger
  operational menus now use bounded panels and push heavier detail into
  explicit sub-tabs instead of forcing the top-level menu itself to become a
  scroll container, and scrolling over an open menu now stays with the active
  menu instead of dragging the background map. Engine and System now also open
  into short overview tabs first, while Fleet, Build, and Intel keep the raw
  command roster, full blueprint catalog, deeper intel, settings, and bot
  automation as explicit advanced disclosures one step deeper. That cuts
  default information overload without removing detail. System Overview now
  also shows live travel posture, destination/ETA, scan freshness, and local
  planet/moon coverage instead of behaving like a thin utility shell, and it
  now explains the temporary “empty” handoff state while the API clears the
  current location between systems. The Catalog panel
  itself now also opens with a compact summary before the raw salvage,
  depletion, event, and history tables, and Inbox/BobNet now call out quick
  counts and actions before the long feed. The Mining surface now also leads
  with a compact network summary so active production, idle miners, and the
  top blocker are visible before the raw AMI/resource detail. Fleet Overview
  now does the same for overall utilization, idle pools, and the main blocker
  before the deeper readiness and AMI sections. Intel Overview now acts as a
  real command summary, pulling threat posture, growth focus, leaderboard
  movement, frontier pressure, logistics posture, and inbox pressure into one
  screen before you dive into the specialist tabs. Intel Threats now also opens
  with a compact posture summary before the detailed asteroid bookkeeping.
  The overview cards across Engine, Fleet, Mining, Build, Intel, and System
  now also share a more consistent visual treatment instead of each tab
  carrying its own ad-hoc inline styling.
  Build Queue now also leads with a short queue posture summary before the raw
  autofactory and unlock tables. When nearby
  unexplored stars run dry, the same frontier planning can now promote the best
  already-scouted unanchored world as the next anchor-chain expansion target
  instead of silently dropping that path; a live ticker along the bottom shows
  the latest activity and copilot advice.
  The **Fleet → Devices** roster also gives per-device manual commands —
  deploy / recall / stow, and **Decommission** (with a confirm prompt) to
  scrap a device you no longer need; the autofactory keeps its blueprint and
  refunds ~60% of the print cost to the device's location.
  Full keyboard navigation and a reduced-motion / high-contrast mode included.
- **BobNet** — the galactic comms layer (channel-based chat over the FTL
  relay network) is surfaced in the cockpit's BobNet panel as collapsible
  per-channel sections: read `#general` / `#trade` chatter, NPC
  announcements, and other replicants' messages each grouped under their
  own channel (with a message count), and send your own from the cockpit.
  BobNet rides on your relays — bring one online to start listening.
- **Inbox** — your message inbox leads with a *Mineable sites & salvage*
  panel that pulls newly-discovered resource sites and salvage (with their
  extractable resources) to the top, so actionable mining intel doesn't get
  buried. Remaining messages are tagged by category — discovery, salvage,
  depletion, achievement, blueprint or info — with mining-relevant tags
  highlighted.
- **Optional AI copilot** (Claude CLI or local Ollama) suggests
  next-step directives — always advisory; you decide whether to act.
- **Operator directives** — set a directive from the cockpit (focus a
  resource, build, explore, or relocate) to steer the bot, or clear it to
  hand control back to the default strategies. `relocate` is the explicit
  opt-in that authorises the bot to recall its fleet and hop to another
  star system (abandoning the current system's stockpile and any immovable
  structures) — it never relocates without it. `trade` is the opt-in that
  authorises the bot to fulfil trades at reachable trade controllers —
  it only ever pays surplus resources (above a reserve floor) for things
  it's short on, and only at controllers in its current system; it never
  trades without it. You can also set the **home base** (the location the
  bot consolidates resources into and builds its system hub) from the
  cockpit — handy for hub-and-spoke play as your galaxy footprint grows.
- **Cross-restart memory** via a local SQLite catalog of sites,
  salvages, history; queryable from the CLI (`san-holo catalog ...`).
  Belt depletion is remembered (6h TTL), so after a restart the bot
  steers clear of known-dead belts instead of re-learning them by
  trial — until they regenerate and re-open. Strategy enable/disable
  toggles you set from the cockpit also persist, so a strategy you
  switched off stays off across restarts instead of re-enabling itself.
- **Game Director readout** — the Engine panel now leads with the current
  objective, blocker, and next action before the advanced strategy list. The
  same summary is echoed in the top status strip so manual pilots can see why
  the bot is acting, waiting, or blocked without decoding every strategy name.
  Its action chips open the relevant cockpit menu/tab directly. In a scanned
  system with no asteroid belt, the exploration loop now keeps survey drones
  free from non-belt survey AMIs and can print a local survey drone when needed
  so the bot can keep scanning planets instead of silently stalling.

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
# san-holo v0.2.0 (commit abcdef1)
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
   Credentials are saved to `~/.config/san-holo/credentials.json` (Linux),
   `~/Library/Application Support/san-holo/credentials.json` (macOS), or
   `%APPDATA%\san-holo\credentials.json` (Windows).

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
   Open http://localhost:8080. The cockpit opens on a live map of your
   system — click a body to inspect it, use the top menu bar for the
   fleet / build / intel panels, and watch the bottom ticker for activity
   and copilot advice.

3. **Go live** when you're comfortable: open **Engine ▾** in the top bar,
   click **Start**, then **Go live** — or start with `san-holo serve -live`.

## CLI reference

```sh
san-holo version          # version + commit
san-holo health           # API connectivity check
san-holo me               # show authenticated account
san-holo serve            # run cockpit at http://localhost:8080
san-holo catalog ...      # query the offline discovery catalog
                          # (unsalvaged | best <res> | events | history | depleted)
san-holo clone            # dry-run cost report for cloning a second replicant
san-holo clone -confirm   # execute the clone bootstrap prints
```

Run `san-holo` with no args for the full flag list.

## Safety defaults

- Bot starts **stopped** and in **dry-run** — you have to explicitly turn
  it on. Until then it polls your account, populates the dashboard, and
  does not send any state-changing commands.
- Every action is logged to the in-cockpit Activity panel before it fires.
- The copilot's advice is **suggestive by default** — you decide whether
  to act on it. You can also set an explicit directive from the cockpit
  (focus_resource / build / explore / relocate / scout / ferry-miners) to
  steer the bot, or clear it. `relocate` is the only way to authorise an
  interstellar hop — the bot recalls + stows its fleet first and will not
  abandon a system otherwise. `scout` (param: a target star like `MENKENTAR`)
  sends the home vessel on a scan-and-return run to a promising nearby
  unexplored star to reveal its resources, then ALWAYS brings it home — your
  base never moves.
  Autonomous scouting ships disabled; enable the `scout` strategy from the
  cockpit to let the bot pick and visit nearby stars on its own.
  `ferry-miners` (param: a target belt designation like `ACHURD-8-L4`) ships
  idle mining drones out to a spoke belt via a surge-capable carrier, where
  that system's AMI controller adopts and runs them — the hub-and-spoke
  restaff move.

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
- **Cockpit shows the map but no live data** — check the status readout on
  the right of the top bar: `LIVE` / `STALE` / `OFFLINE`. `OFFLINE` means the
  API poll is failing; check the binary's stdout for the actual error.
- **Bot is running but my XP / inventory isn't changing** — usually
  one of three things:
  1. **Belt depleted, waiting on the survey AMI** to open new mining
     sites. Expand the bottom **ticker** to see recent activity — if you
     see survey drones cycling "searching", that's the AMI working. To
     force a fresh scan, click a planet or belt on the map and use the
     **Scan** button in its inspector.
  2. **Dry-run is still on.** The top bar shows a `dry-run` badge — if it's
     there, the bot is choosing actions but not sending them. Open
     **Engine ▾** and click **Go live**.
  3. **A device got paused.** Open **Fleet ▾ → Devices** — paused devices
     show in the fleet roster (and AMI status below it). Resume the device
     from the API docs (auto-resume from the cockpit is on the roadmap).

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
