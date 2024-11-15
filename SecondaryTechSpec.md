# Secondary Features Technical Specification

## P1 Priority Features

### GameManager
- `togglePause()` functionality
- `isPaused` state management

---

### Obstacle System

#### Variables:
- `position: Point`
- `typeOfObstacle: string`
- `isActive: boolean`
- `speed: double`
- `size: double`

#### Methods:
- `spawnObstacle()`
- `checkCollisionWithMonkey(monkey: Monkey)`
- `resetObstacle()`

---

### Point Class System

#### Variables:
- `x: double`
- `y: double`

---

### Basic Jump Trajectory System
- Basic implementation of `calculateJumpTrajectory(grabHeight: number, swingSpeed: number)`
- Simple parabolic motion calculations

---

## P2 Priority Features

### Advanced Jump Mechanics
- Custom jump trajectories for each jump
- Complex trajectory calculations based on:
  - Grab height
  - Swing speed
  - Vine angle
- Advanced physics simulation

---

## Future Enhancements

- Advanced obstacle patterns
- Dynamic difficulty adjustment
- Power-up system
- Score multipliers
