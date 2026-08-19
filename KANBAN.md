# Flappy Bird - KanBan Project Structure

## Project: FlappyBench Implementation
**Target**: Single-file HTML5 Flappy Bird clone  
**Model**: DavidAU/Qwen3.5-9B-GGUF (9B, Q8_0) via mlx-serve  
**Decomposition Strategy**: Each card → separate prompt for small models

---

## BOARD: Flappy Bird Development

### BACKLOG (Ready for Decomposition)

| Card ID | Title | Description | Est. Tokens | Complexity |
|---------|-------|-------------|-------------|------------|
| **F-01** | Project Scaffold | HTML5 boilerplate, canvas setup, CSS reset | 500 | Low |
| **F-02** | Game State Machine | START/PLAYING/GAMEOVER state management | 800 | Medium |
| **F-03** | Bird Physics | Gravity, velocity, rotation, flap mechanic | 1000 | Medium |
| **F-04** | Parallax Background | 3-layer scrolling (hills, clouds, ground) | 800 | Medium |
| **F-05** | Pipe System | Spawn, movement, gap generation, cleanup | 1000 | Medium |
| **F-06** | Collision Detection | AABB bird-pipe, floor/ceiling bounds | 800 | Medium |
| **F-07** | Scoring System | Point on pass, high score persistence | 600 | Low |
| **F-08** | UI Screens | Start screen, Game Over overlay, buttons | 700 | Low |
| **F-09** | Input Handling | Spacebar, click, touch → flap/start/restart | 600 | Low |
| **F-10** | Render Loop | requestAnimationFrame, draw order, optimization | 500 | Low |
| **F-11** | Polish & Assets | Bird drawing (eye, beak, wing), pipe caps, ground pattern | 1000 | Medium |
| **F-12** | Integration & Testing | Assemble all modules, fix bugs, verify playability | 1500 | High |

---

## SPRINT 1: Core Mechanics (F-01 to F-06)

