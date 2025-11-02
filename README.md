# r3colab
Public Colab

fernicar was here

## Table of Contents
- [Overview](#overview)
- [Projects](#projects)
  - [Snake Game - AI Learning Edition](#snake-game---ai-learning-edition)
- [User Guide](#user-guide)
  - [Getting Started](#getting-started)
  - [Game Modes](#game-modes)
  - [Controls](#controls)
  - [AI Training System](#ai-training-system)
  - [Theme System](#theme-system)
  - [Metrics Dashboard](#metrics-dashboard)
- [Developer Guide](#developer-guide)
  - [Architecture Overview](#architecture-overview)
  - [Core Components](#core-components)
  - [AI Implementation](#ai-implementation)
  - [Code Structure](#code-structure)
  - [Contributing](#contributing)

## Overview

r3colab is a collaborative development space featuring interactive web-based projects. The primary project is an advanced Snake game that combines classic gameplay with modern AI learning capabilities.

## Projects

### Snake Game - AI Learning Edition

A classic snake game enhanced with cutting-edge AI features, built entirely with HTML5, CSS3, and vanilla JavaScript. This project demonstrates reinforcement learning through Deep Q-Networks (DQN) in a browser-based environment.

**Key Features:**
- Three distinct game modes: Human Play, AI Demo, and AI Training
- Neural network-based AI using Deep Q-Learning
- Real-time training metrics and visualization dashboard
- Multiple visual themes with different gameplay characteristics
- Responsive design with modern neon-cyberpunk aesthetics
- Local storage for high scores and trained AI models
- Audio feedback with procedurally generated 8-bit sounds

**Quick Start:**
1. Open `snake.html` in any modern web browser
2. Choose your game mode (Human, AI Demo, or Training)
3. Watch the AI learn or play yourself!

[Play the game](snake.html)

---

## User Guide

### Getting Started

#### Prerequisites
- Modern web browser (Chrome, Firefox, Safari, or Edge)
- No installation required - runs entirely in the browser

#### First Time Setup
1. Navigate to the project directory
2. Open `snake.html` in your web browser (or use `index.html` which links to it)
3. The game will load with default settings

### Game Modes

The game offers three distinct modes of operation:

#### 1. Human Mode (Default)
**How to Play:**
- Click "Human Mode" button or press any arrow key to start
- Use arrow keys (↑ ↓ ← →) to control the snake
- Eat red food to grow and gain points
- Avoid hitting walls or the snake's own body
- Try to beat your high score stored in localStorage

**Features:**
- Full manual control
- Score tracking with persistent high scores
- Game over detection with visual feedback
- Sound effects for eating food and game over

#### 2. AI Demo Mode
**Purpose:** Watch a pre-trained AI play the game

**How to Use:**
- Click "AI Demo" button to start
- The AI will use its learned strategy to navigate
- Observe decision-making in real-time
- Auto-restarts after game over

**What to Watch For:**
- The AI evaluates 8 directional states (walls, food, body)
- A* pathfinding algorithm guides decision-making
- Performance varies based on training progress

#### 3. AI Training Mode
**Purpose:** Train the neural network through reinforcement learning

**How to Use:**
1. Click "AI Training" button to begin
2. Adjust training speed (1x to 100x) using the slider
3. Watch the AI learn and improve over time
4. Use the metrics dashboard to monitor progress

**Training Speed:**
- Default: 10x speed for balanced learning
- Range: 1x (normal speed) to 100x (ultra-fast training)
- Higher speeds accelerate learning but may reduce visualization clarity

**Training Controls:**
- Save Model: Store current AI weights in localStorage
- Load Model: Restore previously saved model
- Reset AI: Clear all training progress and start fresh

### Controls

#### Keyboard Controls
- **Arrow Keys (↑ ↓ ← →)**: Control snake direction in Human mode
- **Space Bar**: Restart game after game over
- **M Key**: Toggle metrics dashboard
- **Escape Key**: Pause/Resume game

#### UI Controls
- **Mode Buttons**: Switch between Human, AI Demo, and Training modes
- **Training Speed Slider**: Adjust AI training speed (Training mode only)
- **AI Panel Toggle**: Collapse/expand AI controls
- **Theme Selector**: Choose visual themes with different gameplay
- **Metrics Dashboard**: View detailed AI performance statistics

### AI Training System

#### How It Works
The AI uses Deep Q-Network (DQN) reinforcement learning:

1. **State Observation**: AI perceives 8 states:
   - Distance to walls in 4 directions
   - Distance to food in X and Y axes
   - Danger detection (body collision risk)
   - Current direction

2. **Decision Making**: Neural network outputs Q-values for 4 actions (up, down, left, right)

3. **Learning Process**:
   - Receives rewards for eating food (+10)
   - Penalized for dying (-10)
   - Small step penalty to encourage efficiency (-0.01)
   - Uses epsilon-greedy exploration strategy
   - Learns from past experiences (experience replay)

4. **Improvement**: Over time, the AI learns optimal strategies through trial and error

#### Training Tips
- Start with 10x speed for initial training
- Let it run for at least 100-200 episodes to see improvement
- Use 50x-100x speed for extended training sessions
- Save your model periodically to preserve progress
- Monitor the metrics dashboard to track learning curves

### Theme System

The game includes multiple visual themes, each with unique gameplay characteristics:

#### Available Themes

**Classic Theme (Default)**
- Starting length: 3 segments
- Speed: Normal (1.0x)
- Traditional snake game aesthetics

**Neon Theme**
- Starting length: 4 segments
- Speed: Fast (1.5x)
- Vibrant neon colors with glow effects

**Matrix Theme**
- Starting length: 5 segments
- Speed: Very fast (2.0x)
- Green matrix-style visuals
- Challenge mode for experienced players

**Retro Theme**
- Starting length: 3 segments
- Speed: Slow (0.8x)
- Classic arcade aesthetics
- Ideal for beginners

#### Theme Features
- Persistent theme selection (saved to localStorage)
- Each theme affects gameplay speed and difficulty
- Visual particle effects match theme colors
- Statistics tracking per theme

### Metrics Dashboard

Access comprehensive training analytics:

#### Available Metrics

**Episode Metrics:**
- Total Episodes: Number of games played by AI
- Current Reward: Score in the current game
- Average Reward: Moving average (last 100 episodes)
- Best Score: Highest score achieved
- Best Episode: Episode number where best score occurred

**Learning Metrics:**
- Training Time: Total time spent in training mode
- Epsilon: Current exploration rate (1.0 = random, 0.01 = learned)
- Success Rate: Percentage of games scoring 10+ points
- Average Length: Mean number of steps per episode

**Visualization:**
- Real-time reward graph
- Moving averages (10-episode and 50-episode windows)
- Episode trends over time
- Best score indicator line

#### Export Options
- **Export CSV**: Download metrics data in spreadsheet format
- **Export JSON**: Download raw training data for analysis
- **Clear Data**: Reset all metrics (requires confirmation)

---

## Developer Guide

### Architecture Overview

The Snake Game is built as a single-page application using vanilla JavaScript with no external dependencies. The architecture follows a modular pattern with clear separation of concerns:

```
snake.html
├── HTML Structure (DOM elements)
├── CSS Styling (embedded)
└── JavaScript Logic
    ├── Game Engine
    ├── AI System (DQN)
    ├── Rendering System
    ├── UI Controllers
    └── Utilities
```

### Core Components

#### 1. Game Engine

**Main Game Loop**
```javascript
function update() {
    // 1. Handle AI or player input
    // 2. Update snake position
    // 3. Check collisions
    // 4. Update score and game state
    // 5. Trigger rendering
}
```

**Key Functions:**
- `startGame()`: Initializes game loop with theme-specific speed
- `restartGame()`: Resets game state while preserving theme
- `gameOver()`: Handles game end logic for different modes
- `update()`: Main game tick (movement, collision, scoring)
- `draw()`: Renders all visual elements to canvas

**Game State Management:**
```javascript
// Core state variables
let snake = [{x: 10, y: 10}];  // Snake body segments
let dx = 0, dy = 0;              // Direction vectors
let food = {x: 15, y: 15};       // Food position
let score = 0;                   // Current score
let gameRunning = false;         // Game active flag
let currentGameMode = 'human';   // 'human', 'demo', or 'train'
```

#### 2. Neural Network Implementation

**NeuralNetwork Class**
- Implements a 2-layer feed-forward network
- Input layer: 8 neurons (state features)
- Hidden layer: 16 neurons (configurable)
- Output layer: 4 neurons (action Q-values)
- Activation: ReLU for hidden layer, linear for output

**Key Methods:**
```javascript
forward(inputs)          // Forward propagation
backward(inputs, targets) // Backpropagation with gradient descent
predict(inputs)          // Get action Q-values
updateWeights(gradient, learningRate) // Weight updates
```

**Network Architecture:**
```
Input (8) → Hidden (16) → Output (4)
   ↓           ↓            ↓
[State]    [ReLU]      [Q-values]
```

#### 3. DQN Agent System

**DQNAgent Class**
- Implements Deep Q-Learning with experience replay
- Epsilon-greedy exploration strategy
- Training metrics tracking

**Core Parameters:**
```javascript
gamma = 0.95           // Discount factor (future reward importance)
epsilon = 1.0          // Exploration rate (decays over time)
epsilonDecay = 0.995   // Decay rate per episode
epsilonMin = 0.01      // Minimum exploration
learningRate = 0.001   // Neural network learning rate
batchSize = 32         // Experience replay batch size
```

**Key Methods:**
```javascript
getState()              // Extract 8-dimensional state vector
act(state)              // Choose action (ε-greedy)
remember(state, action, reward, nextState, done) // Store experience
replay()                // Train on batch from experience buffer
save()/load()           // Model persistence (localStorage)
```

**State Representation:**
The AI observes 8 features:
1. Distance to left wall (normalized)
2. Distance to right wall (normalized)
3. Distance to top wall (normalized)
4. Distance to bottom wall (normalized)
5. Food X direction (-1, 0, or 1)
6. Food Y direction (-1, 0, or 1)
7. Danger ahead (0 or 1)
8. Current direction (0-3 encoded)

#### 4. A* Pathfinding

**AStarPathfinder Class**
- Provides optimal path finding to food
- Used for AI demo mode and decision guidance

**Implementation:**
```javascript
findPath(start, goal, obstacles)
// Returns: Array of {x, y} coordinates
// Uses: Manhattan distance heuristic
```

**Algorithm Features:**
- Open/closed set management
- Priority queue using f-score (g + h)
- Obstacle avoidance (snake body)
- Grid-based navigation

#### 5. Theme System

**Theme Structure:**
```javascript
{
    id: 'theme-name',
    name: 'Display Name',
    bgColor: '#000000',
    foodColor: '#ff0000',
    headColor: '#00ff00',
    bodyColor: '#00aa00',
    gridColor: 'rgba(255, 255, 255, 0.1)',
    speed: 1.0,        // Multiplier (0.8 = slower, 2.0 = faster)
    startLength: 3     // Initial snake length
}
```

**Theme Management:**
- `selectTheme(themeId)`: Apply theme and restart game
- `saveThemePreference(themeId)`: Persist to localStorage
- `loadThemePreference()`: Restore saved theme
- `updateThemeStats()`: Display theme characteristics

#### 6. Metrics Dashboard

**Data Collection:**
```javascript
episodeHistory = [
    {
        episode: number,
        score: number,
        steps: number,
        epsilon: number,
        timestamp: number
    }
]
```

**Visualization:**
- Canvas-based graph rendering
- Moving averages (MA10, MA50)
- Real-time updates during training
- Export functionality (CSV/JSON)

### AI Implementation

#### Reinforcement Learning Flow

```
1. Environment Step
   ├── Current State → Neural Network
   ├── Network outputs Q-values for actions
   └── Select action (ε-greedy)

2. Execute Action
   ├── Snake moves in chosen direction
   ├── Check collision/food
   └── Calculate reward

3. Store Experience
   └── (state, action, reward, next_state, done) → Replay Buffer

4. Learn (if enough experiences)
   ├── Sample batch from buffer
   ├── Calculate target Q-values
   ├── Backpropagate error
   └── Update network weights

5. Update Exploration
   └── Decay epsilon (less exploration over time)
```

#### Reward Structure

```javascript
// Positive rewards
if (ateFood) reward = +10;

// Negative rewards
if (died) reward = -10;

// Step penalty (encourages efficiency)
reward -= 0.01;
```

#### Training Process

**Phase 1: Exploration (Episodes 1-100)**
- High epsilon (1.0 → 0.6)
- Mostly random actions
- Building experience buffer

**Phase 2: Learning (Episodes 100-500)**
- Medium epsilon (0.6 → 0.2)
- Balancing exploration/exploitation
- Significant performance gains

**Phase 3: Exploitation (Episodes 500+)**
- Low epsilon (0.2 → 0.01)
- Using learned strategy
- Fine-tuning performance

### Code Structure

#### File Organization

```
snake.html (2473 lines)
├── HTML (lines 1-50)
│   └── Basic page structure and UI elements
├── CSS (lines 51-750)
│   ├── Layout and positioning
│   ├── Neon/cyberpunk styling
│   ├── Animations and effects
│   └── Responsive design
└── JavaScript (lines 751-2473)
    ├── Configuration & State (751-850)
    ├── Utility Functions (851-900)
    ├── Theme System (901-980)
    ├── Classes
    │   ├── TrailParticle (980-1015)
    │   ├── NeuralNetwork (1017-1157)
    │   ├── DQNAgent (1158-1473)
    │   └── AStarPathfinder (1622-1725)
    ├── Game Logic (1474-2090)
    │   ├── Sound generation
    │   ├── Canvas management
    │   ├── Input handling
    │   ├── Game loop
    │   └── Rendering
    ├── AI Control (2091-2161)
    └── Metrics System (2162-2473)
```

#### Key Design Patterns

**1. State Management**
- Centralized game state variables
- Immutable state updates
- Clear state transitions

**2. Event-Driven Architecture**
- Keyboard event listeners
- UI button click handlers
- Game loop interval management

**3. Object-Oriented Design**
- Classes for complex components (NN, DQN, Pathfinder)
- Encapsulation of related functionality
- Clear public interfaces

**4. Functional Programming**
- Pure functions for calculations
- Function composition
- Minimal side effects

#### Performance Considerations

**Optimization Techniques:**
1. **Canvas Rendering**
   - Single canvas for all drawing
   - Minimal redraws
   - Layer composition

2. **Game Loop**
   - Variable speed based on training mode
   - Efficient collision detection
   - Throttled updates

3. **AI Training**
   - Experience replay buffer (max 10,000 samples)
   - Batch processing (32 samples)
   - Selective learning (not every frame)

4. **Memory Management**
   - Particle system culling
   - History pruning (max 1000 episodes)
   - localStorage limits

### Contributing

#### Getting Started
1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature-name`
3. Make your changes
4. Test thoroughly in multiple browsers
5. Commit with clear messages
6. Push and create a pull request

#### Code Style Guidelines
- Use 4-space indentation
- Clear, descriptive variable names
- Comment complex logic
- Maintain existing code structure
- Test across browsers

#### Areas for Contribution
- New visual themes
- Alternative AI algorithms (A3C, PPO, etc.)
- Multiplayer features
- Additional game modes
- Performance optimizations
- Mobile touch controls
- Accessibility improvements

#### Testing Checklist
- [ ] Game runs in Chrome, Firefox, Safari
- [ ] All three modes work correctly
- [ ] AI training converges
- [ ] Metrics dashboard updates
- [ ] Theme switching works
- [ ] localStorage persistence
- [ ] No console errors

#### Pull Request Template
```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Documentation update
- [ ] Performance improvement

## Testing
Describe testing performed

## Screenshots
If applicable, add screenshots
```

---

## Technical Details

### Browser Compatibility
- Chrome 90+
- Firefox 88+
- Safari 14+
- Edge 90+

### Storage Requirements
- localStorage: ~2-5 MB for AI models and metrics
- No server-side storage required

### Performance Metrics
- 60 FPS gameplay (human mode)
- Up to 1000 FPS (training mode at 100x speed)
- Neural network inference: <1ms
- Training batch: 2-5ms

---

## License

This project is part of r3colab public collaboration space.

## Credits

Created by the r3colab community.
fernicar was here

---

## Support

For issues, feature requests, or questions:
- Open an issue on GitHub
- Check existing documentation
- Review code comments for implementation details
