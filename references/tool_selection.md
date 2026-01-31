# ChessAgine Tool Selection Guide

## Quick Reference Matrix

| Task | Primary Tools | Secondary Tools |
|------|---------------|-----------------|
| Analyze single position | `get-stockfish-analysis`, `generate-chess-board-view-artificat-html` | `get-leela-analysis`, `get-boardstate-for-fen` |
| Analyze full game | `fetch-lichess-game`, `parse-pgn-into-fens`, `get-stockfish-batch-analysis` | `get-fen-map-lookup`, `generate-dynamic-gameview-html` |
| Study opening | `fen-openingbook-lookup`, `get-lichess-master-games`, `get-stockfish-multipv-analysis` | `get-chessboardmagic-tcec-stats`, `get-chessboardmagic-corr-stats` |
| Find best move | `get-stockfish-best-move` | `get-stockfish-multipv-analysis`, `get-chessdb-analysis` |
| Understand human moves | `get-maia2-analysis`, `get-elite-leela-analysis`, `generate-chess-board-view-artificat-html` | `get-stockfish-analysis` |
| Training puzzles | `fetch-chess-puzzle`, `get-puzzle-themes` | `get-stockfish-best-move` |
| Repertoire work | `get-chessboardmagic-repertoires`, `get-chessboardmagic-repertoire-details` | `get-lichess-master-games`, `get-stockfish-multipv-analysis` |
| Compare with masters | `get-lichess-master-games`, `get-chessboardmagic-tcec-games` | `get-chessboardmagic-corr-games` |
| User game review | `fetch-lichess-games`, `fetch-lichess-game` | `get-chessboardmagic-games`, `get-chessboardmagic-game-details` |

## Decision Trees

### Position Analysis Depth Decision
```
What type of analysis needed?
├─ Best objective move/variations?
│   └─ Use Stockfish (depth 20-25) for accurate tactical solutions
├─ Master-level human perspective?
│   └─ Use Elite Leela (trained on 2500+ master games)
├─ General positional understanding?
│   └─ Use Leela (self-play neural net for nuanced positions)
└─ Student-level move understanding (< 1900 rating)?
    └─ Use Maia2 at appropriate rating level
```

### Engine Selection Decision
```
Which engine to use?
├─ Need objectively best moves & deep analysis?
│   └─ Stockfish (strongest, most accurate variations)
├─ Want master-level human-style moves?
│   └─ Elite Leela (2500+ master games training)
├─ Need positional evaluation?
│   └─ Leela (self-play neural net)
└─ Explaining moves to student at specific rating?
    └─ Maia2 (trained on <1900 human games)
```

### Database Query Decision
```
Need position statistics?
├─ Is it an opening (< 15 moves)?
│   ├─ Yes → Start with `fen-openingbook-lookup`
│   │   ├─ Found → Get master stats: `get-lichess-master-games`
│   │   └─ Not found → Try `get-chessdb-analysis`
│   └─ For preparation → Add TCEC: `get-chessboardmagic-tcec-stats`
└─ Is it middlegame/endgame?
    └─ ChessDB has best coverage: `get-chessdb-analysis`
```

### Visualization Decision
```
What to show user?
├─ Single position analysis?
│   └─ `generate-chess-board-view-artificat-html` (2D default)
├─ Full game or variation?
│   └─ `generate-dynamic-gameview-html` (navigable)
└─ Multiple related positions?
    └─ Multiple `generate-chess-board-view-artificat-html` calls
```

### Game Source Decision
```
Where to fetch games?
├─ User mentioned Lichess username?
│   └─ `fetch-lichess-games` or `fetch-lichess-game`
├─ User has Chessboard Magic account?
│   └─ `get-chessboardmagic-games` or `get-chessboardmagic-game-details`
└─ Specific URL provided?
    └─ Parse URL, use `fetch-lichess-game` with game ID
```

## Tool Combination Patterns

### Pattern 1: Position Deep Dive
```
1. get-boardstate-for-fen (understand position)
2. fen-openingbook-lookup (identify opening if applicable)
3. get-stockfish-multipv-analysis (objective best moves, 3 lines, depth 22)
4. get-elite-leela-analysis (master-level perspective)
5. get-lichess-master-games (what masters play)
6. get-chess-knowledgebase (apply principles)
7. generate-chess-board-view-artificat-html (visualize)
```

### Pattern 2: Game Analysis Lite (Fast)
```
1. fetch-lichess-game (get PGN)
2. parse-pgn-into-fens (extract positions)
3. Identify 3-5 critical moments (user mistakes, tactical points)
4. get-stockfish-analysis on those positions only (depth 20)
5. generate-dynamic-gameview-html (show game with annotations)
```

### Pattern 3: Game Analysis Pro (Thorough)
```
1. fetch-lichess-game or get-chessboardmagic-game-details
2. parse-pgn-into-fens
3. get-fen-map-lookup (map moves to FENs)
4. get-stockfish-batch-analysis (all positions, depth 18)
5. Identify mistakes (eval drops > 100cp)
6. Deep analysis of mistakes:
   - get-stockfish-multipv-analysis (why better moves exist)
   - get-chess-knowledgebase (what principle violated)
7. Check opening: fen-openingbook-lookup + get-lichess-master-games
8. generate-dynamic-gameview-html with detailed annotations
```