### F-01: Project Scaffold
**Prompt Template**:
> Create an HTML5 boilerplate for a Flappy Bird game:
> - Single HTML file with embedded CSS/JS
> - Canvas 320x480, centered on screen
> - CSS reset, no external dependencies
> - Dark sky background (#70c5ce)
> - Score display top-center
> **Output**: Complete boilerplate only, no game logic

**Sub-prompts for tiny models**:
1. HTML5 doctype + head + meta tags
2. Canvas element with ID, width/height
3. CSS reset + canvas styling
4. Score UI element

---

### F-02: Game State Machine
**Prompt Template**:
> Implement a game state machine with three states:
> - `START`: Show start screen, wait for input
> - `PLAYING`: Run game logic, update/draw
> - `GAMEOVER`: Show overlay with score, high score, restart option
> - Transitions: START→PLAYING (input), PLAYING→GAMEOVER (collision), GAMEOVER→PLAYING (restart)
> **Output**: State variable, transition functions, state checks in main loop

**Sub-prompts**:
1. State constants + current state variable
2. `startGame()` function - reset all, set PLAYING
3. `gameOver()` function - set GAMEOVER, update high score
4. Main loop state gate: `if(state!=='PLAYING') return;`

---

### F-03: Bird Physics
**Prompt Template**:
> Create bird physics object with:
> - Properties: x, y, radius, velocity, rotation
> - `flap()`: velocity = -7, rotation = -0.5 (upward tilt)
> - Gravity: +0.35 per frame applied to velocity
> - Position update: y += velocity
> - Rotation: maps velocity to -0.5 (up) to 1.2 (down) radians
> - Clamp rotation between bounds
> **Output**: Bird object + flap() + updateBird() function

**Sub-prompts**:
1. Bird object literal with initial values
2. `flap()` function
3. `updateBird()` - gravity, position, rotation mapping
4. Rotation clamp helper

---

### F-04: Parallax Background
**Prompt Template**:
> Create 3-layer parallax background:
> - **Hills** (back): 4 segments, speed 0.5, green triangles
> - **Clouds** (mid): 3 clouds, speed 0.2-0.5, white circles
> - **Ground** (front): scrolling pattern, speed 2 (matches pipes)
> - All wrap around: when x < -width, x = canvas.width
> - Store as arrays in `bg = { hills: [], clouds: [] }`
> **Output**: bg object init + `updateBackground()` + `drawBackground()`

**Sub-prompts**:
1. Hills array initialization (4 segments)
2. Clouds array initialization (3 clouds)
3. `updateBackground()` - move each layer, wrap logic
4. `drawBackground()` - render hills (triangles) + clouds (circles)

---

### F-05: Pipe System
**Prompt Template**:
> Implement pipe obstacle system:
> - Pipe object: x, topHeight, gap(110), passed flag
> - `spawnPipe()`: random topHeight (50 to H-gap-160), x = canvas.width
> - Spawn every 90 frames
> - Move: x -= 2 per frame
> - Remove when x < -60
> - Track `passed` for scoring
> **Output**: pipes array + spawnPipe() + updatePipes()

**Sub-prompts**:
1. Pipe object structure
2. `spawnPipe()` with random gap position
3. `updatePipes()` - movement, cleanup, pass detection
4. Score increment on pass

---

### F-06: Collision Detection
**Prompt Template**:
> Implement AABB collision detection:
> - Bird: circle (x, y, radius)
> - Pipe: two rectangles (top: 0 to topHeight, bottom: top+gap to H-20)
> - Check: bird circle overlaps pipe rects
> - Also: floor (y+radius > H-20) and ceiling (y-radius < 0)
> - On collision: call `gameOver()`
> **Output**: `checkCollisions()` function called in update loop

**Sub-prompts**:
1. Bird-pipe AABB check (circle vs rect)
2. Floor collision check
3. Ceiling collision check
4. `checkCollisions()` integrating all

---

## SPRINT 2: Scoring & UI (F-07 to F-10)

### F-07: Scoring System
**Prompt Template**:
> Add scoring with localStorage persistence:
> - Increment score when bird passes pipe (pipe.x + 52 < bird.x)
> - Update DOM score display in real-time
> - On game over: if score > highScore, save to localStorage
> - Load highScore on init from localStorage
> - Display both current and best score
> **Output**: Score logic + localStorage read/write + DOM updates

**Sub-prompts**:
1. Score variable + DOM element binding
2. Increment logic in pipe update
3. High score load on init
4. High score save on game over
5. DOM updates for both scores

---

### F-08: UI Screens
**Prompt Template**:
> Create overlay screens using absolute-positioned divs:
> - **Start Screen**: "Flappy Bird" title, "Play Now" button → startGame()
> - **Game Over Screen**: "Game Over", final score, best score, "Play Again" button → startGame()
> - Both: centered, semi-transparent black background, white text
> - CSS classes: `.screen`, `.screen.hidden`, button styling
> **Output**: HTML for screens + CSS + show/hide logic

**Sub-prompts**:
1. Start screen HTML + CSS
2. Game over screen HTML + CSS
3. `showScreen(id)` / `hideScreen(id)` helpers
4. Button onclick handlers

---

### F-09: Input Handling
**Prompt Template**:
> Unified input handler for all interactions:
> - **Keydown**: Space only → flap (PLAYING) or startGame (START/GAMEOVER)
> - **Mousedown** on canvas → same as keydown
> - **Touchstart** on canvas → same, preventDefault for scroll
> - Ignore non-Space keys
> - Prevent default on all handled events
> **Output**: Single `handleInput(e)` + 3 event listeners

**Sub-prompts**:
1. Keydown handler with Space check
2. Mousedown handler
3. Touchstart handler with preventDefault
4. Event listener registration

---

### F-10: Render Loop
**Prompt Template**:
> Main game loop using requestAnimationFrame:
> - `loop()`: update() → draw() → requestAnimationFrame(loop)
> - `update()`: state gate, bird, pipes, background, collisions
> - `draw()`: clear, background, pipes, ground, bird, UI
> - Draw order: sky → hills → clouds → pipes → ground → bird
> - Bird: save context, translate/rotate, draw ellipse+eye+beak+wing, restore
> **Output**: Complete loop + draw functions

**Sub-prompts**:
1. `loop()` with RAF
2. `update()` orchestration
3. `draw()` with proper layer order
4. Bird drawing (transform + shapes)
5. Pipe drawing (rects + caps)
6. Ground drawing (scrolling pattern)

---

## SPRINT 3: Polish & Integration (F-11 to F-12)

### F-11: Polish & Assets
**Prompt Template**:
> Draw procedural graphics (no external images):
> - **Bird**: Yellow ellipse, white eye with black pupil, orange beak triangle, brown wing ellipse (animated with sin)
> - **Pipes**: Green rects + darker caps (wider, shorter)
> - **Ground**: Yellow base + brown scrolling lines
> - **Hills**: Green triangles
> - **Clouds**: 3 overlapping white circles with alpha
> **Output**: Complete drawing code for all entities

**Sub-prompts**:
1. Bird drawing (body, eye, pupil, beak, animated wing)
2. Pipe drawing (top/bottom rects + caps)
3. Ground pattern (scrolling lines)
4. Hills (triangles)
5. Clouds (3-circle clusters)

---

### F-12: Integration & Testing
**Prompt Template**:
> Assemble all modules into single HTML file:
> - Combine: scaffold + state + bird + bg + pipes + collision + score + UI + input + loop + draw
> - Fix: variable scoping, execution order, DOM ready
> - Test: game starts, flaps, pipes spawn, score increments, collision ends, restart works
> - Verify: localStorage persists, touch works, no console errors
> **Output**: Final playable single-file HTML

**Sub-prompts**:
1. Module concatenation order
2. Variable hoisting/initialization fixes
3. DOMContentLoaded wrapper
4. Full playthrough test checklist
5. Bug fix: double loop, canvas sizing, state transitions

---

## DECOMPOSITION PROMPT TEMPLATE

For each card, use this meta-prompt with a small model:

```
ROLE: You are a code generation agent for Flappy Bird.
TASK: Implement ONLY: [CARD TITLE]
CONTEXT: Part of single-file HTML5 Flappy Bird (320x480 canvas)
CONSTRAINTS:
- Vanilla JS only, no libraries
- Output ONLY the requested code section
- No markdown, no explanations
- Follow exact API from spec above
- Target: 500-1500 tokens max

[INSERT CARD SPEC FROM ABOVE]
```

---

## ORCHESTRATOR PROMPT (for KanBan Prompt Orchestrator)

```
You are the KanBan Prompt Orchestrator for FlappyBench.

INPUT: Board state (which cards are DONE, IN_PROGRESS, BLOCKED)
OUTPUT: Next prompt(s) to send to worker model(s)

RULES:
1. Only assign cards with all dependencies DONE
2. Max 3 cards IN_PROGRESS at once
3. Break cards >1000 tokens into sub-prompts
4. Track token budget per model (small model = 2048 context)
5. On failure: decompose further, retry with more context
6. On success: mark DONE, update dependencies, assign next

CURRENT BOARD:
[BOARD STATE JSON]

NEXT ACTION: [ASSIGN CARD / DECOMPOSE / RETRY / INTEGRATE]
```