[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/isnalestari09/tiny-chess/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/isnalestari09/tiny-chess/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

# Tiny Chess — 4 KB Python engine

A chess engine in a single `main.py` (4,089 bytes). Original code, not a
copy of sunfish or any other engine.

## How it works

- 0x88 board, incremental material + piece-square eval, Zobrist hashing.
- Alpha-beta with iterative deepening, check extensions, quiescence search
  (captures only, with a check-evasion cap so it cannot explode).
- Move-only transposition table (best move, for ordering), MVV-LVA capture
  ordering, threefold-repetition detection against the game history.
- Deterministic: a fixed 25,000-node budget per move (6,250 when the
  static eval is below -600), never clock-based, no randomness.
- Talks to the house env over HTTP (`MAKEFUN_ENV_URL`): `POST /game`,
  `POST /move {"game","move"}` (UCI), `POST /resign`. No FEN parsing — the
  position is rebuilt by replaying the move list from the start position.
- `main.py` is the golfed source squeezed and LZMA-compressed (FORMAT_ALONE)
  with a base85 payload and a 62-byte unpacker. The readable source is
  `engine_v22.py` in the build directory (not part of this repo).

## Results (local practice trials vs the house gauntlet)

| trial | result | Elo |
|-------|--------|-----|
| 0 | 2W 1D 3L | 1191 |
| 1 | 3W 0D 3L | 1279 |
| 2 | 4W 0D 2L | 1451 |

Mean practice Elo **1307** (finals threshold 1154). Perft verified on
startpos, Kiwipete, en-passant pin, and promotion/castling positions.
No move exceeded ~5s (limit 10s/move).

## Run

```sh
MAKEFUN_ENV_URL=http://127.0.0.1:8080 python3 main.py
```

## License

MIT — see LICENSE.
