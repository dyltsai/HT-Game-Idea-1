### 1. **GameManager**

Manages the overall game state, including game progression, score, and game-over conditions in an infinite loop format.

#### **Variables**
- `score: int` - The player’s current score based on distance traveled.
- `isGameOver: boolean` - Indicates whether the game has ended.
- `monkey: Monkey` - Instance of the monkey character.
- `vines: List<Vine>` - List of vines that the monkey can grab.
- `obstacles: List<Obstacle>` - List of obstacles in the game.
- `isPaused: boolean` - Whether the game is paused.
- `time: double` - Tracks time passed for various game mechanics such as speed progression and obstacle appearance.
#### *all of the above variables are p0 prioority* ####

#### **Game Manager** **p0, except pausing the game**
- **`initializeGame()`** 
  - **Behavior**: Sets up the game state, initializes score, game-over state, the monkey, vines, obstacles, and other initial game components.
  - **Details**: The game begins by resetting all variables, placing the monkey at the starting point, and preparing the first vine.

- **`startGameLoop()`** 
  - **Behavior**: Begins the infinite game loop. Continuously updates the state of the game, including vine swinging, obstacles, score updates, and checks for game over.
  - **Details**:
    - Starts a `setInterval` or `requestAnimationFrame` loop to continuously update the game state. 
    - Every iteration, the game will update the monkey's position, check if it collides with any obstacles, update the score based on the monkey’s progress, and add new vines and obstacles.

- **`updateScore(int)`**
  - **Behavior**: Adds points to the current score.
  - **Details**: Points are typically added based on distance traveled or successful interactions with obstacles (like avoiding obstacles or successfully jumping).

- **`spawnObstacle()`** 
  - **Behavior**: Adds new obstacles to the game at random intervals or based on difficulty progression.
  - **Details**: Obstacles spawn at varying positions and heights, and their difficulty increases as the player progresses (faster movement, more frequent obstacles).

- **`checkGameOver()`**
  - **Behavior**: Determines whether the game is over based on the `isGameOver` state.
  - **Details**: Checks if the monkey collided with any obstacles. If so, `isGameOver` is set to `true`, ending the game.

- **`resetGame()`**
  - **Behavior**: Resets the game state for replay. This includes resetting score, position, obstacles, vines, and other relevant game components.
  - **Details**: Resets all variables to their initial state and reinitializes the game loop.

- **`updateGameLoop(deltaTime: float)`**
  - **Input**: `deltaTime` - The amount of time passed since the last frame.
  - **Behavior**: This method is called every frame. It updates the state of the game based on the elapsed time:
    - Moves the monkey and vines forward.
    - Increases difficulty by speeding up the vines and obstacles.
    - Spawns new obstacles at random intervals.
    - Checks if the monkey has collided with any obstacles.
  - **Details**: It helps simulate the continuous, infinite nature of the game.

- **`togglePause()`** **p1**
  - **Behavior**: Toggles whether the game is paused or playing.
  - **Details**: Pauses or resumes the game loop.

### 2. **Obstacles** **whole thing is *p1* since the game can still run and be fun without obstacles**

Manages obstacles that appear randomly and interact with the monkey’s movement.

#### **Variables**
- `position: Point` - Position of the obstacle in the game world.
- `typeOfObstacle: string` - Type of obstacle (e.g., "bird", "branch", etc.).
- `isActive: boolean` - Indicates whether the obstacle is currently active or not.
- `speed: double` - The speed at which the obstacle moves toward the monkey.
- `size: double` - Size of the obstacle for collision detection.

#### **Methods**
- **`spawnObstacle()`**
  - **Behavior**: Spawns a new obstacle at a random position or predetermined spawn points. Obstacle type and speed are randomized based on game difficulty.
  - **Details**: Randomly determines the type (e.g., bird, falling branch), speed, and position. As the game progresses, the speed and frequency of obstacles increase.

- **`checkCollisionWithMonkey(monkey: Monkey)`**
  - **Input**: `monkey: Monkey` - The instance of the monkey to check for collisions with.
  - **Behavior**: Checks if the obstacle collides with the monkey.
  - **Details**: Uses the position and size of the obstacle and the monkey to detect any overlap. If a collision is detected, the game ends or deducts lives.

- **`resetObstacle()`**
  - **Behavior**: Resets the obstacle once it has moved out of the screen or has been interacted with by the monkey.
  - **Details**: Places the obstacle back into the game world in a position for the next iteration.

  ---


### 3. **Monkey Class** **everything p0 priority since it is inherent to the game EXCEPT for jumpTrajectory**

- **Variables**
    - `position: Point` - Current position of the monkey on the screen.
    - `grabHeight: double` - The height along the vine at which the monkey grabs; affects jump trajectory and distance.
    - `swingSpeed: double` - Speed of the vine’s swing when the monkey jumps; influences horizontal distance.
    - `maxJumpHeight: double` - Maximum height the monkey can reach in a jump.
    - `jumpTrajectory: Point` - Calculated trajectory for the jump, determining the parabolic path based on the monkey’s grab position and swing speed. **this is p2 priority since a custom trajectory for every jump is completely overindulgent**
    - `jumpInProgress: boolean` - Indicates whether the monkey is currently in a jump.

