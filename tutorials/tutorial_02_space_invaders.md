# Tutorial 02: Building Space Invaders 🚀

**Time**: ~25 minutes  
**Goal**: Build a simplified Space Invaders game with multiple modules  
**Working Directory**: `src/tutorial_02_space_invaders/`

## What You'll Build

A classic Space Invaders game where you control a spaceship, shoot at alien invaders, and try to survive! This tutorial introduces you to multi-file project structure, game objects, and more complex game mechanics.

**Features**:

- Player-controlled spaceship
- Alien enemies that move across the screen
- Shooting mechanics
- Collision detection
- Score tracking
- Organized code across multiple files

## Understanding the Project Structure

Unlike Tutorial 1, this game uses **multiple Python files** to organize code:

- `config.py` - All game settings and constants (colors, speeds, sizes)
- `game.py` - Main game logic and the game loop
- `main.py` - Entry point that starts the game

This is how real games are organized! Each file has a specific purpose, making the code easier to understand and modify.

---

## Part 1: Setting Up Configuration (config.py)

Open `config.py` in your editor. This file will store all our game settings in one place.

### What it does

Configuration files centralize all the "magic numbers" in your game. Instead of hardcoding values throughout your code, you define them once here. This makes it easy to adjust game difficulty, colors, and other settings.

### Copy this code

Replace `# TODO: Add configuration constants here` with:

```python
"""
Configuration constants for Space Invaders game.
"""

from typing import Tuple

# Screen settings
SCREEN_WIDTH: int = 600
SCREEN_HEIGHT: int = 400
FPS: int = 60

# Colors (R, G, B)
COLOR_BACKGROUND: Tuple[int, int, int] = (0, 0, 20)
COLOR_PLAYER: Tuple[int, int, int] = (0, 200, 255)
COLOR_ENEMY: Tuple[int, int, int] = (255, 50, 50)
COLOR_BULLET: Tuple[int, int, int] = (255, 255, 0)
COLOR_TEXT: Tuple[int, int, int] = (255, 255, 255)

# Player settings
PLAYER_START_X: int = 300
PLAYER_START_Y: int = 360
PLAYER_SPEED: int = 5
PLAYER_SIZE: int = 15

# Bullet settings
BULLET_SPEED: int = 7
BULLET_WIDTH: int = 5
BULLET_HEIGHT: int = 10

# Enemy settings
NUM_ENEMIES: int = 5
ENEMY_SPACING: int = 80
ENEMY_START_Y: int = 50
ENEMY_SPEED: int = 3
ENEMY_DESCENT: int = 20
ENEMY_SIZE: int = 12

# Scoring
POINTS_PER_ENEMY: int = 10

# Font settings
FONT_SIZE: int = 28
```

### Explanation

**Type Hints**: Notice `int` and `Tuple[int, int, int]` - these tell Python what type each variable should be

**Naming Convention**: All capitals (like `SCREEN_WIDTH`) indicate these are constants that shouldn't change during the game

**Organization**:

- Screen settings control window size and frame rate
- Colors are RGB tuples `(Red, Green, Blue)` with values 0-255
- Player, bullet, and enemy settings control gameplay
- Organizing by category makes settings easy to find

**Benefits**:

- Want to make the game harder? Change `ENEMY_SPEED` in one place!
- Want different colors? Just update the `COLOR_*` constants
- No need to search through code to find where values are used

**Save the file** (`Cmd+S` or `Ctrl+S`).

---

## Part 2: Creating the Game Class (game.py)

Open `game.py`. This is the heart of our game - it contains the main game loop and all the game logic.

### Copy this code

Replace the entire file content with:

```python
"""
Game class for Space Invaders.
Manages the game loop and game state.
"""

import pygame
import sys
import config


class Game:
    """
    Main game class that manages the game loop and state.
    """

    def __init__(self) -> None:
        """Initialize the game and all components."""
        pygame.init()
        
        # Display setup
        self.screen: pygame.Surface = pygame.display.set_mode(
            (config.SCREEN_WIDTH, config.SCREEN_HEIGHT)
        )
        pygame.display.set_caption("Space Invaders")
        self.clock: pygame.time.Clock = pygame.time.Clock()

        # Font setup
        self.font: pygame.font.Font = pygame.font.Font(None, config.FONT_SIZE)

        # Player state
        self.player_x: int = config.PLAYER_START_X
        self.player_y: int = config.PLAYER_START_Y

        # Bullet state
        self.bullet_x: int = 0
        self.bullet_y: int = config.PLAYER_START_Y
        self.bullet_fired: bool = False

        # Enemy state - create a list of enemy positions
        self.enemies: list = []
        for i in range(config.NUM_ENEMIES):
            enemy_x = 100 + i * config.ENEMY_SPACING
            enemy_y = config.ENEMY_START_Y
            self.enemies.append([enemy_x, enemy_y, True])  # [x, y, alive]
        
        self.enemy_direction: int = config.ENEMY_SPEED

        # Game state
        self.score: int = 0
        self.running: bool = True

    def handle_events(self) -> None:
        """Process all pygame events."""
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                self.running = False

            if event.type == pygame.KEYDOWN:
                # Shoot bullet
                if event.key == pygame.K_SPACE and not self.bullet_fired:
                    self.bullet_fired = True
                    self.bullet_x = self.player_x

                # Quit game
                if event.key == pygame.K_q:
                    self.running = False

    def handle_input(self) -> None:
        """Process continuous keyboard input for player movement."""
        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT] or keys[pygame.K_a]:
            self.player_x -= config.PLAYER_SPEED
            # Keep player on screen
            if self.player_x < config.PLAYER_SIZE:
                self.player_x = config.PLAYER_SIZE
                
        if keys[pygame.K_RIGHT] or keys[pygame.K_d]:
            self.player_x += config.PLAYER_SPEED
            # Keep player on screen
            if self.player_x > config.SCREEN_WIDTH - config.PLAYER_SIZE:
                self.player_x = config.SCREEN_WIDTH - config.PLAYER_SIZE

    def update(self) -> None:
        """Update all game entities and check for collisions."""
        # Update bullet position
        if self.bullet_fired:
            self.bullet_y -= config.BULLET_SPEED
            if self.bullet_y < 0:
                self.bullet_fired = False
                self.bullet_y = config.PLAYER_START_Y

        # Update enemy positions
        move_down = False
        
        # Check if any enemy hit the edge
        for enemy in self.enemies:
            if not enemy[2]:  # Skip dead enemies
                continue
            if enemy[0] <= 10 or enemy[0] >= config.SCREEN_WIDTH - 10:
                move_down = True
                break

        # Move enemies down and reverse direction if needed
        if move_down:
            self.enemy_direction *= -1
            for enemy in self.enemies:
                enemy[1] += config.ENEMY_DESCENT

        # Move enemies horizontally
        for enemy in self.enemies:
            if enemy[2]:  # Only move living enemies
                enemy[0] += self.enemy_direction

        # Check for collisions between bullet and enemies
        if self.bullet_fired:
            for enemy in self.enemies:
                if not enemy[2]:  # Skip dead enemies
                    continue
                
                # Simple collision detection (distance check)
                distance = ((self.bullet_x - enemy[0])**2 + 
                          (self.bullet_y - enemy[1])**2)**0.5
                
                if distance < 20:  # If bullet is close enough to enemy
                    enemy[2] = False  # Mark enemy as dead
                    self.bullet_fired = False
                    self.bullet_y = config.PLAYER_START_Y
                    self.score += config.POINTS_PER_ENEMY
                    break

        # Check if all enemies are destroyed - respawn if so
        if all(not enemy[2] for enemy in self.enemies):
            self.respawn_enemies()

    def respawn_enemies(self) -> None:
        """Respawn all enemies when they're all destroyed."""
        for i in range(config.NUM_ENEMIES):
            self.enemies[i] = [
                100 + i * config.ENEMY_SPACING,
                config.ENEMY_START_Y,
                True
            ]
        self.enemy_direction = config.ENEMY_SPEED

    def draw(self) -> None:
        """Render all game elements to the screen."""
        # Clear screen
        self.screen.fill(config.COLOR_BACKGROUND)

        # Draw score
        score_text = self.font.render(f"Score: {self.score}", True, config.COLOR_TEXT)
        self.screen.blit(score_text, (10, 10))

        # Draw player as a triangle (spaceship)
        player_points = [
            (self.player_x, self.player_y - config.PLAYER_SIZE),  # Top point
            (self.player_x - config.PLAYER_SIZE, self.player_y + config.PLAYER_SIZE),  # Bottom left
            (self.player_x + config.PLAYER_SIZE, self.player_y + config.PLAYER_SIZE)   # Bottom right
        ]
        pygame.draw.polygon(self.screen, config.COLOR_PLAYER, player_points)

        # Draw bullet
        if self.bullet_fired:
            pygame.draw.rect(
                self.screen,
                config.COLOR_BULLET,
                (self.bullet_x - config.BULLET_WIDTH // 2, 
                 self.bullet_y, 
                 config.BULLET_WIDTH, 
                 config.BULLET_HEIGHT)
            )

        # Draw enemies as squares
        for enemy in self.enemies:
            if enemy[2]:  # Only draw living enemies
                pygame.draw.rect(
                    self.screen,
                    config.COLOR_ENEMY,
                    (enemy[0] - config.ENEMY_SIZE, 
                     enemy[1] - config.ENEMY_SIZE,
                     config.ENEMY_SIZE * 2, 
                     config.ENEMY_SIZE * 2)
                )

        # Update display
        pygame.display.flip()

    def run(self) -> None:
        """Main game loop."""
        while self.running:
            self.handle_events()
            self.handle_input()
            self.update()
            self.draw()
            self.clock.tick(config.FPS)

        pygame.quit()
        sys.exit()
```

