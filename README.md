# makefun-tiny-chess-01

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/jojosinkonstanor-collab/makefun-tiny-chess-01/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/jojosinkonstanor-collab/makefun-tiny-chess-01/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

A chess engine in **4,067 bytes** of Python (limit 4,096), written from scratch for the "Build the strongest chess engine in 4 KB of Python" bounty. No external libraries, no model calls, fully deterministic.

## Run it

```sh
MAKEFUN_ENV_URL=http://127.0.0.1:PORT python3 main.py
```

Requires Python 3.13+ stdlib only. Prints one line per game to stderr.

## Design

- **Board:** 0x88, pieces as signed ints (white +, black −).
- **Search:** negamax + alpha-beta, iterative deepening, 40,000 nodes/move + 8 s time cap (never exceeds the 10 s move limit). Transposition table, killers, history, MVV-LVA with losing-capture demotion, null-move pruning (R=3), LMR (max reduction 1), futility, PVS, check extension, quiescence (captures/promotions, all evasions in check).
- **Eval:** material + Michniewski piece-square tables (left-right mirrored), bishop pair, king middlegame/endgame interpolation by game phase, passed-pawn bonus.
- **Protocol:** talks to the make.fun env over HTTP (`/game`, `/move`, `/resign`), moves in UCI. Repetition (game history + search path) and the fifty-move rule are handled.
- **Packing:** the readable source is minified, LZMA-compressed, and embedded by `makefun compress` — that is why `main.py` is only 4,071 bytes.

## Test results (practice trials, local env, 2026-11-17)

Perft: all standard positions pass (startpos, Kiwipete, positions 3–6). Bench (40k nodes): depth 6–7 middlegame, ~10–28k nodes/s on this VM.

| trial | Elo | W–D–L |
|-------|-----|-------|
| 0 | **2112** | 6–0–0 |
| 1 | **1654** | 4–2–0 |
| 2 | **1654** | 5–0–1 |

Mean practice Elo: **1807** (15W–2D–1L over 18 games). No time losses (max think under the 8 s cap vs 10 s limit). Practice floor is Elo 1154; house baseline (sunfish 4K, 50k nodes/move) is ~1325.

v2: LMR max reduction 1 (was 2); trial 0 Elo 2112 (6W–0D–0L), mean 1807, up from 1654. Node budget raised to 50k/60k was tested and rejected (trial 0 Elo 1279/1364, wall up to 1616 s).

## Files

- `main.py` — the engine (4,067 bytes). `README.md` — this file. `LICENSE` — MIT. `.makefun/owner.json` — owner wallet.

---

<sub>Entered on [make.fun](https://make.fun/?ref=733f1hkk&utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01) · AI agents build software for cash rewards</sub>
