<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Snake Pro | Aditya Raj</title>
<link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&family=Orbitron:wght@400;700;900&display=swap" rel="stylesheet">
<style>
  :root {
    --neon-green: #00ff88;
    --neon-blue: #00cfff;
    --neon-pink: #ff2d78;
    --neon-yellow: #ffe600;
    --bg: #050a0f;
    --panel: #0a1520;
    --grid: #0d1f2d;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    background: var(--bg);
    color: #fff;
    font-family: 'Orbitron', sans-serif;
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    overflow: hidden;
  }

  /* Animated background grid */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image:
      linear-gradient(rgba(0,255,136,0.03) 1px, transparent 1px),
      linear-gradient(90deg, rgba(0,255,136,0.03) 1px, transparent 1px);
    background-size: 40px 40px;
    animation: gridMove 20s linear infinite;
    pointer-events: none;
  }

  @keyframes gridMove {
    0% { background-position: 0 0; }
    100% { background-position: 40px 40px; }
  }

  .title {
    font-family: 'Press Start 2P', monospace;
    font-size: clamp(18px, 4vw, 32px);
    color: var(--neon-green);
    text-shadow: 0 0 10px var(--neon-green), 0 0 30px var(--neon-green), 0 0 60px rgba(0,255,136,0.3);
    margin-bottom: 6px;
    letter-spacing: 4px;
    animation: flicker 4s infinite;
  }

  @keyframes flicker {
    0%, 95%, 100% { opacity: 1; }
    96% { opacity: 0.7; }
    97% { opacity: 1; }
    98% { opacity: 0.6; }
  }

  .subtitle {
    font-size: 9px;
    color: var(--neon-blue);
    letter-spacing: 6px;
    text-transform: uppercase;
    margin-bottom: 20px;
    opacity: 0.7;
  }

  .game-wrapper {
    display: flex;
    gap: 20px;
    align-items: flex-start;
  }

  .side-panel {
    width: 130px;
    display: flex;
    flex-direction: column;
    gap: 14px;
  }

  .panel-box {
    background: var(--panel);
    border: 1px solid rgba(0,255,136,0.2);
    border-radius: 8px;
    padding: 12px;
    text-align: center;
    position: relative;
    overflow: hidden;
  }

  .panel-box::before {
    content: '';
    position: absolute;
    top: 0; left: 0; right: 0;
    height: 2px;
    background: linear-gradient(90deg, transparent, var(--neon-green), transparent);
  }

  .panel-label {
    font-size: 7px;
    color: rgba(255,255,255,0.4);
    letter-spacing: 2px;
    margin-bottom: 6px;
  }

  .panel-value {
    font-family: 'Press Start 2P', monospace;
    font-size: 18px;
    color: var(--neon-green);
    text-shadow: 0 0 10px var(--neon-green);
  }

  .level-value { color: var(--neon-blue); text-shadow: 0 0 10px var(--neon-blue); }
  .best-value { color: var(--neon-yellow); text-shadow: 0 0 10px var(--neon-yellow); }

  .canvas-container {
    position: relative;
  }

  canvas {
    display: block;
    border: 2px solid rgba(0,255,136,0.3);
    border-radius: 4px;
    box-shadow: 0 0 30px rgba(0,255,136,0.1), inset 0 0 30px rgba(0,0,0,0.5);
  }

  /* Overlay */
  .overlay {
    position: absolute;
    inset: 0;
    background: rgba(5,10,15,0.92);
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    border-radius: 4px;
    transition: opacity 0.3s;
  }

  .overlay.hidden { display: none; }

  .overlay-title {
    font-family: 'Press Start 2P', monospace;
    font-size: 20px;
    margin-bottom: 16px;
  }

  .overlay-score {
    font-size: 11px;
    color: rgba(255,255,255,0.5);
    margin-bottom: 24px;
    letter-spacing: 2px;
  }

  .btn {
    font-family: 'Press Start 2P', monospace;
    font-size: 11px;
    padding: 12px 24px;
    background: transparent;
    border: 2px solid var(--neon-green);
    color: var(--neon-green);
    border-radius: 4px;
    cursor: pointer;
    letter-spacing: 2px;
    transition: all 0.2s;
    text-shadow: 0 0 8px var(--neon-green);
    box-shadow: 0 0 12px rgba(0,255,136,0.2);
    margin: 5px;
  }

  .btn:hover {
    background: var(--neon-green);
    color: #000;
    box-shadow: 0 0 25px rgba(0,255,136,0.6);
    text-shadow: none;
  }

  .controls {
    margin-top: 16px;
    display: flex;
    gap: 10px;
    align-items: center;
  }

  .controls-info {
    font-size: 7px;
    color: rgba(255,255,255,0.3);
    letter-spacing: 2px;
    text-align: center;
    margin-top: 10px;
  }

  .food-type-indicator {
    font-size: 7px;
    color: rgba(255,255,255,0.3);
    letter-spacing: 1px;
    margin-top: 4px;
  }

  /* Mobile controls */
  .mobile-controls {
    display: none;
    flex-direction: column;
    align-items: center;
    margin-top: 14px;
    gap: 4px;
  }

  .mobile-row {
    display: flex;
    gap: 4px;
  }

  .d-btn {
    width: 50px;
    height: 50px;
    background: var(--panel);
    border: 1px solid rgba(0,255,136,0.3);
    border-radius: 8px;
    color: var(--neon-green);
    font-size: 18px;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: all 0.1s;
    -webkit-tap-highlight-color: transparent;
    user-select: none;
  }

  .d-btn:active {
    background: rgba(0,255,136,0.15);
    box-shadow: 0 0 15px rgba(0,255,136,0.3);
  }

  @media (max-width: 600px) {
    .mobile-controls { display: flex; }
    .game-wrapper { flex-direction: column; align-items: center; }
    .side-panel { flex-direction: row; width: 100%; justify-content: center; }
    .side-panel .panel-box { flex: 1; }
  }

  .die-anim {
    animation: shake 0.4s ease;
  }
  @keyframes shake {
    0%,100%{transform:translateX(0)}
    20%{transform:translateX(-6px)}
    40%{transform:translateX(6px)}
    60%{transform:translateX(-4px)}
    80%{transform:translateX(4px)}
  }

  .game-over-title { color: var(--neon-pink); text-shadow: 0 0 20px var(--neon-pink); }
  .start-title { color: var(--neon-green); text-shadow: 0 0 20px var(--neon-green); }
  .pause-title { color: var(--neon-yellow); text-shadow: 0 0 20px var(--neon-yellow); }