### Explanation

This is a lot of code! Let's break it down by sections:

**The `__init__` Method** (Initialization):

- Sets up Pygame, creates the window, and initializes the clock
- Creates variables to track the player position
- Creates variables to track bullet state
- Creates a list of enemies with their positions and alive status
- Each enemy is stored as `[x_position, y_position, is_alive]`
- Initializes game state like score and running flag

**The `handle_events` Method**:

- Checks for the window close button
- Checks for spacebar press to fire bullets
- Checks for 'Q' key to quit the game
- This handles **one-time events** (like key presses)

**The `handle_input` Method**:

- Checks if arrow keys or A/D are held down
- Moves the player left or right
- Keeps the player from going off-screen
- This handles **continuous input** (holding keys down)

**The `update` Method**:

- Updates bullet position and removes it if it goes off-screen
- Moves enemies across the screen
- When enemies hit the edge, they move down and reverse direction
- Checks for collisions between bullets and enemies using distance formula
- Awards points when enemies are hit
- Respawns enemies when all are destroyed

**The `respawn_enemies` Method**:

- Creates a new wave of enemies
- Resets their positions to the top
- Resets their movement direction

**The `draw` Method**:

- Clears the screen
- Draws the score text
- Draws the player as a triangle (using `pygame.draw.polygon`)
- Draws the bullet as a rectangle
- Draws living enemies as red squares
- Updates the display with `flip()`

**The `run` Method**:

- The main game loop!
- Calls all the other methods in order: events → input → update → draw
- Controls frame rate with `clock.tick()`
- Quits when `running` becomes `False`

**Key Concepts**:

- **Object-Oriented Design**: Everything is organized in a `Game` class
- **Module Import**: We import `config` to use our constants
- **Separation of Concerns**: Each method has one clear job
- **Game Loop Pattern**: Same as Tutorial 1, but more sophisticated

**Save the file**.

---

## Part 3: Creating the Entry Point (main.py)

Open `main.py`. This is the file you'll run to start the game.

### What it does

The main file is kept simple - it just imports the Game class and runs it. This is a common pattern in larger programs.

### Copy this code

Replace the entire file content with:

```python
"""
Space Invaders - Tutorial 02
Main entry point for the game.
"""

from game import Game


def main() -> None:
    """
    Entry point for the Space Invaders game.
    Initializes and runs the game loop.
    """
    game = Game()
    game.run()


if __name__ == "__main__":
    main()
```

### Explanation

**Import**: We import the `Game` class from our `game.py` file

**The `main` Function**:

- Creates a new `Game` object
- Calls its `run()` method to start the game loop

**The `if __name__ == "__main__"` Block**:

- This is a Python idiom that means "only run this code if this file is executed directly"
- It won't run if this file is imported by another file
- This is the standard way to write entry points in Python

