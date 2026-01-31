# ChessAgine Tool Parameters Reference

## FEN Format Requirements
All FEN-accepting tools require valid FEN strings matching:
```
^([rnbqkpRNBQKP1-8]+\/){7}[rnbqkpRNBQKP1-8]+ [bw] [KQkq-]+ [a-h][1-8]|- \d+ \d+$
```

Components:
1. Piece placement (8 ranks, / separated)
2. Side to move (w/b)
3. Castling availability (KQkq or -)
4. En passant square (e.g., e3 or -)
5. Halfmove clock
6. Fullmove number

## Tool Parameter Constraints

### Engine Analysis
**Stockfish depth**: 12-30 (recommend 18-25)
**MultiPV numLines**: 1-5
**Maia2 rating**: Must be one of [1100, 1200, 1300, 1400, 1500, 1600, 1700, 1800, 1900]

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