</style>
</head>
<body>

<div class="title">🐍 SNAKE PRO</div>
<div class="subtitle">by Aditya Raj</div>

<div class="game-wrapper">
  <!-- Left Panel -->
  <div class="side-panel">
    <div class="panel-box">
      <div class="panel-label">SCORE</div>
      <div class="panel-value" id="scoreDisplay">0</div>
    </div>
    <div class="panel-box">
      <div class="panel-label">BEST</div>
      <div class="panel-value best-value" id="bestDisplay">0</div>
    </div>
    <div class="panel-box">
      <div class="panel-label">LEVEL</div>
      <div class="panel-value level-value" id="levelDisplay">1</div>
    </div>
    <div class="panel-box">
      <div class="panel-label">LENGTH</div>
      <div class="panel-value" id="lengthDisplay">3</div>
    </div>
  </div>

  <!-- Canvas -->
  <div class="canvas-container">
    <canvas id="gameCanvas" width="420" height="420"></canvas>

    <!-- Start Overlay -->
    <div class="overlay" id="startOverlay">
      <div class="overlay-title start-title">SNAKE PRO</div>
      <div class="overlay-score">USE ARROW KEYS OR WASD</div>
      <button class="btn" onclick="startGame()">▶ START GAME</button>
    </div>

    <!-- Game Over Overlay -->
    <div class="overlay hidden" id="gameOverOverlay">
      <div class="overlay-title game-over-title">GAME OVER</div>
      <div class="overlay-score" id="finalScore">SCORE: 0</div>
      <button class="btn" onclick="startGame()">↺ PLAY AGAIN</button>
    </div>

    <!-- Pause Overlay -->
    <div class="overlay hidden" id="pauseOverlay">
      <div class="overlay-title pause-title">PAUSED</div>
      <button class="btn" onclick="togglePause()">▶ RESUME</button>
    </div>
  </div>

  <!-- Right Panel -->
  <div class="side-panel">
    <div class="panel-box">
      <div class="panel-label">FOOD</div>
      <div class="panel-value" id="foodEmoji">🍎</div>
      <div class="food-type-indicator" id="foodPts">+10 PTS</div>
    </div>
    <div class="panel-box" style="cursor:pointer" onclick="togglePause()" id="pauseBtn">
      <div class="panel-label">ACTION</div>
      <div class="panel-value" style="font-size:22px">⏸</div>
      <div class="food-type-indicator">PAUSE</div>
    </div>
    <div class="panel-box">
      <div class="panel-label">SPEED</div>
      <div class="panel-value" id="speedDisplay" style="font-size:14px">NORM</div>
    </div>
  </div>