- **Methods**

    - **`calculateJumpTrajectory(grabHeight: number, swingSpeed: number): Point`**
        - **Input**:
            - `grabHeight: number` - Height along the vine where the monkey grabs, ranging from `0` (top of the vine) to `vine.length` (bottom).
            - `swingSpeed: number` - Current speed of the vine swing at the moment the monkey jumps.
        - **Output**: `Point` - Returns a `Point` object representing the initial velocity (both x and y components) of the jump.
        - **Behavior**:
            - Calculates the horizontal (`x`) and vertical (`y`) components of the jump based on `grabHeight` and `swingSpeed`.
            - **Horizontal Speed**: Derived from the `swingSpeed` and adjusted by `Math.sin(swingAngle)` to capture forward motion.
            - **Vertical Speed**: Proportional to `grabHeight` relative to the vine's length and `maxJumpHeight`, giving a parabolic shape based on how high the monkey grabs.
            - This initial velocity (stored in `jumpTrajectory`) dictates the monkey's path until it lands.
            - **Formula**:
                - `jumpTrajectory.x = swingSpeed * Math.cos(swingAngle)`
                - `jumpTrajectory.y = (grabHeight / vine.length) * maxJumpHeight`

    - **`jump()`**
        - **Behavior**: Initiates the jump by setting `jumpInProgress` to true and calculating the initial jump trajectory using `calculateJumpTrajectory`.
        - Updates the monkey’s position over time to follow a parabolic curve based on `jumpTrajectory`.
        - Adjusts the monkey’s `position.x` and `position.y` each frame according to the trajectory:
            - `position.x += jumpTrajectory.x * timeStep`
            - `position.y += jumpTrajectory.y * timeStep - (gravity * timeStep^2 / 2)`
            - Applies gravity to the `y` component for a realistic downward curve.
        - Ends the jump when the monkey lands on the next vine or the ground.

    - **`resetJump()`**
        - **Behavior**: Resets `jumpInProgress` to false, clears `jumpTrajectory`, and resets the monkey to a neutral position on a new vine after landing.
        
- **`landOnVine(vine: Vine, grabHeight: float)`**
  - **Input**: `vine: Vine`, `grabHeight: float` - The position and the height at which the monkey grabs the vine.
  - **Behavior**: Positions the monkey on the vine at the given height. The height affects how the swing behaves.
  - **Details**:
    - `monkey.position.x = vine.position.x`
    - `monkey.position.y = vine.position.y + grabHeight`

---

### ** 4. Vine Class** **p0 since the vines can be standardized**

Represents a vine for swinging, with a simplified approach to handle different lengths, swing speed, and the monkey’s grab point.

- **Variables**
    - `anchorPoint: Point` - The fixed anchor point of the vine on the screen.
    - `length: int` - The length of the vine.
    - `swingSpeed: double` - Controls the speed of the swing; the time taken to swing from one end to the other.
    - `currentAngle: double` - The current angle of the vine’s swing in radians, starting from the resting position.
    - `grabHeight: double` - Position along the vine where the monkey grabs, from `0` (top) to `length` (bottom).

- **Methods**

    - **`swing(timeStep: number)`**
        - **Input**: `timeStep: number` - The time increment for each frame update.
        - **Behavior**:
            - Updates `currentAngle` based on `swingSpeed` and `timeStep` to create a simple oscillating swing motion:
                - **Formula**: `currentAngle = Math.sin(timeStep * swingSpeed) * maxSwingAngle`
            - `maxSwingAngle` is a constant defining the maximum angle the vine reaches from the resting position.

    - **`getMonkeyPosition(): Point`**
        - **Output**: `Point` - Returns the position of the monkey along the vine’s arc, based on `grabHeight` and `currentAngle`.
        - **Behavior**:
            - Calculates the `x` and `y` position of the monkey:
                - `x = anchorPoint.x + Math.sin(currentAngle) * grabHeight`
                - `y = anchorPoint.y + Math.cos(currentAngle) * grabHeight`
            - Returns a `Point` object representing the monkey's position on the vine.

    - **`resetSwing()`**
        - **Behavior**: Resets `currentAngle` to zero to start a new swing cycle when needed.

---

### 5. **Point** **p1 since this is erraneous**

Represents a 2D point in space, used for positions and directional calculations such as `jumpTrajectory` for the monkey’s jump.

- **Variables**
    - `x: double` - The horizontal (x-axis) component of the point, representing position or velocity in the x-direction.
    - `y: double` - The vertical (y-axis) component of the point, representing position or velocity in the y-direction.

---

## [Monkey Vroom Vroom Architecture on Figma](https://www.figma.com/board/O0nuwyYQQlZSh1bG26ezDd/Monkey-Vroom-Vroom-Architecture?node-id=0-1&node-type=canvas&t=C6XpV7kCmZ9VqWIF-0)