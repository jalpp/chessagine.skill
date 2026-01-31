# ChessAgine Skill - Quick Reference

## What This Skill Does

The ChessAgine skill enables Claude to be an expert chess buddy and analyst by leveraging 40+ specialized tools from the ChessAgine MCP server. This skill provides:

- **Comprehensive position analysis** using multiple engines (Stockfish, Leela, Maia2)
- **Game analysis** from Lichess and Chessboard Magic
- **Opening preparation** with master games, TCEC, and correspondence databases
- **Interactive puzzle training** with 100+ themes
- **Repertoire building** and maintenance
- **Multi-source validation** combining engines, databases, and chess principles

## Key Features

### Multi-Engine Analysis
- **Stockfish 17.1**: Tactical analysis, best moves (depth 12-30)
- **Leela Chess Zero**: Positional/strategic evaluation
- **Elite Leela**: Master-level understanding (trained on 2500-3000 rated games)
- **Maia2**: Human-like analysis at specific ratings (1100-1900)

### Database Integration
- **12k Opening Book**: Fast lookup with ECO codes and opening names
- **Lichess Master Database**: Grandmaster game statistics
- **TCEC Database**: Top computer engine games
- **Correspondence Database**: Deep human analysis
- **ChessDB**: Large general position database

### Visualization
- 2D/3D board rendering for positions
- Dynamic game viewer with move navigation
- Interactive puzzle presentation

## File Structure

```
chessagine/
├── SKILL.md                              # Main skill instructions
└── references/
    ├── workflow_patterns.md              # Detailed analysis workflows
    ├── tool_selection.md                 # Decision trees and tool selection guide
    └── api_reference.md                  # Parameter constraints and formats
```

## Example Use Cases

### 1. Position Analysis
User: "Analyze this position: rnbqkb1r/pppp1ppp/5n2/4p3/4P3/5N2/PPPP1PPP/RNBQKB1R w KQkq - 4 3"

Claude will:
- Visualize the position (2D board)
- Identify the opening (Petrov Defense)
- Analyze with Stockfish (multiple candidate moves)
- Check master game statistics
- Apply chess principles
- Provide clear recommendations

### 2. Game Review
User: "Analyze my last Lichess game"

Claude will:
- Fetch the user's recent games
- Parse the selected game
- Identify critical moments
- Deep analyze mistakes
- Check opening theory
- Present an interactive game viewer

### 3. Opening Study
User: "Help me study the Sicilian Dragon"

Claude will:
- Look up Dragon positions in opening book
- Show master statistics and win rates
- Check computer evaluations (TCEC)
- Analyze key positions with multiple engines
- Fetch representative master games
- Compare with user's repertoire (if available)

### 4. Puzzle Training
User: "Give me a tactics puzzle on pins"

Claude will:
- Fetch a puzzle with the "pin" theme
- Present the position visually
- Wait for user's solution
- Verify with engine
- Explain the solution using chess principles
- Offer another puzzle

## When the Skill Triggers

The skill automatically activates when users mention:
- Chess positions (FEN notation)
- Chess games (PGN format)
- Lichess URLs or game IDs
- Opening names (e.g., "Sicilian Defense", "King's Indian")
- Analysis requests ("analyze this", "what's the best move")
- Chess improvement ("help me study", "improve my endgames")
- Puzzle requests ("give me a tactics puzzle")
- Repertoire work ("build a repertoire", "check my opening lines")

## Core Design Principles

### 1. Always Visualize
Every analysis includes a visual board representation - no "blind" analysis

### 2. Multi-Source Validation
Important positions are cross-checked across multiple engines and databases

### 3. Progressive Detail
Start with quick overview, deepen only where critical - efficient use of computational resources

### 4. Right Tool for the Job
Tactical positions → Stockfish
Strategic positions → Leela
Teaching contexts → Maia2
Database lookups → Opening book → ChessDB → Master games

### 5. User-Focused
Analysis matches user's skill level and needs, explained in clear chess terms

## Reference Files Explained

### workflow_patterns.md
Contains complete workflows for:
- Game analysis (quick and thorough)
- Opening preparation
- Position evaluation
- Repertoire building
- Puzzle training
- User performance analysis
- Multi-engine comparison strategies

### tool_selection.md
Provides:
- Quick reference matrix of tools by task
- Decision trees for engine selection
- Database query strategies
- Visualization guidelines
- Tool combination patterns
- Common pitfalls to avoid

### api_reference.md
Documents:
- FEN format requirements
- Engine parameter constraints (depth, MultiPV, ratings)
- Move notation formats (SAN/UCI)
- Return value structures
- Error patterns and handling

## Advanced Features

### Batch Analysis
Efficiently analyze multiple positions from a game in a single operation

### Multi-Engine Comparison
For critical positions, compare Stockfish (tactics), Leela (strategy), and Maia2 (human perspective)

### Smart Fallbacks
If one database lacks information, automatically falls back to alternatives

### Rating-Aware Teaching
Uses Maia2 to predict and explain moves at the user's skill level

## Installation & Usage

1. **Load the skill**: Upload `chessagine.skill` to your skills directory
2. **Ensure ChessAgine MCP is connected**: The skill requires the [ChessAgine MCP server](https://github.com/jalpp/chessagine-mcp)
3. **Start analyzing**: Just ask chess-related questions naturally

## Example Queries That Work Great

- "Analyze this position: [FEN]"
- "What's the best move in this position?"
- "Analyze my last game on Lichess"
- "Help me study the Italian Game"
- "Give me a tactics puzzle on forks rated 1500-1700"
- "Compare my repertoire with master games"
- "Explain this endgame: [FEN]"
- "What's the evaluation of this position?"
- "Find my mistakes in this game: [PGN]"

## Technical Notes

- **FEN Validation**: All position tools require valid FEN format
- **Depth Recommendations**: 
  - Quick: 15-18
  - Standard: 20-22
  - Deep: 25-30
- **Maia2 Ratings**: Must use exact values [1100, 1200, 1300, 1400, 1500, 1600, 1700, 1800, 1900]
- **Batch Limit**: Optimal for 10-50 positions per batch

## Troubleshooting

**Issue**: "Invalid FEN format"
**Solution**: Ensure FEN includes all components: position, side to move, castling, en passant, clocks

**Issue**: "No opening found"
**Solution**: Position may be beyond opening phase, try ChessDB or skip opening analysis

**Issue**: "User not found"
**Solution**: User may not have a Lichess/Chessboard Magic account, focus on position analysis

## Contributing Improvements

To improve this skill:
1. Identify common usage patterns
2. Note inefficiencies or struggles
3. Update relevant reference files
4. Test changes
5. Re-package the skill

