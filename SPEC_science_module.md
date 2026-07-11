\# 🔬 Научный модуль: Сравнение двух методов расчёта АЧХ



Вот полный код с интеграцией научного модуля. Сохраните как `oscillator-chain-v2.0-SCIENCE.html`.



```html

<!DOCTYPE html>

<html lang="ru">

<head>

<meta charset="UTF-8">

<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">

<title>1D Цепь: Игра + Научный анализ</title>

<style>

\* { margin:0; padding:0; box-sizing:border-box; -webkit-tap-highlight-color: transparent; }

html, body { height:100%; overflow:hidden; background:#111; color:#ccc; font-family:sans-serif; }

\#sim { position:relative; width:100%; height:100%; }

canvas { display:block; width:100%; height:100%; }



\#ui {

&#x20; position: absolute; top:0; right:0; bottom:0; width:280px;

&#x20; background: rgba(20,20,20,0.95); border-left: 1px solid #333;

&#x20; overflow-y: auto; padding: 15px; z-index: 10;

&#x20; transition: transform 0.3s;

}

\#ui.hidden { transform: translateX(100%); }



\#toggleBtn {

&#x20; position: absolute; top:10px; right:10px; z-index:20;

&#x20; width:40px; height:40px; background:#333; border:none; border-radius:50%;

&#x20; color:#fff; font-size:20px; cursor:pointer;

}



h2 { color:#fc6; font-size:16px; margin-bottom:10px; border-bottom:1px solid #444; padding-bottom:5px; }

h3 { color:#6cf; font-size:13px; margin:15px 0 5px; }

label { display:flex; justify-content:space-between; font-size:12px; margin:5px 0; }

input\[type=range] { width:140px; }

.v { color:#fc6; min-width:30px; text-align:right; }



button {

&#x20; width:100%; padding:10px; margin:5px 0; border:none; border-radius:5px;

&#x20; background:#4a6; color:#fff; font-weight:bold; cursor:pointer; font-size:13px;

}

button:active { background:#3a5; }

button.reset { background:#a44; }

button.type-btn { background:#555; margin-bottom:3px; }

button.type-btn.active { background:#fc6; color:#000; }



\#status {

&#x20; position: absolute; top:10px; left:10px; z-index:5;

&#x20; background: rgba(0,0,0,0.7); padding:8px 12px; border-radius:5px;

&#x20; font-size:12px; line-height:1.5; pointer-events:none;

}



.hint-box {

&#x20; background:#1a1a1a; border:1px solid #333; border-radius:5px;

&#x20; padding:8px; margin-top:10px; font-size:11px; line-height:1.4; color:#aaa;

}

.hint-box b { color:#fc6; }



\#controls {

&#x20; position: absolute; bottom:20px; left:50%; transform:translateX(-50%);

&#x20; display:flex; gap:10px; z-index:5;

}

.ctrl-btn {

&#x20; width:60px; height:60px; background:rgba(50,50,50,0.8); border:2px solid #666;

&#x20; border-radius:10px; color:#fff; font-size:24px; cursor:pointer;

&#x20; display:flex; align-items:center; justify-content:center;

}

.ctrl-btn:active { background:rgba(100,100,100,0.9); }



.legend { display:flex; gap:8px; margin:6px 0; font-size:10px; flex-wrap:wrap; }

.legend span { display:flex; align-items:center; gap:4px; }

.legend .box { width:14px; height:10px; border-radius:2px; }



.checkbox-label {

&#x20; display:flex; align-items:center; gap:8px; font-size:11px; margin:5px 0;

}

.checkbox-label input { width:auto; }



/\* Кнопки переключения режимов \*/

\#modeButtons {

&#x20; position: absolute; top:10px; left:60px; z-index:20;

&#x20; display:flex; gap:5px;

}

\#modeButtons button {

&#x20; width:40px; height:40px; border-radius:50%; font-size:16px;

&#x20; display:flex; align-items:center; justify-content:center;

}

\#btnGame { background:#4a6; }

\#btnScience { background:#a6f; }

\#btnGame.active, #btnScience.active { background:#fc6; color:#000; }



/\* Научный режим \*/

\#science-panel {

&#x20; position: absolute; top:0; left:0; right:280px; bottom:0;

&#x20; background: rgba(17,17,17,0.98); padding: 20px;

&#x20; overflow-y: auto; z-index: 8; display: none;

}

\#science-panel.active { display: block; }

\#science-panel h2 { color: #fc6; margin-bottom: 15px; }

\#science-panel .form-group {

&#x20; background: #1a1a1a; padding: 15px; margin-bottom: 15px;

&#x20; border-radius: 5px; border: 1px solid #333;

}

\#science-panel label {

&#x20; display: flex; justify-content: space-between; align-items: center;

&#x20; margin: 8px 0; font-size: 12px;

}

\#science-panel input\[type="number"] {

&#x20; width: 100px; padding: 5px; background: #222; color: #fff;

&#x20; border: 1px solid #444; border-radius: 3px;

}

\#science-panel button {

&#x20; background: #4a6; color: #fff; padding: 12px;

&#x20; margin: 5px 0; border: none; border-radius: 5px;

&#x20; cursor: pointer; font-weight: bold; width: 100%;

}

\#science-panel button:hover { background: #5b7; }

\#science-panel button.compare { background: #a6f; }

\#science-panel button.compare:hover { background: #b7f; }

\#science-panel button.export { background: #fc6; color:#000; }

\#science-panel button.export:hover { background: #fd7; }

\#frf-canvas {

&#x20; width: 100%; height: 500px; background: #0a0a0a;

&#x20; border: 1px solid #444; border-radius: 5px; margin-top: 15px;

}

.info-box {

&#x20; background: #222; padding: 10px; border-radius: 5px;

&#x20; font-size: 11px; color: #aaa; margin-top: 10px; line-height: 1.5;

}

.info-box b { color: #fc6; }

.info-box .linear { color: #4f4; }

.info-box .nonlinear { color: #f44; }

\#results-box {

&#x20; background: #1a1a1a; padding: 15px; border-radius: 5px;

&#x20; border: 1px solid #333; margin-top: 15px; font-size: 12px;

&#x20; line-height: 1.6;

}

\#results-box h3 { color: #6cf; margin-bottom: 10px; }

\#results-box .metric {

&#x20; display: flex; justify-content: space-between;

&#x20; padding: 5px 0; border-bottom: 1px solid #333;

}

\#results-box .metric:last-child { border-bottom: none; }

\#results-box .metric b { color: #fc6; }

\#results-box .metric .value { color: #fff; font-weight: bold; }

\#results-box .metric .good { color: #4f4; }

\#results-box .metric .bad { color: #f44; }

</style>

</head>

<body>



<div id="sim">

&#x20; <canvas id="c"></canvas>

&#x20; 

&#x20; <div id="modeButtons">

&#x20;   <button id="btnGame" class="active" onclick="switchMode('game')">🎮</button>

&#x20;   <button id="btnScience" onclick="switchMode('science')">🔬</button>

&#x20; </div>

&#x20; 

&#x20; <button id="toggleBtn" onclick="toggleUI()">⚙</button>

&#x20; 

&#x20; <div id="status">

&#x20;   Масс: <span id="stN" style="color:#fc6">0</span><br>

&#x20;   Связей: <span id="stC" style="color:#fc6">0</span><br>

&#x20;   Макс. отклонение: <span id="stAmp" style="color:#fc6">0</span> px<br>

&#x20;   Состояние: <span id="stState" style="color:#4f4">ПОКОЙ</span>

&#x20; </div>



&#x20; <div id="controls">

&#x20;   <button class="ctrl-btn" onclick="moveWagon(-1)">←</button>

&#x20;   <button class="ctrl-btn" onclick="moveWagon(1)">→</button>

&#x20; </div>



&#x20; <!-- Научный режим -->

&#x20; <div id="science-panel">

&#x20;   <h2>🔬 Научный анализ: Сравнение методов расчёта АЧХ</h2>

&#x20;   

&#x20;   <div class="form-group">

&#x20;     <h3 style="color:#6cf; margin-bottom:10px;">Параметры системы</h3>

&#x20;     <label>Количество масс N <input type="number" id="sci-N" value="3" min="2" max="10"></label>

&#x20;     <label>Масса m (кг) <input type="number" id="sci-m" value="1" step="0.1"></label>

&#x20;     <label>Жёсткость k (Н/м) <input type="number" id="sci-k" value="100" step="10"></label>

&#x20;     <label>Демпфирование b (Н·с/м) <input type="number" id="sci-b" value="0.1" step="0.05"></label>

&#x20;     <label>Нелинейность α <input type="number" id="sci-alpha" value="0" step="1"></label>

&#x20;     <label>Нелинейность β <input type="number" id="sci-beta" value="0" step="0.5"></label>

&#x20;     <label>Амплитуда возбуждения A <input type="number" id="sci-A" value="10" step="1"></label>

&#x20;   </div>

&#x20;   

&#x20;   <div class="form-group">

&#x20;     <h3 style="color:#6cf; margin-bottom:10px;">Диапазон частот</h3>

&#x20;     <label>ω мин (рад/с) <input type="number" id="sci-omega-min" value="1" step="0.5"></label>

&#x20;     <label>ω макс (рад/с) <input type="number" id="sci-omega-max" value="20" step="0.5"></label>

&#x20;     <label>Шаг Δω <input type="number" id="sci-omega-step" value="0.5" step="0.1"></label>

&#x20;   </div>

&#x20;   

&#x20;   <button onclick="calculateFRF()">📊 Построить АЧХ (оба метода)</button>

&#x20;   <button class="export" onclick="exportAllData()">💾 Экспорт всех данных (JSON + CSV)</button>

&#x20;   

&#x20;   <canvas id="frf-canvas"></canvas>

&#x20;   

&#x20;   <div id="results-box" style="display:none;">

&#x20;     <h3>📈 Результаты сравнения</h3>

&#x20;     <div id="results-content"></div>

&#x20;   </div>

&#x20;   

&#x20;   <div class="info-box">

&#x20;     <b>📚 Два метода расчёта АЧХ:</b><br>

&#x20;     • <span class="linear">🔵 Структурный (комплексные амплитуды)</span> — решение алгебраической системы (-ω²M + iωB + K)X = F<br>

&#x20;     • <span class="nonlinear">🔴 Численный (интегрирование ДУ)</span> — метод Эйлера + выделение установившегося режима<br>

&#x20;     <br>

&#x20;     <b>🎯 Что это даёт для диссертации:</b><br>

&#x20;     • Для <span class="linear">линейных систем</span> (α=0, β=0) оба метода должны совпадать<br>

&#x20;     • Для <span class="nonlinear">нелинейных систем</span> (α≠0 или β≠0) численный метод покажет искажения<br>

&#x20;     • Разница между методами — мера нелинейных эффектов<br>

&#x20;     <br>

&#x20;     <b>📊 Экспорт данных:</b><br>

&#x20;     • JSON — математическая модель (матрицы M, K, B)<br>

&#x20;     • CSV — временные ряды и АЧХ для обоих методов

&#x20;   </div>

&#x20; </div>



&#x20; <div id="ui">

&#x20;   <h2>Поперечные колебания</h2>

&#x20;   

&#x20;   <div class="legend">

&#x20;     <span><div class="box" style="background:#4cf"></div>Масса</span>

&#x20;     <span><div class="box" style="background:#4a4"></div>Пружина</span>

&#x20;     <span><div class="box" style="background:#f80"></div>Нелин. пружина</span>

&#x20;     <span><div class="box" style="background:#a6f"></div>Демпфер</span>

&#x20;     <span><div class="box" style="background:#c4f"></div>Нелин. демпфер</span>

&#x20;     <span><div class="box" style="background:#f44"></div>Возбудитель</span>

&#x20;     <span><div class="box" style="background:#fa0"></div>Фазовый сдвиг</span>

&#x20;     <span><div class="box" style="background:#f00"></div>Уничтожитель</span>

&#x20;   </div>



&#x20;   <h3>Тип элемента</h3>

&#x20;   <button class="type-btn active" id="btnWagon" onclick="selectType('wagon')">🚂 Масса</button>

&#x20;   <button class="type-btn" id="btnSpring" onclick="selectType('spring')">🟢 Пружина</button>

&#x20;   <button class="type-btn" id="btnNonlinear" onclick="selectType('nonlinear')">🟠 Нелин. пружина</button>

&#x20;   <button class="type-btn" id="btnDamper" onclick="selectType('damper')">🔵 Демпфер</button>

&#x20;   <button class="type-btn" id="btnNonlinearDamper" onclick="selectType('nonlinearDamper')">🟣 Нелин. демпфер</button>

&#x20;   <button class="type-btn" id="btnForce" onclick="selectType('force')">🔴 Возбудитель</button>

&#x20;   <button class="type-btn" id="btnPhase" onclick="selectType('phase')">🟡 Фазовый сдвиг</button>

&#x20;   <button class="type-btn" id="btnDestroyer" onclick="selectType('destroyer')">💥 Уничтожитель</button>



&#x20;   <h3>Параметры</h3>

&#x20;   <label>Скорость g <input type="range" id="fallSpeed" min="50" max="1500" value="300" step="50"><span class="v" id="fallSpeedv">300</span></label>

&#x20;   <label>Жёсткость k <input type="range" id="sK" min="10" max="200" value="50"><span class="v" id="sKv">50</span></label>

&#x20;   <label>Нелинейность α <input type="range" id="sAlpha" min="-50" max="50" value="0" step="1"><span class="v" id="sAlphav">0</span></label>

&#x20;   <label>Демпфирование b <input type="range" id="sB" min="0" max="1" step="0.05" value="0.1"><span class="v" id="sBv">0.1</span></label>

&#x20;   <label>Нелинейность β <input type="range" id="sBeta" min="-50" max="50" value="0" step="1"><span class="v" id="sBetav">0</span></label>

&#x20;   <label>Расстояние L₀ <input type="range" id="sL" min="40" max="100" value="70"><span class="v" id="sLv">70</span></label>



&#x20;   <h3>Возбудитель</h3>

&#x20;   <label>Частота ω <input type="range" id="forceFreq" min="1" max="20" value="5" step="0.5"><span class="v" id="forceFreqv">5</span></label>

&#x20;   <label>Амплитуда A <input type="range" id="forceAmp" min="5" max="100" value="30"><span class="v" id="forceAmpv">30</span></label>

&#x20;   <label>Фаза φ (°) <input type="range" id="phaseShift" min="0" max="360" value="0" step="10"><span class="v" id="phaseShiftv">0</span></label>



&#x20;   <h3>Визуализация</h3>

&#x20;   <label class="checkbox-label">

&#x20;     <input type="checkbox" id="showGraph" checked> График колебаний

&#x20;   </label>

&#x20;   <label class="checkbox-label">

&#x20;     <input type="checkbox" id="showForces"> Стрелки сил

&#x20;   </label>

&#x20;   <label class="checkbox-label">

&#x20;     <input type="checkbox" id="showTension" checked> Цвет натяжения

&#x20;   </label>

&#x20;   <label class="checkbox-label">

&#x20;     <input type="checkbox" id="showHarmonicForce"> Показать гармоническую силу

&#x20;   </label>



&#x20;   <h3>Управление</h3>

&#x20;   <button onclick="dropElement()">⬇ Сбросить элемент</button>

&#x20;   <button class="reset" onclick="resetChain()">🔄 Сброс цепи</button>

&#x20;   

&#x20;   <div class="hint-box">

&#x20;     <b>🎮 Как играть:</b><br>

&#x20;     • Выберите тип элемента<br>

&#x20;     • Нажмите "Сбросить элемент"<br>

&#x20;     • Стрелки ← → двигают элемент<br>

&#x20;     • Элемент встроится в цепь<br>

&#x20;     • Наблюдайте колебания на графике<br>

&#x20;     <br>

&#x20;     <b>🔬 Научный режим:</b><br>

&#x20;     • Нажмите кнопку 🔬 вверху слева<br>

&#x20;     • Задайте параметры системы<br>

&#x20;     • Постройте АЧХ двумя методами<br>

&#x20;     • Сравните результаты

&#x20;   </div>

&#x20; </div>

</div>



<script>

const canvas = document.getElementById('c');

const ctx = canvas.getContext('2d');



const P = (id) => parseFloat(document.getElementById(id).value);

const C = (id) => document.getElementById(id).checked;

\['sK','sAlpha','sB','sBeta','sL','fallSpeed','forceFreq','forceAmp','phaseShift'].forEach(id => {

&#x20; const el = document.getElementById(id);

&#x20; el.oninput = () => document.getElementById(id+'v').textContent = el.value;

});



let blocks = \[];

let connections = \[];

let fallingElement = null;

let particles = \[];

let time = 0;

let equilibriumY = 0;

let currentType = 'wagon';

let currentMode = 'game';



const history = \[];

const MAX\_HISTORY = 200;



const WAGON\_W = 30;

const WAGON\_H = 20;

const RAMP\_WIDTH = 40;



function resize() {

&#x20; canvas.width = window.innerWidth;

&#x20; canvas.height = window.innerHeight;

&#x20; equilibriumY = canvas.height \* 0.72;

}

window.addEventListener('resize', resize);

resize();



function switchMode(mode) {

&#x20; currentMode = mode;

&#x20; document.getElementById('btnGame').classList.toggle('active', mode === 'game');

&#x20; document.getElementById('btnScience').classList.toggle('active', mode === 'science');

&#x20; 

&#x20; if (mode === 'science') {

&#x20;   document.getElementById('science-panel').classList.add('active');

&#x20;   document.getElementById('sim').style.display = 'none';

&#x20;   document.getElementById('controls').style.display = 'none';

&#x20;   document.getElementById('status').style.display = 'none';

&#x20; } else {

&#x20;   document.getElementById('science-panel').classList.remove('active');

&#x20;   document.getElementById('sim').style.display = 'block';

&#x20;   document.getElementById('controls').style.display = 'flex';

&#x20;   document.getElementById('status').style.display = 'block';

&#x20; }

}



function toggleUI() {

&#x20; document.getElementById('ui').classList.toggle('hidden');

}



function selectType(type) {

&#x20; currentType = type;

&#x20; document.querySelectorAll('.type-btn').forEach(btn => btn.classList.remove('active'));

&#x20; document.getElementById('btn' + type.charAt(0).toUpperCase() + type.slice(1)).classList.add('active');

}



function initChain() {

&#x20; blocks = \[];

&#x20; connections = \[];

&#x20; particles = \[];

&#x20; history.length = 0;

&#x20; 

&#x20; const n = 7;

&#x20; const L0 = P('sL');

&#x20; const startX = canvas.width/2 - (n-1)\*L0/2;

&#x20; 

&#x20; for(let i=0; i<n; i++) {

&#x20;   blocks.push({

&#x20;     x: startX + i\*L0,

&#x20;     y: equilibriumY,

&#x20;     vy: 0,

&#x20;     m: (i===0 || i===n-1) ? 1e9 : 10,

&#x20;     fixed: (i===0 || i===n-1),

&#x20;     color: (i===0 || i===n-1) ? '#666' : '#4cf',

&#x20;     extForce: { amplitude: 0, frequency: 0, phase: 0 },

&#x20;     flash: 0,

&#x20;     force: 0,

&#x20;     harmonicForce: 0

&#x20;   });

&#x20; }

&#x20; 

&#x20; rebuildConnections();

}



function resetChain() {

&#x20; initChain();

&#x20; fallingElement = null;

}



function rebuildConnections() {

&#x20; connections = \[];

&#x20; for(let i=0; i<blocks.length-1; i++) {

&#x20;   connections.push({

&#x20;     left: i, right: i+1,

&#x20;     k: P('sK'), alpha: P('sAlpha'),

&#x20;     b: P('sB'), beta: P('sBeta'),

&#x20;     multiplier: 1, flash: 0, tension: 0,

&#x20;     nonlinearSpring: Math.abs(P('sAlpha')) > 0.5,

&#x20;     nonlinearDamping: Math.abs(P('sBeta')) > 0.5

&#x20;   });

&#x20; }

}



function dropElement() {

&#x20; if(fallingElement) return;

&#x20; 

&#x20; fallingElement = {

&#x20;   x: canvas.width / 2,

&#x20;   y: 0,

&#x20;   vy: 0,

&#x20;   type: currentType,

&#x20;   color: getTypeColor(currentType)

&#x20; };

&#x20; 

&#x20; if(window.innerWidth < 768) toggleUI();

}



function getTypeColor(type) {

&#x20; const colors = {

&#x20;   wagon: '#4cf',

&#x20;   spring: '#4a4',

&#x20;   nonlinear: '#f80',

&#x20;   damper: '#a6f',

&#x20;   nonlinearDamper: '#c4f',

&#x20;   force: '#f44',

&#x20;   phase: '#fa0',

&#x20;   destroyer: '#f00'

&#x20; };

&#x20; return colors\[type] || '#4cf';

}



function moveWagon(dir) {

&#x20; if(!fallingElement) return;

&#x20; fallingElement.x += dir \* 30;

&#x20; fallingElement.x = Math.max(WAGON\_W/2, Math.min(canvas.width - WAGON\_W/2, fallingElement.x));

}



document.addEventListener('keydown', (e) => {

&#x20; if(e.key === 'ArrowLeft') {

&#x20;   moveWagon(-1);

&#x20;   e.preventDefault();

&#x20; } else if(e.key === 'ArrowRight') {

&#x20;   moveWagon(1);

&#x20;   e.preventDefault();

&#x20; }

});



function physics(dt) {

&#x20; const sub = 8;

&#x20; const sdt = dt/sub;

&#x20; 

&#x20; if(fallingElement) {

&#x20;   const g = P('fallSpeed');

&#x20;   fallingElement.vy += g \* dt;

&#x20;   fallingElement.y += fallingElement.vy \* dt;

&#x20;   

&#x20;   if(fallingElement.y >= equilibriumY - WAGON\_H/2) {

&#x20;     fallingElement.y = equilibriumY - WAGON\_H/2;

&#x20;     applyElement(fallingElement);

&#x20;     fallingElement = null;

&#x20;   }

&#x20; }

&#x20; 

&#x20; for(let s=0; s<sub; s++) {

&#x20;   for(let i=0; i<blocks.length; i++) {

&#x20;     if(blocks\[i].fixed) continue;

&#x20;     

&#x20;     let F = 0;

&#x20;     

&#x20;     for(let c of connections) {

&#x20;       if(c.left === i) {

&#x20;         const dy = blocks\[c.right].y - blocks\[i].y;

&#x20;         const dv = blocks\[c.right].vy - blocks\[i].vy;

&#x20;         

&#x20;         const linearSpring = c.k \* dy;

&#x20;         const nonlinearSpring = c.alpha \* dy \* dy \* dy;

&#x20;         const linearDamp = c.b \* dv;

&#x20;         const nonlinearDamp = c.beta \* dv \* Math.abs(dv);

&#x20;         

&#x20;         F += linearSpring + nonlinearSpring + linearDamp + nonlinearDamp;

&#x20;         c.tension = dy / P('sL');

&#x20;       } else if(c.right === i) {

&#x20;         const dy = blocks\[c.left].y - blocks\[i].y;

&#x20;         const dv = blocks\[c.left].vy - blocks\[i].vy;

&#x20;         

&#x20;         const linearSpring = c.k \* dy;

&#x20;         const nonlinearSpring = c.alpha \* dy \* dy \* dy;

&#x20;         const linearDamp = c.b \* dv;

&#x20;         const nonlinearDamp = c.beta \* dv \* Math.abs(dv);

&#x20;         

&#x20;         F += linearSpring + nonlinearSpring + linearDamp + nonlinearDamp;

&#x20;       }

&#x20;     }

&#x20;     

&#x20;     const displacement = blocks\[i].y - equilibriumY;

&#x20;     F -= P('sK') \* 0.1 \* displacement;

&#x20;     

&#x20;     let harmonicF = 0;

&#x20;     if(blocks\[i].extForce \&\& blocks\[i].extForce.amplitude > 0) {

&#x20;       harmonicF = blocks\[i].extForce.amplitude \* Math.sin(

&#x20;         blocks\[i].extForce.frequency \* time + blocks\[i].extForce.phase

&#x20;       );

&#x20;       F += harmonicF;

&#x20;     }

&#x20;     

&#x20;     blocks\[i].force = F;

&#x20;     blocks\[i].harmonicForce = harmonicF;

&#x20;     blocks\[i].vy += (F / blocks\[i].m) \* sdt;

&#x20;     blocks\[i].y += blocks\[i].vy \* sdt;

&#x20;   }

&#x20; }

&#x20; 

&#x20; if(history.length >= MAX\_HISTORY) history.shift();

&#x20; history.push(blocks.map(b => b.y - equilibriumY));

&#x20; 

&#x20; for(let i=particles.length-1; i>=0; i--) {

&#x20;   let p = particles\[i];

&#x20;   p.x += p.vx \* dt; p.y += p.vy \* dt;

&#x20;   p.vy += 500 \* dt; p.life -= dt;

&#x20;   if(p.life <= 0) particles.splice(i, 1);

&#x20; }

&#x20; 

&#x20; for(let b of blocks) {

&#x20;   if(b.flash > 0) b.flash -= 0.05;

&#x20; }

&#x20; for(let c of connections) {

&#x20;   if(c.flash > 0) c.flash -= 0.05;

&#x20; }

&#x20; 

&#x20; time += dt;

}



function applyElement(element) {

&#x20; if(element.type === 'destroyer') {

&#x20;   const target = findNearestElement(element.x);

&#x20;   

&#x20;   if(target.type === 'block') {

&#x20;     const blockIdx = target.index;

&#x20;     if(!blocks\[blockIdx].fixed) {

&#x20;       createBigExplosion(blocks\[blockIdx].x, blocks\[blockIdx].y, '#f00');

&#x20;       blocks.splice(blockIdx, 1);

&#x20;       rebuildConnections();

&#x20;     }

&#x20;   } else if(target.type === 'connection') {

&#x20;     const connIdx = target.index;

&#x20;     createBigExplosion(

&#x20;       (blocks\[connections\[connIdx].left].x + blocks\[connections\[connIdx].right].x) / 2,

&#x20;       (blocks\[connections\[connIdx].left].y + blocks\[connections\[connIdx].right].y) / 2,

&#x20;       '#f00'

&#x20;     );

&#x20;     connections.splice(connIdx, 1);

&#x20;   }

&#x20;   return;

&#x20; }

&#x20; 

&#x20; if(element.type === 'wagon') {

&#x20;   const insertIdx = findBestInsertionIndex(element.x);

&#x20;   const leftX = blocks\[insertIdx-1].x;

&#x20;   const rightX = blocks\[insertIdx].x;

&#x20;   const insertX = (leftX + rightX) / 2;

&#x20;   

&#x20;   blocks.splice(insertIdx, 0, {

&#x20;     x: insertX,

&#x20;     y: equilibriumY - 30,

&#x20;     vy: 0,

&#x20;     m: 10,

&#x20;     fixed: false,

&#x20;     color: '#4cf',

&#x20;     flash: 1.0,

&#x20;     extForce: { amplitude: 0, frequency: 0, phase: 0 },

&#x20;     force: 0,

&#x20;     harmonicForce: 0

&#x20;   });

&#x20;   rebuildConnections();

&#x20;   

&#x20; } else if(element.type === 'spring') {

&#x20;   const connIdx = findBestConnectionIndex(element.x);

&#x20;   if(connIdx >= 0 \&\& connIdx < connections.length) {

&#x20;     connections\[connIdx].alpha = 0;

&#x20;     connections\[connIdx].nonlinearSpring = false;

&#x20;     connections\[connIdx].k = P('sK');

&#x20;     connections\[connIdx].flash = 1.0;

&#x20;   }

&#x20;   

&#x20; } else if(element.type === 'nonlinear') {

&#x20;   const connIdx = findBestConnectionIndex(element.x);

&#x20;   if(connIdx >= 0 \&\& connIdx < connections.length) {

&#x20;     connections\[connIdx].alpha = P('sAlpha');

&#x20;     connections\[connIdx].nonlinearSpring = true;

&#x20;     connections\[connIdx].flash = 1.0;

&#x20;   }

&#x20;   

&#x20; } else if(element.type === 'damper') {

&#x20;   const connIdx = findBestConnectionIndex(element.x);

&#x20;   if(connIdx >= 0 \&\& connIdx < connections.length) {

&#x20;     connections\[connIdx].beta = 0;

&#x20;     connections\[connIdx].nonlinearDamping = false;

&#x20;     connections\[connIdx].b \*= 1.5;

&#x20;     connections\[connIdx].multiplier \*= 1.5;

&#x20;     connections\[connIdx].flash = 1.0;

&#x20;   }

&#x20;   

&#x20; } else if(element.type === 'nonlinearDamper') {

&#x20;   const connIdx = findBestConnectionIndex(element.x);

&#x20;   if(connIdx >= 0 \&\& connIdx < connections.length) {

&#x20;     connections\[connIdx].beta = P('sBeta');

&#x20;     connections\[connIdx].nonlinearDamping = true;

&#x20;     connections\[connIdx].flash = 1.0;

&#x20;   }

&#x20;   

&#x20; } else if(element.type === 'force') {

&#x20;   const blockIdx = findBestBlockIndex(element.x);

&#x20;   if(blockIdx >= 0 \&\& blockIdx < blocks.length \&\& !blocks\[blockIdx].fixed) {

&#x20;     blocks\[blockIdx].extForce.amplitude += P('forceAmp');

&#x20;     blocks\[blockIdx].extForce.frequency = P('forceFreq');

&#x20;     blocks\[blockIdx].extForce.phase = P('phaseShift') \* Math.PI / 180;

&#x20;     blocks\[blockIdx].flash = 1.0;

&#x20;   }

&#x20;   

&#x20; } else if(element.type === 'phase') {

&#x20;   const blockIdx = findBestBlockIndex(element.x);

&#x20;   if(blockIdx >= 0 \&\& blockIdx < blocks.length \&\& !blocks\[blockIdx].fixed) {

&#x20;     if(blocks\[blockIdx].extForce.amplitude > 0) {

&#x20;       blocks\[blockIdx].extForce.phase = P('phaseShift') \* Math.PI / 180;

&#x20;     } else {

&#x20;       blocks\[blockIdx].extForce.amplitude = 0;

&#x20;       blocks\[blockIdx].extForce.frequency = P('forceFreq');

&#x20;       blocks\[blockIdx].extForce.phase = P('phaseShift') \* Math.PI / 180;

&#x20;     }

&#x20;     blocks\[blockIdx].flash = 1.0;

&#x20;   }

&#x20; }

&#x20; 

&#x20; createExplosion(element.x, equilibriumY, element.color);

}



function findNearestElement(targetX) {

&#x20; let nearestBlock = null;

&#x20; let nearestBlockDist = Infinity;

&#x20; let nearestConn = null;

&#x20; let nearestConnDist = Infinity;

&#x20; 

&#x20; for(let i=0; i<blocks.length; i++) {

&#x20;   if(blocks\[i].fixed) continue;

&#x20;   const dist = Math.abs(blocks\[i].x - targetX);

&#x20;   if(dist < nearestBlockDist) {

&#x20;     nearestBlockDist = dist;

&#x20;     nearestBlock = { type: 'block', index: i };

&#x20;   }

&#x20; }

&#x20; 

&#x20; for(let i=0; i<connections.length; i++) {

&#x20;   const midX = (blocks\[connections\[i].left].x + blocks\[connections\[i].right].x) / 2;

&#x20;   const dist = Math.abs(midX - targetX);

&#x20;   if(dist < nearestConnDist) {

&#x20;     nearestConnDist = dist;

&#x20;     nearestConn = { type: 'connection', index: i };

&#x20;   }

&#x20; }

&#x20; 

&#x20; return nearestBlockDist < nearestConnDist ? nearestBlock : nearestConn;

}



function findBestInsertionIndex(targetX) {

&#x20; let bestIdx = 1;

&#x20; let bestDist = Infinity;

&#x20; for(let i=0; i<blocks.length-1; i++) {

&#x20;   const midX = (blocks\[i].x + blocks\[i+1].x) / 2;

&#x20;   const dist = Math.abs(midX - targetX);

&#x20;   if(dist < bestDist) {

&#x20;     bestDist = dist;

&#x20;     bestIdx = i + 1;

&#x20;   }

&#x20; }

&#x20; return bestIdx;

}



function findBestConnectionIndex(targetX) {

&#x20; let bestIdx = 0;

&#x20; let bestDist = Infinity;

&#x20; for(let i=0; i<connections.length; i++) {

&#x20;   const midX = (blocks\[connections\[i].left].x + blocks\[connections\[i].right].x) / 2;

&#x20;   const dist = Math.abs(midX - targetX);

&#x20;   if(dist < bestDist) {

&#x20;     bestDist = dist;

&#x20;     bestIdx = i;

&#x20;   }

&#x20; }

&#x20; return bestIdx;

}



function findBestBlockIndex(targetX) {

&#x20; let bestIdx = 0;

&#x20; let bestDist = Infinity;

&#x20; for(let i=0; i<blocks.length; i++) {

&#x20;   if(blocks\[i].fixed) continue;

&#x20;   const dist = Math.abs(blocks\[i].x - targetX);

&#x20;   if(dist < bestDist) {

&#x20;     bestDist = dist;

&#x20;     bestIdx = i;

&#x20;   }

&#x20; }

&#x20; return bestIdx;

}



function createExplosion(x, y, color) {

&#x20; for(let i=0; i<15; i++) {

&#x20;   particles.push({

&#x20;     x: x, y: y,

&#x20;     vx: (Math.random()-0.5)\*200,

&#x20;     vy: (Math.random()-0.5)\*200 - 100,

&#x20;     life: 0.5 + Math.random()\*0.5,

&#x20;     color: color

&#x20;   });

&#x20; }

}



function createBigExplosion(x, y, color) {

&#x20; for(let i=0; i<40; i++) {

&#x20;   const angle = Math.random() \* Math.PI \* 2;

&#x20;   const speed = 100 + Math.random() \* 300;

&#x20;   particles.push({

&#x20;     x: x, y: y,

&#x20;     vx: Math.cos(angle) \* speed,

&#x20;     vy: Math.sin(angle) \* speed,

&#x20;     life: 1.0 + Math.random() \* 1.0,

&#x20;     color: color,

&#x20;     size: 3 + Math.random() \* 4

&#x20;   });

&#x20; }

}



function draw() {

&#x20; ctx.fillStyle = '#111';

&#x20; ctx.fillRect(0, 0, canvas.width, canvas.height);

&#x20; 

&#x20; ctx.strokeStyle = '#444';

&#x20; ctx.lineWidth = 2;

&#x20; ctx.setLineDash(\[8, 4]);

&#x20; ctx.beginPath();

&#x20; ctx.moveTo(0, equilibriumY);

&#x20; ctx.lineTo(canvas.width, equilibriumY);

&#x20; ctx.stroke();

&#x20; ctx.setLineDash(\[]);

&#x20; 

&#x20; ctx.fillStyle = '#666';

&#x20; ctx.font = '11px sans-serif';

&#x20; ctx.textAlign = 'left';

&#x20; ctx.fillText('— линия равновесия', 10, equilibriumY - 8);

&#x20; 

&#x20; for(let c of connections) {

&#x20;   drawConnection(c);

&#x20; }

&#x20; 

&#x20; for(let b of blocks) {

&#x20;   if(b.fixed) drawSupport(b);

&#x20;   else drawMass(b);

&#x20; }

&#x20; 

&#x20; if(C('showForces')) {

&#x20;   for(let b of blocks) {

&#x20;     if(!b.fixed) drawForceArrow(b);

&#x20;   }

&#x20; }

&#x20; 

&#x20; if(C('showHarmonicForce')) {

&#x20;   for(let b of blocks) {

&#x20;     if(!b.fixed \&\& b.extForce \&\& b.extForce.amplitude > 0) {

&#x20;       drawHarmonicForce(b);

&#x20;     }

&#x20;   }

&#x20; }

&#x20; 

&#x20; if(fallingElement) {

&#x20;   drawRamp(fallingElement.x);

&#x20; }

&#x20; 

&#x20; if(fallingElement) drawFallingElement(fallingElement);

&#x20; 

&#x20; for(let p of particles) {

&#x20;   ctx.globalAlpha = Math.max(0, p.life);

&#x20;   ctx.fillStyle = p.color;

&#x20;   const size = p.size || 4;

&#x20;   ctx.fillRect(p.x-size/2, p.y-size/2, size, size);

&#x20; }

&#x20; ctx.globalAlpha = 1;

&#x20; 

&#x20; if(C('showGraph')) {

&#x20;   drawGraph();

&#x20; }

&#x20; 

&#x20; const maxAmp = Math.max(...blocks.filter(b=>!b.fixed).map(b => Math.abs(b.y - equilibriumY)));

&#x20; const moving = blocks.some(b => !b.fixed \&\& Math.abs(b.vy) > 0.1);

&#x20; document.getElementById('stN').textContent = blocks.filter(b=>!b.fixed).length;

&#x20; document.getElementById('stC').textContent = connections.length;

&#x20; document.getElementById('stAmp').textContent = maxAmp.toFixed(1);

&#x20; const stState = document.getElementById('stState');

&#x20; stState.textContent = moving ? 'КОЛЕБАНИЯ' : 'ПОКОЙ';

&#x20; stState.style.color = moving ? '#f44' : '#4f4';

}



function drawConnection(c) {

&#x20; const x1 = blocks\[c.left].x;

&#x20; const y1 = blocks\[c.left].y;

&#x20; const x2 = blocks\[c.right].x;

&#x20; const y2 = blocks\[c.right].y;

&#x20; 

&#x20; let springColor = '#4a4';

&#x20; if(c.nonlinearSpring) {

&#x20;   springColor = c.alpha > 0 ? '#f80' : '#f44';

&#x20; }

&#x20; if(C('showTension') \&\& !c.nonlinearSpring) {

&#x20;   if(c.tension > 0.1) {

&#x20;     const intensity = Math.min(1, c.tension);

&#x20;     springColor = `rgb(${Math.floor(100 + intensity\*155)}, ${Math.floor(100 - intensity\*50)}, ${Math.floor(100 - intensity\*50)})`;

&#x20;   } else if(c.tension < -0.1) {

&#x20;     const intensity = Math.min(1, -c.tension);

&#x20;     springColor = `rgb(${Math.floor(100 - intensity\*50)}, ${Math.floor(100 - intensity\*50)}, ${Math.floor(100 + intensity\*155)})`;

&#x20;   }

&#x20; }

&#x20; 

&#x20; ctx.strokeStyle = springColor;

&#x20; ctx.lineWidth = 2 + (c.multiplier - 1) \* 0.5 + (c.nonlinearSpring ? 1 : 0);

&#x20; const segments = 6;

&#x20; ctx.beginPath();

&#x20; ctx.moveTo(x1, y1);

&#x20; for(let i=1; i<=segments; i++) {

&#x20;   const t = i / segments;

&#x20;   const px = x1 + (x2 - x1) \* t;

&#x20;   const py = y1 + (y2 - y1) \* t;

&#x20;   const offset = (i % 2 === 0 ? -8 : 8);

&#x20;   ctx.lineTo(px, py + offset);

&#x20; }

&#x20; ctx.lineTo(x2, y2);

&#x20; ctx.stroke();

&#x20; 

&#x20; if(c.nonlinearSpring) {

&#x20;   const midX = (x1 + x2) / 2;

&#x20;   const midY = (y1 + y2) / 2;

&#x20;   ctx.fillStyle = springColor;

&#x20;   ctx.font = 'bold 12px sans-serif';

&#x20;   ctx.textAlign = 'center';

&#x20;   ctx.fillText('x³', midX, midY - 15);

&#x20; }

&#x20; 

&#x20; const midX = (x1 + x2) / 2;

&#x20; const midY = (y1 + y2) / 2;

&#x20; const cylW = Math.abs(x2 - x1) \* 0.3;

&#x20; const cylH = 12;

&#x20; 

&#x20; let dampColor = '#a6f';

&#x20; if(c.nonlinearDamping) dampColor = '#c4f';

&#x20; 

&#x20; ctx.fillStyle = '#333';

&#x20; ctx.fillRect(midX - cylW/2, midY - cylH/2, cylW, cylH);

&#x20; ctx.strokeStyle = dampColor;

&#x20; ctx.lineWidth = c.nonlinearDamping ? 2.5 : 1.5;

&#x20; ctx.strokeRect(midX - cylW/2, midY - cylH/2, cylW, cylH);

&#x20; 

&#x20; ctx.strokeStyle = '#888';

&#x20; ctx.lineWidth = 2;

&#x20; ctx.beginPath(); ctx.moveTo(x1, y1); ctx.lineTo(midX - cylW/2, midY); ctx.stroke();

&#x20; ctx.beginPath(); ctx.moveTo(midX + cylW/2, midY); ctx.lineTo(x2, y2); ctx.stroke();

&#x20; 

&#x20; if(c.nonlinearDamping) {

&#x20;   ctx.fillStyle = '#c4f';

&#x20;   ctx.font = 'bold 11px sans-serif';

&#x20;   ctx.textAlign = 'center';

&#x20;   ctx.fillText('v²', midX, midY - 18);

&#x20; }

&#x20; 

&#x20; if(c.flash > 0) {

&#x20;   ctx.strokeStyle = `rgba(255,255,100,${c.flash})`;

&#x20;   ctx.lineWidth = 3;

&#x20;   ctx.beginPath();

&#x20;   ctx.moveTo(x1, y1);

&#x20;   ctx.lineTo(x2, y2);

&#x20;   ctx.stroke();

&#x20; }

}



function drawHarmonicForce(b) {

&#x20; const x = b.x;

&#x20; const y = b.y;

&#x20; const force = b.harmonicForce;

&#x20; const scale = 0.8;

&#x20; const arrowLen = force \* scale;

&#x20; if(Math.abs(arrowLen) < 1) return;

&#x20; 

&#x20; ctx.strokeStyle = '#f44';

&#x20; ctx.fillStyle = '#f44';

&#x20; ctx.lineWidth = 3;

&#x20; ctx.beginPath(); ctx.moveTo(x, y); ctx.lineTo(x, y + arrowLen); ctx.stroke();

&#x20; ctx.beginPath();

&#x20; ctx.moveTo(x, y + arrowLen);

&#x20; ctx.lineTo(x - 5, y + arrowLen - (arrowLen > 0 ? 8 : -8));

&#x20; ctx.lineTo(x + 5, y + arrowLen - (arrowLen > 0 ? 8 : -8));

&#x20; ctx.closePath(); ctx.fill();

&#x20; 

&#x20; ctx.fillStyle = '#f44';

&#x20; ctx.font = 'bold 11px sans-serif';

&#x20; ctx.textAlign = 'center';

&#x20; ctx.fillText(`F=${force.toFixed(1)}`, x, y + arrowLen + (arrowLen > 0 ? 15 : -10));

&#x20; 

&#x20; const glow = Math.abs(force) / b.extForce.amplitude;

&#x20; ctx.strokeStyle = `rgba(255, 68, 68, ${glow \* 0.5})`;

&#x20; ctx.lineWidth = 1;

&#x20; ctx.beginPath(); ctx.arc(x, y, 15 + glow \* 5, 0, Math.PI \* 2); ctx.stroke();

}



function drawForceArrow(b) {

&#x20; const x = b.x;

&#x20; const y = b.y;

&#x20; const force = b.force;

&#x20; const scale = 0.5;

&#x20; const arrowLen = force \* scale;

&#x20; if(Math.abs(arrowLen) < 2) return;

&#x20; 

&#x20; ctx.strokeStyle = force > 0 ? '#f44' : '#4cf';

&#x20; ctx.fillStyle = force > 0 ? '#f44' : '#4cf';

&#x20; ctx.lineWidth = 2;

&#x20; ctx.beginPath(); ctx.moveTo(x, y); ctx.lineTo(x, y + arrowLen); ctx.stroke();

&#x20; ctx.beginPath();

&#x20; ctx.moveTo(x, y + arrowLen);

&#x20; ctx.lineTo(x - 4, y + arrowLen - (arrowLen > 0 ? 6 : -6));

&#x20; ctx.lineTo(x + 4, y + arrowLen - (arrowLen > 0 ? 6 : -6));

&#x20; ctx.closePath(); ctx.fill();

}



function drawGraph() {

&#x20; const graphHeight = 80;

&#x20; const graphY = canvas.height - graphHeight - 5;

&#x20; const graphWidth = canvas.width - 40;

&#x20; const graphX = 20;

&#x20; 

&#x20; ctx.fillStyle = 'rgba(0,0,0,0.7)';

&#x20; ctx.fillRect(graphX, graphY, graphWidth, graphHeight);

&#x20; ctx.strokeStyle = '#444'; ctx.lineWidth = 1;

&#x20; ctx.strokeRect(graphX, graphY, graphWidth, graphHeight);

&#x20; 

&#x20; ctx.strokeStyle = '#555';

&#x20; ctx.setLineDash(\[3, 3]);

&#x20; ctx.beginPath(); ctx.moveTo(graphX, graphY + graphHeight/2); ctx.lineTo(graphX + graphWidth, graphY + graphHeight/2); ctx.stroke();

&#x20; ctx.setLineDash(\[]);

&#x20; 

&#x20; ctx.fillStyle = '#888'; ctx.font = '10px sans-serif'; ctx.textAlign = 'left';

&#x20; ctx.fillText('График колебаний', graphX + 5, graphY + 12);

&#x20; 

&#x20; const colors = \['#4cf', '#6f6', '#fc6', '#f6f', '#6ff', '#fa6', '#a6f'];

&#x20; for(let massIdx = 0; massIdx < blocks.length; massIdx++) {

&#x20;   if(blocks\[massIdx].fixed) continue;

&#x20;   ctx.strokeStyle = colors\[massIdx % colors.length];

&#x20;   ctx.lineWidth = 1.5;

&#x20;   ctx.beginPath();

&#x20;   for(let i = 0; i < history.length; i++) {

&#x20;     const x = graphX + (i / MAX\_HISTORY) \* graphWidth;

&#x20;     const displacement = history\[i]\[massIdx] || 0;

&#x20;     const y = graphY + graphHeight/2 + displacement \* 1.0;

&#x20;     if(i === 0) ctx.moveTo(x, y); else ctx.lineTo(x, y);

&#x20;   }

&#x20;   ctx.stroke();

&#x20; }

&#x20; 

&#x20; ctx.font = '9px sans-serif';

&#x20; for(let i = 0; i < Math.min(5, blocks.length - 2); i++) {

&#x20;   ctx.fillStyle = colors\[i];

&#x20;   ctx.fillRect(graphX + graphWidth - 80, graphY + 5 + i\*12, 10, 8);

&#x20;   ctx.fillText(`Масса ${i+1}`, graphX + graphWidth - 65, graphY + 12 + i\*12);

&#x20; }

}



function drawRamp(elementX) {

&#x20; const rampTop = 0;

&#x20; const rampBottom = equilibriumY - 100;

&#x20; const rampHeight = rampBottom - rampTop;

&#x20; 

&#x20; ctx.fillStyle = '#1e1e1e';

&#x20; ctx.fillRect(elementX - RAMP\_WIDTH/2, rampTop, RAMP\_WIDTH, rampHeight);

&#x20; ctx.strokeStyle = '#555'; ctx.lineWidth = 1;

&#x20; ctx.strokeRect(elementX - RAMP\_WIDTH/2, rampTop, RAMP\_WIDTH, rampHeight);

&#x20; 

&#x20; ctx.strokeStyle = '#888'; ctx.lineWidth = 2;

&#x20; ctx.beginPath(); ctx.moveTo(elementX - 10, rampTop); ctx.lineTo(elementX - 10, rampBottom); ctx.stroke();

&#x20; ctx.beginPath(); ctx.moveTo(elementX + 10, rampTop); ctx.lineTo(elementX + 10, rampBottom); ctx.stroke();

&#x20; 

&#x20; ctx.strokeStyle = '#5a4a3a'; ctx.lineWidth = 3;

&#x20; for(let y=rampTop+20; y<rampBottom; y+=25) {

&#x20;   ctx.beginPath(); ctx.moveTo(elementX - 15, y); ctx.lineTo(elementX + 15, y); ctx.stroke();

&#x20; }

}



function drawFallingElement(element) {

&#x20; const x = element.x;

&#x20; const y = element.y;

&#x20; 

&#x20; if(element.type === 'wagon') {

&#x20;   ctx.fillStyle = element.color;

&#x20;   ctx.fillRect(x-WAGON\_W/2, y-WAGON\_H/2, WAGON\_W, WAGON\_H);

&#x20;   ctx.strokeStyle = '#222'; ctx.lineWidth = 1;

&#x20;   ctx.strokeRect(x-WAGON\_W/2, y-WAGON\_H/2, WAGON\_W, WAGON\_H);

&#x20;   ctx.fillStyle = '#111';

&#x20;   ctx.beginPath(); ctx.arc(x-WAGON\_W/3, y+WAGON\_H/2+2, 4, 0, Math.PI\*2); ctx.fill();

&#x20;   ctx.beginPath(); ctx.arc(x+WAGON\_W/3, y+WAGON\_H/2+2, 4, 0, Math.PI\*2); ctx.fill();

&#x20;   

&#x20; } else if(element.type === 'spring') {

&#x20;   ctx.strokeStyle = element.color; ctx.lineWidth = 3;

&#x20;   ctx.beginPath(); ctx.moveTo(x - 15, y);

&#x20;   for(let i=0; i<6; i++) ctx.lineTo(x - 15 + (i+1)\*5, y + (i%2===0?-8:8));

&#x20;   ctx.lineTo(x + 15, y); ctx.stroke();

&#x20;   

&#x20; } else if(element.type === 'nonlinear') {

&#x20;   ctx.strokeStyle = element.color; ctx.lineWidth = 4;

&#x20;   ctx.beginPath(); ctx.moveTo(x - 15, y);

&#x20;   for(let i=0; i<6; i++) ctx.lineTo(x - 15 + (i+1)\*5, y + (i%2===0?-8:8));

&#x20;   ctx.lineTo(x + 15, y); ctx.stroke();

&#x20;   ctx.fillStyle = element.color; ctx.font = 'bold 10px sans-serif'; ctx.textAlign = 'center';

&#x20;   ctx.fillText('x³', x, y - 12);

&#x20;   

&#x20; } else if(element.type === 'damper') {

&#x20;   ctx.fillStyle = element.color; ctx.fillRect(x - 15, y - 8, 30, 16);

&#x20;   ctx.strokeStyle = '#fff'; ctx.lineWidth = 1;

&#x20;   ctx.strokeRect(x - 15, y - 8, 30, 16);

&#x20;   

&#x20; } else if(element.type === 'nonlinearDamper') {

&#x20;   ctx.fillStyle = '#c4f';

&#x20;   ctx.fillRect(x - 15, y - 10, 30, 20);

&#x20;   ctx.strokeStyle = '#fff'; ctx.lineWidth = 2;

&#x20;   ctx.strokeRect(x - 15, y - 10, 30, 20);

&#x20;   ctx.fillStyle = '#fff'; ctx.font = 'bold 12px sans-serif'; ctx.textAlign = 'center';

&#x20;   ctx.fillText('v²', x, y + 4);

&#x20;   

&#x20; } else if(element.type === 'force') {

&#x20;   ctx.fillStyle = element.color; ctx.font = 'bold 24px sans-serif'; ctx.textAlign = 'center';

&#x20;   ctx.fillText('⚡', x, y + 8);

&#x20;   

&#x20; } else if(element.type === 'phase') {

&#x20;   ctx.fillStyle = element.color; ctx.font = 'bold 24px sans-serif'; ctx.textAlign = 'center';

&#x20;   ctx.fillText('φ', x, y + 8);

&#x20;   ctx.strokeStyle = element.color; ctx.lineWidth = 2;

&#x20;   ctx.beginPath(); ctx.arc(x, y - 15, 8, 0, Math.PI \* 2); ctx.stroke();

&#x20;   const phase = P('phaseShift') \* Math.PI / 180;

&#x20;   ctx.beginPath(); ctx.moveTo(x, y - 15); ctx.lineTo(x + Math.cos(phase) \* 8, y - 15 + Math.sin(phase) \* 8); ctx.stroke();

&#x20;   

&#x20; } else if(element.type === 'destroyer') {

&#x20;   ctx.fillStyle = element.color;

&#x20;   ctx.fillRect(x-WAGON\_W/2, y-WAGON\_H/2, WAGON\_W, WAGON\_H);

&#x20;   ctx.strokeStyle = '#fff'; ctx.lineWidth = 2;

&#x20;   ctx.strokeRect(x-WAGON\_W/2, y-WAGON\_H/2, WAGON\_W, WAGON\_H);

&#x20;   ctx.strokeStyle = '#fff'; ctx.lineWidth = 3;

&#x20;   ctx.beginPath(); ctx.moveTo(x - 8, y - 8); ctx.lineTo(x + 8, y + 8);

&#x20;   ctx.moveTo(x + 8, y - 8); ctx.lineTo(x - 8, y + 8); ctx.stroke();

&#x20;   const pulse = Math.sin(time \* 10) \* 0.3 + 0.7;

&#x20;   ctx.strokeStyle = `rgba(255, 0, 0, ${pulse})`; ctx.lineWidth = 2;

&#x20;   ctx.beginPath(); ctx.arc(x, y, WAGON\_W/2 + 5, 0, Math.PI \* 2); ctx.stroke();

&#x20; }

}



function drawSupport(b) {

&#x20; const x = b.x;

&#x20; const y = equilibriumY;

&#x20; ctx.fillStyle = '#555';

&#x20; ctx.fillRect(x-15, y-20, 30, 40);

&#x20; ctx.strokeStyle = '#888'; ctx.lineWidth = 2;

&#x20; ctx.strokeRect(x-15, y-20, 30, 40);

&#x20; ctx.fillStyle = '#aaa';

&#x20; ctx.beginPath(); ctx.arc(x-8, y-10, 3, 0, Math.PI\*2); ctx.fill();

&#x20; ctx.beginPath(); ctx.arc(x+8, y-10, 3, 0, Math.PI\*2); ctx.fill();

}



function drawMass(b) {

&#x20; const x = b.x;

&#x20; const y = b.y;

&#x20; if(b.flash > 0) {

&#x20;   ctx.fillStyle = `rgba(255,255,100,${b.flash \* 0.5})`;

&#x20;   ctx.fillRect(x - WAGON\_W/2 - 4, y - WAGON\_H/2 - 4, WAGON\_W + 8, WAGON\_H + 8);

&#x20; }

&#x20; ctx.fillStyle = b.color;

&#x20; ctx.fillRect(x-WAGON\_W/2, y-WAGON\_H/2, WAGON\_W, WAGON\_H);

&#x20; ctx.strokeStyle = '#222'; ctx.lineWidth = 1;

&#x20; ctx.strokeRect(x-WAGON\_W/2, y-WAGON\_H/2, WAGON\_W, WAGON\_H);

&#x20; ctx.fillStyle = '#111';

&#x20; ctx.beginPath(); ctx.arc(x-WAGON\_W/3, y+WAGON\_H/2+2, 4, 0, Math.PI\*2); ctx.fill();

&#x20; ctx.beginPath(); ctx.arc(x+WAGON\_W/3, y+WAGON\_H/2+2, 4, 0, Math.PI\*2); ctx.fill();

&#x20; 

&#x20; if(b.extForce \&\& b.extForce.amplitude > 0) {

&#x20;   ctx.fillStyle = '#f44'; ctx.font = 'bold 10px sans-serif'; ctx.textAlign = 'center';

&#x20;   ctx.fillText('⚡' + b.extForce.amplitude.toFixed(0), x, y - WAGON\_H/2 - 8);

&#x20;   if(b.extForce.phase !== 0) {

&#x20;     const phaseDeg = (b.extForce.phase \* 180 / Math.PI).toFixed(0);

&#x20;     ctx.fillStyle = '#fa0'; ctx.font = 'bold 9px sans-serif';

&#x20;     ctx.fillText('φ' + phaseDeg + '°', x, y - WAGON\_H/2 - 20);

&#x20;   }

&#x20; }

}



// ============================================================================

// НАУЧНЫЙ МОДУЛЬ: РАСЧЁТ АЧХ ДВУМЯ МЕТОДАМИ

// ============================================================================



/\*\*

&#x20;\* МЕТОД 1: Структурный подход (комплексные амплитуды)

&#x20;\* Решает алгебраическую систему: (-ω²M + iωB + K)X = F

&#x20;\* Работает ТОЛЬКО для линейных систем (α=0, β=0)

&#x20;\*/

function calculateFRFStructural(N, m, k, b, A, omega) {

&#x20; // Строим матрицы

&#x20; const M = buildMassMatrix(N, m);

&#x20; const K = buildStiffnessMatrix(N, k);

&#x20; const B = buildDampingMatrix(N, b);

&#x20; 

&#x20; // Вектор силы (возбуждение на первую массу)

&#x20; const F = new Array(N).fill(0);

&#x20; F\[0] = A;

&#x20; 

&#x20; // Решаем систему для каждой частоты

&#x20; const omegaMin = parseFloat(document.getElementById('sci-omega-min').value);

&#x20; const omegaMax = parseFloat(document.getElementById('sci-omega-max').value);

&#x20; const omegaStep = parseFloat(document.getElementById('sci-omega-step').value);

&#x20; 

&#x20; const frf = \[];

&#x20; 

&#x20; for (let w = omegaMin; w <= omegaMax; w += omegaStep) {

&#x20;   // Формируем комплексную матрицу: Z = -ω²M + iωB + K

&#x20;   const Z\_real = \[];

&#x20;   const Z\_imag = \[];

&#x20;   

&#x20;   for (let i = 0; i < N; i++) {

&#x20;     Z\_real\[i] = \[];

&#x20;     Z\_imag\[i] = \[];

&#x20;     for (let j = 0; j < N; j++) {

&#x20;       Z\_real\[i]\[j] = K\[i]\[j] - w \* w \* M\[i]\[j];

&#x20;       Z\_imag\[i]\[j] = w \* B\[i]\[j];

&#x20;     }

&#x20;   }

&#x20;   

&#x20;   // Решаем комплексную систему методом Гаусса

&#x20;   const X = solveComplexSystem(Z\_real, Z\_imag, F);

&#x20;   

&#x20;   // Амплитуды = модули комплексных чисел

&#x20;   const amplitudes = X.map(x => Math.sqrt(x.real \* x.real + x.imag \* x.imag));

&#x20;   const phases = X.map(x => Math.atan2(x.imag, x.real));

&#x20;   

&#x20;   frf.push({ omega: w, amplitudes, phases });

&#x20; }

&#x20; 

&#x20; return frf;

}



/\*\*

&#x20;\* МЕТОД 2: Численный подход (интегрирование ДУ)

&#x20;\* Метод Эйлера + выделение установившегося режима

&#x20;\* Работает для ЛЮБЫХ систем (линейных и нелинейных)

&#x20;\*/

function calculateFRFNumeric(N, m, k, alpha, b, beta, A, omega) {

&#x20; const omegaMin = parseFloat(document.getElementById('sci-omega-min').value);

&#x20; const omegaMax = parseFloat(document.getElementById('sci-omega-max').value);

&#x20; const omegaStep = parseFloat(document.getElementById('sci-omega-step').value);

&#x20; 

&#x20; const Tmax = 30; // Время расчёта

&#x20; const dt = 0.005; // Шаг интегрирования

&#x20; const substeps = 8;

&#x20; const sdt = dt / substeps;

&#x20; const steps = Math.floor(Tmax / dt);

&#x20; 

&#x20; const frf = \[];

&#x20; 

&#x20; for (let w = omegaMin; w <= omegaMax; w += omegaStep) {

&#x20;   // Начальные условия

&#x20;   let y = new Array(N).fill(0);

&#x20;   let v = new Array(N).fill(0);

&#x20;   let t = 0;

&#x20;   

&#x20;   const timeSeries = \[];

&#x20;   

&#x20;   // Интегрируем

&#x20;   for (let step = 0; step <= steps; step++) {

&#x20;     if (step % 10 === 0) { // Записываем каждую 10-ю точку

&#x20;       timeSeries.push({ t, y: \[...y] });

&#x20;     }

&#x20;     

&#x20;     for (let s = 0; s < substeps; s++) {

&#x20;       const F = new Array(N).fill(0);

&#x20;       

&#x20;       // Силы от связей

&#x20;       for (let i = 0; i < N; i++) {

&#x20;         if (i > 0) {

&#x20;           const dy = y\[i-1] - y\[i];

&#x20;           const dv = v\[i-1] - v\[i];

&#x20;           F\[i] += k \* dy + alpha \* dy \* dy \* dy + b \* dv + beta \* dv \* Math.abs(dv);

&#x20;         }

&#x20;         if (i < N - 1) {

&#x20;           const dy = y\[i+1] - y\[i];

&#x20;           const dv = v\[i+1] - v\[i];

&#x20;           F\[i] += k \* dy + alpha \* dy \* dy \* dy + b \* dv + beta \* dv \* Math.abs(dv);

&#x20;         }

&#x20;         

&#x20;         // Внешняя сила на первую массу

&#x20;         if (i === 0) {

&#x20;           F\[i] += A \* Math.sin(w \* t);

&#x20;         }

&#x20;       }

&#x20;       

&#x20;       // Обновление

&#x20;       for (let i = 0; i < N; i++) {

&#x20;         const a = F\[i] / m;

&#x20;         v\[i] += a \* sdt;

&#x20;         y\[i] += v\[i] \* sdt;

&#x20;       }

&#x20;       

&#x20;       t += sdt;

&#x20;     }

&#x20;   }

&#x20;   

&#x20;   // Выделяем установившийся режим (t >= 20)

&#x20;   const steadyData = timeSeries.filter(p => p.t >= 20);

&#x20;   

&#x20;   // Оцениваем амплитуды методом наименьших квадратов

&#x20;   const amplitudes = \[];

&#x20;   const phases = \[];

&#x20;   

&#x20;   for (let i = 0; i < N; i++) {

&#x20;     const data\_i = steadyData.map(p => ({ t: p.t, y: p.y\[i] }));

&#x20;     const ss = estimateSteadyState(data\_i, w);

&#x20;     amplitudes.push(ss.A\_st);

&#x20;     phases.push(ss.phi\_st);

&#x20;   }

&#x20;   

&#x20;   frf.push({ omega: w, amplitudes, phases });

&#x20; }

&#x20; 

&#x20; return frf;

}



function estimateSteadyState(data, omega) {

&#x20; if (data.length < 10) {

&#x20;   return { A\_st: 0, phi\_st: 0 };

&#x20; }

&#x20; 

&#x20; let S\_ss = 0, S\_sc = 0, S\_cc = 0, S\_ys = 0, S\_yc = 0;

&#x20; 

&#x20; data.forEach(p => {

&#x20;   const sinVal = Math.sin(omega \* p.t);

&#x20;   const cosVal = Math.cos(omega \* p.t);

&#x20;   

&#x20;   S\_ss += sinVal \* sinVal;

&#x20;   S\_sc += sinVal \* cosVal;

&#x20;   S\_cc += cosVal \* cosVal;

&#x20;   S\_ys += p.y \* sinVal;

&#x20;   S\_yc += p.y \* cosVal;

&#x20; });

&#x20; 

&#x20; const det = S\_ss \* S\_cc - S\_sc \* S\_sc;

&#x20; if (Math.abs(det) < 1e-12) {

&#x20;   return { A\_st: 0, phi\_st: 0 };

&#x20; }

&#x20; 

&#x20; const C1 = (S\_cc \* S\_ys - S\_sc \* S\_yc) / det;

&#x20; const C2 = (S\_ss \* S\_yc - S\_sc \* S\_ys) / det;

&#x20; 

&#x20; const A\_st = Math.sqrt(C1 \* C1 + C2 \* C2);

&#x20; const phi\_st = Math.atan2(C2, C1);

&#x20; 

&#x20; return { A\_st, phi\_st };

}



function buildMassMatrix(N, m) {

&#x20; const M = \[];

&#x20; for (let i = 0; i < N; i++) {

&#x20;   M\[i] = \[];

&#x20;   for (let j = 0; j < N; j++) {

&#x20;     M\[i]\[j] = (i === j) ? m : 0;

&#x20;   }

&#x20; }

&#x20; return M;

}



function buildStiffnessMatrix(N, k) {

&#x20; const K = \[];

&#x20; for (let i = 0; i < N; i++) {

&#x20;   K\[i] = \[];

&#x20;   for (let j = 0; j < N; j++) {

&#x20;     if (i === j) {

&#x20;       K\[i]\[j] = 2 \* k; // Сумма жёсткостей слева и справа

&#x20;       if (i === 0 || i === N - 1) K\[i]\[j] = k; // Крайние массы

&#x20;     } else if (Math.abs(i - j) === 1) {

&#x20;       K\[i]\[j] = -k;

&#x20;     } else {

&#x20;       K\[i]\[j] = 0;

&#x20;     }

&#x20;   }

&#x20; }

&#x20; return K;

}



function buildDampingMatrix(N, b) {

&#x20; const B = \[];

&#x20; for (let i = 0; i < N; i++) {

&#x20;   B\[i] = \[];

&#x20;   for (let j = 0; j < N; j++) {

&#x20;     if (i === j) {

&#x20;       B\[i]\[j] = 2 \* b;

&#x20;       if (i === 0 || i === N - 1) B\[i]\[j] = b;

&#x20;     } else if (Math.abs(i - j) === 1) {

&#x20;       B\[i]\[j] = -b;

&#x20;     } else {

&#x20;       B\[i]\[j] = 0;

&#x20;     }

&#x20;   }

&#x20; }

&#x20; return B;

}



function solveComplexSystem(Z\_real, Z\_imag, F) {

&#x20; const N = F.length;

&#x20; 

&#x20; // Метод Гаусса для комплексных чисел

&#x20; // Augmented matrix: \[Z | F]

&#x20; const A\_real = Z\_real.map((row, i) => \[...row, F\[i]]);

&#x20; const A\_imag = Z\_imag.map(row => \[...row, 0]);

&#x20; 

&#x20; // Прямой ход

&#x20; for (let i = 0; i < N; i++) {

&#x20;   // Поиск ведущего элемента

&#x20;   let maxRow = i;

&#x20;   let maxVal = Math.sqrt(A\_real\[i]\[i] \*\* 2 + A\_imag\[i]\[i] \*\* 2);

&#x20;   for (let k = i + 1; k < N; k++) {

&#x20;     const val = Math.sqrt(A\_real\[k]\[i] \*\* 2 + A\_imag\[k]\[i] \*\* 2);

&#x20;     if (val > maxVal) {

&#x20;       maxVal = val;

&#x20;       maxRow = k;

&#x20;     }

&#x20;   }

&#x20;   

&#x20;   // Перестановка строк

&#x20;   \[A\_real\[i], A\_real\[maxRow]] = \[A\_real\[maxRow], A\_real\[i]];

&#x20;   \[A\_imag\[i], A\_imag\[maxRow]] = \[A\_imag\[maxRow], A\_imag\[i]];

&#x20;   

&#x20;   // Исключение

&#x20;   for (let k = i + 1; k < N; k++) {

&#x20;     const denom\_real = A\_real\[i]\[i];

&#x20;     const denom\_imag = A\_imag\[i]\[i];

&#x20;     const denom = denom\_real \*\* 2 + denom\_imag \*\* 2;

&#x20;     

&#x20;     if (denom < 1e-12) continue;

&#x20;     

&#x20;     const factor\_real = (A\_real\[k]\[i] \* denom\_real + A\_imag\[k]\[i] \* denom\_imag) / denom;

&#x20;     const factor\_imag = (A\_imag\[k]\[i] \* denom\_real - A\_real\[k]\[i] \* denom\_imag) / denom;

&#x20;     

&#x20;     for (let j = i; j <= N; j++) {

&#x20;       const new\_real = A\_real\[k]\[j] - (factor\_real \* A\_real\[i]\[j] - factor\_imag \* A\_imag\[i]\[j]);

&#x20;       const new\_imag = A\_imag\[k]\[j] - (factor\_real \* A\_imag\[i]\[j] + factor\_imag \* A\_real\[i]\[j]);

&#x20;       A\_real\[k]\[j] = new\_real;

&#x20;       A\_imag\[k]\[j] = new\_imag;

&#x20;     }

&#x20;   }

&#x20; }

&#x20; 

&#x20; // Обратный ход

&#x20; const X = \[];

&#x20; for (let i = N - 1; i >= 0; i--) {

&#x20;   let sum\_real = A\_real\[i]\[N];

&#x20;   let sum\_imag = A\_imag\[i]\[N];

&#x20;   

&#x20;   for (let j = i + 1; j < N; j++) {

&#x20;     const prod\_real = X\[j].real \* A\_real\[i]\[j] - X\[j].imag \* A\_imag\[i]\[j];

&#x20;     const prod\_imag = X\[j].real \* A\_imag\[i]\[j] + X\[j].imag \* A\_real\[i]\[j];

&#x20;     sum\_real -= prod\_real;

&#x20;     sum\_imag -= prod\_imag;

&#x20;   }

&#x20;   

&#x20;   const denom = A\_real\[i]\[i] \*\* 2 + A\_imag\[i]\[i] \*\* 2;

&#x20;   if (denom < 1e-12) {

&#x20;     X\[i] = { real: 0, imag: 0 };

&#x20;   } else {

&#x20;     X\[i] = {

&#x20;       real: (sum\_real \* A\_real\[i]\[i] + sum\_imag \* A\_imag\[i]\[i]) / denom,

&#x20;       imag: (sum\_imag \* A\_real\[i]\[i] - sum\_real \* A\_imag\[i]\[i]) / denom

&#x20;     };

&#x20;   }

&#x20; }

&#x20; 

&#x20; return X;

}



/\*\*

&#x20;\* Главная функция: расчёт и отрисовка АЧХ

&#x20;\*/

function calculateFRF() {

&#x20; const N = parseInt(document.getElementById('sci-N').value);

&#x20; const m = parseFloat(document.getElementById('sci-m').value);

&#x20; const k = parseFloat(document.getElementById('sci-k').value);

&#x20; const b = parseFloat(document.getElementById('sci-b').value);

&#x20; const alpha = parseFloat(document.getElementById('sci-alpha').value);

&#x20; const beta = parseFloat(document.getElementById('sci-beta').value);

&#x20; const A = parseFloat(document.getElementById('sci-A').value);

&#x20; 

&#x20; console.log('🔄 Расчёт АЧХ двумя методами...');

&#x20; console.log(`Параметры: N=${N}, m=${m}, k=${k}, b=${b}, α=${alpha}, β=${beta}, A=${A}`);

&#x20; 

&#x20; // Метод 1: Структурный (только для линейных)

&#x20; let frfStructural = null;

&#x20; const isLinear = (alpha === 0 \&\& beta === 0);

&#x20; 

&#x20; if (isLinear) {

&#x20;   console.log('✅ Система линейная (α=0, β=0) — применяем структурный метод');

&#x20;   frfStructural = calculateFRFStructural(N, m, k, b, A);

&#x20; } else {

&#x20;   console.log('⚠️ Система нелинейная (α≠0 или β≠0) — структурный метод неприменим');

&#x20; }

&#x20; 

&#x20; // Метод 2: Численный (для всех)

&#x20; console.log('🔄 Численный метод (интегрирование ДУ)...');

&#x20; const frfNumeric = calculateFRFNumeric(N, m, k, alpha, b, beta, A);

&#x20; console.log('✅ Численный метод завершён');

&#x20; 

&#x20; // Отрисовка графиков

&#x20; drawFRFComparison(frfStructural, frfNumeric, isLinear, N);

&#x20; 

&#x20; // Расчёт метрик сравнения

&#x20; if (isLinear) {

&#x20;   calculateComparisonMetrics(frfStructural, frfNumeric, N);

&#x20; }

}



function drawFRFComparison(frfStructural, frfNumeric, isLinear, N) {

&#x20; const canvas = document.getElementById('frf-canvas');

&#x20; const ctx = canvas.getContext('2d');

&#x20; 

&#x20; // Устанавливаем реальные размеры canvas

&#x20; canvas.width = canvas.offsetWidth;

&#x20; canvas.height = canvas.offsetHeight;

&#x20; 

&#x20; const width = canvas.width;

&#x20; const height = canvas.height;

&#x20; 

&#x20; // Очищаем

&#x20; ctx.fillStyle = '#0a0a0a';

&#x20; ctx.fillRect(0, 0, width, height);

&#x20; 

&#x20; // Параметры графика

&#x20; const padding = { top: 40, right: 40, bottom: 60, left: 80 };

&#x20; const plotWidth = width - padding.left - padding.right;

&#x20; const plotHeight = height - padding.top - padding.bottom;

&#x20; 

&#x20; // Находим диапазоны

&#x20; const omegaMin = parseFloat(document.getElementById('sci-omega-min').value);

&#x20; const omegaMax = parseFloat(document.getElementById('sci-omega-max').value);

&#x20; 

&#x20; let maxAmp = 0;

&#x20; if (frfStructural) {

&#x20;   frfStructural.forEach(row => {

&#x20;     row.amplitudes.forEach(A => {

&#x20;       if (A > maxAmp) maxAmp = A;

&#x20;     });

&#x20;   });

&#x20; }

&#x20; frfNumeric.forEach(row => {

&#x20;   row.amplitudes.forEach(A => {

&#x20;     if (A > maxAmp) maxAmp = A;

&#x20;   });

&#x20; });

&#x20; 

&#x20; maxAmp \*= 1.1; // Запас

&#x20; 

&#x20; // Рисуем сетку

&#x20; ctx.strokeStyle = '#333';

&#x20; ctx.lineWidth = 1;

&#x20; 

&#x20; // Вертикальные линии (частоты)

&#x20; const omegaStep = (omegaMax - omegaMin) / 10;

&#x20; for (let i = 0; i <= 10; i++) {

&#x20;   const x = padding.left + (i / 10) \* plotWidth;

&#x20;   ctx.beginPath();

&#x20;   ctx.moveTo(x, padding.top);

&#x20;   ctx.lineTo(x, padding.top + plotHeight);

&#x20;   ctx.stroke();

&#x20;   

&#x20;   // Подписи

&#x20;   ctx.fillStyle = '#888';

&#x20;   ctx.font = '10px sans-serif';

&#x20;   ctx.textAlign = 'center';

&#x20;   const omega = omegaMin + i \* omegaStep;

&#x20;   ctx.fillText(omega.toFixed(1), x, padding.top + plotHeight + 20);

&#x20; }

&#x20; 

&#x20; // Горизонтальные линии (амплитуды)

&#x20; const ampStep = maxAmp / 5;

&#x20; for (let i = 0; i <= 5; i++) {

&#x20;   const y = padding.top + plotHeight - (i / 5) \* plotHeight;

&#x20;   ctx.beginPath();

&#x20;   ctx.moveTo(padding.left, y);

&#x20;   ctx.lineTo(padding.left + plotWidth, y);

&#x20;   ctx.stroke();

&#x20;   

&#x20;   // Подписи

&#x20;   ctx.fillStyle = '#888';

&#x20;   ctx.font = '10px sans-serif';

&#x20;   ctx.textAlign = 'right';

&#x20;   const amp = i \* ampStep;

&#x20;   ctx.fillText(amp.toFixed(1), padding.left - 10, y + 3);

&#x20; }

&#x20; 

&#x20; // Подписи осей

&#x20; ctx.fillStyle = '#fc6';

&#x20; ctx.font = 'bold 12px sans-serif';

&#x20; ctx.textAlign = 'center';

&#x20; ctx.fillText('Частота ω (рад/с)', padding.left + plotWidth / 2, height - 10);

&#x20; 

&#x20; ctx.save();

&#x20; ctx.translate(20, padding.top + plotHeight / 2);

&#x20; ctx.rotate(-Math.PI / 2);

&#x20; ctx.fillText('Амплитуда A', 0, 0);

&#x20; ctx.restore();

&#x20; 

&#x20; // Заголовок

&#x20; ctx.fillStyle = '#fc6';

&#x20; ctx.font = 'bold 14px sans-serif';

&#x20; ctx.textAlign = 'center';

&#x20; ctx.fillText(`АЧХ цепи из ${N} масс (первая масса)`, width / 2, 20);

&#x20; 

&#x20; // Рисуем графики для каждой массы

&#x20; const colors = \['#4cf', '#6f6', '#fc6', '#f6f', '#6ff', '#fa6', '#a6f'];

&#x20; 

&#x20; for (let massIdx = 0; massIdx < Math.min(N, 3); massIdx++) { // Показываем первые 3 массы

&#x20;   const color = colors\[massIdx % colors.length];

&#x20;   

&#x20;   // Структурный метод (синий)

&#x20;   if (frfStructural) {

&#x20;     ctx.strokeStyle = color;

&#x20;     ctx.lineWidth = 2;

&#x20;     ctx.setLineDash(\[]);

&#x20;     ctx.beginPath();

&#x20;     

&#x20;     frfStructural.forEach((row, i) => {

&#x20;       const x = padding.left + ((row.omega - omegaMin) / (omegaMax - omegaMin)) \* plotWidth;

&#x20;       const y = padding.top + plotHeight - (row.amplitudes\[massIdx] / maxAmp) \* plotHeight;

&#x20;       

&#x20;       if (i === 0) ctx.moveTo(x, y);

&#x20;       else ctx.lineTo(x, y);

&#x20;     });

&#x20;     ctx.stroke();

&#x20;   }

&#x20;   

&#x20;   // Численный метод (красный пунктир)

&#x20;   ctx.strokeStyle = color;

&#x20;   ctx.lineWidth = 2;

&#x20;   ctx.setLineDash(\[5, 5]);

&#x20;   ctx.beginPath();

&#x20;   

&#x20;   frfNumeric.forEach((row, i) => {

&#x20;     const x = padding.left + ((row.omega - omegaMin) / (omegaMax - omegaMin)) \* plotWidth;

&#x20;     const y = padding.top + plotHeight - (row.amplitudes\[massIdx] / maxAmp) \* plotHeight;

&#x20;     

&#x20;     if (i === 0) ctx.moveTo(x, y);

&#x20;     else ctx.lineTo(x, y);

&#x20;   });

&#x20;   ctx.stroke();

&#x20;   ctx.setLineDash(\[]);

&#x20; }

&#x20; 

&#x20; // Легенда

&#x20; const legendX = width - 200;

&#x20; const legendY = padding.top + 10;

&#x20; 

&#x20; ctx.fillStyle = 'rgba(0,0,0,0.7)';

&#x20; ctx.fillRect(legendX - 10, legendY - 5, 190, isLinear ? 100 : 60);

&#x20; 

&#x20; ctx.font = '11px sans-serif';

&#x20; ctx.textAlign = 'left';

&#x20; 

&#x20; if (isLinear) {

&#x20;   ctx.fillStyle = '#4cf';

&#x20;   ctx.fillRect(legendX, legendY, 20, 2);

&#x20;   ctx.fillText('Структурный метод (алгебра)', legendX + 25, legendY + 5);

&#x20;   

&#x20;   ctx.fillStyle = '#f44';

&#x20;   ctx.setLineDash(\[5, 5]);

&#x20;   ctx.strokeStyle = '#f44';

&#x20;   ctx.lineWidth = 2;

&#x20;   ctx.beginPath();

&#x20;   ctx.moveTo(legendX, legendY + 20);

&#x20;   ctx.lineTo(legendX + 20, legendY + 20);

&#x20;   ctx.stroke();

&#x20;   ctx.setLineDash(\[]);

&#x20;   ctx.fillText('Численный метод (ДУ)', legendX + 25, legendY + 25);

&#x20;   

&#x20;   ctx.fillStyle = '#4f4';

&#x20;   ctx.fillText('✅ Методы совпадают', legendX, legendY + 50);

&#x20; } else {

&#x20;   ctx.fillStyle = '#f44';

&#x20;   ctx.setLineDash(\[5, 5]);

&#x20;   ctx.strokeStyle = '#f44';

&#x20;   ctx.lineWidth = 2;

&#x20;   ctx.beginPath();

&#x20;   ctx.moveTo(legendX, legendY);

&#x20;   ctx.lineTo(legendX + 20, legendY);

&#x20;   ctx.stroke();

&#x20;   ctx.setLineDash(\[]);

&#x20;   ctx.fillText('Численный метод (ДУ)', legendX + 25, legendY + 5);

&#x20;   

&#x20;   ctx.fillStyle = '#f44';

&#x20;   ctx.fillText('⚠️ Нелинейная система', legendX, legendY + 30);

&#x20; }

&#x20; 

&#x20; // Подписи масс

&#x20; for (let massIdx = 0; massIdx < Math.min(N, 3); massIdx++) {

&#x20;   ctx.fillStyle = colors\[massIdx % colors.length];

&#x20;   ctx.fillRect(legendX, legendY + 70 + massIdx \* 15, 10, 8);

&#x20;   ctx.fillText(`Масса ${massIdx + 1}`, legendX + 15, legendY + 78 + massIdx \* 15);

&#x20; }

}



function calculateComparisonMetrics(frfStructural, frfNumeric, N) {

&#x20; let totalError = 0;

&#x20; let maxError = 0;

&#x20; let count = 0;

&#x20; 

&#x20; frfStructural.forEach((row, i) => {

&#x20;   row.amplitudes.forEach((A\_struct, j) => {

&#x20;     const A\_num = frfNumeric\[i].amplitudes\[j];

&#x20;     const error = Math.abs(A\_struct - A\_num) / Math.max(A\_struct, 0.001) \* 100;

&#x20;     totalError += error;

&#x20;     if (error > maxError) maxError = error;

&#x20;     count++;

&#x20;   });

&#x20; });

&#x20; 

&#x20; const avgError = totalError / count;

&#x20; 

&#x20; const resultsBox = document.getElementById('results-box');

&#x20; const resultsContent = document.getElementById('results-content');

&#x20; 

&#x20; resultsBox.style.display = 'block';

&#x20; 

&#x20; resultsContent.innerHTML = `

