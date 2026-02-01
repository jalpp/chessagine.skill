---
name: chessagine
description: Comprehensive chess analysis using the ChessAgine MCP. Use this skill when users request chess-related tasks including analyzing positions or games, studying openings, finding best moves, comparing with master/computer games, solving puzzles, building repertoires, reviewing user games from Lichess or Chessboard Magic, or any chess related request. Triggers on chess positions (FEN), PGN games, Lichess URLs, opening names, or general chess requests.
---

# ChessAgine Skill

ChessAgine provides comprehensive chess analysis through 40+ specialized tools integrating Stockfish, Leela Chess Zero, Maia2, Lichess, Chessboard Magic, and multiple chess databases.

## Core Workflow Principles

### Context Priority
When handling chess requests, always:
1. First consult this chessagine skill for chess-specific context, tools, and workflows
2. Then reference user's project memory related to chess for personalized preferences or context

### Always Visualize
Every position analysis must include visualization using `generate-chess-board-view-artificat-html` (2D default) or `generate-dynamic-gameview-html` (for games with move navigation).

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
1. `get-boardstate-for-fen` (understand position)
2. `generate-chess-board-view-artificat-html` (visualize)
3. `get-stockfish-multipv-analysis` (3 lines, depth 22)
4. `fen-openingbook-lookup` (if opening)
5. `get-lichess-master-games` (master practice)
6. Apply chess principles from `get-chess-knowledgebase`

### Analyze Game
1. `fetch-lichess-game` (get PGN)
2. `parse-pgn-into-fens` (extract positions)
3. Select 3-5 critical moments
4. `get-stockfish-analysis` on critical positions (depth 20)
5. `generate-dynamic-gameview-html` (navigable game view)

### Study Opening
1. `fen-openingbook-lookup` (identify opening, common moves)
2. `get-lichess-master-games` (master statistics)
3. `get-chessboardmagic-tcec-stats` (computer evaluation)
4. `get-stockfish-multipv-analysis` (5 lines, depth 25)
5. Fetch sample games from relevant database

### Find Best Move
1. `get-stockfish-best-move` (tactical solution)
2. `get-stockfish-multipv-analysis` (alternatives, 3 lines)
3. Cross-reference with `get-chessdb-analysis`
4. Explain using `get-chess-knowledgebase` principles

### Puzzle Training
1. `get-puzzle-themes` (show available themes)
2. `fetch-chess-puzzle` (with theme/rating filters)
3. `generate-chess-board-view-artificat-html` (present)
4. After attempt: `get-stockfish-best-move` (verify)
5. Explain with chess principles

## Tool Categories

### Position Analysis
- `get-stockfish-analysis` - Tactical evaluation (depth 18-25)
- `get-stockfish-multipv-analysis` - Show alternatives (3-5 lines)
- `get-stockfish-best-move` - Quick best move without full analysis
- `get-leela-analysis` - Strategic/positional evaluation
- `get-elite-leela-analysis` - Master-level positional understanding
- `get-maia2-analysis` - Human-like moves at specific rating (1100-1900)
- `get-chessdb-analysis` - Large database position lookup

### Opening Research
- `fen-openingbook-lookup` - Fast lookup in 12k position database
- `get-lichess-master-games` - Grandmaster game statistics
- `get-chessboardmagic-tcec-stats` - Top computer engine statistics
- `get-chessboardmagic-corr-stats` - Correspondence chess statistics

### Game Analysis
- `fetch-lichess-game` - Get game PGN from Lichess
- `fetch-lichess-games` - User's 20 most recent games
- `get-chessboardmagic-games` - User's Chessboard Magic games
- `get-chessboardmagic-game-details` - Detailed single game
- `parse-pgn-into-fens` - Convert PGN to FEN list
- `get-fen-map-lookup` - Map moves to FENs
- `get-stockfish-batch-analysis` - Analyze multiple positions

### Visualization
- `generate-chess-board-view-artificat-html` - Single position (2D/3D)
- `generate-dynamic-gameview-html` - Full game with navigation

### Move Validation
- `is-legal-move` - Check move legality (SAN or UCI)
- `get-boardstate-for-move` - Get resulting position
- `get-boardstate-for-fen` - Describe current position

### Learning & Training
- `fetch-chess-puzzle` - Random puzzle (filterable)
- `get-puzzle-themes` - Available puzzle themes
- `get-chess-knowledgebase` - Silman Imbalances, Fine's principles, endgame rules
- `get-chessagine-stater-prompts` - Example analysis prompts

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
| "Analyze my last game" | Get Lichess username → Fetch recent games → Analyze selected game |
| "What's the best move?" | Quick best move + alternatives + explanation |
| "Help me study the Sicilian" | Opening study workflow with multiple databases |
| "I'm stuck in this endgame" | Endgame analysis + knowledgebase principles |
| Lichess URL provided | Parse game ID → Fetch game → Full analysis |
| "Give me a tactics puzzle" | Puzzle training session with explanations |

## Advanced Features

For detailed guidance on:
- **Workflow patterns**: See [references/workflow_patterns.md](references/workflow_patterns.md)
- **Tool selection**: See [references/tool_selection.md](references/tool_selection.md)
- **API parameters**: See [references/api_reference.md](references/api_reference.md)
- **Chess knowledge**: See [references/chess_knowledgebase.md](references/chess_knowledgebase.md)

## Best Practices

### Performance Optimization
- Use batch analysis for 10+ positions
- Analyze fewer positions deeper vs many shallowly
- Start with opening book (fastest) before databases

### User Experience
- Always visualize positions
- Explain evaluations in chess terms (not just numbers)
- Reference chess principles from knowledgebase
- Provide actionable advice
- Match analysis depth to user expertise

### Error Handling
- **Retry Logic**: If any tool call returns an error, automatically retry up to 2 times before informing the user
- On third failure: Clearly inform user that the tool encountered an error, specify which tool failed, and suggest alternatives when available
- Validate FEN format before tool calls
- Check move legality with `is-legal-move` before analysis
- Graceful fallbacks: book → ChessDB → Lichess → TCEC
- Handle missing user accounts gracefully

## Key Reminders

- **Context priority**: Always consult chessagine skill first, then user's chess-related project memory
- **Retry on errors**: Retry failed tool calls up to 2 times; inform user only after 3rd failure
- **Always visualize**: No position analysis without visualization
- **FEN validation**: Check format before API calls
- **Progressive detail**: Quick overview → selective deep dive
- **Multi-source**: Combine engines, databases, and principles
- **User-focused**: Match analysis to user's needs and skill level