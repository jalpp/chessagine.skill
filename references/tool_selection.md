# ChessAgine Tool Selection Guide (v0.6.3)

## Quick Reference Matrix

| Task | Primary Tools | Secondary Tools |
|------|---------------|-----------------|
| Analyze single position | `get-stockfish-analysis`, `render_chess_board` | `get-leela-analysis`, `get-boardstate-for-fen`, `get-theme-scores` |
| Analyze full game (quick) | `generate-game-review` | `render_pgn_viewer` |
| Analyze full game (deep) | `fetch-lichess-game`, `find-critical-moments`, `get-stockfish-analysis` | `parse-pgn-into-fens`, `get-fen-map-lookup`, `render_pgn_viewer` |
| Study opening | `fen-openingbook-lookup`, `get-lichess-master-games`, `get-stockfish-multipv-analysis` | `get-chessboardmagic-tcec-stats`, `get-chessboardmagic-corr-stats` |
| Find best move | `get-stockfish-best-move` | `get-stockfish-multipv-analysis`, `get-chessdb-analysis`, `get-chessdb-pv` |
| Understand human moves | `get-maia2-analysis`, `get-elite-leela-analysis`, `render_chess_board` | `get-stockfish-analysis` |
| Training puzzles | `fetch-chess-puzzle`, `get-puzzle-themes` | `get-stockfish-best-move` |
| Repertoire work | `get-chessboardmagic-repertoires`, `get-chessboardmagic-repertoire-details` | `get-lichess-master-games`, `get-stockfish-multipv-analysis` |
| Compare with masters | `get-lichess-master-games`, `get-chessboardmagic-tcec-games` | `get-chessboardmagic-corr-games` |
| User game review | `fetch-lichess-games`, `fetch-lichess-game` | `get-chessboardmagic-games`, `get-chessboardmagic-game-details` |
| Compare variations | `compare-variations` | `get-theme-scores`, `render_chess_board` |
| Find turning points | `find-critical-moments` | `analyze-variation-themes` |
| Theme/strategic breakdown | `get-theme-scores`, `get-theme-progression` | `analyze-variation-themes` |
| Tactical quick check | `get-tactical-position-summary` | `get-stockfish-best-move` |
| Chess 960 | Any analysis tool + `is960: true` | Skip opening book/master game lookups |

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
├─ Strategic breakdown by theme?
│   └─ Use get-theme-scores (material, kingSafety, space etc.)
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
    └─ ChessDB has best coverage: `get-chessdb-analysis` / `get-chessdb-pv`
```

### Visualization Decision (v0.6.3)
```
What to show user?
├─ Single position analysis?
│   └─ `render_chess_board` with FEN
├─ Full game or variation?
│   └─ `render_pgn_viewer` with PGN
└─ Multiple related positions?
    └─ Multiple `render_chess_board` calls
```

### Game Analysis Decision
```
How thorough does the game review need to be?
├─ Quick/summary review?
│   └─ `generate-game-review` (single call, text format)
├─ Structured data for further processing?
│   └─ `generate-game-review` with format: "json"
├─ Deep dive into specific moments?
│   └─ parse-pgn-into-fens → find-critical-moments → stockfish deep dive
└─ Full manual control?
    └─ parse-pgn-into-fens → get-fen-map-lookup → batch analysis
```

### Theme Analysis Decision
```
What strategic question are you answering?
├─ "How is the position at move X?" → get-theme-scores (single FEN snapshot)
├─ "How did kingSafety evolve over this variation?" → get-theme-progression
├─ "Where did the game turn?" → find-critical-moments on full game
├─ "Which of these lines is strategically better?" → compare-variations
└─ "Give me the full theme picture of this game" → generate-game-review
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
6. get-theme-scores (strategic breakdown: material, kingSafety, space)
7. render_chess_board (visualize)
```

### Pattern 2: Game Analysis Quick (Single Call)
```
1. fetch-lichess-game (get PGN) or accept user PGN
2. generate-game-review (format: "text", criticalMomentThreshold: 0.5)
3. render_pgn_viewer (show navigable game)
```

### Pattern 3: Game Analysis Pro (Thorough)
```
1. fetch-lichess-game or get-chessboardmagic-game-details
2. parse-pgn-into-fens
3. find-critical-moments (identify turning points automatically)
4. get-stockfish-batch-analysis (all positions, depth 18)
5. Deep analysis of critical moments:
   - get-stockfish-multipv-analysis (why better moves exist)
   - get-theme-scores (what changed strategically)
