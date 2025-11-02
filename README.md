# r3colab

Public Colab - A collection of interactive web projects and experiments.

## Projects

### Snake Game - AI Learning Edition

A modern implementation of the classic Snake game featuring an integrated Deep Q-Learning Network (DQN) for AI training and gameplay. Built with HTML5, CSS3, and vanilla JavaScript.

**Quick Start:**
1. Open `snake.html` in your web browser
2. Choose your game mode and start playing!

[Play the game](snake.html)

---

## User Guide

### Getting Started

#### Playing the Game (Human Mode)

1. **Launch the Game**: Open `snake.html` in any modern web browser
2. **Start Playing**: Press any arrow key (↑ ↓ ← →) to begin
3. **Objective**: Control the snake to eat red food pellets and grow longer
4. **Avoid**: Hitting the walls or running into your own body
5. **Score**: Earn points for each food item consumed (points vary by theme)

#### Game Controls

- **Arrow Keys (↑ ↓ ← →)**: Control snake direction
- **M Key**: Toggle the AI Training Metrics Dashboard
- **Restart Button**: Reset the game at any time

### Features Walkthrough

#### 1. Game Modes

The game offers three distinct modes accessible from the AI Controls panel:

**👤 Human Play Mode** (Default)
- Full manual control via arrow keys
- Track your high score with local storage persistence
- Challenge yourself to beat your personal best

**🎮 AI Demo Mode**
- Watch the AI play using its trained neural network
- AI uses epsilon-greedy policy for decision making
- Automatically restarts after game over
- Great for seeing the AI's current skill level

**🧠 AI Training Mode**
- Train the neural network through reinforcement learning
- Adjustable training speed (1x to 100x)
- Real-time statistics showing learning progress
- Automatically plays continuous games to improve

#### 2. Snake Themes

Four unique snake themes, each with different gameplay characteristics:

**🐍 Cyber Viper** (Default)
- **Speed**: 100% (Normal)
- **Point Multiplier**: 1.0x (10 points per food)
- **Trail Effects**: None
- **Starting Length**: 1 segment
- **Best For**: Balanced gameplay

**⚡ Neon Speedster**
- **Speed**: 130% (Faster movement)
- **Point Multiplier**: 0.8x (8 points per food)
- **Trail Effects**: Yes (electric particle trail)
- **Starting Length**: 1 segment
- **Best For**: High-speed action and reflex testing

**💎 Data Collector**
- **Speed**: 75% (Slower movement)
- **Point Multiplier**: 1.5x (15 points per food)
- **Trail Effects**: None
- **Starting Length**: 2 segments
- **Best For**: High score runs and strategic play

**👻 Quantum Ghost**
- **Speed**: 110% (Slightly faster)
- **Point Multiplier**: 1.2x (12 points per food)
- **Trail Effects**: Yes (ethereal particle trail)
- **Starting Length**: 1 segment
- **Best For**: Bonus scoring with moderate challenge

**Point Multiplier Mechanic**: Each theme applies a multiplier to the base 10 points earned per food item. This allows different themes to offer varying risk-reward tradeoffs - faster themes earn fewer points while slower themes can achieve higher scores.

**Trail Particle Effects**: Themes with trail effects (⚡ Neon Speedster and 👻 Quantum Ghost) leave animated particle trails behind the snake's movement, providing visual feedback and enhancing the cyberpunk aesthetic. Trail particles fade over time and do not affect gameplay.

**Theme Selection**: Click any theme card in the left panel to switch themes. Your preference is automatically saved.

#### 3. AI Training System

The game includes a sophisticated Deep Q-Learning Network that learns to play Snake through trial and error.

**Starting AI Training:**
1. Click **🧠 AI Training** in the AI Controls panel
2. Adjust the **Training Speed** slider (1x-100x) to control how fast the AI learns
3. The AI will play continuously, learning from each game

**Training Speed Explained:**
- **1x-10x**: Watch the AI learn in real-time
- **10x-50x**: Accelerated training with visible gameplay
- **50x-100x**: Maximum speed training for quick results

