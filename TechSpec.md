### 1. **GameManager**

Manages the main game loop, difficulty progression, and overall game state.

- **Variables**
  - `score: int` - Tracks the player's current score.
  - `isGameOver: boolean` - Indicates if the game is over.
  - `currentVine: Vine` - Tracks the current vine the monkey is on.
  - `monkey: Monkey` - Instance of the Monkey character.
  - `obstacles: list of Obstacle` - List of active obstacles like birds.
  - `difficultyLevel: int` - The current difficulty level, affecting vine speed and obstacle frequency.

- **Methods**
  - **`initializeGame`**
    - **Behavior**: Sets up the initial game state, including the first vine and monkey position.
  - **`updateScore`**
    - **Behavior**: Increases the score as the monkey moves farther in the game.
  - **`progressDifficulty`**
    - **Behavior**: Adjusts the game difficulty based on the player’s progress, increasing vine speed and obstacle frequency.
  - **`checkGameOver`**
    - **Behavior**: Ends the game if certain conditions (e.g., collision or missed vine) are met.
  - **`resetGame`**
    - **Behavior**: Resets the game to its initial state for a new session.

### 2. **Vine Class**

#### **Variables**
- `length: float` - Length of the vine.
- `swingSpeed: float` - Speed at which the vine swings back and forth.
- `swingRange: float` - Maximum horizontal distance the vine swings (left-right range).
- `position: Point` - The anchor point of the vine.
- `isSwinging: boolean` - Whether the vine is actively swinging or not.
- `grabRange: float` - The vertical range the monkey can grab the vine from top to bottom.

#### **Methods**
- **`startSwing()`**
  - **Behavior**: Starts the swinging motion. Initializes the swing state.
  - **Details**: Sets `isSwinging = true`.

- **`updateSwingPosition(deltaTime: float)`**
  - **Input**: `deltaTime` - The time passed since the last update.
  - **Behavior**: Updates the vine’s position based on its swing.
  - **Details**:
    - The horizontal position is based on the `swingRange` and the **current phase** of the swing.
    - The vine's **swing amplitude** changes based on how far the monkey is from the top or bottom of the vine.
    - `position.x = anchorPosition.x + swingRange * Math.sin(swingAngle)`
    - The swing is calculated from an angle, where `swingAngle` changes with time and varies based on the `swingSpeed`.

- **`calculateSwingPosition(monkeyPosition: Point)`**
  - **Input**: `monkeyPosition` - Position of the monkey on the vine.
  - **Behavior**: Calculates the vine's position based on the vertical height where the monkey grabs.
  - **Details**: 
    - If the monkey grabs lower on the vine (closer to the bottom), the swing is more pronounced (wider arc).
    - `swingRange = baseSwingRange + (grabHeight / length) * maxSwingRange`
    - The range of the swing depends on the vertical position (`grabHeight`) on the vine.

- **`landOnVine(monkeyPosition: Point)`**
  - **Input**: `monkeyPosition` - Position where the monkey grabs the vine.
  - **Behavior**: Adjusts the position of the monkey to grab the vine anywhere along its length.
  - **Details**:
    - The vine is now grabbable at any point, so `monkeyPosition.y` will directly correspond to where the monkey grabs.
    - `monkey.position.y = position.y + grabHeight`

- **`resetSwing()`**
  - **Behavior**: Resets the swinging motion to its initial state.
  - **Details**: 
    - Resets `isSwinging = false` and recalculates the vine’s position to its starting point.

### 3. **Monkey Class**

#### **Variables**
- `position: Point` - The current position of the monkey.
- `grabHeight: float` - The height at which the monkey grabs the vine.
- `isJumping: boolean` - Whether the monkey is in the air, jumping off the vine.
- `jumpTrajectory: Point` - The calculated jump trajectory that is based on the point the monkey grabbed.

#### **Methods**
- **`jumpOffVine(vine: Vine)`**
  - **Input**: `vine: Vine` - The vine the monkey is jumping off.
  - **Behavior**: Calculates the jump trajectory based on where the monkey grabs the vine. The farther the monkey grabs from the top, the stronger the horizontal swing force.
  - **Details**: 
    - The jump trajectory (`jumpTrajectory`) depends on the **height** at which the monkey grabs the vine:
      - `jumpTrajectory.x = swingSpeed * Math.sin(swingAngle)`
      - `jumpTrajectory.y = (grabHeight / length) * maxJumpHeight`
    - The `jumpTrajectory` provides the direction and distance the monkey will jump off the vine, affected by its grab position.

- **`landOnVine(vine: Vine, grabHeight: float)`**
  - **Input**: `vine: Vine`, `grabHeight: float` - The position and the height at which the monkey grabs the vine.
  - **Behavior**: Positions the monkey on the vine at the given height. The height affects how the swing behaves.
  - **Details**:
    - `monkey.position.x = vine.position.x`
    - `monkey.position.y = vine.position.y + grabHeight`

---

### 4. **Obstacle**

Represents birds or other obstacles the monkey must avoid.

- **Variables**
  - `position: Point` - Current position of the obstacle.
  - `movementSpeed: float` - Speed at which the obstacle moves.

- **Methods**
  - **`move`**
    - **Behavior**: Moves the obstacle across the screen at a set speed.
  - **`checkCollisionWithMonkey`**
    - **Input**: `Monkey monkey`
    - **Output**: `boolean` - Returns true if the obstacle collides with the monkey.

---

### 5. **UIManager**

Manages on-screen displays, such as the score and game over message.

- **Variables**
  - `scoreDisplay: UIElement` - Displays the current score.
  - `gameOverDisplay: UIElement` - Shows the game over screen when triggered.

- **Methods**
  - **`updateScoreDisplay`**
    - **Behavior**: Refreshes the score display as the player’s score increases.
  - **`showGameOver`**