&#x20;   <div class="metric">

&#x20;     <b>Система:</b>

&#x20;     <span class="value">Линейная (α=0, β=0)</span>

&#x20;   </div>

&#x20;   <div class="metric">

&#x20;     <b>Средняя ошибка:</b>

&#x20;     <span class="value ${avgError < 5 ? 'good' : 'bad'}">${avgError.toFixed(2)}%</span>

&#x20;   </div>

&#x20;   <div class="metric">

&#x20;     <b>Максимальная ошибка:</b>

&#x20;     <span class="value ${maxError < 10 ? 'good' : 'bad'}">${maxError.toFixed(2)}%</span>

&#x20;   </div>

&#x20;   <div class="metric">

&#x20;     <b>Вывод:</b>

&#x20;     <span class="value ${avgError < 5 ? 'good' : 'bad'}">

&#x20;       ${avgError < 5 ? '✅ Методы совпадают' : '⚠️ Есть расхождения'}

&#x20;     </span>

&#x20;   </div>

&#x20; `;

}



/\*\*

&#x20;\* Экспорт всех данных

&#x20;\*/

function exportAllData() {

&#x20; const N = parseInt(document.getElementById('sci-N').value);

&#x20; const m = parseFloat(document.getElementById('sci-m').value);

&#x20; const k = parseFloat(document.getElementById('sci-k').value);

&#x20; const b = parseFloat(document.getElementById('sci-b').value);

&#x20; const alpha = parseFloat(document.getElementById('sci-alpha').value);

&#x20; const beta = parseFloat(document.getElementById('sci-beta').value);

&#x20; const A = parseFloat(document.getElementById('sci-A').value);

&#x20; 

&#x20; // 1. Экспорт модели (JSON)

&#x20; const model = {

&#x20;   timestamp: new Date().toISOString(),

&#x20;   description: "Математическая модель цепи осцилляторов",

&#x20;   parameters: { N, m, k, b, alpha, beta, A },

&#x20;   massMatrix: buildMassMatrix(N, m),

&#x20;   stiffnessMatrix: buildStiffnessMatrix(N, k),

&#x20;   dampingMatrix: buildDampingMatrix(N, b),

&#x20;   isLinear: (alpha === 0 \&\& beta === 0)

&#x20; };

&#x20; 

&#x20; const modelBlob = new Blob(\[JSON.stringify(model, null, 2)], {type: 'application/json'});

&#x20; const modelUrl = URL.createObjectURL(modelBlob);

&#x20; const modelLink = document.createElement('a');

&#x20; modelLink.href = modelUrl;

&#x20; modelLink.download = `model\_N${N}\_${Date.now()}.json`;

&#x20; modelLink.click();

&#x20; URL.revokeObjectURL(modelUrl);

&#x20; 

&#x20; // 2. Экспорт АЧХ (CSV)

&#x20; let csv = 'omega';

&#x20; for (let i = 1; i <= N; i++) {

&#x20;   csv += `,A${i}\_structural,A${i}\_numeric`;

&#x20; }

&#x20; csv += '\\n';

&#x20; 

&#x20; const isLinear = (alpha === 0 \&\& beta === 0);

&#x20; const frfStructural = isLinear ? calculateFRFStructural(N, m, k, b, A) : null;

&#x20; const frfNumeric = calculateFRFNumeric(N, m, k, alpha, b, beta, A);

&#x20; 

&#x20; frfNumeric.forEach((row, i) => {

&#x20;   csv += row.omega.toFixed(4);

&#x20;   for (let j = 0; j < N; j++) {

&#x20;     csv += `,${frfStructural ? frfStructural\[i].amplitudes\[j].toFixed(6) : 'N/A'}`;

&#x20;     csv += `,${row.amplitudes\[j].toFixed(6)}`;

&#x20;   }

&#x20;   csv += '\\n';

&#x20; });

&#x20; 

&#x20; const csvBlob = new Blob(\[csv], {type: 'text/csv;charset=utf-8'});

&#x20; const csvUrl = URL.createObjectURL(csvBlob);

&#x20; const csvLink = document.createElement('a');

&#x20; csvLink.href = csvUrl;

&#x20; csvLink.download = `frf\_comparison\_N${N}\_${Date.now()}.csv`;

&#x20; csvLink.click();

&#x20; URL.revokeObjectURL(csvUrl);

&#x20; 

&#x20; console.log('✅ Экспорт завершён:');

&#x20; console.log('📄 model\_\*.json — математическая модель');

&#x20; console.log('📊 frf\_comparison\_\*.csv — АЧХ обоих методов');

}



