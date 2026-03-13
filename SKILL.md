---
name: chessagine-mcp-v060
description: Comprehensive chess analysis using the ChessAgine MCP. Use this skill when users request chess-related tasks including analyzing positions or games, studying openings, finding best moves, comparing with master/computer games, solving puzzles, building repertoires, reviewing user games from Lichess or Chessboard Magic, Chess 960 / Fischer Random analysis, theme analysis, variation comparison, game reviews, or any chess related request. Triggers on chess positions (FEN), PGN games, Lichess URLs, opening names, Chess 960 positions, or general chess requests.
---

# ChessAgine MCP Skill v0.6.3 (chessagine-mcp-v060)

ChessAgine provides comprehensive chess analysis through 40+ specialized tools integrating Stockfish, Leela Chess Zero, Maia2, Lichess, Chessboard Magic, and multiple chess databases. v0.6.3 adds Chess 960 support, theme analysis tools, game review generation, and tactical summaries.

## Core Workflow Principles

### Context Priority
When handling chess requests, always:
1. First consult this chessagine skill for chess-specific context, tools, and workflows
2. Then reference user's project memory related to chess for personalized preferences or context

### Always Visualize
Every position analysis must include visualization using the correct MCP rendering tools:
- **Single positions**: Use `chessagine-mcp-v0.6.3:render_chess_board` with FEN
- **Full games**: Use `chessagine-mcp-v0.6.3:render_pgn_viewer` with PGN

### Chess 960 / Fischer Random Support
Many tools now accept an `is960: boolean` parameter (default: false). Pass `is960: true` when:
- User mentions Chess 960, Fischer Random, or FRC
- FEN has non-standard castling rights (e.g., HAha instead of KQkq)
- User explicitly mentions a randomized starting position

Tools supporting `is960`: `get-boardstate-for-fen`, `get-boardstate-for-move`, `is-legal-move`, `get-theme-scores`, `get-theme-progression`, `analyze-variation-themes`, `compare-variations`, `find-critical-moments`, `generate-game-review`

### Multi-Source Validation
For important positions, combine multiple data sources:
- Opening database (fast orientation)
- Engine analysis (best moves)
- Master games (practical validation)
- TCEC/Correspondence (computer/deep human perspective)

### Right Engine for the Job
- **Stockfish** (depth 18-25): Best objective moves through brute-force calculation
- **Elite Leela**: Master-level human perspective (~2500 rating) for strategic insights
- **Leela**: Neural network for positional understanding
- **Maia2** (rating 1100-1900): Human-like moves matching specific player strengths

### Progressive Depth
Start with quick overview, deepen analysis only where critical:
- Quick check: depth 15-18
- Standard analysis: depth 20-22
- Critical positions: depth 25-30

## Quick Start Workflows

### Analyze Position
1. `chessagine-mcp-v0.6.3:get-boardstate-for-fen` (understand position)
2. `chessagine-mcp-v0.6.3:render_chess_board` (visualize)
3. `chessagine-mcp-v0.6.3:get-stockfish-multipv-analysis` (3 lines, depth 22)
4. `chessagine-mcp-v0.6.3:fen-openingbook-lookup` (if opening)
5. `chessagine-mcp-v0.6.3:get-lichess-master-games` (master practice)
6. `chessagine-mcp-v0.6.3:get-theme-scores` (optional: material, space, king safety breakdown)

### Analyze Game (Quick)
1. `chessagine-mcp-v0.6.3:fetch-lichess-game` (get PGN) or accept user PGN
2. `chessagine-mcp-v0.6.3:generate-game-review` (comprehensive theme review with critical moments)
3. `chessagine-mcp-v0.6.3:render_pgn_viewer` (navigable game view)

### Analyze Game (Deep)
1. `chessagine-mcp-v0.6.3:fetch-lichess-game` (get PGN)
2. `chessagine-mcp-v0.6.3:parse-pgn-into-fens` (extract positions)
3. `chessagine-mcp-v0.6.3:find-critical-moments` (identify key turning points)
4. `chessagine-mcp-v0.6.3:get-stockfish-analysis` on critical positions (depth 20)
5. `chessagine-mcp-v0.6.3:render_pgn_viewer` (navigable game view)

### Study Opening
1. `chessagine-mcp-v0.6.3:fen-openingbook-lookup` (identify opening, common moves)
2. `chessagine-mcp-v0.6.3:get-lichess-master-games` (master statistics)
3. `chessagine-mcp-v0.6.3:get-chessboardmagic-tcec-stats` (computer evaluation)
4. `chessagine-mcp-v0.6.3:get-stockfish-multipv-analysis` (5 lines, depth 25)
5. Fetch sample games from relevant database

