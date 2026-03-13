# ChessAgine Tool Parameters Reference (v0.6.3)

## FEN Format Requirements
All FEN-accepting tools require valid FEN strings matching:
```
^([rnbqkpRNBQKP1-8]+\/){7}[rnbqkpRNBQKP1-8]+ [bw] [KQkq-]+ [a-h][1-8]|- \d+ \d+$
```

Components:
1. Piece placement (8 ranks, / separated)
2. Side to move (w/b)
3. Castling availability (KQkq or -; Chess 960 uses file letters e.g. HAha)
4. En passant square (e.g., e3 or -)
5. Halfmove clock
6. Fullmove number

## Tool Parameter Constraints

### Engine Analysis
**Stockfish depth**: 12-30 (recommend 18-25)
**MultiPV numLines**: 1-5
**Maia2 rating**: Must be one of [1100, 1200, 1300, 1400, 1500, 1600, 1700, 1800, 1900]

### Chess 960 (is960 parameter)
Pass `is960: true` on any supporting tool when working with Fischer Random positions.
Supporting tools: `get-boardstate-for-fen`, `get-boardstate-for-move`, `is-legal-move`,
`get-theme-scores`, `get-theme-progression`, `analyze-variation-themes`, `compare-variations`,
`find-critical-moments`, `generate-game-review`

### Theme Analysis (New in v0.6.3)
**Theme enum**: `material | mobility | space | positional | kingSafety | tactical | lightsqaureControl | darksqaureControl`
**color**: `w` or `b` (side to evaluate from)
**threshold** (find-critical-moments): 0.1-2.0, default 0.5 (lower = more moments detected)

### generate-game-review
**format**: `text` (human-readable) or `json` (structured data)
**criticalMomentThreshold**: 0.1-2.0, default 0.5
**is960**: boolean, default false

### compare-variations input shape
```json
{
  "color": "w",
  "rootFen": "<FEN>",
  "variations": [
    { "name": "Main line", "moves": ["e4", "e5", "Nf3"] },
    { "name": "Alternative", "moves": ["e4", "e5", "d4"] }
  ],
  "is960": false
}
```

### Puzzle Fetching
**ratingFrom**: Minimum 1000
**ratingTo**: Maximum 2500
**themes**: Array of theme strings (see `get-puzzle-themes`)

### Move Formats
**SAN** (Standard Algebraic Notation): Nf3, e4, Qxd5
**UCI** (Universal Chess Interface): e2e4, g1f3, d8d5

Both formats accepted by `is-legal-move` and `get-boardstate-for-move`

## Common Parameters

### Game/Study IDs
- **Lichess game ID**: 8-character alphanumeric (e.g., "abc12345")
- **Lichess game URL**: Full URL (e.g., "https://lichess.org/abc12345")
- **Study ID**: Alphanumeric string (e.g., "WTvnkWAL")

### PGN Format
Standard PGN with headers and movetext:
```
[Event "Casual Game"]
[Site "lichess.org"]
[Date "2024.01.15"]
[White "Player1"]
[Black "Player2"]
[Result "1-0"]

1. e4 e5 2. Nf3 Nc6 3. Bb5 a6 1-0
```

## Return Value Structures

### Opening Book Lookup
Returns: `{name, eco, moves, fen, popularity}`

### Master Games
Returns: Statistics object with move frequency, win rates per move

### Engine Analysis
Returns: Evaluation (centipawns or mate), best move, PV line

### TCEC/Correspondence Stats
Returns: Game statistics, move frequencies, results breakdown

### Board State
Returns: Text description of position including piece locations, threats, material count

### Theme Scores
Returns: Object with scores per theme (positive = White better, negative = Black better, 0 = equal)

### generate-game-review (text format)
Returns: Human-readable report covering theme progression, critical moments, opening/middlegame/endgame phases

### generate-game-review (json format)
Returns: Structured JSON with per-move theme data and annotated critical moments

### get-tactical-position-summary
Returns: List of tactical features — hanging pieces, forks, pins, semi-protected pieces

## Error Patterns

### Common Errors
- Invalid FEN format → Check FEN structure
- Out-of-range depth → Use 12-30 for Stockfish
- Invalid Maia rating → Use exact values from list
- Missing game ID → Parse URL to extract ID
- Illegal move → Verify with `is-legal-move` first

### Graceful Degradation
- Opening not in book → Use ChessDB or Lichess instead
- No TCEC data → Fall back to correspondence or master games
- Tool timeout → Reduce depth or batch size
- Chess 960 position → Skip opening book / master game lookups (standard chess only)