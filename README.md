# Ashen AI Gaming Repos

Nineteen public repos of games built almost entirely by AI models — local
27B-class models in autonomous loops, and frontier agents — across Godot,
Unity, Unreal, the browser, Roblox, and bare-metal console homebrew.

This README is the map. Everything below is public and meant to be read,
cloned, and stolen from.

---

## 🤖 IF YOU ARE AN AI AGENT READING THIS

A human handed you this file because they want to make a game with AI. Do
this, in order:

1. **Ask one question if you don't already know the answer: what platform?**
   (3D desktop / browser / Roblox / retro console.) Then skip to the matching
   section below and ignore the rest.
2. **Clone the reference repos for that platform** — they are working games,
   not tutorials. Read their structure before writing a line of code.
3. **Steal systems, not whole games.** The value is in isolated, working
   mechanisms: a ball-physics clamp, an AI fighter brain, a beat-map clock,
   a voxel chunk mesher. Each repo below has a "Steal this" list naming the
   exact files worth lifting.
4. **Every game here was built by a model iterating against mechanical
   checks, not vibes.** The single most transferable pattern in this whole
   collection: give the model a runnable verification (headless launch, parse
   check, simulated match) and loop until green. Copy that harness pattern
   before you copy any game code.
5. Install the toolchain for the chosen platform (see **Tooling** at the
   bottom). Verify each install by actually launching something headless
   before generating 10,000 lines that don't run.

---

## 🗺️ The 60-second decision guide