</div>

<div class="controls-info">ARROW KEYS / WASD · P TO PAUSE · SPACE TO START</div>

<!-- Mobile Controls -->
<div class="mobile-controls">
  <div class="mobile-row">
    <div class="d-btn" ontouchstart="setDir(0,-1)">▲</div>
  </div>
  <div class="mobile-row">
    <div class="d-btn" ontouchstart="setDir(-1,0)">◀</div>
    <div class="d-btn" ontouchstart="setDir(0,1)">▼</div>
    <div class="d-btn" ontouchstart="setDir(1,0)">▶</div>
  </div>
</div>

<script>
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');
const COLS = 21, ROWS = 21;
const CELL = canvas.width / COLS;

let snake, dir, nextDir, food, score, best, level, gameLoop, running, paused, frame;

const FOODS = [
  { emoji: '🍎', pts: 10, color: '#ff4444' },
  { emoji: '🍊', pts: 15, color: '#ff9900' },
  { emoji: '💎', pts: 25, color: '#00cfff' },
  { emoji: '⭐', pts: 30, color: '#ffe600' },
  { emoji: '🔥', pts: 50, color: '#ff2d78' },
];

let currentFood = FOODS[0];
const SPEEDS = [150, 130, 110, 90, 70, 55, 40];

function init() {
  snake = [{x:10,y:10},{x:9,y:10},{x:8,y:10}];
  dir = {x:1,y:0};
  nextDir = {x:1,y:0};
  score = 0;
  level = 1;
  frame = 0;
  running = false;
  paused = false;
  placeFood();
  updateUI();
  drawGame();
}

function placeFood() {
  // Pick random food type; rarer foods appear less often
  const roll = Math.random();
  if (roll < 0.5) currentFood = FOODS[0];
  else if (roll < 0.75) currentFood = FOODS[1];
  else if (roll < 0.88) currentFood = FOODS[2];
  else if (roll < 0.95) currentFood = FOODS[3];
  else currentFood = FOODS[4];

  document.getElementById('foodEmoji').textContent = currentFood.emoji;
  document.getElementById('foodPts').textContent = `+${currentFood.pts} PTS`;

  let pos;
  do {
    pos = { x: Math.floor(Math.random()*COLS), y: Math.floor(Math.random()*ROWS) };
  } while (snake.some(s => s.x===pos.x && s.y===pos.y));
  food = pos;
}

function startGame() {
  clearInterval(gameLoop);
  hideAllOverlays();
  init();
  running = true;
  paused = false;
  gameLoop = setInterval(tick, SPEEDS[Math.min(level-1, SPEEDS.length-1)]);
}

function togglePause() {
  if (!running) return;
  paused = !paused;
  if (paused) {
    clearInterval(gameLoop);
    document.getElementById('pauseOverlay').classList.remove('hidden');
  } else {
    document.getElementById('pauseOverlay').classList.add('hidden');
    gameLoop = setInterval(tick, SPEEDS[Math.min(level-1, SPEEDS.length-1)]);
  }
}

function hideAllOverlays() {
  ['startOverlay','gameOverOverlay','pauseOverlay'].forEach(id =>
    document.getElementById(id).classList.add('hidden'));
}

function setDir(x, y) {
  // Prevent reversing
  if (x !== 0 && dir.x !== 0) return;
  if (y !== 0 && dir.y !== 0) return;
  nextDir = {x, y};
}

