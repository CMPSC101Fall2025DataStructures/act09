# Tutorial 01: Creating Your First Pygame Game 🎮

**Time**: ~5 minutes  
**Goal**: Learn the basics of Pygame by creating a simple interactive game  
**Working Directory**: `src/tutorial_01_simple_game/`

## What You'll Build

A simple interactive game with a red circle that you can move around the screen using arrow keys. This tutorial introduces you to the fundamental concepts of game programming: the game loop, event handling, and rendering graphics.

## Step 1: Understanding the Starter File

Open the file `simple_game.py` in your code editor. You'll see a template with TODO comments. We'll fill in each section step by step.

## Step 2: Import Pygame

**What it does**: Imports the Pygame library so we can use its game development functions.

**Copy this code** to replace `# TODO: Add imports here`:

```python
import pygame
```

**Explanation**: The `pygame` module provides everything we need to create games: graphics, sound, input handling, and more. By importing it, we get access to all these tools.

---

## Step 3: Initialize Pygame

**What it does**: Starts up the Pygame engine and prepares it for use.

**Copy this code** to replace `# TODO: Add pygame initialization`:

```python
pygame.init()
```

**Explanation**: `pygame.init()` initializes all the Pygame modules (graphics, sound, fonts, etc.). This must be called before using any other Pygame functions.

---

## Step 4: Set Up the Game Window

**What it does**: Creates the game window where everything will be displayed.

**Copy this code** to replace `# TODO: Add screen setup`:

```python
screen = pygame.display.set_mode((600, 400))
pygame.display.set_caption("My First Pygame!")
```

**Explanation**:

- `pygame.display.set_mode((600, 400))` creates a window that is 600 pixels wide and 400 pixels tall
- `pygame.display.set_caption()` sets the title that appears at the top of the window
- `screen` is our drawing surface - everything we want to show will be drawn on this

---

## Step 5: Set Up Game Variables

**What it does**: Creates a clock for controlling frame rate and sets the initial position of our circle.

**Copy this code** to replace `# TODO: Add clock and position variables`:

```python
clock = pygame.time.Clock()

# Starting position of our circle (center of screen)
x, y = 300, 200

# Game running state
running = True
```

**Explanation**:

- `clock` helps us control how fast the game runs (frames per second)
- `x, y` stores the position of our circle on screen
- `running` is a flag that keeps the game loop going; when it's `False`, the game stops

---

## Step 6: Create the Game Loop

**What it does**: The heart of any game - a loop that continuously updates and draws everything.

**Copy this code** to replace `# TODO: Add game loop`:

```python
while running:
    # Handle events (user input)
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    # Get all pressed keys
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT]:
        x -= 3
    if keys[pygame.K_RIGHT]:
        x += 3
    if keys[pygame.K_UP]:
        y -= 3
    if keys[pygame.K_DOWN]:
        y += 3

    # Clear screen with dark gray
    screen.fill((30, 30, 30))
    
    # Draw our red circle
    pygame.draw.circle(screen, (255, 0, 0), (x, y), 20)

    # Update the display
    pygame.display.flip()
    
    # Control frame rate (60 FPS)
    clock.tick(60)
```

**Explanation**:

**Event Handling**:

- `pygame.event.get()` retrieves all events (mouse clicks, key presses, window close, etc.)
- We check if the user clicked the close button (`pygame.QUIT`) and stop the game if so

**Keyboard Input**:

- `pygame.key.get_pressed()` returns the state of all keyboard keys
- We check if arrow keys are pressed and move the circle accordingly
- Moving left/right changes `x`, moving up/down changes `y`
- Note: In Pygame, `y` increases downward (0 is at the top)

**Drawing**:

- `screen.fill((30, 30, 30))` clears the screen with a dark gray color (RGB values)
- `pygame.draw.circle()` draws a red circle at position `(x, y)` with radius 20
- Colors are specified as RGB tuples: `(Red, Green, Blue)` where each value is 0-255

**Display Update**:

- `pygame.display.flip()` actually shows everything we drew on screen
- Without this, you wouldn't see anything!

**Frame Rate**:

- `clock.tick(60)` limits the game to 60 frames per second
- This makes movement smooth and consistent across different computers

---

## Step 7: Clean Up

**What it does**: Properly closes Pygame when the game ends.

**Copy this code** to replace `# TODO: Add pygame quit`:

```python
pygame.quit()
```

**Explanation**: `pygame.quit()` cleanly shuts down all Pygame modules. It's good practice to call this when your game ends.

---

## Step 8: Save and Run Your Game!

### Save Your File

Press `Cmd+S` (macOS) or `Ctrl+S` to save `simple_game.py`.

### Step 9: Running the Game

```bash
# Make sure you're in the root (main) directory of your project
uv run src/tutorial_01_simple_game/simple_game.py 
```

You should see output indicating that Pygame is being installed. This only needs to be done once per tutorial directory.

### Play Your Game

You should see a window with a red circle. Use the arrow keys to move it around!

- **Left Arrow**: Move left
- **Right Arrow**: Move right
- **Up Arrow**: Move up
- **Down Arrow**: Move down
- **Close Window**: Exit

---

## Understanding the Game Loop Pattern

The game loop you just created follows a pattern used in almost every game:

1. **Handle Input**: Check what the player is doing
2. **Update Game State**: Change positions, check collisions, update scores, etc.
3. **Render**: Draw everything on screen
4. **Control Timing**: Maintain consistent frame rate

This pattern repeats 60 times per second, creating the illusion of smooth motion and interactivity!

## Experiment and Explore

Try making these modifications to understand the code better:

1. **Change the circle color**: Modify the RGB values in `pygame.draw.circle()`
   - Try `(0, 255, 0)` for green or `(0, 0, 255)` for blue

2. **Change the movement speed**: Modify the `3` in `x -= 3` to make it faster or slower

3. **Change the background color**: Modify the RGB values in `screen.fill()`

4. **Make the circle bigger**: Change the `20` in `pygame.draw.circle()` to a larger number

5. **Add another shape**: Try adding `pygame.draw.rect(screen, (0, 255, 0), (100, 100, 50, 50))` before the `flip()` call

---

## Key Takeaways

You've just learned the fundamentals of game programming:

- **Pygame initialization**: Setting up the game engine
- **The game loop**: The infinite loop that runs your game
- **Event handling**: Responding to user input
- **Rendering**: Drawing graphics on screen
- **Frame rate control**: Keeping your game running smoothly

These concepts apply to almost every game you'll ever create, from simple 2D games to complex 3D applications!

---

**Ready for the next challenge?** Head to `tutorials/tutorial_02_space_invaders.md` to build a more complex game with multiple files and game objects! 🚀
