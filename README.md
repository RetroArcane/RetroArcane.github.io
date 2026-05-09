# RetroArcane.github.io
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>RETROARC — Classic Arcade Collection</title>
<link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&family=Share+Tech+Mono&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #080810;
    --surface: #0f0f1e;
    --surface2: #16162a;
    --gold: #f0c040;
    --red: #ff4444;
    --cyan: #40e0ff;
    --green: #40ff80;
    --purple: #c040ff;
    --border: #2a2a4a;
    --text: #e0e0f0;
    --muted: #666688;
    --pixel: 'Press Start 2P', monospace;
    --mono: 'Share Tech Mono', monospace;
  }
  * { margin: 0; padding: 0; box-sizing: border-box; }
  body { background: var(--bg); color: var(--text); font-family: var(--mono); min-height: 100vh; overflow-x: hidden; }
  body::before {
    content: ''; position: fixed; inset: 0;
    background: repeating-linear-gradient(0deg, transparent, transparent 2px, rgba(0,0,0,0.04) 2px, rgba(0,0,0,0.04) 4px);
    pointer-events: none; z-index: 9999;
  }
  .stars {
    position: fixed; inset: 0;
    background-image:
      radial-gradient(1px 1px at 20% 30%, rgba(255,255,255,0.15), transparent),
      radial-gradient(1px 1px at 60% 70%, rgba(255,255,255,0.1), transparent),
      radial-gradient(1px 1px at 80% 20%, rgba(255,255,255,0.12), transparent),
      radial-gradient(1px 1px at 40% 80%, rgba(255,255,255,0.08), transparent),
      radial-gradient(1px 1px at 10% 60%, rgba(255,255,255,0.1), transparent);
    pointer-events: none; z-index: 0;
  }
  header {
    position: relative; z-index: 10; padding: 24px 32px 16px;
    border-bottom: 1px solid var(--border); display: flex; align-items: center; justify-content: space-between;
    background: rgba(8,8,16,0.9); backdrop-filter: blur(8px);
  }
  .logo { font-family: var(--pixel); font-size: 18px; color: var(--gold); text-shadow: 0 0 20px rgba(240,192,64,0.5); letter-spacing: 2px; }
  .logo span { color: var(--red); }
  #total-score { font-family: var(--pixel); font-size: 9px; color: var(--gold); }
  #menu { position: relative; z-index: 10; padding: 40px 32px; max-width: 1100px; margin: 0 auto; }
  .menu-title { font-family: var(--pixel); font-size: 11px; color: var(--muted); letter-spacing: 3px; margin-bottom: 28px; }
  .game-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(155px, 1fr)); gap: 16px; }
  .game-card {
    background: var(--surface); border: 1px solid var(--border); border-radius: 4px;
    overflow: hidden; cursor: pointer; transition: border-color 0.2s, transform 0.15s; position: relative;
  }
  .game-card:hover { border-color: var(--gold); transform: translateY(-3px); }
  .game-card:hover .card-art { filter: brightness(1.2); }
  .card-art { height: 110px; display: flex; align-items: center; justify-content: center; font-size: 48px; transition: filter 0.2s; position: relative; }
  .card-art.snake-art    { background: radial-gradient(ellipse at center, #0a2e0a, #040a04); }
  .card-art.pong-art     { background: radial-gradient(ellipse at center, #0a0a2e, #04040a); }
  .card-art.tetris-art   { background: radial-gradient(ellipse at center, #1a0a2e, #0a040a); }
  .card-art.memory-art   { background: radial-gradient(ellipse at center, #2e0a0a, #0a0404); }
  .card-art.breakout-art { background: radial-gradient(ellipse at center, #2e1a0a, #0a0804); }
  .card-art.racing-art   { background: radial-gradient(ellipse at center, #0a2e1a, #040a08); }
  .card-art.invaders-art { background: radial-gradient(ellipse at center, #0a102e, #04060a); }
  .card-info { padding: 12px 14px; }
  .card-name { font-family: var(--pixel); font-size: 9px; color: var(--text); margin-bottom: 6px; letter-spacing: 1px; }
  .card-meta { font-size: 11px; color: var(--muted); }
  .card-best { font-size: 11px; color: var(--gold); }
  .card-badge { position: absolute; top: 8px; right: 8px; font-family: var(--pixel); font-size: 7px; background: var(--gold); color: #1a1a2e; padding: 3px 6px; border-radius: 2px; }
  .play-btn { display: block; width: 100%; padding: 8px; background: transparent; border: none; border-top: 1px solid var(--border); color: var(--gold); font-family: var(--pixel); font-size: 8px; cursor: pointer; letter-spacing: 2px; transition: background 0.15s; }
  .play-btn:hover { background: rgba(240,192,64,0.1); }
  #game-screen { display: none; position: fixed; inset: 0; z-index: 100; background: var(--bg); flex-direction: column; align-items: center; }
  .game-header { width: 100%; padding: 14px 24px; border-bottom: 1px solid var(--border); display: flex; align-items: center; gap: 20px; background: rgba(8,8,16,0.95); }
  .back-btn { font-family: var(--pixel); font-size: 8px; color: var(--muted); cursor: pointer; padding: 6px 12px; border: 1px solid var(--border); background: transparent; border-radius: 2px; transition: color 0.15s, border-color 0.15s; letter-spacing: 1px; }
  .back-btn:hover { color: var(--gold); border-color: var(--gold); }
  .game-title-bar { font-family: var(--pixel); font-size: 11px; color: var(--gold); letter-spacing: 2px; }
  .score-display { margin-left: auto; font-family: var(--pixel); font-size: 9px; color: var(--cyan); letter-spacing: 1px; }
  .game-area { flex: 1; display: flex; align-items: center; justify-content: center; flex-direction: column; gap: 16px; padding: 20px; overflow: auto; }
  canvas { border: 2px solid var(--border); border-radius: 2px; image-rendering: pixelated; display: block; }
  .game-controls { display: flex; gap: 10px; align-items: center; }
  .ctrl-btn { font-family: var(--pixel); font-size: 7px; padding: 8px 14px; background: var(--surface2); border: 1px solid var(--border); color: var(--text); cursor: pointer; border-radius: 2px; letter-spacing: 1px; transition: border-color 0.15s, color 0.15s; }
  .ctrl-btn:hover { border-color: var(--gold); color: var(--gold); }
  .ctrl-hint { font-size: 11px; color: var(--muted); }
  .game-over-overlay { display: none; position: absolute; inset: 0; background: rgba(8,8,16,0.88); align-items: center; justify-content: center; flex-direction: column; gap: 16px; z-index: 10; }
  .game-over-overlay.show { display: flex; }
  .game-over-title { font-family: var(--pixel); font-size: 20px; color: var(--red); text-shadow: 0 0 30px rgba(255,68,68,0.6); animation: flicker 2s infinite; }
  @keyframes flicker { 0%,95%,100%{opacity:1} 96%{opacity:0.7} 97%{opacity:1} 98%{opacity:0.5} }
  .final-score { font-family: var(--pixel); font-size: 11px; color: var(--gold); }
  .dpad { display: none; grid-template-columns: repeat(3, 44px); grid-template-rows: repeat(3, 44px); gap: 4px; }
  .dpad-btn { background: var(--surface2); border: 1px solid var(--border); border-radius: 4px; display: flex; align-items: center; justify-content: center; color: var(--text); font-size: 18px; cursor: pointer; user-select: none; -webkit-tap-highlight-color: transparent; }
  .dpad-btn:active { background: var(--surface); border-color: var(--gold); }
  @media (max-width:600px),(pointer:coarse) { .dpad { display: grid; } }
  .mem-card { width: 68px; height: 68px; background: var(--surface2); border: 1px solid var(--border); border-radius: 4px; display: flex; align-items: center; justify-content: center; font-size: 28px; cursor: pointer; transition: transform 0.15s, border-color 0.15s; user-select: none; }
  .mem-card.face-down { font-size: 0; }
  .mem-card:hover { border-color: var(--gold); }
  .mem-card.matched { border-color: var(--green); opacity: 0.6; pointer-events: none; }
</style>
</head>
<body>
<div class="stars"></div>
<header>
  <div class="logo">RETRO<span>ARC</span></div>
  <div><span id="total-score">SCORE: 0</span></div>
</header>

<div id="menu">
  <div class="menu-title">// SELECT YOUR GAME</div>
  <div class="game-grid">
    <div class="game-card" onclick="startGame('snake')">
      <div class="card-art snake-art">🐍</div>
      <div class="card-info"><div class="card-name">SNAKE</div><div class="card-meta">Arrow keys / WASD</div><div class="card-best" id="best-snake">BEST: —</div></div>
      <button class="play-btn">▶ PLAY</button>
    </div>
    <div class="game-card" onclick="startGame('pong')">
      <div class="card-art pong-art">🏓</div>
      <div class="card-info"><div class="card-name">PONG</div><div class="card-meta">W/S keys to move</div><div class="card-best" id="best-pong">BEST: —</div></div>
      <button class="play-btn">▶ PLAY</button>
    </div>
    <div class="game-card" onclick="startGame('tetris')">
      <div class="card-art tetris-art" style="position:relative">🟦<span class="card-badge">HOT</span></div>
      <div class="card-info"><div class="card-name">TETRIS</div><div class="card-meta">Arrow keys / rotate</div><div class="card-best" id="best-tetris">BEST: —</div></div>
      <button class="play-btn">▶ PLAY</button>
    </div>
    <div class="game-card" onclick="startGame('memory')">
      <div class="card-art memory-art">🃏</div>
      <div class="card-info"><div class="card-name">MEMORY</div><div class="card-meta">Match pairs · streak bonus</div><div class="card-best" id="best-memory">BEST: —</div></div>
      <button class="play-btn">▶ PLAY</button>
    </div>
    <div class="game-card" onclick="startGame('breakout')">
      <div class="card-art breakout-art">🧱</div>
      <div class="card-info"><div class="card-name">BREAKOUT</div><div class="card-meta">Mouse / touch paddle</div><div class="card-best" id="best-breakout">BEST: —</div></div>
      <button class="play-btn">▶ PLAY</button>
    </div>
    <div class="game-card" onclick="startGame('racing')">
      <div class="card-art racing-art" style="position:relative">🏎️<span class="card-badge">NEW</span></div>
      <div class="card-info"><div class="card-name">RACING</div><div class="card-meta">← → dodge traffic</div><div class="card-best" id="best-racing">BEST: —</div></div>
      <button class="play-btn">▶ PLAY</button>
    </div>
    <div class="game-card" onclick="startGame('invaders')">
      <div class="card-art invaders-art" style="position:relative">👾<span class="card-badge">NEW</span></div>
      <div class="card-info"><div class="card-name">INVADERS</div><div class="card-meta">← → Space to shoot</div><div class="card-best" id="best-invaders">BEST: —</div></div>
      <button class="play-btn">▶ PLAY</button>
    </div>
  </div>
</div>

<div id="game-screen">
  <div class="game-header">
    <button class="back-btn" onclick="goMenu()">◀ MENU</button>
    <div class="game-title-bar" id="game-title-bar">GAME</div>
    <div class="score-display" id="score-display">SCORE: 0</div>
  </div>
  <div class="game-area">
    <div id="canvas-container"></div>
    <div class="game-controls" id="game-controls"></div>
  </div>
</div>

<script>
let currentGame=null;
let scores=JSON.parse(localStorage.getItem('retroarc_scores')||'{}');
let totalScore=scores.total||0;

function saveScore(game,score){
  const n=typeof score==='number'?score:parseInt(score)||0;
  if(scores[game]==null||n>scores[game])scores[game]=n;
  scores.total=(scores.total||0)+Math.floor(n/10);
  totalScore=scores.total;
  localStorage.setItem('retroarc_scores',JSON.stringify(scores));
  updateBestDisplays();
  document.getElementById('total-score').textContent='SCORE: '+totalScore;
}
function updateBestDisplays(){
  ['snake','pong','tetris','memory','breakout','racing','invaders'].forEach(g=>{
    const el=document.getElementById('best-'+g);
    if(el&&scores[g]!=null)el.textContent='BEST: '+scores[g];
  });
}
updateBestDisplays();
document.getElementById('total-score').textContent='SCORE: '+totalScore;

function startGame(name){
  document.getElementById('menu').style.display='none';
  document.getElementById('game-screen').style.display='flex';
  document.getElementById('game-title-bar').textContent=name.toUpperCase();
  document.getElementById('score-display').textContent='SCORE: 0';
  const cc=document.getElementById('canvas-container');
  cc.innerHTML='';cc.style.position='';
  document.getElementById('game-controls').innerHTML='';
  currentGame=name;
  ({snake:initSnake,pong:initPong,tetris:initTetris,memory:initMemory,breakout:initBreakout,racing:initRacing,invaders:initInvaders})[name](cc,document.getElementById('game-controls'));
}
function goMenu(){
  stopAll();
  document.getElementById('game-screen').style.display='none';
  document.getElementById('menu').style.display='block';
}
function stopAll(){
  if(window._gameLoop){clearInterval(window._gameLoop);window._gameLoop=null;}
  if(window._gameAF){cancelAnimationFrame(window._gameAF);window._gameAF=null;}
  document.onkeydown=null; document.onkeyup=null;
}
function setScore(n){document.getElementById('score-display').textContent='SCORE: '+n;}
function makeGameOver(cc,score,onRestart){
  saveScore(currentGame,score);
  const ov=document.createElement('div');
  ov.className='game-over-overlay show';
  ov.innerHTML=`<div class="game-over-title">GAME OVER</div><div class="final-score">SCORE: ${score}</div><button class="ctrl-btn" id="rb">▶ PLAY AGAIN</button><button class="ctrl-btn" onclick="goMenu()">◀ MENU</button>`;
  cc.style.position='relative'; cc.appendChild(ov);
  ov.querySelector('#rb').onclick=()=>{ov.remove();onRestart();};
}

/* ===== SNAKE (180ms = slower) ===== */
function initSnake(cc,ctrl){
  const W=400,H=400,SZ=20;
  const cv=document.createElement('canvas'); cv.width=W; cv.height=H; cv.style.cssText='border:2px solid #2a2a4a;'; cc.appendChild(cv);
  const ctx=cv.getContext('2d');
  let snake,dir,nd,food,score,running;
  function reset(){snake=[{x:10,y:10},{x:9,y:10},{x:8,y:10}];dir={x:1,y:0};nd={x:1,y:0};food=rf();score=0;running=true;setScore(0);cc.querySelector('.game-over-overlay')?.remove();}
  function rf(){let f;do{f={x:~~(Math.random()*20),y:~~(Math.random()*20)};}while(snake.some(s=>s.x===f.x&&s.y===f.y));return f;}
  function draw(){
    ctx.fillStyle='#0d0d1a';ctx.fillRect(0,0,W,H);
    ctx.fillStyle='#1a1a2e';for(let x=0;x<20;x++)for(let y=0;y<20;y++)ctx.fillRect(x*SZ+9,y*SZ+9,2,2);
    ctx.fillStyle='#ff4444';ctx.shadowColor='#ff4444';ctx.shadowBlur=10;ctx.fillRect(food.x*SZ+2,food.y*SZ+2,SZ-4,SZ-4);ctx.shadowBlur=0;
    snake.forEach((s,i)=>{ctx.fillStyle=i===0?'#40ff80':`hsl(${140+i*2},80%,${i?45:60}%)`;if(!i){ctx.shadowColor='#40ff80';ctx.shadowBlur=8;}ctx.fillRect(s.x*SZ+1,s.y*SZ+1,SZ-2,SZ-2);ctx.shadowBlur=0;});
  }
  function tick(){
    if(!running)return; dir=nd;
    const h={x:snake[0].x+dir.x,y:snake[0].y+dir.y};
    if(h.x<0||h.x>=20||h.y<0||h.y>=20||snake.some(s=>s.x===h.x&&s.y===h.y)){running=false;makeGameOver(cc,score,reset);return;}
    snake.unshift(h);
    if(h.x===food.x&&h.y===food.y){score+=10;setScore(score);food=rf();}else snake.pop();
    draw();
  }
  document.onkeydown=e=>{const m={ArrowUp:{x:0,y:-1},ArrowDown:{x:0,y:1},ArrowLeft:{x:-1,y:0},ArrowRight:{x:1,y:0},w:{x:0,y:-1},s:{x:0,y:1},a:{x:-1,y:0},d:{x:1,y:0}};const v=m[e.key];if(v&&!(v.x===-dir.x&&v.y===-dir.y)){nd=v;e.preventDefault();}};
  const dp=document.createElement('div');dp.className='dpad';dp.innerHTML=`<div></div><div class="dpad-btn" data-d="up">▲</div><div></div><div class="dpad-btn" data-d="left">◀</div><div></div><div class="dpad-btn" data-d="right">▶</div><div></div><div class="dpad-btn" data-d="down">▼</div><div></div>`;
  dp.querySelectorAll('.dpad-btn').forEach(b=>b.addEventListener('touchstart',e=>{e.preventDefault();const m={up:{x:0,y:-1},down:{x:0,y:1},left:{x:-1,y:0},right:{x:1,y:0}};const v=m[b.dataset.d];if(v&&!(v.x===-dir.x&&v.y===-dir.y))nd=v;}));
  cc.appendChild(dp);
  ctrl.innerHTML='<span class="ctrl-hint">Arrow keys or WASD to move</span>';
  reset();draw();window._gameLoop=setInterval(tick,180);
}

/* ===== PONG (easier AI: speed 2.5, only tracks when ball approaches) ===== */
function initPong(cc,ctrl){
  const W=500,H=360;
  const cv=document.createElement('canvas');cv.width=W;cv.height=H;cv.style.cssText='border:2px solid #2a2a4a;';cc.appendChild(cv);
  const ctx=cv.getContext('2d');
  let py,ay,bx,by,bdx,bdy,ps,as,running;
  const PH=70,PW=10,BR=7,SPD=3.2;
  function reset(){py=H/2-PH/2;ay=H/2-PH/2;bx=W/2;by=H/2;bdx=SPD*(Math.random()<.5?1:-1);bdy=SPD*.8*(Math.random()<.5?1:-1);ps=0;as=0;running=true;setScore(0);cc.querySelector('.game-over-overlay')?.remove();}
  const keys={};
  document.onkeydown=e=>{keys[e.key]=true;e.preventDefault();};
  document.onkeyup=e=>{keys[e.key]=false;};
  function draw(){
    ctx.fillStyle='#080810';ctx.fillRect(0,0,W,H);
    ctx.setLineDash([8,8]);ctx.strokeStyle='#2a2a4a';ctx.lineWidth=2;ctx.beginPath();ctx.moveTo(W/2,0);ctx.lineTo(W/2,H);ctx.stroke();ctx.setLineDash([]);
    ctx.font='bold 32px "Press Start 2P"';ctx.fillStyle='#2a2a4a';ctx.textAlign='center';ctx.fillText(ps,W/4,50);ctx.fillText(as,3*W/4,50);
    ctx.fillStyle='#40e0ff';ctx.shadowColor='#40e0ff';ctx.shadowBlur=10;ctx.fillRect(20,py,PW,PH);
    ctx.fillStyle='#ff4444';ctx.shadowColor='#ff4444';ctx.fillRect(W-30,ay,PW,PH);ctx.shadowBlur=0;
    ctx.fillStyle='#f0c040';ctx.shadowColor='#f0c040';ctx.shadowBlur=12;ctx.beginPath();ctx.arc(bx,by,BR,0,Math.PI*2);ctx.fill();ctx.shadowBlur=0;
  }
  function tick(){
    if(!running)return;
    if((keys['w']||keys['ArrowUp'])&&py>0)py-=6;
    if((keys['s']||keys['ArrowDown'])&&py<H-PH)py+=6;
    // AI only reacts when ball is coming toward it, and slowly
    if(bdx>0){const mid=ay+PH/2;if(mid<by-8)ay=Math.min(ay+2.2,H-PH);else if(mid>by+8)ay=Math.max(ay-2.2,0);}
    bx+=bdx;by+=bdy;
    if(by<=BR||by>=H-BR)bdy*=-1;
    if(bx-BR<=30&&bx-BR>=18&&by>=py&&by<=py+PH){bdx=Math.abs(bdx);bdy+=(by-(py+PH/2))*.08;}
    if(bx+BR>=W-30&&bx+BR<=W-18&&by>=ay&&by<=ay+PH){bdx=-Math.abs(bdx);bdy+=(by-(ay+PH/2))*.08;}
    const spd=Math.sqrt(bdx*bdx+bdy*bdy);if(spd>8){bdx=bdx/spd*8;bdy=bdy/spd*8;}
    if(bx<0){as++;setScore(ps);if(as>=7){running=false;makeGameOver(cc,ps,reset);return;}bx=W/2;by=H/2;bdx=SPD;bdy=SPD*.8;}
    if(bx>W){ps++;setScore(ps);if(ps>=7){running=false;makeGameOver(cc,ps,reset);return;}bx=W/2;by=H/2;bdx=-SPD;bdy=SPD*.8;}
    draw();
  }
  ctrl.innerHTML='<span class="ctrl-hint">W/S or ↑↓ to move your paddle (blue) · First to 7 wins</span>';
  reset();draw();window._gameLoop=setInterval(tick,16);
}

/* ===== TETRIS ===== */
function initTetris(cc,ctrl){
  const CW=10,CH=20,BS=28,W=CW*BS,H=CH*BS;
  const cv=document.createElement('canvas');cv.width=W;cv.height=H;cv.style.cssText='border:2px solid #2a2a4a;';cc.appendChild(cv);
  const ctx=cv.getContext('2d');
  const PS=[[[1,1,1,1]],[[1,1],[1,1]],[[0,1,0],[1,1,1]],[[1,0,0],[1,1,1]],[[0,0,1],[1,1,1]],[[0,1,1],[1,1,0]],[[1,1,0],[0,1,1]]];
  const PC=['#40e0ff','#f0c040','#c040ff','#ff8040','#4080ff','#40ff80','#ff4444'];
  let board,piece,px,py,pcol,score,running;
  function np(){const i=~~(Math.random()*PS.length);piece=PS[i].map(r=>[...r]);pcol=PC[i];px=~~(CW/2)-~~(piece[0].length/2);py=0;if(col(0,0)){running=false;makeGameOver(cc,score,reset);}}
  function reset(){board=Array.from({length:CH},()=>Array(CW).fill(null));score=0;running=true;setScore(0);cc.querySelector('.game-over-overlay')?.remove();np();}
  function col(dx,dy,p=piece){for(let r=0;r<p.length;r++)for(let c=0;c<p[r].length;c++){if(!p[r][c])continue;const nx=px+c+dx,ny=py+r+dy;if(nx<0||nx>=CW||ny>=CH)return true;if(ny>=0&&board[ny][nx])return true;}return false;}
  function lock(){
    for(let r=0;r<piece.length;r++)for(let c=0;c<piece[r].length;c++)if(piece[r][c]&&py+r>=0)board[py+r][px+c]=pcol;
    let ln=0;for(let r=CH-1;r>=0;r--){if(board[r].every(c=>c)){board.splice(r,1);board.unshift(Array(CW).fill(null));ln++;r++;}}
    score+=[0,40,100,300,1200][ln]||0;setScore(score);np();
  }
  function rot(p){return Array.from({length:p[0].length},(_,r)=>Array.from({length:p.length},(_,c)=>p[p.length-1-c][r]));}
  function draw(){
    ctx.fillStyle='#0d0d1a';ctx.fillRect(0,0,W,H);
    ctx.strokeStyle='#141428';ctx.lineWidth=1;
    for(let x=0;x<=CW;x++){ctx.beginPath();ctx.moveTo(x*BS,0);ctx.lineTo(x*BS,H);ctx.stroke();}
    for(let y=0;y<=CH;y++){ctx.beginPath();ctx.moveTo(0,y*BS);ctx.lineTo(W,y*BS);ctx.stroke();}
    for(let r=0;r<CH;r++)for(let c=0;c<CW;c++){if(board[r][c]){ctx.fillStyle=board[r][c];ctx.fillRect(c*BS+1,r*BS+1,BS-2,BS-2);ctx.fillStyle='rgba(255,255,255,0.15)';ctx.fillRect(c*BS+1,r*BS+1,BS-2,4);}}
    let gy=0;while(!col(0,gy+1))gy++;
    piece.forEach((row,r)=>row.forEach((v,c)=>{if(v){ctx.fillStyle='rgba(255,255,255,0.07)';ctx.fillRect((px+c)*BS+1,(py+gy+r)*BS+1,BS-2,BS-2);}}));
    ctx.shadowBlur=8;ctx.shadowColor=pcol;
    piece.forEach((row,r)=>row.forEach((v,c)=>{if(v){ctx.fillStyle=pcol;ctx.fillRect((px+c)*BS+1,(py+r)*BS+1,BS-2,BS-2);ctx.fillStyle='rgba(255,255,255,0.2)';ctx.fillRect((px+c)*BS+1,(py+r)*BS+1,BS-2,4);}}));
    ctx.shadowBlur=0;
  }
  document.onkeydown=e=>{
    if(!running)return;
    if(e.key==='ArrowLeft'&&!col(-1,0)){px--;draw();e.preventDefault();}
    else if(e.key==='ArrowRight'&&!col(1,0)){px++;draw();e.preventDefault();}
    else if(e.key==='ArrowDown'){if(!col(0,1))py++;else lock();draw();e.preventDefault();}
    else if(e.key==='ArrowUp'||e.key===' '){const r=rot(piece);if(!col(0,0,r))piece=r;draw();e.preventDefault();}
  };
  ctrl.innerHTML='<span class="ctrl-hint">← → move &nbsp;|&nbsp; ↑/Space rotate &nbsp;|&nbsp; ↓ drop faster</span>';
  reset();draw();window._gameLoop=setInterval(()=>{if(!running)return;if(!col(0,1))py++;else lock();draw();},500);
}

/* ===== MEMORY (score-based, streak multiplier, auto-restart board) ===== */
function initMemory(cc,ctrl){
  const EMOJIS=['🎮','👾','🚀','⭐','💎','🔥','🎯','🌈'];
  let cards,flipped,score,busy,streak;
  function reset(){
    const pairs=[...EMOJIS,...EMOJIS].sort(()=>Math.random()-.5);
    flipped=[];score=score||0;busy=false;streak=0;
    cc.querySelector('#mgrid')?.remove();
    const g=document.createElement('div');g.id='mgrid';g.style.cssText='display:grid;grid-template-columns:repeat(4,1fr);gap:8px;';
    cards=pairs.map((em,i)=>{const c=document.createElement('div');c.className='mem-card face-down';c.dataset.e=em;c.innerHTML='❓';c.addEventListener('click',()=>flip(c));g.appendChild(c);return c;});
    cc.appendChild(g);
    ctrl.innerHTML='<span class="ctrl-hint">Match pairs · streak bonus multiplier · board refreshes when cleared</span>';
  }
  function flip(card){
    if(busy||card.classList.contains('matched')||flipped.includes(card))return;
    card.classList.remove('face-down');card.innerHTML=card.dataset.e;flipped.push(card);
    if(flipped.length===2){
      busy=true;
      if(flipped[0].dataset.e===flipped[1].dataset.e){
        flipped[0].classList.add('matched');flipped[1].classList.add('matched');
        streak++;score+=10*streak;setScore(score);saveScore('memory',score);
        flipped=[];busy=false;
        if(cards.every(c=>c.classList.contains('matched')))setTimeout(reset,700);
      } else {
        streak=0;
        setTimeout(()=>{flipped.forEach(c=>{c.classList.add('face-down');c.innerHTML='❓';});flipped=[];busy=false;},900);
      }
    }
  }
  score=0;setScore(0);reset();
}

/* ===== BREAKOUT ===== */
function initBreakout(cc,ctrl){
  const W=500,H=380;
  const cv=document.createElement('canvas');cv.width=W;cv.height=H;cv.style.cssText='border:2px solid #2a2a4a;cursor:none;';cc.appendChild(cv);
  const ctx=cv.getContext('2d');
  const PW=80,PH=10,BR=7,ROWS=5,COLS=10;
  let px,bx,by,bdx,bdy,bricks,score,lives,running;
  function mb(){const cols=['#ff4444','#ff8040','#f0c040','#40ff80','#40e0ff'];return Array.from({length:ROWS},(_,r)=>Array.from({length:COLS},(_,c)=>({x:c*52+10,y:r*22+40,alive:true,color:cols[r]})));}
  function reset(){px=W/2-PW/2;bx=W/2;by=H-60;bdx=3.5*(Math.random()<.5?1:-1);bdy=-3.5;bricks=mb();score=0;lives=3;running=true;setScore(score);cc.querySelector('.game-over-overlay')?.remove();}
  cv.addEventListener('mousemove',e=>{const r=cv.getBoundingClientRect();px=Math.min(Math.max(e.clientX-r.left-PW/2,0),W-PW);});
  cv.addEventListener('touchmove',e=>{e.preventDefault();const r=cv.getBoundingClientRect();px=Math.min(Math.max(e.touches[0].clientX-r.left-PW/2,0),W-PW);},{passive:false});
  function draw(){
    ctx.fillStyle='#080810';ctx.fillRect(0,0,W,H);
    bricks.forEach(row=>row.forEach(b=>{if(!b.alive)return;ctx.fillStyle=b.color;ctx.shadowColor=b.color;ctx.shadowBlur=4;ctx.fillRect(b.x,b.y,48,16);ctx.fillStyle='rgba(255,255,255,0.2)';ctx.fillRect(b.x,b.y,48,4);ctx.shadowBlur=0;}));
    for(let i=0;i<lives;i++){ctx.fillStyle='#ff4444';ctx.beginPath();ctx.arc(14+i*20,18,5,0,Math.PI*2);ctx.fill();}
    ctx.fillStyle='#40e0ff';ctx.shadowColor='#40e0ff';ctx.shadowBlur=10;ctx.fillRect(px,H-30,PW,PH);ctx.shadowBlur=0;
    ctx.fillStyle='#f0c040';ctx.shadowColor='#f0c040';ctx.shadowBlur=10;ctx.beginPath();ctx.arc(bx,by,BR,0,Math.PI*2);ctx.fill();ctx.shadowBlur=0;
  }
  function tick(){
    if(!running)return;bx+=bdx;by+=bdy;
    if(bx<=BR||bx>=W-BR)bdx*=-1;if(by<=BR)bdy*=-1;
    if(by+BR>=H-30&&bx>=px&&bx<=px+PW&&bdy>0){bdy=-Math.abs(bdy);bdx+=((bx-(px+PW/2))/PW)*2;}
    if(by>H+20){lives--;if(lives<=0){running=false;makeGameOver(cc,score,reset);return;}bx=W/2;by=H-60;bdx=3.5*(Math.random()<.5?1:-1);bdy=-3.5;}
    bricks.forEach(row=>row.forEach(b=>{if(!b.alive)return;if(bx>b.x&&bx<b.x+48&&by-BR<b.y+16&&by+BR>b.y){b.alive=false;bdy*=-1;score+=10;setScore(score);}}));
    if(bricks.flat().every(b=>!b.alive)){running=false;makeGameOver(cc,score,reset);}
    draw();
  }
  ctrl.innerHTML='<span class="ctrl-hint">Mouse / touch to control paddle</span>';
  reset();draw();window._gameLoop=setInterval(tick,16);
}

/* ===== RACING ===== */
function initRacing(cc,ctrl){
  const W=360,H=500,LANES=4,LW=80,RX=40,CW=36,CH=56;
  const cv=document.createElement('canvas');cv.width=W;cv.height=H;cv.style.cssText='border:2px solid #2a2a4a;';cc.appendChild(cv);
  const ctx=cv.getContext('2d');
  const keys={};
  let px,score,spd,traffic,roff,running,frame;

  function reset(){px=W/2-CW/2;score=0;spd=3;traffic=[];roff=0;frame=0;running=true;setScore(0);cc.querySelector('.game-over-overlay')?.remove();}

  function drawCar(x,y,col,isP){
    ctx.fillStyle=col;ctx.shadowColor=col;ctx.shadowBlur=isP?12:4;
    ctx.fillRect(x+4,y+10,CW-8,CH-18);ctx.fillRect(x+9,y+2,CW-18,14);ctx.fillRect(x+9,y+CH-14,CW-18,12);
    ctx.shadowBlur=0;
    ctx.fillStyle='rgba(160,210,255,0.55)';ctx.fillRect(x+11,y+11,CW-22,9);ctx.fillRect(x+11,y+CH-22,CW-22,8);
    ctx.fillStyle='#111';ctx.fillRect(x,y+14,7,10);ctx.fillRect(x+CW-7,y+14,7,10);ctx.fillRect(x,y+CH-24,7,10);ctx.fillRect(x+CW-7,y+CH-24,7,10);
    if(isP){ctx.fillStyle='#fff';ctx.shadowColor='#fff';ctx.shadowBlur=6;ctx.fillRect(x+6,y+3,5,4);ctx.fillRect(x+CW-11,y+3,5,4);ctx.fillStyle='#f44';ctx.shadowColor='#f44';ctx.fillRect(x+6,y+CH-7,5,4);ctx.fillRect(x+CW-11,y+CH-7,5,4);ctx.shadowBlur=0;}
  }

  function draw(){
    ctx.fillStyle='#0a0a14';ctx.fillRect(0,0,W,H);
    ctx.fillStyle='#0a1a0a';ctx.fillRect(0,0,RX,H);ctx.fillRect(RX+LANES*LW,0,W-RX-LANES*LW,H);
    ctx.fillStyle='#1a1a2a';ctx.fillRect(RX,0,LANES*LW,H);
    // lane dashes
    ctx.strokeStyle='rgba(240,192,64,0.5)';ctx.lineWidth=2;ctx.setLineDash([28,20]);ctx.lineDashOffset=-(roff%48);
    for(let l=1;l<LANES;l++){ctx.beginPath();ctx.moveTo(RX+l*LW,0);ctx.lineTo(RX+l*LW,H);ctx.stroke();}
    ctx.setLineDash([]);
    ctx.strokeStyle='#ffffff';ctx.lineWidth=3;
    ctx.beginPath();ctx.moveTo(RX,0);ctx.lineTo(RX,H);ctx.stroke();
    ctx.beginPath();ctx.moveTo(RX+LANES*LW,0);ctx.lineTo(RX+LANES*LW,H);ctx.stroke();
    traffic.forEach(t=>drawCar(t.x,t.y,t.c,false));
    drawCar(px,H-CH-20,'#40ff80',true);
    ctx.fillStyle='rgba(0,0,0,0.5)';ctx.fillRect(0,H-26,W,26);
    ctx.fillStyle='#f0c040';ctx.font='7px "Press Start 2P"';ctx.textAlign='left';ctx.fillText('SCORE '+score,8,H-9);
    ctx.textAlign='right';ctx.fillText(Math.floor(spd*15)+'km/h',W-8,H-9);
  }

  function tick(){
    if(!running)return;
    frame++;roff+=spd;score=~~(frame*spd/10);setScore(score);
    spd=3+frame*0.0007;
    if((keys['ArrowLeft']||keys['a'])&&px>RX)px-=5;
    if((keys['ArrowRight']||keys['d'])&&px<RX+LANES*LW-CW)px+=5;
    if(frame%Math.max(28,65-~~(spd*4))===0){
      const lane=~~(Math.random()*LANES);
      const cols=['#ff4444','#40e0ff','#c040ff','#f0c040','#ff8040'];
      traffic.push({x:RX+lane*LW+(LW-CW)/2,y:-CH,c:cols[~~(Math.random()*cols.length)]});
    }
    traffic.forEach(t=>t.y+=spd+1.2);
    traffic=traffic.filter(t=>t.y<H+CH);
    for(const t of traffic){
      if(px<t.x+CW-5&&px+CW>t.x+5&&H-CH-20<t.y+CH-5&&H-20>t.y+5){running=false;makeGameOver(cc,score,reset);return;}
    }
    draw();
  }

  document.onkeydown=e=>{keys[e.key]=true;if(['ArrowLeft','ArrowRight','ArrowUp','ArrowDown',' '].includes(e.key))e.preventDefault();};
  document.onkeyup=e=>{keys[e.key]=false;};

  // mobile arrows
  const mc=document.createElement('div');mc.style.cssText='display:flex;gap:10px;margin-top:8px;';
  mc.innerHTML=`<button class="dpad-btn" id="rl" style="width:54px;height:48px;font-size:22px">◀</button><button class="dpad-btn" id="rr" style="width:54px;height:48px;font-size:22px">▶</button>`;
  cc.appendChild(mc);
  mc.querySelector('#rl').addEventListener('touchstart',e=>{e.preventDefault();keys['ArrowLeft']=true;});mc.querySelector('#rl').addEventListener('touchend',()=>keys['ArrowLeft']=false);
  mc.querySelector('#rr').addEventListener('touchstart',e=>{e.preventDefault();keys['ArrowRight']=true;});mc.querySelector('#rr').addEventListener('touchend',()=>keys['ArrowRight']=false);
  ctrl.innerHTML='<span class="ctrl-hint">← → dodge traffic · speed increases over time</span>';
  reset();draw();window._gameLoop=setInterval(tick,16);
}

/* ===== SPACE INVADERS ===== */
function initInvaders(cc,ctrl){
  const W=480,H=460,ROWS=4,COLS=10,IW=26,IH=18,IP=16;
  const cv=document.createElement('canvas');cv.width=W;cv.height=H;cv.style.cssText='border:2px solid #2a2a4a;';cc.appendChild(cv);
  const ctx=cv.getContext('2d');
  const PW=44,keys={};
  let px,bullets,ibullets,invaders,score,lives,idir,ispd,frame,running,cooldown;

  function makeInv(){
    return Array.from({length:ROWS},(_,r)=>Array.from({length:COLS},(_,c)=>({x:44+c*(IW+IP),y:44+r*(IH+IP),alive:true,row:r}))).flat();
  }
  function reset(){
    px=W/2-PW/2;bullets=[];ibullets=[];invaders=makeInv();
    score=0;lives=3;idir=1;ispd=0.35;frame=0;running=true;cooldown=0;
    setScore(0);cc.querySelector('.game-over-overlay')?.remove();
  }

  function drawInv(x,y,row,anim){
    const cols=['#ff4444','#ff8040','#f0c040','#40ff80'];
    const c=cols[row%cols.length];
    ctx.fillStyle=c;ctx.shadowColor=c;ctx.shadowBlur=5;
    ctx.fillRect(x+3,y+3,IW-6,IH-6);
    const lg=anim?2:0;
    ctx.fillRect(x,y+5+lg,4,7);ctx.fillRect(x+IW-4,y+5+lg,4,7);
    ctx.fillRect(x+5,y+IH-3+lg,4,3);ctx.fillRect(x+IW-9,y+IH-3+lg,4,3);
    ctx.fillStyle='#080810';ctx.shadowBlur=0;
    ctx.fillRect(x+6,y+5,4,4);ctx.fillRect(x+IW-10,y+5,4,4);
  }

  function drawShip(){
    ctx.fillStyle='#40e0ff';ctx.shadowColor='#40e0ff';ctx.shadowBlur=10;
    ctx.fillRect(px+4,H-32,PW-8,10);ctx.fillRect(px+10,H-42,PW-20,14);ctx.fillRect(px+PW/2-3,H-48,6,8);
    ctx.shadowBlur=0;
  }

  function draw(){
    ctx.fillStyle='#080810';ctx.fillRect(0,0,W,H);
    // parallax stars
    ctx.fillStyle='rgba(255,255,255,0.25)';
    for(let i=0;i<40;i++){ctx.fillRect((i*79+frame*.15)%W,(i*53+frame*.08)%H,1,1);}
    const anim=~~(frame/18)%2===0;
    invaders.forEach(inv=>{if(inv.alive)drawInv(inv.x,inv.y,inv.row,anim);});
    bullets.forEach(b=>{ctx.fillStyle='#40ff80';ctx.shadowColor='#40ff80';ctx.shadowBlur=8;ctx.fillRect(b.x-2,b.y-10,4,12);ctx.shadowBlur=0;});
    ibullets.forEach(b=>{ctx.fillStyle='#ff4444';ctx.shadowColor='#ff4444';ctx.shadowBlur=6;ctx.fillRect(b.x-2,b.y,4,12);ctx.shadowBlur=0;});
    drawShip();
    ctx.fillStyle='#40ff80';ctx.fillRect(0,H-22,W,2);
    for(let i=0;i<lives;i++){ctx.fillStyle='#40e0ff';ctx.fillRect(8+i*24,H-14,16,8);}
  }

  function tick(){
    if(!running)return;
    frame++;cooldown=Math.max(0,cooldown-1);
    if((keys['ArrowLeft']||keys['a'])&&px>0)px-=5;
    if((keys['ArrowRight']||keys['d'])&&px<W-PW)px+=5;
    if((keys[' ']||keys['ArrowUp'])&&cooldown===0){bullets.push({x:px+PW/2,y:H-44});cooldown=16;}

    const alive=invaders.filter(i=>i.alive);
    if(alive.length===0){invaders=makeInv();ispd+=0.15;}
    alive.forEach(inv=>inv.x+=idir*ispd);
    const minX=Math.min(...alive.map(i=>i.x));
    const maxX=Math.max(...alive.map(i=>i.x+IW));
    if(maxX>W-8||minX<8){idir*=-1;alive.forEach(i=>i.y+=14);}
    if(alive.some(i=>i.y+IH>H-30)){running=false;makeGameOver(cc,score,reset);return;}

    if(frame%Math.max(20,75-~~(score/15))===0&&alive.length){
      const s=alive[~~(Math.random()*alive.length)];
      ibullets.push({x:s.x+IW/2,y:s.y+IH});
    }

    bullets=bullets.filter(b=>{b.y-=10;return b.y>0;});
    ibullets=ibullets.filter(b=>{b.y+=4;return b.y<H;});

    for(let bi=bullets.length-1;bi>=0;bi--){
      const b=bullets[bi];
      for(let ii=0;ii<invaders.length;ii++){
        const inv=invaders[ii];if(!inv.alive)continue;
        if(b.x>inv.x&&b.x<inv.x+IW&&b.y<inv.y+IH&&b.y>inv.y){
          inv.alive=false;bullets.splice(bi,1);score+=10*(inv.row+1);setScore(score);break;
        }
      }
    }
    for(let bi=ibullets.length-1;bi>=0;bi--){
      const b=ibullets[bi];
      if(b.x>px&&b.x<px+PW&&b.y>H-48&&b.y<H-20){ibullets.splice(bi,1);lives--;if(lives<=0){running=false;makeGameOver(cc,score,reset);return;}}
    }
    draw();
  }

  document.onkeydown=e=>{keys[e.key]=true;if([' ','ArrowLeft','ArrowRight','ArrowUp'].includes(e.key))e.preventDefault();};
  document.onkeyup=e=>{keys[e.key]=false;};

  const mc=document.createElement('div');mc.style.cssText='display:flex;gap:8px;margin-top:8px;align-items:center;';
  mc.innerHTML=`<button class="dpad-btn" id="il" style="width:52px;height:48px;font-size:22px">◀</button><button class="ctrl-btn" id="if" style="padding:14px 22px;font-size:11px">FIRE</button><button class="dpad-btn" id="ir" style="width:52px;height:48px;font-size:22px">▶</button>`;
  cc.appendChild(mc);
  mc.querySelector('#il').addEventListener('touchstart',e=>{e.preventDefault();keys['ArrowLeft']=true;});mc.querySelector('#il').addEventListener('touchend',()=>keys['ArrowLeft']=false);
  mc.querySelector('#ir').addEventListener('touchstart',e=>{e.preventDefault();keys['ArrowRight']=true;});mc.querySelector('#ir').addEventListener('touchend',()=>keys['ArrowRight']=false);
  mc.querySelector('#if').addEventListener('touchstart',e=>{e.preventDefault();keys[' ']=true;});mc.querySelector('#if').addEventListener('touchend',()=>keys[' ']=false);
  ctrl.innerHTML='<span class="ctrl-hint">← → move &nbsp;|&nbsp; Space/↑ shoot · top rows = more points</span>';
  reset();draw();window._gameLoop=setInterval(tick,16);
}
</script>
</body>
</html>