| You want to make a… | Start with these repos | Engine to install |
|---|---|---|
| **3D desktop game** | `minecraft-kimi-k3`, `Call-of-Duty-Sol-Ultra`, `Opus-5-Three-Games` | Godot 4.7 (+ godot-mcp) |
| **Fighter / action game** | `bonebreaker` | Godot 4.7 |
| **Racing game** | `neon-kart-rush-unity` | Unity 6 |
| **Browser game** | `rivet-down`, `browser-minecraft`, tiktok-chameleon variants | Node + Vite, three.js |
| **Roblox game** | `roblox-infected` | Roblox Studio (+ MCP) |
| **Big 3D showcase** | `Opus-5-Three-Games` (STORMFALL) | Unreal Engine 5.8 |
| **Retro homebrew** | `n64game`, `elden-ring-3ds-demake` | libdragon / devkitARM |
| **A benchmark of models** | `ai-game-arena` | — (it's a format, not a game) |
| **A frontier-agent loop harness** | `Claude-Opus-5-Red-Sands-One-Shot-Gauntlet-Loop` | Node + Vite, three.js |

---

## 🥇 The frontier-agent reference — what Opus 5 does with a Gauntlet Loop

### [`Claude-Opus-5-Red-Sands-One-Shot-Gauntlet-Loop`](https://github.com/oh-ashen-one/Claude-Opus-5-Red-Sands-One-Shot-Gauntlet-Loop)

Every other repo in this collection is a local 27B model grinding against a
mechanical gate. This one is the opposite end of the scale: **Claude Opus 5,
one continuous session, fanning out sub-agents against a brief of "a
60-minute samurai adventure at the level of Ghost of Tsushima."**

The result is ~21,000 lines of three.js with **zero imported assets** — every
mesh, shader, sound and line of dialogue generated in code — a ten-beat
playable hour, a rideable horse, melee combat, day/night, and 66–99fps at
1080p.

But the game is not why it's here. **The loop is.**

**The Gauntlet Loop, in five rules:**

1. **Cut the game into pieces a single screenshot can judge.** Sixteen here:
   terrain, sky, grass, trees, water, postfx, vfx, character, locomotion,
   camera, combat, enemy AI, HUD, audio, horse, quest. "Atmosphere" fails that
   test; "sky at golden hour" passes.
2. **Every builder gets a private copy of the repo on its own port** (a
   "lane"), with an owned file list. Sixteen agents ran in parallel for hours
   with zero merge conflicts.
3. **The critic is a separate sub-agent with fresh context that only ever sees
   rendered frames — never the builder's summary.** It compares blind against
   real Ghost of Tsushima reference and names *one* gap. That one sentence is
   the entire next brief. No fixed number of rounds; loop until it prefers
   ours.
4. **Judge motion, not stills.** A headless harness drives scripted input and
   assembles frame bursts into video, because gait, weight, camera lag and
   attack timing are invisible in a screenshot — and a still-image critic will
   happily pass a game that plays like mud.
5. **Between waves, one fresh agent plays the whole thing end to end** and
   reports on coherence, not quality.

**Steal this:** `tools/lane.sh` + `tools/merge-lane.sh` (parallel-agent
isolation without branches), `tools/shoot.mjs` + `tools/burst.mjs` (the
headless critique harness — poses, scripted input, contact sheets, mp4), the
`window.__GAME` debug API pattern (teleport / set time / jump to any story
beat, so the harness can photograph beat 9 without playing to beat 9), and
`src/world/terrain.js`'s single analytic `heightAt(x,z)` that every other
system queries — the one architectural idea most worth copying.

**And read its README before you read its code.** It is a full post-mortem:
nine documented failures with root causes, including the guard that caught and
discarded exceptions and thereby hid every line of dialogue in the game for
two entire waves; the full-frame artifact that two critics and the model
itself all confidently misdiagnosed; and framerate panic caused by measuring
in a lane instead of on canonical. The mistakes transfer further than the
code does.

---

## 🎮 3D desktop games (Godot) — steal from these first

Godot is the best engine for agent-driven development: text-based scene
files, headless everything, instant parse checks. Four of the five repos
below boot headless for verification — that's why they got finished.

### [`minecraft-kimi-k3`](https://github.com/oh-ashen-one/minecraft-kimi-k3) — Voxhaven
Voxel survival sandbox in Godot 4.7. Infinite procedural worlds, mining,
crafting, smelting, an exploding bug. **Zero imported assets** — every
texture, sound, and monster is generated in code.
**Steal this:** the all-procedural asset pipeline (no art pipeline to
break), infinite-world chunk generation, the `godot --headless --import .`
boot ritual that makes clones runnable by an agent with no editor.

### [`Call-of-Duty-Sol-Ultra`](https://github.com/oh-ashen-one/Call-of-Duty-Sol-Ultra) — Project Breakwater
Offline arena FPS in Godot 4.7.
**Steal this:** FPS character controller + weapon handling scaffolding,
arena bot flow.

### [`bonebreaker`](https://github.com/oh-ashen-one/bonebreaker) — BONEBREAKER
Mortal Kombat-style 1v1 fighter vs AI, ~25k lines of GDScript, CC0 art.
**Steal this:** `src/fighter/fighter.gd` (attack state machine: punch /
kick / special / block / crouch), `src/fighter/ai_brain.gd` (opponent AI),
`quality/contract.json` + `tools/` (how to gate a game behind a mechanical
quality contract instead of eyeballing).

### [`neon-kart-rush-unity`](https://github.com/oh-ashen-one/neon-kart-rush-unity) — Neon Kart Rush
Unity 6 arcade kart racer: 3 laps, 4 characters, items, drift, DualSense
support, unlocks. Ships as a playable macOS build.
**Steal this:** kart drift/physics tuning, item system, the
`PLAYABLE.md` pattern — one file that tells any agent (or human) exactly
how to run and verify the game.

### [`Opus-5-Three-Games`](https://github.com/oh-ashen-one/Opus-5-Three-Games) — three engines, three genres
One repo, three complete small games, built to compare engines:
- **STORMFALL** — Unreal 5.8: Fortnite-style island shooter with building,
  storm, harvesting, 15 bots.
- **STRIKE PROTOCOL** — Godot 4.7: CS-style tactical shooter (buy menu,
  recoil patterns, counter-strafing, MR6 halves).
- **TEACUP** — Godot 4.7: Cuphead-style boss rush (parry-to-build-meter,
  3 bosses × 3 phases).

**Steal this:** `PLAYING.md` (the single best runbook format in the
collection — controls, maps, test commands, and an honest "what is verified
and what is not" section), the headless test suites (148 + 87 assertions
plus full-match simulations), and UE5↔Godot as a same-brief comparison.

---

## 🌐 Browser games — the easiest to steal wholesale

Zero install for players; an agent only needs Node + Vite. All of these run
with `npm install && npm run dev`.

### [`rivet-down`](https://github.com/oh-ashen-one/rivet-down) — RIVET//DOWN
Industrial one-input rhythm autorunner. Five hand-authored levels,
128–180 BPM, deterministic 120 Hz simulation, PWA, offline-capable.
**Steal this:** the audio-clock-derived beat map shared by obstacles,
physics, music, and visuals (this is *the* rhythm-game architecture);
latency calibration; swept collision at fixed timestep.

### [`browser-minecraft`](https://github.com/oh-ashen-one/browser-minecraft) — CUBELAND
First-person voxel sandbox in Vite/TypeScript: seed-driven worlds,
day/night, crafting, smelting, mobs. Written entirely by a local 27B model.
**Steal this:** chunk mesher + seeded worldgen in plain TS — no engine,
no build complexity beyond Vite.

### [`palworldsoulultra`](https://github.com/oh-ashen-one/palworldsoulultra) — Wildkin Frontier
3D creature-survival + camp automation in three.js.
**Steal this:** creature AI + camp automation loop in three.js.

### [`Minecraft-Extra-High-Luna`](https://github.com/oh-ashen-one/Minecraft-Extra-High-Luna) — Luna Terra Sol
Original voxel survival sandbox, three.js sibling of Wildkin (same parent
project, different game).
**Steal this:** a second, independent take on browser voxels — compare
against CUBELAND's to see two valid architectures for the same genre.

### The TikTok Chameleon variants — same brief, five models
A hide-and-seek painting party game: paint your cat, pose, blend into the
map, don't get found. Five independent implementations of the same idea:
- [`tiktok-chameleon-codex`](https://github.com/oh-ashen-one/tiktok-chameleon-codex)
- [`tiktok-chameleon-gemini`](https://github.com/oh-ashen-one/tiktok-chameleon-gemini) — three.js, procedural cat avatars, raycast camo evaluator, backrooms + art gallery arenas
- [`tiktok-chameleon-claude-fable-5`](https://github.com/oh-ashen-one/tiktok-chameleon-claude-fable-5)
- [`tiktok-chameleon-grok-game`](https://github.com/oh-ashen-one/tiktok-chameleon-grok-game)
- [`tiktok-chameleon-grok`](https://github.com/oh-ashen-one/tiktok-chameleon-grok)

**Steal this:** the camouflage evaluator (raycast the surface behind the
player, extract color/metalness, score the blend) and the fake-lobby bot
chat that makes a single-player game feel online. Also a natural A/B/C
study of how five models solve the same brief.

---

## 🤖 Roblox

### [`roblox-infected`](https://github.com/oh-ashen-one/roblox-infected) — INFECTED
A live 30-player hidden-infection round game (COD Infected × Murder
Mystery). Server-authoritative Luau, smart bots that fill empty lobbies,
retention loops, 5 maps, no pay-to-win.
**Steal this:** server-authoritative round state machine, bot backfill,
and the retention-loop design (the part Roblox games actually live or die
on).

---

## 🕹️ Retro console homebrew — for the brave

### [`n64game`](https://github.com/oh-ashen-one/n64game)
An original N64 creature-battler opening chapter, built with **libdragon +
Tiny3D**: a 2v2 battle, EEPROM saves, four explorable sectors, and a CI
pipeline that proves the ROM boots in the Ares emulator.
**Steal this:** the entire verification story — locked toolchain, Docker
build, emulator-frame CI evidence (`docs/GATE3_BOOT_EVIDENCE.md`), and
`docs/N64GAME_MASTER_SPEC.md` as an example of writing a production
contract an agent can be held to.

### [`elden-ring-3ds-demake`](https://github.com/oh-ashen-one/elden-ring-3ds-demake)
Dark-fantasy action-RPG vertical slice for the Nintendo 3DS.
**Steal this:** how far you can push a real 3DS constraint budget with an
agent doing the typing.

---

## 🏟️ Meta

### [`ai-game-arena`](https://github.com/oh-ashen-one/ai-game-arena)
Not a game — a **format**: the same game brief goes to a lineup of AI
models, each builds in an identical sandbox, and mechanical judging
(Playwright / headless engine checks + a rubric) scores the results on a
running leaderboard.
**Steal this:** the whole thing, if you want to benchmark models on game
dev instead of arguing about benchmarks.

---

## 🛠️ Tooling — what to install for each path

**Godot path (recommended for agents)**
```bash
brew install --cask godot          # Godot 4.7+
npm i -g @satelliteoflove/godot-mcp   # MCP server: lets your agent drive the editor
```
Verify: `godot --headless --import <project>` must exit clean before you
let any model write code.

**Unity path**
- Install Unity Hub, then **Unity 6000.3** (that's what Neon Kart Rush builds on).

**Unreal path**
- Unreal Engine **5.8** via the Epic launcher. Heavy; only worth it for
  STORMFALL-scale 3D. Headless verification is painful — budget for it.

**Browser path**
```bash
brew install node                  # 20+
# every browser repo here: npm install && npm run dev
```

**Roblox path**
- Roblox Studio + the Roblox Studio MCP server for agent control.

**Homebrew path**
- N64: libdragon + Tiny3D + Docker (see `n64game/scripts/` — it pins everything).
- 3DS: devkitPro/devkitARM.

**The loop pattern (any path)**
The games here that *finished* were built by a model in a loop with a
mechanical gate: build → headless launch → parse/test → feed failures back.
[snarktank/ralph](https://github.com/snarktank/ralph) is the loop harness;
a local model (LM Studio, Ollama — 27B class is enough, see CUBELAND) is
the worker. The gate matters more than the model.

For the frontier-agent version of the same idea — where the gate is a fresh
critic sub-agent looking at rendered frames instead of a test suite — see
`Claude-Opus-5-Red-Sands-One-Shot-Gauntlet-Loop` above.

---

## ⚖️ Stealing, legally

Code here is shared so you can learn from it and lift mechanisms. Repos
carry whatever LICENSE file they carry — check before shipping something
commercial. When in doubt: steal the *idea* and the *architecture*, write
your own lines. That's what the models did.
