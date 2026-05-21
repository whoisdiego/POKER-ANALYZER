# Poker Analyzer

A single-file, zero-dependency Texas Hold'em hand analyzer that runs entirely in the browser. Given your hole cards and any community cards already on the board, it runs a **Monte Carlo simulation** to estimate your probability of winning, tying, or losing — and shows the distribution of hand ranks for both you and your opponents.

---

## Features

- **Visual card picker** — click any slot to select a card by suit and rank
- **Supports all streets** — Hole Cards, Flop, Turn, and River
- **Configurable players** — 2 to 9 players
- **Configurable simulations** — 10k (fast), 50k (balanced), 100k (precise)
- **Hand rank chart** — animated bar chart comparing your hand distribution vs opponents
- **Win / Tie / Lose bar** — color-coded summary with percentages
- **Correct kicker resolution** — hands of the same category are broken by kicker values, not treated as ties

---

## How It Works

### Card Space

Once you select your hole cards and any community cards, the remaining deck has:

$$52 - \text{known cards} = \text{available cards}$$

The opponent's possible hands are drawn from that reduced deck, so the probability space shrinks correctly as more cards are revealed.

### Monte Carlo Simulation

For each simulation:

1. Shuffle the remaining deck
2. Deal community cards needed to complete the board (Turn, River if not set)
3. Deal 2 cards to each opponent
4. Evaluate the best 5-card hand out of 7 for each player using all `C(7,2) = 21` combinations
5. Compare hands — including kicker tiebreaking
6. Record Win / Tie / Lose and the hand rank achieved

After `N` simulations, every count is divided by `N` to get a percentage.

### Hand Evaluator

`scoreHand(five)` returns an array like:

```
[category, val1, val2, val3, val4, val5]
```

Where `category` is:

| Value | Hand |
|-------|------|
| 8 | Straight Flush |
| 7 | Four of a Kind |
| 6 | Full House |
| 5 | Flush |
| 4 | Straight |
| 3 | Three of a Kind |
| 2 | Two Pair |
| 1 | One Pair |
| 0 | High Card |

Values after the category are ordered by frequency then by rank, so `compareHands(a, b)` can resolve ties correctly element by element. For example:

```
Pair of Aces,  K kicker → [1, 14, 14, 13, ...]
Pair of Kings, A kicker → [1, 13, 13, 14, ...]
→ Pair of Aces wins
```

### Combinatorics Behind the Probabilities

The total number of possible opponent hands after `k` known cards is:

$$\binom{52-k}{2}$$

For example with 5 known cards (2 hole + 3 flop):

$$\binom{47}{2} = 1081 \text{ possible opponent hands}$$

Probabilities for specific hand types are calculated by summing mutually exclusive favorable cases:

$$P = \frac{\sum \text{ favorable combinations}}{\binom{47}{2}}$$

---

## Usage

```
1. Open poker.html in your browser
2. Click the hole card slots and select your 2 cards
3. Optionally add Flop (3 cards), Turn, and River
4. Set the number of players and simulations
5. Click ▶ Analyze
```

---


## Accuracy

| Simulations | Typical error | Time (approx.) |
|-------------|--------------|----------------|
| 10,000 | ±1.0% | < 1 sec |
| 50,000 | ±0.4% | 2–4 sec |
| 100,000 | ±0.3% | 5–10 sec |

Accuracy improves as more community cards are known, since the remaining deck is smaller and the outcome space is reduced.

---

## Known Limitations

- Evaluator compares hand ranks and kickers but does not account for **suit-based tiebreaks** (irrelevant in standard poker rules — suits never break ties)
- Multi-way pots track whether *any* opponent beats you, not per-opponent win rates
- No pot odds, implied odds, or GTO calculations — this is an equity estimator only

---

## Tech Stack

| | |
|---|---|
| Language | Vanilla JavaScript (ES6+) |
| Styling | CSS custom properties, Google Fonts (Oswald, IBM Plex Mono) |
| Algorithm | Monte Carlo simulation |
| Dependencies | None |

---

## License

MIT — do whatever you want with it. :)