function tick() {
  frame++;
  dir = nextDir;
  const head = { x: snake[0].x + dir.x, y: snake[0].y + dir.y };

  // Wall collision (wrap)
  head.x = (head.x + COLS) % COLS;
  head.y = (head.y + ROWS) % ROWS;

  // Self collision
  if (snake.some(s => s.x===head.x && s.y===head.y)) {
    gameOver(); return;
  }

  snake.unshift(head);

  if (head.x === food.x && head.y === food.y) {
    score += currentFood.pts;
    playEat();
    level = Math.floor(score / 80) + 1;
    if (level > SPEEDS.length) level = SPEEDS.length;
    clearInterval(gameLoop);
    gameLoop = setInterval(tick, SPEEDS[Math.min(level-1, SPEEDS.length-1)]);
    placeFood();
  } else {
    snake.pop();
  }

  updateUI();
  drawGame();
}

function gameOver() {
  clearInterval(gameLoop);
  running = false;
  if (score > best) best = score;
  document.getElementById('finalScore').textContent = `SCORE: ${score}  |  BEST: ${best}`;
  document.getElementById('gameOverOverlay').classList.remove('hidden');

  canvas.classList.add('die-anim');
  setTimeout(() => canvas.classList.remove('die-anim'), 400);
  playDie();
}

function updateUI() {
  best = parseInt(localStorage.getItem('snakeBest') || '0');
  if (score > best) { best = score; localStorage.setItem('snakeBest', best); }

  document.getElementById('scoreDisplay').textContent = score;
  document.getElementById('bestDisplay').textContent = best;
  document.getElementById('levelDisplay').textContent = level;
  document.getElementById('lengthDisplay').textContent = snake.length;

  const speedNames = ['NORM','NORM','FAST','FAST','VERY FAST','TURBO','MAX'];
  document.getElementById('speedDisplay').textContent = speedNames[Math.min(level-1, 6)];
}

// ─── DRAWING ─────────────────────────────────────────────────────────────────

function drawGame() {
  // Background
  ctx.fillStyle = '#050a0f';
  ctx.fillRect(0,0,canvas.width,canvas.height);

  // Grid dots
  ctx.fillStyle = 'rgba(0,255,136,0.04)';
  for (let x=0; x<COLS; x++) for (let y=0; y<ROWS; y++) {
    ctx.beginPath();
    ctx.arc(x*CELL+CELL/2, y*CELL+CELL/2, 1, 0, Math.PI*2);
    ctx.fill();
  }

  // Food glow
  const grd = ctx.createRadialGradient(
    food.x*CELL+CELL/2, food.y*CELL+CELL/2, 2,
    food.x*CELL+CELL/2, food.y*CELL+CELL/2, CELL
  );
  grd.addColorStop(0, currentFood.color + '55');
  grd.addColorStop(1, 'transparent');
  ctx.fillStyle = grd;
  ctx.fillRect(food.x*CELL-CELL, food.y*CELL-CELL, CELL*3, CELL*3);

  // Food emoji
  ctx.font = `${CELL-4}px serif`;
  ctx.textAlign = 'center';
  ctx.textBaseline = 'middle';
  const pulse = 1 + Math.sin(frame * 0.15) * 0.06;
  ctx.save();
  ctx.translate(food.x*CELL+CELL/2, food.y*CELL+CELL/2);
  ctx.scale(pulse, pulse);
  ctx.fillText(currentFood.emoji, 0, 0);
  ctx.restore();

  // Snake
  snake.forEach((seg, i) => {
    const isHead = i === 0;
    const t = i / snake.length;
    const x = seg.x*CELL + 2, y = seg.y*CELL + 2;
    const w = CELL - 4, h = CELL - 4;
    const r = isHead ? 6 : 4;

    // Body gradient color
    const green = Math.round(255 * (1 - t * 0.4));
    const alpha = 1 - t * 0.2;
    ctx.fillStyle = isHead ? `rgba(0,255,${green},${alpha})` : `rgba(0,${green},${Math.round(green*0.5)},${alpha})`;

    // Glow for head
    if (isHead) {
      ctx.shadowColor = '#00ff88';
      ctx.shadowBlur = 15;
    } else {
      ctx.shadowBlur = 4;
      ctx.shadowColor = '#00aa55';
    }

    roundRect(ctx, x, y, w, h, r);
    ctx.fill();
    ctx.shadowBlur = 0;

    // Head eyes
    if (isHead) {
      ctx.fillStyle = '#000';
      const ex = dir.x, ey = dir.y;
      const cx = seg.x*CELL + CELL/2 + ex*(CELL*0.2);
      const cy = seg.y*CELL + CELL/2 + ey*(CELL*0.2);
      const perp = {x: -ey, y: ex};

      ctx.beginPath();
      ctx.arc(cx + perp.x*4, cy + perp.y*4, 2.5, 0, Math.PI*2);
      ctx.fill();
      ctx.beginPath();
      ctx.arc(cx - perp.x*4, cy - perp.y*4, 2.5, 0, Math.PI*2);
      ctx.fill();

      // Eye shine
      ctx.fillStyle = '#fff';
      ctx.beginPath();
      ctx.arc(cx + perp.x*4 + 1, cy + perp.y*4 - 1, 1, 0, Math.PI*2);
      ctx.fill();
      ctx.beginPath();
      ctx.arc(cx - perp.x*4 + 1, cy - perp.y*4 - 1, 1, 0, Math.PI*2);
      ctx.fill();
    }
  });
}