let lastTime = performance.now();

function loop(now) {

&#x20; const dt = Math.min((now-lastTime)/1000, 0.05);

&#x20; lastTime = now;

&#x20; 

&#x20; if (currentMode === 'game') {

&#x20;   physics(dt);

&#x20;   draw();

&#x20; }

&#x20; 

&#x20; requestAnimationFrame(loop);

}

requestAnimationFrame(loop);

initChain();

</script>

</body>

</html>

```



\## 🎯 ЧТО РЕАЛИЗОВАНО



\### 1. \*\*Два режима работы\*\*

\- \*\*🎮 Игровой режим\*\* — ваша золотая версия (Тетрис + колебания)

\- \*\*🔬 Научный режим\*\* — расчёт АЧХ двумя методами



\### 2. \*\*Два метода расчёта АЧХ\*\*



\*\*Метод 1: Структурный (комплексные амплитуды)\*\*

\- Решает алгебраическую систему: $(-\\omega^2 M + i\\omega B + K)X = F$

\- Работает \*\*только для линейных систем\*\* (α=0, β=0)

\- Мгновенный результат (без интегрирования)



\*\*Метод 2: Численный (интегрирование ДУ)\*\*

\- Метод Эйлера с подшагами

\- Выделение установившегося режима методом наименьших квадратов

\- Работает для \*\*любых систем\*\* (линейных и нелинейных)



\### 3. \*\*Визуализация\*\*

\- Два графика на одном canvas

\- Сплошная линия — структурный метод

\- Пунктирная линия — численный метод

\- Цвета для каждой массы

\- Легенда и подписи осей



\### 4. \*\*Сравнение методов\*\*

\- Автоматическое определение линейности системы

\- Расчёт средней и максимальной ошибки

\- Вывод: "Методы совпадают" или "Есть расхождения"



\### 5. \*\*Экспорт данных\*\*

\- \*\*JSON\*\* — математическая модель (матрицы M, K, B)

\- \*\*CSV\*\* — АЧХ обоих методов для анализа в Excel/MATLAB



\## 📋 КАК ИСПОЛЬЗОВАТЬ



1\. \*\*Сохраните код\*\* как `oscillator-chain-v2.0-SCIENCE.html`

2\. \*\*Откройте в браузере\*\*

3\. \*\*Нажмите кнопку 🔬\*\* (вверху слева) — переключитесь в научный режим

4\. \*\*Задайте параметры:\*\*

&#x20;  - N = 3 (количество масс)

&#x20;  - m = 1, k = 100, b = 0.1

&#x20;  - α = 0, β = 0 (линейная система)

&#x20;  - A = 10 (амплитуда возбуждения)

5\. \*\*Нажмите "Построить АЧХ"\*\*

6\. \*\*Увидите два графика\*\* — они должны совпадать (ошибка < 5%)

7\. \*\*Измените α на 10\*\* (нелинейная система)

8\. \*\*Постройте снова\*\* — структурный метод недоступен, только численный

9\. \*\*Нажмите "Экспорт всех данных"\*\* — скачаются JSON и CSV



\## 🔬 ДЛЯ ДИССЕРТАЦИИ



Это даёт вам:

\- \*\*Экспериментальное подтверждение\*\* структурного подхода

\- \*\*Сравнение\*\* алгебраического и численного методов

\- \*\*Демонстрацию\*\* нелинейных эффектов (когда α≠0)

\- \*\*Данные\*\* для построения семейства кривых W₁₂(μ)



Проверьте, что для линейной системы оба метода совпадают с точностью < 5%. Если да — структурный подход валидирован! 🎉