6. Check opening: fen-openingbook-lookup + get-lichess-master-games
7. render_pgn_viewer with detailed annotations
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
4. render_chess_board (present puzzle)
5. User attempts solution
6. get-stockfish-best-move (verify solution)
7. get-tactical-position-summary (explain tactics: forks, pins etc.)
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
   - generate-game-review (full review)
   - get-stockfish-multipv-analysis on blunders
   - Human perspective: get-elite-leela-analysis
   - Perspective at user rating: get-maia2-analysis
```

### Pattern 8: Variation Comparison
```
1. Establish rootFen (position where lines diverge)
2. compare-variations with named variation arrays
3. Interpret per-variation theme scores
4. render_chess_board on resulting positions of each line
5. get-stockfish-multipv-analysis on the best-scoring line
```

### Pattern 9: Chess 960 Analysis
```
1. Confirm is960: true for all supporting tool calls
2. get-boardstate-for-fen (is960: true)
3. render_chess_board (works for all variants)
4. get-stockfish-multipv-analysis (Stockfish handles 960 natively)
5. SKIP: fen-openingbook-lookup, get-lichess-master-games (standard chess DBs only)
6. get-theme-scores (is960: true) for strategic breakdown
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
   - Select critical moments using `find-critical-moments`
   - Use `generate-game-review` for quick overview
   - Deep analysis only where needed

2. **Wrong engine for job**:
   - Don't use Leela for sharp tactical positions (use Stockfish)
   - Don't use Maia2 for finding best moves (only for student-level prediction)
   - Don't use Stockfish when you need master-level perspective (use Elite Leela)

3. **Chess 960 mistakes**:
   - Always pass `is960: true` for Fischer Random positions
   - Do NOT call opening book / master DB lookups for 960 positions
   - Stockfish handles 960 castling rules natively

4. **Missing visualization**:
   - Always show position when analyzing using `render_chess_board`
   - Use `render_pgn_viewer` for games, not static boards

5. **Not using theme tools**:
   - `get-theme-scores` is fast and gives strategic context
   - Use `find-critical-moments` before manually identifying key positions
   - `generate-game-review` replaces the slow parse→batch→annotate workflow

6. **FEN handling errors**:
   - Ensure full FEN with side to move, castling, etc.
   - Validate before passing to tools
   - Use `is-legal-move` before `get-boardstate-for-move`

## User Intent Recognition

### Analysis Request Signals
- "analyze this position/game" → Full analysis with Stockfish + theme tools
- "what's the best move" → `get-stockfish-best-move`
- "compare with masters" → Database lookups + Elite Leela
- "is this opening good" → Opening study workflow with Stockfish + Elite Leela
- "review my game" → `generate-game-review`
- "when did I go wrong" → `find-critical-moments`
- "compare these lines" → `compare-variations`
- "chess 960 / Fischer random" → All tools with `is960: true`

### Time Sensitivity
- "Quick analysis" → `generate-game-review` or Stockfish depth 18
- "Deep dive" → Stockfish depth 25+, Elite Leela, databases, theme analysis
- No time mention → Standard Stockfish depth 20-22

## v0.6.3 Changes from v0.6.0

**New Theme Analysis Tools:**
- ✅ `generate-game-review` - Replaces manual parse→batch→annotate workflow
- ✅ `analyze-variation-themes` - Theme evolution across moves
- ✅ `compare-variations` - Side-by-side variation comparison
- ✅ `find-critical-moments` - Auto-detect turning points
- ✅ `get-theme-scores` - Per-FEN strategic breakdown
- ✅ `get-theme-progression` - Single theme tracking
- ✅ `get-tactical-position-summary` - Quick tactics overview
- ✅ `get-chessdb-pv` - ChessDB principal variation

**Chess 960 Support:**
- ✅ `is960?: boolean` on all position/theme analysis tools

**MCP Prefix Update:**
- All calls now use `chessagine-mcp-v0.6.3:` prefix