### Find Best Move
1. `chessagine-mcp-v0.6.3:get-stockfish-best-move` (tactical solution)
2. `chessagine-mcp-v0.6.3:get-stockfish-multipv-analysis` (alternatives, 3 lines)
3. Cross-reference with `chessagine-mcp-v0.6.3:get-chessdb-analysis`
4. Optionally add `chessagine-mcp-v0.6.3:get-chessdb-pv` for engine best line from ChessDB

### Compare Variations
1. `chessagine-mcp-v0.6.3:compare-variations` (pass rootFen + named variation arrays)
2. Interpret theme scores per variation (material, mobility, space, positional, kingSafety, tactical)
3. `chessagine-mcp-v0.6.3:render_chess_board` on key resulting positions

### Puzzle Training
1. `chessagine-mcp-v0.6.3:get-puzzle-themes` (show available themes)
2. `chessagine-mcp-v0.6.3:fetch-chess-puzzle` (with theme/rating filters)
3. `chessagine-mcp-v0.6.3:render_chess_board` (present)
4. After attempt: `chessagine-mcp-v0.6.3:get-stockfish-best-move` (verify)

### Chess 960 Analysis
1. Confirm `is960: true` for all supporting tool calls
2. `chessagine-mcp-v0.6.3:get-boardstate-for-fen` with `is960: true`
3. `chessagine-mcp-v0.6.3:render_chess_board` (visualization works for all variants)
4. `chessagine-mcp-v0.6.3:get-stockfish-multipv-analysis` (Stockfish handles 960 natively)
5. Note: Opening book and master game databases are standard chess only — skip for 960

## Tool Categories

### Position Analysis
- `get-stockfish-analysis` - Tactical evaluation (depth 18-25)
- `get-stockfish-multipv-analysis` - Show alternatives (3-5 lines)
- `get-stockfish-best-move` - Quick best move without full analysis
- `get-stockfish-batch-analysis` - Analyze multiple positions in batch
- `get-leela-analysis` - Strategic/positional evaluation
- `get-elite-leela-analysis` - Master-level positional understanding
- `get-maia2-analysis` - Human-like moves at specific rating (1100-1900)
- `get-chessdb-analysis` - Large database position lookup
- `get-chessdb-pv` - Principal variation (best line) from ChessDB (**new in v0.6.3**)
- `queue-chessdb-analysis` - Queue position for background ChessDB analysis

### Theme Analysis (New in v0.6.3)
- `get-theme-scores` - Scores for material, mobility, space, positional, kingSafety, tactical, light/dark square control at a FEN. Supports `is960`.
- `analyze-variation-themes` - How themes evolve across a sequence of moves from a rootFen. Supports `is960`.
- `get-theme-progression` - Track a single theme (e.g., kingSafety) over a variation. Supports `is960`.
- `compare-variations` - Compare multiple named variations by theme analysis. Supports `is960`.
- `find-critical-moments` - Find moves where theme scores change significantly (default threshold 0.5). Supports `is960`.
- `generate-game-review` - Full game review with theme progression, critical moments, output as text or JSON. Supports `is960`.

Theme enum values: `material | mobility | space | positional | kingSafety | tactical | lightsqaureControl | darksqaureControl`

### Tactical Analysis
- `get-tactical-position-summary` - Hanging pieces, forks, pins, semi-protected pieces for a FEN (**new in v0.6.3**)

### Opening Research
- `fen-openingbook-lookup` - Fast lookup in 12k position database
- `get-lichess-master-games` - Grandmaster game statistics
- `get-chessboardmagic-tcec-stats` - Top computer engine statistics
- `get-chessboardmagic-corr-stats` - Correspondence chess statistics

### Game Analysis
- `fetch-lichess-game` - Get game PGN from Lichess (URL or ID)
- `fetch-lichess-games` - User's 20 most recent games
- `get-chessboardmagic-games` - User's Chessboard Magic games
- `get-chessboardmagic-game-details` - Detailed single game
- `parse-pgn-into-fens` - Convert PGN to FEN list
- `get-fen-map-lookup` - Map moves to FENs (before or after)
- `get-chessboardmagic-tcec-games` - TCEC games reaching a position
- `get-chessboardmagic-corr-games` - Correspondence games reaching a position

### Visualization (MCP v0.6.3 Tools)
- `render_chess_board` - Display single position from FEN
- `render_pgn_viewer` - Interactive game viewer with move navigation

### Move Validation
- `is-legal-move` - Check move legality (SAN or UCI). Supports `is960`.
- `get-boardstate-for-move` - Get resulting position after move. Supports `is960`.
- `get-boardstate-for-fen` - Describe current position. Supports `is960`.

### Learning & Training
- `fetch-chess-puzzle` - Random puzzle (filterable by theme + rating 1000-2500)
- `get-puzzle-themes` - Available puzzle themes list

