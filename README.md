
# ChessAgine Skill

The ChessAgine skill enables Claude to be a chess buddy and analyst by leveraging 40+ specialized tools from the ChessAgine MCP server.

## Installation

1. Download `chessagine.skill` from this repository
2. Open Claude Desktop
3. Go to **Settings** → **Capabilities** → **Skills**
4. Click **Add Skill** and select the `chessagine.skill` file
5. Restart Claude Desktop

## Usage

Simply ask Claude about chess:

- "Analyze this position: rnbqkbnr/pppppppp/8/8/4P3/8/PPPP1PPP/RNBQKBNR b KQkq - 0 1"
- "Review my last Lichess game"
- "Help me study the Sicilian Defense"
- "Give me a tactics puzzle on pins"
- "What's the best move here?"

## Requirements

- Claude Desktop (latest version)
- [ChessAgine MCP Server](https://github.com/jalpp/chessagine-mcp) installed and running

## Features

- **Multi-engine analysis** with Stockfish, Leela, and Maia2
- **Game review** from Lichess and other sources
- **Opening preparation** with master game statistics
- **Interactive puzzle training** across 100+ themes
- **Position visualization** with 2D/3D boards

## Documentation

See [ChessAgine_Skill_Guide.md](./ChessAgine_Skill_Guide.md) for complete reference.

## Authors

@jalpp