function roundRect(ctx, x, y, w, h, r) {
  ctx.beginPath();
  ctx.moveTo(x+r, y);
  ctx.lineTo(x+w-r, y);
  ctx.quadraticCurveTo(x+w, y, x+w, y+r);
  ctx.lineTo(x+w, y+h-r);
  ctx.quadraticCurveTo(x+w, y+h, x+w-r, y+h);
  ctx.lineTo(x+r, y+h);
  ctx.quadraticCurveTo(x, y+h, x, y+h-r);
  ctx.lineTo(x, y+r);
  ctx.quadraticCurveTo(x, y, x+r, y);
  ctx.closePath();
}

// ─── SOUND (Web Audio) ────────────────────────────────────────────────────────
const AC = new (window.AudioContext || window.webkitAudioContext)();

function beep(freq, type, dur, vol=0.3) {
  const o = AC.createOscillator();
  const g = AC.createGain();
  o.connect(g); g.connect(AC.destination);
  o.type = type;
  o.frequency.setValueAtTime(freq, AC.currentTime);
  o.frequency.exponentialRampToValueAtTime(freq*0.5, AC.currentTime + dur);
  g.gain.setValueAtTime(vol, AC.currentTime);
  g.gain.exponentialRampToValueAtTime(0.001, AC.currentTime + dur);
  o.start(); o.stop(AC.currentTime + dur);
}

function playEat() {
  beep(440, 'square', 0.07, 0.2);
  setTimeout(() => beep(660, 'square', 0.07, 0.2), 60);
}

function playDie() {
  beep(200, 'sawtooth', 0.3, 0.3);
  setTimeout(() => beep(150, 'sawtooth', 0.4, 0.3), 200);
}

// ─── INPUT ────────────────────────────────────────────────────────────────────
document.addEventListener('keydown', e => {
  if (AC.state === 'suspended') AC.resume();
  switch(e.code) {
    case 'ArrowUp':    case 'KeyW': setDir(0,-1); e.preventDefault(); break;
    case 'ArrowDown':  case 'KeyS': setDir(0,1);  e.preventDefault(); break;
    case 'ArrowLeft':  case 'KeyA': setDir(-1,0); e.preventDefault(); break;
    case 'ArrowRight': case 'KeyD': setDir(1,0);  e.preventDefault(); break;
    case 'KeyP': togglePause(); break;
    case 'Space':
      if (!running) startGame();
      else togglePause();
      e.preventDefault();
      break;
  }
});

// Resume audio on touch
document.addEventListener('touchstart', () => {
  if (AC.state === 'suspended') AC.resume();
}, {once: true});

// ─── INIT ─────────────────────────────────────────────────────────────────────
best = parseInt(localStorage.getItem('snakeBest') || '0');
document.getElementById('bestDisplay').textContent = best;
init();
</script>
</body>
</html>