**Save the file**.

---

## Step 4: Run Your Space Invaders Game

You've completed all three files! Now let's run the game.

### Make sure you're in the correct directory

```bash
pwd #MacOS and Lunix command
# type "path" from the Windows terminal to find the current directory (I think!!)
# Should show: ..PATH../src/tutorial_02_space_invaders
```

## Step 5: Running the Game

```bash
# Make sure you're in the root (main) directory of your project
uv run src/tutorial_02_space_invaders/main.py 
```

You should see output indicating that Pygame is being installed. This only needs to be done once per tutorial directory.

### Play the game

- **Left Arrow or A**: Move spaceship left
- **Right Arrow or D**: Move spaceship right
- **Spacebar**: Fire bullet
- **Q**: Quit game

Try to shoot all the enemies! When you destroy them all, they'll respawn for another wave.

---

## Understanding Multi-File Organization

You just created a game with three separate files:

**config.py**:

- Contains all constants and settings
- Easy to modify without touching game logic
- Imported by other files using `import config`

**game.py**:

- Contains the `Game` class with all game logic
- Organized into methods, each with a specific purpose
- Imports `config` to use the constants

**main.py**:

- Entry point for the program
- Imports `Game` from `game.py`
- Keeps the startup code simple and clean

**Why Organize This Way?**

- **Clarity**: Each file has a clear purpose
- **Maintainability**: Easy to find and modify specific parts
- **Scalability**: Easy to add new files for new features
- **Reusability**: Other programs could import and use your `Game` class
- **Professional Practice**: This is how real game projects are structured

---

## How the Modules Work Together

Let's trace what happens when you run the game:

1. Execute program
2. **main.py**: Imports `Game` from `game.py`
3. **game.py**: Imports `config` to get constants
4. **main.py**: Creates a `Game()` object
5. **game.py**: `__init__` runs, setting up the game using values from `config`
6. **main.py**: Calls `game.run()`
7. **game.py**: The game loop starts, calling methods in sequence
8. Game runs until you quit!

This is **module composition** - multiple files working together to create a complete program.

---

## Understanding the Game Mechanics

### Collision Detection

The game uses a simple distance formula to detect collisions:

```python
distance = ((bullet_x - enemy_x)**2 + (bullet_y - enemy_y)**2)**0.5
```

This is the Pythagorean theorem! If the distance is small enough, we count it as a hit.

### Enemy Movement Pattern

Enemies move in the classic Space Invaders pattern:

1. Move horizontally across the screen
2. When one hits the edge, ALL enemies move down and reverse direction
3. This creates the advancing wave effect

### Game State Management

The game tracks various states:

- Player position
- Bullet position and whether it's fired
- Enemy positions and alive status
- Score
- Running state

All these variables work together to create the complete game experience!

---

## Experiment and Extend

Try modifying the game to understand it better:

1. **Make it harder**: In `config.py`, increase `ENEMY_SPEED` or `NUM_ENEMIES`

2. **Change colors**: Modify the `COLOR_*` constants in `config.py`

3. **Adjust scoring**: Change `POINTS_PER_ENEMY` in `config.py`

4. **Make the player faster**: Increase `PLAYER_SPEED` in `config.py`

5. **Add more enemies**: Increase `NUM_ENEMIES` (try 8 or 10!)

6. **Change bullet speed**: Modify `BULLET_SPEED` in `config.py`

Notice how you can change all these settings without touching the game logic in `game.py`? That's the power of good organization!

---

## Key Takeaways

Congratulations! You've learned:

- **Multi-file project structure**: Organizing code across modules
- **Configuration management**: Centralizing settings in one place
- **Object-oriented programming**: Using classes to organize game logic
- **Module imports**: How Python files work together
- **Game mechanics**: Collision detection, enemy AI, scoring
- **Professional practices**: How real games are structured

These skills apply to any large programming project, not just games!

---

## Completion

You've now completed both tutorials and created two working games! Make sure to:

1. **Save all your files**
2. **Test both games** to ensure they work
3. **Complete the reflection** in `writing/reflection.md`
4. **Commit and push your work** to GitHub

Great job on completing Activity 09! You're now ready to start creating your own games with Pygame! 🎮✨
