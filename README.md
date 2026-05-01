# Chess Bot

A Python chess engine built from scratch for a university competition. The bot plays autonomously — it receives a board state in FEN format, searches for the best move, and returns it in UCI format within a strict 4-second time limit.

No external engines. No APIs. No hardcoded moves.

---

## How It Works

The engine is built around **Negamax with Alpha-Beta Pruning**, wrapped in **Iterative Deepening** to stay within the time limit. At each move, it searches progressively deeper (depth 1, 2, 3...) and always returns the best move from the last *fully completed* depth — so it never times out without having an answer.

### Search

- **Negamax + Alpha-Beta Pruning** — a standard minimax formulation where each player maximises their own score. Alpha-beta eliminates branches that cannot influence the result, roughly doubling effective search depth.
- **Iterative Deepening** — the bot searches depth 1 through 8, committing only to fully completed depths. If time runs out mid-depth, the previous depth's result is returned safely.
- **Quiescence Search** — when the main search reaches depth 0, it continues searching captures and promotions until the position is "quiet". This prevents the horizon effect, where the engine misses a losing capture just outside its search window.

### Move Ordering

Good move ordering is critical for alpha-beta efficiency — the more cutoffs that happen early, the fewer nodes need to be searched.

Moves are tried in this priority order:
1. **Captures** — sorted by MVV-LVA (Most Valuable Victim, Least Valuable Attacker): prefer capturing a queen with a pawn over capturing a pawn with a queen
2. **Promotions** — always high priority
3. **Checks** — moves that put the opponent in check
4. **All other moves** — in default order

### Evaluation

The evaluation function returns a score from the perspective of the side to move (positive = good for the current player).

**Material balance** uses standard piece values:

| Piece | Value |
|-------|-------|
| Pawn | 100 |
| Knight | 320 |
| Bishop | 330 |
| Rook | 500 |
| Queen | 900 |

**Piece-square tables** add a positional bonus or penalty depending on where each piece stands. For example:
- Knights are rewarded for central squares and penalised on the edges
- Pawns are rewarded for advancement and central presence
- The king is penalised for being in the centre during the middlegame and rewarded for being tucked behind pawns after castling

---

## Competition Constraints

This bot was built under the following strict rules:

| Constraint | Detail |
|------------|--------|
| Time limit | 4 seconds per move — exceeded = forfeit |
| No external engines | Stockfish and similar are banned |
| No external APIs | All computation must be local |
| No hardcoded moves | Every move must be computed |
| Allowed library | `python-chess` for board representation and legal move generation only |

---

## Running Locally

```bash
pip install python-chess
python engine.py
```

To test against the random bot or any other bot, edit the `BOT1` and `BOT2` variables at the top of `engine.py`:

```python
BOT1 = "my_bot"      # your bot (filename without .py)
BOT2 = "bot_random"  # opponent
```

---

## Project Structure

```
chess-bot/
├── my_bot.py        # The chess engine — competition submission
├── engine.py        # Match runner provided by competition organisers
├── bot_random.py    # Baseline opponent: picks a random legal move
└── requirements.txt
└── assets/
    └── certificate.png
```

> `engine.py` was provided by the competition organisers as the official test harness. It handles turn management, time enforcement, and result detection. It is included here so the bot can be tested locally in the same environment used during the competition.

---

## Requirements

```
python-chess
```

Install with:

```bash
pip install -r requirements.txt
```

Python 3.8 or higher recommended.

---

## Result

> 🏆 1st Place — Chess Bot Arena · April 2026
 [View Certificate](assets/certificate.png)
