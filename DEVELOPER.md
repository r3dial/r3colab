# Developer Documentation

## Table of Contents

1. [Architecture Overview](#architecture-overview)
2. [Code Structure](#code-structure)
3. [Core Systems](#core-systems)
4. [Implementation Patterns](#implementation-patterns)
5. [AI/ML Components](#aiml-components)
6. [Contributing Guidelines](#contributing-guidelines)
7. [Extending the Game](#extending-the-game)

---

## Architecture Overview

### High-Level Design

The Snake Game is built as a single-page application with a modular architecture separating concerns:

```
┌─────────────────────────────────────────┐
│         User Interface Layer            │
│  (HTML5 Canvas + CSS3 + DOM Controls)   │
└──────────────┬──────────────────────────┘
               │
┌──────────────▼──────────────────────────┐
│        Game Engine Layer                │
│  (Game Loop, Input, Collision, State)   │
└──────────────┬──────────────────────────┘
               │
┌──────────────▼──────────────────────────┐
│         AI/ML Layer                     │
│  (Neural Network, DQN Agent, Training)  │
└──────────────┬──────────────────────────┘
               │
┌──────────────▼──────────────────────────┐
│      Persistence Layer                  │
│  (LocalStorage for models & settings)   │
└─────────────────────────────────────────┘
```

### Technology Stack

- **Frontend**: HTML5, CSS3, Vanilla JavaScript (ES6+)
- **Rendering**: Canvas 2D API
- **Audio**: Web Audio API
- **Storage**: Web Storage API (localStorage)
- **AI Framework**: Custom DQN implementation (no external ML libraries)

### Key Architectural Decisions

1. **No External Dependencies**: Entire game runs on vanilla JavaScript for portability and learning purposes
2. **Single-File Architecture**: All code contained in one HTML file for easy distribution
3. **Custom Neural Network**: Implemented from scratch to demonstrate ML fundamentals
4. **Client-Side Only**: No server required, runs entirely in the browser

---

## Code Structure

### File Organization

The `snake.html` file contains all code organized into logical sections:

```
snake.html (2474 lines)
├── HTML Structure (lines 1-786)
│   ├── Head (meta, title)
│   ├── Styles (embedded CSS)
│   └── Body (game container, panels, modals)
└── JavaScript (lines 788-2473)
    ├── UI Panel Management (lines 797-824)
    ├── Snake Theme System (lines 826-1014)
    ├── Neural Network Implementation (lines 1016-1155)
    ├── DQN Agent (lines 1158-1462)
    ├── Sound System (lines 1471-1517)
    ├── Game Core Logic (lines 1519-2089)
    ├── A* Pathfinding (lines 1621-1723)
    ├── Metrics Dashboard (lines 2162-2442)
    └── Initialization (lines 2459-2471)
```

### Main Components

**CSS Styling** (`<style>` section, lines 7-654)
- Responsive design with flexbox/grid
- Neon glow effects using CSS shadows
- Animations and transitions
- Panel collapse/expand states
- Media queries for mobile support

**Game State Variables** (lines 1519-1556)
```javascript
let snake = [{x: 10, y: 10}]  // Snake body segments
let dx = 0, dy = 0             // Direction vector
let food = {x: ?, y: ?}        // Food position
let score = 0                  // Current score
let gameRunning = false        // Game state flag
```

**Update Loop** (lines 1818-1933)
- Called at regular intervals via `setInterval`
- Handles AI decision making or player input
- Updates snake position
- Checks collisions
- Manages rewards for AI training
- Triggers rendering

---

## Core Systems

### 1. Game Loop System

**Location**: `snake.html:1818-1933`

The game loop follows a classic update-render pattern:

```javascript
function update() {
    // 1. Get current state
    const currentState = aiAgent.getState(...)

    // 2. Determine next action (AI or player)
    if (currentGameMode === 'demo' || 'train') {
        // AI makes decision
    } else {
        // Process player input queue
    }

    // 3. Update game state
    // - Move snake
    // - Check collisions
    // - Handle food consumption

    // 4. Train AI (if in training mode)
    if (currentGameMode === 'train') {
        aiAgent.remember(...)
        aiAgent.replay()
    }

    // 5. Render
    draw()
}
```

**Timing Control**:
```javascript
// Base interval adjusted by theme speed and training speed
const interval = Math.max(10, 100 / trainingSpeed / currentTheme.speed)
gameLoop = setInterval(update, interval)
```

### 2. Input System

**Location**: `snake.html:1557-1606`

**Input Queue Pattern**: Prevents input loss during fast key presses

```javascript
let inputQueue = []           // Buffer for pending inputs
const maxInputQueue = 2       // Limit queue size

// Key press adds to queue
function handleKeyPress(e) {
    if (inputQueue.length >= maxInputQueue) return

    // Validate direction (no 180° turns)
    const lastDirection = inputQueue.length > 0
        ? inputQueue[inputQueue.length - 1]
        : {dx, dy}

    // Add valid input to queue
    if (/* valid direction */) {
        inputQueue.push({dx: newDx, dy: newDy})
    }
}

// Update loop consumes queue
if (inputQueue.length > 0) {
    const input = inputQueue.shift()
    dx = input.dx
    dy = input.dy
}
```

### 3. Collision Detection

**Location**: `snake.html:1875-1897`

**Wall Collision**:
```javascript
if (head.x < 0 || head.x >= tileCount ||
    head.y < 0 || head.y >= tileCount) {
    gameOver()
}
```

**Self Collision**:
```javascript
if (snake.some(segment => segment.x === head.x && segment.y === head.y)) {
    gameOver()
}
```

**Food Collision**:
```javascript
if (head.x === food.x && head.y === food.y) {
    score += Math.round(10 * currentTheme.pointMultiplier)
    placeFood()
    // Snake grows (don't pop tail)
} else {
    snake.pop()  // Remove tail to maintain length
}
```

### 4. Rendering System

**Location**: `snake.html:1935-2038`

**Canvas Setup**:
```javascript
const canvas = document.getElementById('gameCanvas')
const ctx = canvas.getContext('2d')

// Responsive sizing
function resizeCanvas() {
    canvas.width = window.innerWidth
    canvas.height = window.innerHeight
    tileCount = Math.floor(Math.min(canvas.width, canvas.height) / gridSize)
}
```

**Rendering Pipeline**:
```javascript
function draw() {
    // 1. Clear canvas
    ctx.fillStyle = '#000000'
    ctx.fillRect(0, 0, canvas.width, canvas.height)

    // 2. Calculate centering offset
    const offsetX = (canvas.width - gridWidth) / 2
    const offsetY = (canvas.height - gridHeight) / 2

    // 3. Draw grid lines
    // 4. Draw food with glow effects
    // 5. Draw trail particles (if theme has trails)
    // 6. Draw snake with theme colors
    // 7. Draw snake eyes
}
```

**Visual Effects**:
- Shadow blur for neon glow: `ctx.shadowBlur = 20`
- Shadow color for glow color: `ctx.shadowColor = '#ff00ff'`
- Particle system for trail effects

### 5. Theme System

**Location**: `snake.html:826-1014`

**Theme Object Structure**:
```javascript
const snakeThemes = {
    cyberViper: {
        id: 'cyberViper',
        name: 'Cyber Viper',
        icon: '🐍',
        headColor: '#39ff14',
        bodyColor: '#00ffff',
        shadowColor: '#39ff14',
        trailEffect: false,
        speed: 1.0,
        pointMultiplier: 1.0,
        startLength: 1,
        description: 'Balanced stats for all-around gameplay'
    },
    // ... more themes
}
```

**Theme Impact**:
- **Visual**: Colors and trail particles
- **Gameplay**: Speed multiplier affects game loop interval
- **Scoring**: Point multiplier affects reward calculation
- **Starting Conditions**: Initial snake length

**Trail Particle System** (lines 980-1014):
```javascript
class TrailParticle {
    constructor(x, y, color) {
        this.alpha = 1.0  // Fade out over time
        this.size = Math.random() * 3 + 2
    }

    update() {
        this.alpha -= 0.02  // Gradual fade
    }

    draw(ctx, offsetX, offsetY, gridSize) {
        ctx.globalAlpha = this.alpha
        ctx.shadowBlur = 10
        // Draw glowing particle
    }
}
```

### 6. Persistence System

**Location**: Throughout codebase

**Saved Data Types**:

1. **High Score** (line 1552):
```javascript
localStorage.setItem('snakeHighScore', highScore)
highScore = localStorage.getItem('snakeHighScore') || 0
```

2. **Theme Preference** (lines 886-896):
```javascript
localStorage.setItem('snakeThemePreference', themeId)
const savedTheme = localStorage.getItem('snakeThemePreference')
```

3. **Panel States** (lines 813-823):
```javascript
localStorage.setItem(panelId + '_collapsed', 'true')
```

4. **AI Model** (lines 1407-1444):
```javascript
// Save complete model
const data = {
    network: this.network.toJSON(),
    gamesPlayed, totalScore, bestScore,
    episodeHistory, totalTrainingTime
}
localStorage.setItem('snakeAIModel', JSON.stringify(data))
```

---

## AI/ML Components

### Neural Network Architecture

**Location**: `snake.html:1016-1155`

**Network Structure**:
```
Input Layer (22 neurons)
    ↓
Hidden Layer (128 neurons, ReLU activation)
    ↓
Output Layer (4 neurons, linear activation)
```

**Input Features** (22 total):
1. Normalized head position (x, y)
2. Normalized food position (x, y)
3. Current direction (4 one-hot encoded values)
4. Danger detection in 8 directions (8 binary values)
5. Food direction indicators (4 binary values)
6. Distance to food (1 normalized value)
7. Snake length normalized (1 value)

**Implementation Details**:

```javascript
class NeuralNetwork {
    constructor(inputSize, hiddenSize, outputSize) {
        // Xavier initialization for better training
        this.weightsIH = this.randomMatrix(inputSize, hiddenSize,
            Math.sqrt(2.0 / inputSize))
        this.weightsHO = this.randomMatrix(hiddenSize, outputSize,
            Math.sqrt(2.0 / hiddenSize))
        this.biasH = new Array(hiddenSize).fill(0)
        this.biasO = new Array(outputSize).fill(0)
    }

    forward(inputs) {
        // Hidden layer with ReLU
        const hidden = []
        for (let i = 0; i < this.hiddenSize; i++) {
            let sum = this.biasH[i]
            for (let j = 0; j < this.inputSize; j++) {
                sum += inputs[j] * this.weightsIH[j][i]
            }
            hidden[i] = this.relu(sum)  // max(0, sum)
        }

        // Output layer (linear)
        const output = []
        for (let i = 0; i < this.outputSize; i++) {
            let sum = this.biasO[i]
            for (let j = 0; j < this.hiddenSize; j++) {
                sum += hidden[j] * this.weightsHO[j][i]
            }
            output[i] = sum  // Q-values
        }

        return { hidden, output }
    }

    train(inputs, targetQValues, learningRate = 0.001) {
        // Backpropagation with gradient descent
        const { hidden, output } = this.forward(inputs)

        // Calculate errors
        const outputErrors = targetQValues.map((target, i) => target - output[i])

        // Backpropagate to hidden layer
        const hiddenErrors = /* weighted sum of output errors */

        // Update weights and biases
        // Only update if ReLU was active (derivative = 1 if x > 0, else 0)
    }
}
```

### DQN Agent

**Location**: `snake.html:1158-1462`

**Deep Q-Learning Algorithm**:

The agent learns by approximating the Q-function: Q(state, action) → expected reward

**Key Components**:

1. **Dual Networks**:
```javascript
this.network = new NeuralNetwork(22, 128, 4)       // Policy network
this.targetNetwork = this.network.copy()           // Target network (updated periodically)
```

2. **Experience Replay**:
```javascript
this.memory = []  // Stores (state, action, reward, nextState, done) tuples
this.memorySize = 10000
this.batchSize = 32

remember(state, action, reward, nextState, done) {
    this.memory.push({ state, action, reward, nextState, done })
    if (this.memory.length > this.memorySize) {
        this.memory.shift()  // Remove oldest
    }
}
```

3. **Epsilon-Greedy Policy**:
```javascript
act(state, training = false) {
    if (training && Math.random() < this.epsilon) {
        return Math.floor(Math.random() * 4)  // Explore
    }

    const qValues = this.network.predict(state)
    return qValues.indexOf(Math.max(...qValues))  // Exploit
}
```

4. **Training (Experience Replay)**:
```javascript
replay() {
    if (this.memory.length < this.batchSize) return

    // Sample random batch
    const batch = /* randomly sample from memory */

    // Train on each experience
    batch.forEach(({ state, action, reward, nextState, done }) => {
        const target = this.network.predict(state)

        if (done) {
            target[action] = reward
        } else {
            // Bellman equation
            const nextQValues = this.targetNetwork.predict(nextState)
            target[action] = reward + this.gamma * Math.max(...nextQValues)
        }

        this.network.train(state, target, this.learningRate)
    })
}
```

5. **Hyperparameters**:
```javascript
this.gamma = 0.95              // Discount factor (future reward importance)
this.epsilon = 1.0             // Initial exploration rate
this.epsilonMin = 0.05         // Minimum exploration
this.epsilonDecay = 0.999      // Decay rate per episode
this.learningRate = 0.0005     // Neural network learning rate
this.updateTargetEvery = 10    // Sync target network frequency
```

**Reward Structure**:
- Eating food: +10
- Dying (collision): -10
- Each step without food: -0.1 (encourages efficiency)

### State Representation

**Location**: `snake.html:1191-1230`

**Feature Engineering**:

```javascript
getState(snake, food, tileCount, dx, dy) {
    const head = snake[0]

    // Normalize positions to [0, 1]
    const normalizedHeadX = head.x / tileCount
    const normalizedHeadY = head.y / tileCount
    const normalizedFoodX = food.x / tileCount
    const normalizedFoodY = food.y / tileCount

    // Direction encoding (one-hot)
    const dirUp = dy === -1 ? 1 : 0
    const dirDown = dy === 1 ? 1 : 0
    const dirLeft = dx === -1 ? 1 : 0
    const dirRight = dx === 1 ? 1 : 0

    // Danger detection (8 directions around head)
    const dangers = this.detectDangers(snake, head, tileCount)

    // Food direction (relative to head)
    const foodUp = food.y < head.y ? 1 : 0
    const foodDown = food.y > head.y ? 1 : 0
    const foodLeft = food.x < head.x ? 1 : 0
    const foodRight = food.x > head.x ? 1 : 0

    // Manhattan distance to food (normalized)
    const foodDist = Math.sqrt(
        Math.pow(food.x - head.x, 2) +
        Math.pow(food.y - head.y, 2)
    ) / tileCount

    // Snake length (normalized)
    const snakeLength = snake.length / (tileCount * tileCount)

    return [/* all 22 features */]
}
```

This representation provides the AI with:
- **Spatial awareness**: Where am I? Where is the food?
- **Temporal awareness**: Which direction am I moving?
- **Safety awareness**: What's dangerous around me?
- **Goal awareness**: Where should I go?
- **Progress awareness**: How long is my snake?

### A* Pathfinding (Demo Mode Fallback)

**Location**: `snake.html:1621-1808`

While the DQN learns to play, there's also an A* pathfinding algorithm used as a fallback:

```javascript
class AStarPathfinder {
    findPath(start, goal) {
        const openSet = [start]
        const closedSet = new Set()
        const cameFrom = new Map()
        const gScore = new Map()  // Cost from start
        const fScore = new Map()  // Estimated total cost

        gScore.set(key(start), 0)
        fScore.set(key(start), this.heuristic(start, goal))

        while (openSet.length > 0) {
            // Get node with lowest fScore
            const current = /* node with min fScore */

            if (current === goal) {
                return /* reconstruct path */
            }

            // Explore neighbors
            for (const neighbor of this.getNeighbors(current)) {
                if (this.isCollision(neighbor)) continue

                const tentativeGScore = gScore.get(current) + 1

                if (tentativeGScore < gScore.get(neighbor)) {
                    cameFrom.set(neighbor, current)
                    gScore.set(neighbor, tentativeGScore)
                    fScore.set(neighbor, tentativeGScore + heuristic(neighbor, goal))
                }
            }
        }

        return null  // No path found
    }

    heuristic(a, b) {
        // Manhattan distance
        return Math.abs(a.x - b.x) + Math.abs(a.y - b.y)
    }
}
```

---

## Implementation Patterns

### 1. Module Pattern (Pseudo-Modules)

While the code is in one file, it's organized into logical sections with clear separation:

```javascript
// ===== NEURAL NETWORK IMPLEMENTATION =====
class NeuralNetwork { ... }

// ===== DQN AGENT =====
class DQNAgent { ... }

// ===== A* PATHFINDING =====
class AStarPathfinder { ... }
```

### 2. State Management Pattern

Global state variables with clear ownership:

```javascript
// Game state (owned by game loop)
let snake, dx, dy, food, score, gameRunning

// AI state (owned by DQN agent)
const aiAgent = new DQNAgent()
let currentGameMode = 'human'

// Theme state (owned by theme system)
let currentTheme = snakeThemes.cyberViper
```

### 3. Event-Driven Architecture

```javascript
// Keyboard events
document.addEventListener('keydown', handleKeyPress)
document.addEventListener('keydown', (e) => {
    if (e.key.toLowerCase() === 'm') {
        toggleMetricsDashboard()
    }
})

// Window events
window.addEventListener('resize', () => {
    resizeCanvas()
    draw()
})

// UI events (onclick attributes in HTML)
onclick="setGameMode('train')"
onclick="toggleMetricsDashboard()"
```

### 4. Separation of Concerns

**Rendering** (draw function):
- Only responsible for visual output
- No game logic
- Reads state, doesn't modify it

**Update** (update function):
- Only responsible for game logic
- Modifies state
- Doesn't handle rendering directly

**AI Training** (DQN agent):
- Encapsulated in DQNAgent class
- No direct game manipulation
- Communicates through state and actions

### 5. Object-Oriented Patterns

**Encapsulation**:
```javascript
class DQNAgent {
    #privateMethod() { }  // Could use private fields (ES2022)

    publicMethod() {
        this.#privateMethod()
    }
}
```

**Polymorphism** (Duck typing):
```javascript
// Different render strategies based on theme
if (currentTheme.trailEffect) {
    trailParticles.forEach(particle => particle.draw(ctx, ...))
}
```

### 6. Factory Pattern

**Trail Particle Creation**:
```javascript
if (currentTheme.trailEffect) {
    trailParticles.push(new TrailParticle(x, y, currentTheme.headColor))
}
```

### 7. Strategy Pattern

**Game Mode Strategies**:
```javascript
// Different AI strategies based on mode
if (currentGameMode === 'demo' || currentGameMode === 'train') {
    const action = aiAgent.act(currentState, currentGameMode === 'train')
} else if (currentGameMode === 'human') {
    // Process input queue
}
```

---

## Contributing Guidelines

### Code Style

1. **Indentation**: 4 spaces
2. **Naming Conventions**:
   - Variables: `camelCase`
   - Constants: `UPPER_SNAKE_CASE` (if truly constant)
   - Classes: `PascalCase`
   - Private methods: `_prefixedUnderscore` (convention)

3. **Comments**:
   - Use section headers: `// ===== SECTION NAME =====`
   - Explain "why" not "what" for complex logic
   - Document function parameters and return values

4. **Function Length**: Keep functions focused and under 50 lines when possible

### Adding New Features

#### Adding a New Snake Theme

1. Add theme object to `snakeThemes` (line 827):
```javascript
newTheme: {
    id: 'newTheme',
    name: 'New Theme Name',
    icon: '🎯',
    headColor: '#rrggbb',
    bodyColor: '#rrggbb',
    shadowColor: '#rrggbb',
    trailEffect: true/false,
    speed: 1.0,  // Multiplier
    pointMultiplier: 1.0,
    startLength: 1,
    description: 'Description text'
}
```

2. Theme automatically appears in UI - no additional code needed!

#### Adding New AI Inputs

1. Modify `getState()` method (line 1191):
```javascript
getState(snake, food, tileCount, dx, dy) {
    // Add your new feature
    const myNewFeature = /* calculate feature */

    // Add to return array
    return [
        /* existing features */,
        myNewFeature
    ]
}
```

2. Update network input size (line 1161):
```javascript
this.network = new NeuralNetwork(23, 128, 4)  // Was 22, now 23
```

3. Retrain the model (new architecture incompatible with old saves)

#### Adding New Metrics

1. Add tracking variable to DQNAgent constructor (line 1159)
2. Update metric in appropriate location (e.g., `endEpisode`)
3. Add display in metrics dashboard HTML (lines 714-785)
4. Update `updateMetricsDashboard()` function (line 2172)

### Testing

**Manual Testing Checklist**:
- [ ] Human mode works with all themes
- [ ] AI demo mode functions
- [ ] AI training mode learns over time
- [ ] Model save/load preserves state
- [ ] Metrics export correctly
- [ ] Responsive design works on mobile
- [ ] No console errors

**Performance Testing**:
- Train at 100x speed for 1000 episodes - should not crash
- Verify memory doesn't grow unbounded
- Check FPS remains smooth at all speeds

### Performance Optimization Tips

1. **Batch Training**: Already implemented - `replay()` trains on 32 samples at once
2. **Target Network**: Reduces training instability
3. **Memory Limits**: Experience replay buffer capped at 10,000
4. **Canvas Optimization**:
   - Only redraw when needed
   - Use `requestAnimationFrame` for smoother rendering (potential improvement)

---

## Extending the Game

### Adding Multiplayer

**Approach**:
1. Create separate snake arrays: `snake1`, `snake2`
2. Dual input handlers: WASD for player 1, arrows for player 2
3. Modify collision detection to check both snakes
4. Update rendering to draw both snakes

**Challenges**:
- Food collision race conditions
- Screen space management
- Fairness in food placement

### Adding New Game Modes

**Example: Survival Mode (no walls)**

```javascript
// Add mode selector
<button onclick="setWallMode('wrap')">Wrap Mode</button>

// Modify collision detection
if (head.x < 0) {
    if (wallMode === 'wrap') {
        head.x = tileCount - 1  // Wrap to other side
    } else {
        gameOver()
    }
}
```

### Adding Power-Ups

**Implementation**:

1. Create power-up object:
```javascript
let powerUp = {
    x: Math.floor(Math.random() * tileCount),
    y: Math.floor(Math.random() * tileCount),
    type: 'speed',  // 'speed', 'slow', 'invincible', etc.
    duration: 5000  // milliseconds
}
```

2. Render power-up:
```javascript
// In draw() function
ctx.fillStyle = '#ffff00'
ctx.fillRect(
    offsetX + powerUp.x * gridSize,
    offsetY + powerUp.y * gridSize,
    gridSize, gridSize
)
```

3. Collision detection:
```javascript
if (head.x === powerUp.x && head.y === powerUp.y) {
    activatePowerUp(powerUp.type)
    respawnPowerUp()
}
```

4. Update AI state representation to include power-up location

### Adding Obstacles

**Static Obstacles**:
```javascript
const obstacles = [
    {x: 5, y: 5},
    {x: 15, y: 15}
]

// In collision detection
if (obstacles.some(obs => obs.x === head.x && obs.y === head.y)) {
    gameOver()
}

// In AI danger detection
function detectDangers(snake, head, tileCount) {
    // Also check obstacles
    if (obstacles.some(obs => obs.x === newX && obs.y === newY)) {
        return 1  // Danger!
    }
}
```

### Adding Sound Effects

**Already Implemented**: See lines 1471-1517

**Adding New Sounds**:
```javascript
function play8bitSound(type) {
    const oscillator = audioContext.createOscillator()
    const gainNode = audioContext.createGain()

    oscillator.connect(gainNode)
    gainNode.connect(audioContext.destination)

    switch(type) {
        case 'powerup':
            oscillator.type = 'sine'
            oscillator.frequency.setValueAtTime(600, now)
            oscillator.frequency.exponentialRampToValueAtTime(1200, now + 0.2)
            gainNode.gain.setValueAtTime(0.3, now)
            gainNode.gain.exponentialRampToValueAtTime(0.01, now + 0.2)
            oscillator.start(now)
            oscillator.stop(now + 0.2)
            break
    }
}
```

### Network Architecture Experiments

**Deeper Network**:
```javascript
class DeepNN {
    constructor() {
        this.layer1 = new Matrix(22, 256)  // Input to hidden1
        this.layer2 = new Matrix(256, 128) // Hidden1 to hidden2
        this.layer3 = new Matrix(128, 64)  // Hidden2 to hidden3
        this.layer4 = new Matrix(64, 4)    // Hidden3 to output
    }
}
```

**Different Activation Functions**:
```javascript
leakyRelu(x, alpha = 0.01) {
    return x > 0 ? x : alpha * x
}

tanh(x) {
    return Math.tanh(x)
}
```

**Batch Normalization**:
```javascript
batchNorm(values) {
    const mean = values.reduce((a, b) => a + b) / values.length
    const variance = values.reduce((a, b) => a + Math.pow(b - mean, 2), 0) / values.length
    return values.map(v => (v - mean) / Math.sqrt(variance + 1e-8))
}
```

---

## Advanced Topics

### Reinforcement Learning Theory

**Q-Learning Foundation**:

The Q-function represents the expected cumulative reward:
```
Q(s, a) = expected total reward from state s taking action a
```

**Bellman Equation** (recursive definition):
```
Q(s, a) = r + γ × max[Q(s', a')]
          ↑   ↑        ↑
     immediate  discount  best future
      reward     factor   Q-value
```

**Deep Q-Learning**: Use neural network to approximate Q(s, a)

**Why Target Network?**
- Training uses moving target (Q-values change during training)
- Target network updated less frequently (every 10 episodes)
- Stabilizes training by providing consistent targets

**Why Experience Replay?**
- Breaks correlation between consecutive samples
- Reuses experiences multiple times (data efficient)
- Smooths out distribution of training data

### Hyperparameter Tuning

**Learning Rate** (`0.0005`):
- Too high: Network doesn't converge, erratic behavior
- Too low: Training too slow
- Adaptive learning rate could help

**Epsilon Decay** (`0.999`):
- Controls exploration/exploitation tradeoff
- Current: ~1000 episodes to reach minimum
- Faster decay: Exploits earlier, might miss better strategies
- Slower decay: Explores longer, slower learning

**Discount Factor** (`0.95`):
- How much to value future rewards
- 0: Only immediate rewards matter (myopic)
- 1: All future rewards equally important
- 0.95: Good balance for game with steps

**Batch Size** (`32`):
- Tradeoff between speed and stability
- Larger: More stable gradients, slower updates
- Smaller: Faster updates, more variance

### Future Enhancements

1. **Priority Experience Replay**: Sample important experiences more often
2. **Dueling DQN**: Separate value and advantage streams
3. **Double DQN**: Reduce overestimation bias
4. **Recurrent Networks**: LSTM for temporal patterns
5. **Curriculum Learning**: Start with easier scenarios
6. **Transfer Learning**: Train on small grid, transfer to larger

---

## FAQ

**Q: Why vanilla JavaScript instead of frameworks?**
A: Educational purposes - easier to understand all components without framework abstractions. Also makes the game maximally portable.

**Q: Why is the neural network implemented from scratch?**
A: To demonstrate how neural networks work at a fundamental level without hiding behind library abstractions. Great for learning.

**Q: How long should I train the AI?**
A: Typically 1000-5000 episodes at high speed. You'll see epsilon decay and scores improve over time. Diminishing returns after ~10,000 episodes.

**Q: Can I use the trained model in other projects?**
A: The model is specific to this game's state representation and action space. However, the architecture and training approach can be adapted to other grid-based games.

**Q: How can I make the AI learn faster?**
A: Increase training speed (slider), tune hyperparameters, improve state representation, or adjust reward structure.

---

## Resources

### Reinforcement Learning
- [Sutton & Barto: Reinforcement Learning](http://incompleteideas.net/book/the-book.html)
- [DeepMind DQN Paper](https://www.nature.com/articles/nature14236)
- [OpenAI Spinning Up](https://spinningup.openai.com/)

### Neural Networks
- [Neural Networks and Deep Learning](http://neuralnetworksanddeeplearning.com/)
- [3Blue1Brown Neural Network Series](https://www.youtube.com/playlist?list=PLZHQObOWTQDNU6R1_67000Dx_ZCJB-3pi)

### Canvas API
- [MDN Canvas Tutorial](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Tutorial)
- [HTML5 Canvas Deep Dive](https://joshondesign.com/p/books/canvasdeepdive/toc.html)

### Web Audio API
- [MDN Web Audio](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API)

---

## License

Open source - feel free to learn from, modify, and share!

**Happy coding! 🐍✨**