### Repertoire
- `get-chessboardmagic-repertoires` - User's repertoire list
- `get-chessboardmagic-repertoire-details` - Detailed repertoire lines
- `fetch-lichess-studies` - User's Lichess studies
- `fetch-lichess-study-pgn` - Specific study in PGN

### User Info
- `get-lichess-username` - Current user's Lichess username

## Common Request Patterns

| User Request | Workflow |
|--------------|----------|
| "Analyze this position [FEN]" | Position Deep Dive (see references/workflow_patterns.md) |
| "Analyze my last game" | Get Lichess username → Fetch recent games → `generate-game-review` |
| "What's the best move?" | Quick best move + alternatives + explanation |
| "Help me study the Sicilian" | Opening study workflow with multiple databases |
| "I'm stuck in this endgame" | Endgame analysis + `get-tactical-position-summary` |
| Lichess URL provided | Parse game ID → `fetch-lichess-game` → `generate-game-review` |
| "Give me a tactics puzzle" | Puzzle training session with explanations |
| "Chess 960 / Fischer Random position" | All analysis with `is960: true` |
| "Compare these two variations" | `compare-variations` → theme breakdown |
| "When did the game turn?" | `find-critical-moments` on game variation |
| "How is my king safety?" | `get-theme-scores` → kingSafety reading |

## Advanced Features

For detailed guidance on:
- **Workflow patterns**: See [references/workflow_patterns.md](references/workflow_patterns.md)
- **Tool selection**: See [references/tool_selection.md](references/tool_selection.md)
- **API parameters**: See [references/api_reference.md](references/api_reference.md)
- **Chess knowledge**: See [references/chess_knowledgebase.md](references/chess_knowledgebase.md)

## Best Practices

### Performance Optimization
- Use `generate-game-review` for fast comprehensive game analysis (replaces manual parse → batch analyze)
- Use `find-critical-moments` to identify key positions before deep diving
- Use batch analysis for 10+ positions
- Start with opening book (fastest) before databases
- Use `get-chessdb-pv` for quick best line before committing to deep Stockfish search

### User Experience
- Always visualize positions using MCP rendering tools
- Explain evaluations in chess terms (not just numbers)
- Match analysis depth to user expertise
- For theme scores: positive = White better, negative = Black better, zero = equal

### Error Handling
- **Retry Logic**: If any tool call returns an error, automatically retry up to 2 times before informing the user
- On third failure: Clearly inform user that the tool encountered an error, specify which tool failed, and suggest alternatives when available
- Validate FEN format before tool calls
- Check move legality with `is-legal-move` before analysis
- Graceful fallbacks: book → ChessDB → Lichess → TCEC
- Handle missing user accounts gracefully
- For Chess 960: skip opening book and master game lookups (databases are standard chess only)

## Key Reminders

- **MCP version**: All tool calls use prefix `chessagine-mcp-v0.6.3:` (not v0.6.0)
- **Chess 960**: Pass `is960: true` on all supporting tools for Fischer Random positions
- **New theme tools**: Use `generate-game-review` for fast game analysis; `get-theme-scores` / `compare-variations` for strategic breakdown
- **Retry on errors**: Retry failed tool calls up to 2 times; inform user only after 3rd failure
- **Always visualize**: Use `render_chess_board` for positions, `render_pgn_viewer` for games
- **FEN validation**: Check format before API calls
- **Progressive detail**: Quick overview → selective deep dive
- **Multi-source**: Combine engines, databases, and principles
- **User-focused**: Match analysis to user's needs and skill level

## Changes in v0.6.3 vs v0.6.0

**New Tools:**
- ✅ `generate-game-review` - One-call comprehensive game review with theme progression
- ✅ `analyze-variation-themes` - Theme changes across a move sequence
- ✅ `compare-variations` - Side-by-side theme comparison of named variations
- ✅ `find-critical-moments` - Auto-detect turning points in a game
- ✅ `get-theme-scores` - Material/mobility/space/kingSafety/tactical scores per FEN
- ✅ `get-theme-progression` - Track a single theme across moves
- ✅ `get-tactical-position-summary` - Hanging pieces, forks, pins at a glance
- ✅ `get-chessdb-pv` - ChessDB principal variation retrieval

**Chess 960 Support:**
- ✅ `is960?: boolean` parameter added to: `get-boardstate-for-fen`, `get-boardstate-for-move`, `is-legal-move`, `get-theme-scores`, `get-theme-progression`, `analyze-variation-themes`, `compare-variations`, `find-critical-moments`, `generate-game-review`

**Removed Tools (not in v0.6.3):**
- ❌ `get-chess-knowledgebase` (removed; use references/chess_knowledgebase.md directly)
- ❌ `get-chessagine-stater-prompts` (removed)

**MCP Prefix:**
- All tool calls must use `chessagine-mcp-v0.6.3:` (updated from `chessagine-mcp-v0.6.0:`)