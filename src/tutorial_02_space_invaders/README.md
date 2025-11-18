# Tutorial 02: Space Invaders

This directory contains a simplified Space Invaders game from Tutorial 2.

## Running the Game

```bash
# Make sure you're in the root (main) directory of your project
uv run src/tutorial_02_space_invaders/main.py
```

## Controls

- **Arrow Keys / A and D**: Move ship left and right
- **Spacebar**: Fire bullet
- **Q**: Quit game
- **Close Window**: Exit the game

## What This Game Demonstrates

- Multi-file project structure
- Game object organization (main, game, config)
- Configuration management with a separate module
- Player movement and shooting
- Enemy spawning and management
- Collision detection
- Score tracking
- Module imports and organization

## Project Files

- `main.py` - Entry point that starts the game
- `game.py` - Main game loop and logic
- `config.py` - All game configuration constants
