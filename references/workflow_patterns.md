# ChessAgine Workflow Patterns

## Game Analysis Workflows

### Complete Game Analysis
1. Fetch game: `fetch-lichess-game` or `get-chessboardmagic-game-details`
2. Parse to FENs: `parse-pgn-into-fens`
3. Batch analyze critical positions: `get-stockfish-batch-analysis`
4. Cross-reference with opening book: `fen-openingbook-lookup`
5. Compare with master games at key positions: `get-lichess-master-games`
6. Generate visualization: `generate-dynamic-gameview-html`

### Opening Preparation
1. Start from opening FEN
2. Get master statistics: `get-lichess-master-games`
3. Get TCEC computer statistics: `get-chessboardmagic-tcec-stats`
4. Get correspondence statistics: `get-chessboardmagic-corr-stats`
5. Analyze with multiple engines:
   - Stockfish (tactical): `get-stockfish-multipv-analysis`
   - Elite Leela (positional): `get-elite-leela-analysis`
6. Look up opening name: `fen-openingbook-lookup`
7. Fetch relevant master games: `get-chessboardmagic-tcec-games`

### Position Evaluation
1. Get board state: `get-boardstate-for-fen`
2. Multi-engine analysis:
   - Stockfish for tactics: `get-stockfish-analysis` (depth 20-25)
   - Leela for strategic evaluation: `get-leela-analysis`
   - Maia2 for human-like perspective: `get-maia2-analysis`
3. Database lookup: `get-chessdb-analysis`
4. Check opening book: `fen-openingbook-lookup`
5. Apply chess principles: `get-chess-knowledgebase`
6. Visualize: `generate-chess-board-view-artificat-html`

### Repertoire Building
1. Fetch existing repertoires: `get-chessboardmagic-repertoires`
2. Get detailed lines: `get-chessboardmagic-repertoire-details`
3. For each critical position:
   - Analyze with Stockfish: `get-stockfish-multipv-analysis` (3-5 lines)
   - Check master games: `get-lichess-master-games`
   - Verify with correspondence stats: `get-chessboardmagic-corr-stats`
4. Test against user's games: `get-chessboardmagic-games`

### Puzzle Training
1. Get puzzle themes: `get-puzzle-themes`
2. Fetch targeted puzzle: `fetch-chess-puzzle` with theme/rating filters
3. Present position: `generate-chess-board-view-artificat-html`
4. After user attempts, analyze: `get-stockfish-best-move`
5. Explain with principles: `get-chess-knowledgebase`

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

### Comparative Analysis Pattern
For important positions, run multiple engines and compare:
1. Stockfish for best objective moves
2. Leela for strategic alternatives
3. Maia2 at user's rating for realistic human moves
4. Synthesize insights from all three perspectives

## Database Integration Patterns

### Comprehensive Position Research
1. Opening book: `fen-openingbook-lookup` (fast, 12k positions)
2. Master games: `get-lichess-master-games` (human grandmaster practice)
3. TCEC stats: `get-chessboardmagic-tcec-stats` (top computer play)
4. Correspondence: `get-chessboardmagic-corr-stats` (deep human analysis)
5. ChessDB: `get-chessdb-analysis` (large position database)

### Progressive Research Strategy
Start broad, then narrow:
1. First check opening book for quick orientation
2. If in book, get master stats for popularity
3. For critical lines, check TCEC for computer evaluation
4. For unclear positions, check correspondence for human judgment
5. Use ChessDB for less common positions

## Visualization Best Practices

### 2D vs 3D Board Views
- **2D view**: Default for analysis, clearer for most users
- **3D view**: Special cases (beginner engagement, presentation)

### Dynamic Game View
Use `generate-dynamic-gameview-html` when:
- Showing full game with move navigation
- Demonstrating opening variations
- Replaying critical sequences
- Teaching tactical patterns through multiple positions

### Static Position View
Use `generate-chess-board-view-artificat-html` when:
- Analyzing single position
- Presenting puzzle
- Showing final position
- Quick position verification

## FEN Handling Patterns

### FEN Validation
Always validate FEN format before API calls:
- Pattern: `^([rnbqkpRNBQKP1-8]+\/){7}[rnbqkpRNBQKP1-8]+ [bw] [KQkq-]+ ...`
- Full FEN includes: position, side to move, castling, en passant, halfmove, fullmove

### Move Verification
Before analyzing a move sequence:
1. Check legality: `is-legal-move`
2. Get resulting position: `get-boardstate-for-move`
3. Then analyze the new FEN

### PGN to FEN Conversion
For game analysis:
1. Parse entire game: `parse-pgn-into-fens`
2. Create FEN map: `get-fen-map-lookup` (before/after move)
3. Select critical positions for deep analysis
4. Avoid analyzing every position (too slow)

## Error Handling & Fallbacks

### Missing Data Patterns
- Opening not in book → Use ChessDB or Lichess master games
- No TCEC games → Fall back to correspondence or master games
- User not on Lichess → Skip personal game analysis
- Position too complex for quick analysis → Reduce depth or use faster engine

### Rate Limiting
- Batch operations when possible: `get-stockfish-batch-analysis`
- Cache results within conversation
- For deep analysis, analyze fewer but more critical positions

### User Experience
- Always show position visually when analyzing
- Explain engine evaluations in chess terms (advantages, threats)
- Reference chess principles from knowledgebase
- Provide actionable advice, not just numbers