### Pattern 4: Opening Study
```
1. Start with opening FEN
2. fen-openingbook-lookup (get name, common continuations)
3. get-lichess-master-games (master-level statistics and games)
4. get-chessboardmagic-tcec-stats (computer evaluation)
5. get-chessboardmagic-corr-stats (correspondence games - deep analysis)
6. For main line and critical alternatives:
   - get-stockfish-multipv-analysis (5 lines, depth 25 for accurate variations)
   - get-elite-leela-analysis (master-style move selection)
7. Fetch sample games: get-chessboardmagic-tcec-games
```

### Pattern 5: Repertoire Analysis
```
1. get-chessboardmagic-repertoires (list user's repertoires)
2. get-chessboardmagic-repertoire-details (get specific repertoire)
3. For each main line FEN:
   - get-lichess-master-games (verify soundness)
   - get-stockfish-multipv-analysis (find improvements)
4. Compare with user's actual games: get-chessboardmagic-games
5. Find positions where repertoire wasn't followed
```

### Pattern 6: Puzzle Training Session
```
1. get-puzzle-themes (show available themes)
2. User selects theme and rating range
3. fetch-chess-puzzle (with filters)
4. generate-chess-board-view-artificat-html (present puzzle)
5. User attempts solution
6. get-stockfish-best-move (verify solution)
7. If wrong: explain with get-chess-knowledgebase principles
8. Fetch next puzzle and repeat
```

### Pattern 7: User Performance Analysis
```
1. fetch-lichess-games (recent 20 games)
2. For each game:
   - Parse to identify opening: fen-openingbook-lookup
   - Quick batch analysis: get-stockfish-batch-analysis
3. Aggregate statistics:
   - Common openings played
   - Average mistake severity
   - Time management patterns (from PGN)
4. Deep dive into worst game:
   - Full analysis with get-stockfish-multipv-analysis
   - Human perspective: get-elite-leela-analysis
   - Perspective at user rating: get-maia2-analysis
```

## Engine Selection Guidelines

### Stockfish
- **Strongest objective engine** for finding best moves and variations
- Best for: Tactical analysis, finding errors, generating accurate alternatives
- Use depths: 20-25 for critical positions, 18-20 for standard analysis

### Elite Leela
- **Neural net trained on 2500+ master games** for master-level move selection
- Best for: Understanding how strong humans approach positions, opening preparation
- Provides practical, human-style moves over purely objective ones

### Leela
- **Self-play neural net** focused on positional understanding
- Best for: Nuanced positional evaluation, long-term strategic assessment
- Less reliable for sharp tactical positions

### Maia2
- **Trained on human games < 1900 rating level** for human move prediction
- Best for: Explaining where players at specific rating levels make moves
- Use for: understanding beginner/intermediate thinking patterns
- Never use for finding best moves

## Engine Depth Guidelines

### Stockfish Depth Selection
- **Depth 12-15**: Quick position check, move legality verification
- **Depth 18-20**: Standard analysis, most positions
- **Depth 22-25**: Important positions, critical decisions, opening preparation


### When to Use MultiPV
- **numLines=3**: Standard for showing alternatives
- **numLines=5**: Opening preparation, multiple candidate moves
- **numLines=1**: Just need best move (use `get-stockfish-best-move` instead)

### Batch Analysis Optimization
- Use for 10+ positions from same game
- Reduces overhead vs individual calls
- Automatically uses reasonable depth (18-20)

## Common Pitfalls to Avoid

1. **Over-analysis**: Don't analyze every position at depth 25
   - Select critical moments
   - Use batch analysis for overview
   - Deep analysis only where needed

2. **Wrong engine for job**:
   - Don't use Leela for sharp tactical positions (use Stockfish)
   - Don't use Maia2 for finding best moves (only for student-level prediction)
   - Don't use Stockfish when you need master-level perspective (use Elite Leela)
   - Match engine to analysis goal, not just position type

3. **Ignoring context**:
   - Check if user has Lichess/Chessboard Magic account before calling those tools
   - Verify FEN format before analysis
   - Consider time constraints (user wants quick answer vs deep study)

4. **Missing visualization**:
   - Always show position when analyzing
   - Use dynamic view for games, static for single positions

5. **Not leveraging databases**:
   - Opening book lookup is fast - use it first
   - Master games provide practical validation
   - TCEC/correspondence data adds computer/human perspective

6. **FEN handling errors**:
   - Ensure full FEN with side to move, castling, etc.
   - Validate before passing to tools
   - Use `is-legal-move` before `get-boardstate-for-move`

## User Intent Recognition

### Analysis Request Signals
- "analyze this position/game" → Full analysis with Stockfish + Elite Leela
- "what's the best move" → `get-stockfish-best-move`
- "compare with masters" → Database lookups + Elite Leela
- "is this opening good" → Opening study workflow with Stockfish + Elite Leela

### Time Sensitivity
- "Quick analysis" → Stockfish depth 18, fewer tools
- "Deep dive" → Stockfish depth 25+, Elite Leela, databases
- No time mention → Standard Stockfish depth 20-22