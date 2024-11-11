# Monkey Vroom Vroom Game Architecture

## 1. Core Game Structure

### **GameManager**
- **Purpose**: Oversees the entire game. This is the main controller that starts the game, switches scenes (like start, gameplay, and game over), and manages the player’s score and difficulty level.
- **Variables**:
  - `app`: Creates the main PixiJS application, which handles rendering and user interactions.
  - `currentScene`: Stores the currently active scene (like Start, Game, or Game Over).
  - `score`: Tracks the player’s score.
  - `difficulty`: Keeps track of the current difficulty level, which increases as the player progresses.
- **Methods**:
  - `initialize()`: Sets up the game by creating the PixiJS app, setting up the main game container, and starting with the `StartScene`.
  - `changeScene(newScene)`: Switches the active scene by removing the old one and setting the new one.
  - `update(delta)`: Runs every frame, calling the update method of the active scene.

---

## 2. Scenes

Each scene (start, game, and game over) is managed independently, allowing smooth transitions between different game states.

### **BaseScene (Generic Class)**
- **Purpose**: Acts as a base template for specific scenes (e.g., start, game, game over). Provides shared functionality.
- **Variables**:
  - `container`: Holds all visual elements of the scene (sprites, text, etc.).
- **Methods**:
  - `update(delta)`: Placeholder that is customized in each specific scene to handle updates.
  - `destroy()`: Cleans up all elements when the scene changes to free up memory.

### **StartScene**
- **Purpose**: Displays the start screen where players can begin the game.
- **Methods**:
  - `showTitle()`: Displays the game title and instructions.
  - `startGame()`: Begins the game by switching to `GameScene` when the player presses "Start".

### **GameScene**
- **Purpose**: The main gameplay scene where the monkey swings from vine to vine, encountering obstacles and collecting points.
- **Variables**:
  - `monkey`: Represents the monkey character.
  - `vines`: A list of vine objects for the monkey to swing on.
  - `obstacles`: A list of obstacles, such as birds.
  - `scoreText`: Displays the current score.
- **Methods**:
  - `spawnVine()`: Creates a new vine for the monkey to jump to.
  - `spawnObstacle()`: Adds a new obstacle to challenge the player.
  - `update(delta)`: Updates the position of all game elements, checks for collisions, and applies game logic (like difficulty scaling).

### **GameOverScene**
- **Purpose**: Shows the final score and an option to restart the game.
- **Methods**:
  - `showFinalScore()`: Displays the player’s final score.
  - `restartGame()`: Restarts the game by switching back to `StartScene`.

---

## 3. Game Objects

### **Monkey**
- **Purpose**: Represents the player’s character, a monkey that swings on vines.
- **Variables**:
  - `sprite`: The PixiJS sprite for the monkey image.
  - `velocity`: Controls the monkey’s speed and jump direction.
  - `isSwinging`: Tracks if the monkey is currently swinging on a vine.
- **Methods**:
  - `swing()`: Animates the monkey swinging on a vine.
  - `jump()`: Launches the monkey into the air, using `velocity` to control its direction.
  - `applyGravity(delta)`: Gradually pulls the monkey down if it’s in the air.

### **Vine**
- **Purpose**: A vine for the monkey to grab onto and swing.
- **Variables**:
  - `sprite`: The visual representation of the vine.
  - `swingSpeed`: Determines how fast the vine swings back and forth.
- **Methods**:
  - `updateSwing(delta)`: Moves the vine in a swinging motion.
  - `checkGrab(monkey)`: Detects if the monkey has successfully grabbed the vine.

### **Obstacle**
- **Purpose**: Represents obstacles, such as birds, that the monkey must avoid.
- **Variables**:
  - `sprite`: The visual representation of the obstacle.
  - `speed`: Controls how fast the obstacle moves across the screen.
- **Methods**:
  - `updatePosition(delta)`: Moves the obstacle horizontally across the screen.
  - `checkCollision(monkey)`: Checks if the obstacle collides with the monkey.

### **Collectible**
- **Purpose**: Items that provide bonus points when collected.
- **Variables**:
  - `sprite`: The visual representation of the collectible item.
  - `points`: The value in points for collecting this item.
- **Methods**:
  - `updatePosition(delta)`: Keeps the collectible in sync with the scene’s movement.
  - `checkCollection(monkey)`: Detects if the monkey collects the item and increases the score.

---

## 4. Systems and Utilities

### **Physics System**
- **Purpose**: Manages physics for swinging, jumping, and gravity.
- **Methods**:
  - `calculateJump(monkey, vine)`: Calculates the path for the monkey’s jump.
  - `applyGravity(monkey, delta)`: Pulls the monkey down while it’s mid-air.

### **Collision System**
- **Purpose**: Checks for interactions between the monkey and other game objects.
- **Methods**:
  - `checkCollision(sprite1, sprite2)`: Checks if two sprites touch each other.
  - `resolveCollision(monkey, obstacle)`: Ends the game if the monkey hits an obstacle.

### **Difficulty System**
- **Purpose**: Increases the game’s difficulty over time.
- **Variables**:
  - `difficultyLevel`: Tracks the current level of difficulty.
- **Methods**:
  - `increaseDifficulty()`: Adjusts factors like vine speed, gap distances, and obstacle frequency.
  - `checkProgress(score)`: Periodically checks the score and increases `difficultyLevel` accordingly.

---

## 5. UI Elements

### **ScoreDisplay**
- **Purpose**: Displays the player’s score during gameplay.
- **Variables**:
  - `text`: The score text shown on the screen.
- **Methods**:
  - `updateScore(newScore)`: Updates the text to show the current score.

### **Leaderboard**
- **Purpose**: Shows the top scores.
- **Methods**:
  - `fetchScores()`: Retrieves top scores from the server.
  - `render()`: Displays the leaderboard on the screen.

---

## 6. Backend Communication

### **ScoreManager**
- **Purpose**: Manages saving and retrieving player scores.
- **Methods**:
  - `saveScore(score)`: Sends the player’s score to a backend server for saving.
  - `getTopScores()`: Retrieves leaderboard scores for display.

---

## 7. Data Flow

1. **Game Start**: `GameManager` initializes `StartScene`, which waits for the player to start the game.
2. **Gameplay**:
   - `GameScene` runs the main gameplay loop.
   - `Monkey`, `Vine`, and `Obstacle` objects interact, with the `Monkey` swinging and jumping.
   - `Physics System` manages gravity and swing motion.
   - `Collision System` checks if the monkey hits obstacles or collects items.
3. **Difficulty Scaling**: The `Difficulty System` gradually increases game speed and challenge as the score increases.
4. **Game Over**: When the monkey hits an obstacle, `GameOverScene` displays the final score and allows the player to retry.
5. **Score Submission**: `ScoreManager` saves the score if it’s high enough for the leaderboard.