**Understanding AI Progress:**
- **Games Played**: Total number of training episodes completed
- **Average Score**: Mean score across all games
- **Best AI Score**: Highest score achieved during training
- **Epsilon**: Exploration rate (1.0 = random, 0.05 = mostly using learned strategy)

#### 4. Metrics Dashboard

Press **M** to open the comprehensive metrics dashboard showing:

**Real-Time Statistics:**
- Total Episodes: Number of games played
- Current Reward: Score from the active game
- Average Reward: Mean score over last 100 episodes
- Best Score: All-time highest score with episode number
- Training Time: Total time spent in training mode
- Epsilon: Current exploration/exploitation balance
- Success Rate: Percentage of games scoring ≥50 points
- Avg Episode Length: Mean number of steps per game

**Reward Progression Graph:**
- Visual chart showing score trends over episodes
- Multiple moving averages (10-episode and 50-episode)
- Best score reference line
- Episode-by-episode reward history

**Export Options:**
- **📥 Export CSV**: Download training data as spreadsheet
- **📥 Export JSON**: Export complete metrics with metadata
- **🗑️ Clear Data**: Reset metrics (keeps trained model)

#### 5. Model Management

**💾 Save Model**
- Saves the current neural network to browser localStorage
- Preserves all training progress, statistics, and episode history
- Useful for backing up a well-trained AI

**📂 Load Model**
- Restores a previously saved neural network
- Loads all associated statistics and training history
- Continues training from where you left off

**🔄 Reset Model**
- Completely resets the neural network to untrained state
- Clears all training progress and statistics
- Use this to start fresh training from scratch

### Tips and Strategies

**For Human Players:**
- Plan your path ahead - don't just chase the food
- Use the edges strategically but carefully
- Create patterns to avoid trapping yourself
- Different themes offer different challenges - experiment!

**For AI Training:**
- Train at high speed (50x+) for 500-1000 episodes initially
- Watch epsilon decrease to see the AI shift from exploration to exploitation
- Best results typically appear after 1000+ training episodes
- Use Demo mode to evaluate the trained AI's performance
- Save your model before resetting or closing the browser

### Troubleshooting

**Game won't start:**
- Ensure JavaScript is enabled in your browser
- Try refreshing the page
- Check browser console for errors

**AI not improving:**
- Training requires many episodes (1000+) to see significant improvement
- Try increasing training speed
- Reset the model if it seems stuck in bad behaviors

**Model won't save:**
- Check that localStorage is enabled in your browser
- Ensure you have sufficient storage space
- Try clearing old browser data

---

## Technical Information

**Technology Stack:**
- HTML5 Canvas for rendering
- Vanilla JavaScript (no external dependencies)
- Web Audio API for sound effects
- LocalStorage for persistence

**Browser Compatibility:**
- Chrome/Edge (recommended)
- Firefox
- Safari
- Any modern browser with HTML5 Canvas and ES6 support

**Performance:**
- Optimized for smooth gameplay at all training speeds
- Efficient neural network implementation
- Minimal memory footprint

---

## Developer Documentation

### AI Architecture

**Deep Q-Learning Network (DQN) Parameters:**
- **Network Architecture**: 22 inputs → 128 hidden neurons → 4 outputs (up, down, left, right)
- **Discount Factor (gamma)**: 0.95 - balances immediate vs future rewards
- **Learning Rate**: 0.0005 - controls weight update step size
- **Exploration Rate (epsilon)**: Starts at 1.0, decays to minimum of 0.05
- **Epsilon Decay**: 0.999 per episode
- **Experience Replay**: 10,000 memory size, batch size of 32
- **Target Network Update**: Every 10 episodes

**Reward Structure:**
- **Food Consumption**: +10 points (encourages food collection)
- **Movement Penalty**: -0.1 per step (encourages efficiency and shorter paths)
- **Death Penalty**: -10 points (discourages collisions with walls and self)

This reward structure ensures the AI learns to balance food collection with efficient pathfinding while avoiding dangerous situations.

For detailed technical documentation, architecture overview, and contribution guidelines, see [DEVELOPER.md](DEVELOPER.md).

---

## Credits

Developed as part of the r3colab public collaboration project.

**Contributors:**
- fernicar

---

## License

Open source - feel free to learn from, modify, and share!
