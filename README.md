# Pulsar: The Memory Maze

Pulsar: The Memory Maze is a high-performance, browser-based arcade survival game. Players navigate dynamically generated perfect mazes under strict time cycles, alternating between moments of full visibility and complete darkness. The project is built entirely from scratch using pure Vanilla JavaScript, HTML5, and CSS3, demonstrating advanced DOM manipulation, custom physics, and decoupled state management without the use of external game engines or frameworks.

## Technical Architecture

The codebase strictly adheres to object-oriented programming principles and modular design using ECMAScript 6 (ES6) classes under strict mode ("use strict"). 

### 1. Centralized Event Management
Communication between distinct systems (such as the timer, UI, and game engine) is handled by a custom `EventBus` class extending the native `EventTarget`. This allows for a completely decoupled architecture where game loop events (e.g., phase transitions) are broadcasted and listened to asynchronously.

### 2. Level Generation (MazeGenerator)
The grid structure is calculated using an Iterative Randomized Depth-First Search (DFS) algorithm. 
* Perfect Maze Construction: The algorithm guarantees a perfect maze with a single valid path from start to finish and no isolated sections.
* Viewport-Aware Aspect Ratio: During configuration, the generator evaluates the window dimensions to determine the optimal grid layout, creating tall matrices for mobile portrait orientations and wide matrices for desktop monitors.

### 3. Dual Timer System
Time management is split into two independent engines to separate visual mechanics from player scoring:
* TimerEngine: Operates the strict 10-second environmental cycle (2,000ms Flash phase for memorization, 8,000ms Dash phase in the dark). It uses `requestAnimationFrame` to broadcast progress ratio updates to the UI.
* StageTimer: A high-precision elapsed time tracker that counts raw milliseconds strictly for score calculation. It only pauses when a level is completed, preventing players from exploiting collisions to pause the timer.

### 4. Custom Physics and Collision (Player)
Instead of HTML5 Canvas, the game utilizes pure DOM elements for rendering. The `Player` class implements a continuous velocity-vector system backed by Axis-Aligned Bounding Box (AABB) collision detection.
* Per-Axis Evaluation: Movement vectors are tested independently on the X and Y axes against wall boundaries. This allows for smooth wall-sliding logic rather than halting all momentum upon diagonal collisions.
* Spatial Mapping: The player's logical coordinates are translated into CSS `transform: translate()` values for optimal GPU-accelerated rendering.

### 5. Input Translation Layers
The control logic seamlessly supports both desktop and touch environments simultaneously:
* KeyboardController: Maps WASD and Arrow key events into normalized 2D movement vectors.
* VirtualJoystick: A custom touch-input interface that dynamically anchors to the user's initial touch position (restricted to the lower 58% of the screen). It calculates drag distance and angle, clamping the output to a maximum radius of 50 pixels to generate smooth analog vectors.

### 6. Scoring and Progression
The `ScoreManager` handles a 5-level escalation curve.
* Algorithm: Level scores are calculated based on completion speed using the formula: Base Points (5000) minus Elapsed Seconds multiplied by a Time Penalty (100).
* Penalty Mechanics: Players start with 3 lives per game. Colliding with a wall during the dark phase strips a life, triggers a 750ms invulnerability window, and initiates a camera-shake sequence.

### 7. Core Mechanics and Power-ups
* Dynamic Spotlight: During the dark phase, visibility is restricted to a small radius around the player. This is achieved via a CSS `radial-gradient` mask controlled by CSS Custom Properties (`--spot-x`, `--spot-y`) updated in real-time by the rendering loop.
* Manual Pulse: Players are given 3 manual flash charges per game. Triggering a pulse (via Spacebar or the UI button) bypasses the standard cycle, illuminating the maze for exactly 5,000ms without affecting the underlying ambient timer.

## Technology Stack

The application relies entirely on native web APIs:
* HTML5: Semantic structure and touch-action suppression to prevent native mobile scrolling interference.
* CSS3: Advanced visual processing utilizing `box-shadow` for intense neon bloom effects, CSS variables for real-time masking, and hardware-accelerated animations (`will-change: transform`) on high-volatility layers like the player entity and screen-shake wrapper.
* Vanilla JavaScript (ES6+): Drives the game loop, algorithm generation, event delegation, and DOM updates via DocumentFragments to minimize reflow and repaint costs.

## Deployment and Execution

The game is packaged as a single, self-contained `index.html` file. It requires no build tools, bundlers, or local servers. 

To run the project, simply open the file in any modern, standards-compliant web browser.
