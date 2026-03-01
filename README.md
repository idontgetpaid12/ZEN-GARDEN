# 🌸 Ashton's Zen Garden
A relaxing idle game where you grow plants, survive storms, and party during disco events.

## 🛠 Features
* **Idle Growth:** Plants earn petals over time.
* **Weather Events:** Survive the Storm to get bonus rewards!
* **Fullscreen Support:** Play in immersive mode with the click of a button.

## 📜 License & Credits
* **Developer:** Ashton
* **License:** MIT License
* Created for the Zen Coding Community.
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Zen Garden: Ashton Edition</title>
    <style>
        :root { --p-pink: #ff74b1; --p-purple: #6c5ce7; --gold: #ffd700; --dirt: #4a2c12; }
        body { background: #f0f2f5; margin: 0; display: flex; justify-content: center; overflow-x: hidden; font-family: 'Arial Black', sans-serif; }
        
        /* Modal Styles */
        #license-modal {
            display: none; position: fixed; z-index: 100; left: 0; top: 0; width: 100%; height: 100%;
            background: rgba(0,0,0,0.8); align-items: center; justify-content: center;
        }
        .modal-content {
            background: white; padding: 30px; border-radius: 20px; width: 80%; max-width: 600px;
            max-height: 70vh; overflow-y: auto; font-family: monospace; font-size: 12px; line-height: 1.5;
        }

        .zen-site-wrapper {
            width: 100%; max-width: 1000px; margin: 10px; background: #a8dadc;
            border-radius: 25px; border: 6px solid #3d240f;
            display: flex; flex-direction: column; overflow: hidden; user-select: none;
        }
        #zen-header { 
            background: white; padding: 15px; display: flex; flex-wrap: wrap; 
            justify-content: center; gap: 15px; align-items: center; border-bottom: 3px solid #ddd;
        }
        #p-count { color: var(--p-pink); font-size: 24px; background: #fff0f5; padding: 5px 20px; border-radius: 50px; border: 3px solid #ffdeeb; }
        .timer-box { background: #f1f2f6; padding: 8px 15px; border-radius: 12px; font-size: 11px; border: 1px solid #ccc; font-weight: bold; min-width: 110px; text-align: center; }
        #game-log { background: #2d3436; color: #00ff88; padding: 10px; font-family: monospace; font-size: 12px; text-align: center; min-height: 15px; text-transform: uppercase; }
        
        .zen-main { display: flex; padding: 20px; gap: 20px; justify-content: center; }
        #zen-grid { 
            display: grid; grid-template-columns: repeat(4, 1fr); grid-gap: 12px; 
            background: var(--dirt); padding: 20px; border-radius: 25px; border: 10px solid #3d240f;
            width: 100%; max-width: 480px; box-shadow: inset 0 0 30px rgba(0,0,0,0.5);
        }
        .slot { 
            aspect-ratio: 1/1; background: #795548; border-radius: 15px; 
            display: flex; align-items: center; justify-content: center; 
            font-size: 40px; cursor: pointer; position: relative; border: 4px solid transparent; 
        }
        .zen-side { background: white; padding: 15px; border-radius: 20px; width: 190px; display: flex; flex-direction: column; gap: 8px; }
        .zen-btn { padding: 10px; border-radius: 12px; border: 3px solid #eee; cursor: pointer; font-weight: bold; font-size: 11px; background: white; transition: 0.1s; }
        .zen-btn:active { transform: scale(0.95); }
        .zen-btn.active { border-color: #2ecc71; background: #e8f5e9; }
        .p-bar-wrap { position: absolute; bottom: 10px; width: 70%; height: 7px; background: rgba(0,0,0,0.5); border-radius: 4px; overflow: hidden; pointer-events: none; z-index: 1; }
        .p-bar-fill { height: 100%; background: #00ff88; width: 0%; transition: width 0.1s linear; }
        
        .credit-box {
            margin-top: auto; padding: 10px; background: #f8f9fa; border: 2px dashed #ddd; 
            border-radius: 15px; text-align: center; font-family: sans-serif;
        }
        .owner-name { font-size: 14px; color: #2d3436; display: block; font-weight: bold; }

        .zen-site-wrapper.storm { background: #2d3436 !important; }
        .zen-site-wrapper.disco { animation: disco-bg 0.5s infinite alternate; }
        @keyframes disco-bg { from { background: #ff9ff3; } to { background: #7ed6df; } }
        .survivor { border-color: var(--gold) !important; box-shadow: 0 0 15px var(--gold); }
        .lets-dance span { display: inline-block; animation: dance 0.5s infinite alternate; }
        @keyframes dance { from { transform: translateY(0); } to { transform: translateY(-10px); } }
        @media (max-width: 800px) { .zen-main { flex-direction: column; align-items: center; } }
    </style>
</head>
<body onload="init()">

<div id="license-modal" onclick="closeLicense()">
    <div class="modal-content" onclick="event.stopPropagation()">
        <h3>MIT License</h3>
        <p>Copyright (c) 2026 Ashton</p>
        <p>Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:</p>
        <p>The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.</p>
        <p>THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.</p>
        <button class="zen-btn" onclick="closeLicense()" style="width:100%; background:var(--p-purple); color:white; border:none;">CLOSE</button>
    </div>
</div>

<div class="zen-site-wrapper" id="game-container">
    <div id="zen-header">
        <div id="p-count">🌸 10</div>
        <div class="timer-box" id="ev-label">EVENT: --:--</div>
        <div class="timer-box" id="q-label" style="color:var(--p-purple)">REFRESH: --:--</div>
        <button onclick="save(true)" class="zen-btn" style="background:#2ecc71; color:white; border:none; padding:10px 15px;">SAVE</button>
        <input type="text" id="code-in" placeholder="CODE" style="width:70px; padding:8px; border-radius:10px; border:1px solid #ccc; font-size:10px;">
        <button onclick="applyCode()" class="zen-btn" style="background:var(--p-purple); color:white; border:none; padding:10px 15px;">OK</button>
    </div>

    <div id="game-log">ZEN GARDEN | REWARDS: 2X</div>

    <div class="zen-main">
        <div class="zen-side">
            <div class="zen-btn active" id="t0" onclick="setTool(0)">🌱 Grass (10)</div>
            <div class="zen-btn" id="t1" onclick="setTool(1)">🌼 Daisy (25)</div>
            <div class="zen-btn" id="t2" onclick="setTool(2)">🌷 Tulip (120)</div>
            <div class="zen-btn" id="t3" onclick="setTool(3)">🌳 Oak (2k)</div>
            <div class="zen-btn" id="t4" onclick="setTool(4)">🍌 Banana (8k)</div>
            <div class="zen-btn" id="t5" onclick="setTool(5)">🍋 Lemon (25k)</div>
            <hr>
            <button class="zen-btn" onclick="plantAll()" style="background:#6c5ce7; color:white; border:none;">🚜 PLANT ALL</button>
            <button class="zen-btn" onclick="harvestAll()" style="background:#00ff88; color:#333; border:none;">✨ HARVEST ALL</button>
            <button class="zen-btn" onclick="setTool(-1)" style="background:#ff7675; color:white; border:none;">🗑️ SHOVEL</button>
        </div>

        <div id="zen-grid"></div>

        <div class="zen-side">
            <h4 style="margin:0; font-size:12px;">QUESTS</h4>
            <div id="quest-list" style="font-size:10px; color:#444; min-height: 120px;"></div>
            <button class="zen-btn" onclick="buyPack()" style="background:orange; color:white; border:none; margin-top:10px;">🎁 PACK (1k)</button>
            
            <div class="credit-box">
                <span style="font-size: 9px; color: #7f8c8d;">OWNER</span>
                <span class="owner-name">ASHTON</span>
                <button onclick="openLicense()" style="border:none; background:none; color:var(--p-purple); font-size:9px; text-decoration:underline; cursor:pointer; margin-top:5px;">📜 VIEW LICENSE</button>
            </div>
        </div>
    </div>
</div>

<script>
    let petals = 10, tool = 0, harvested = 0;
    let plots = Array(16).fill(null).map(() => ({ stage: 0, type: -1, time: 0, max: 0, sur: false, dan: false }));
    let eTime = 2700, qTime = 600, quests = [], mode = "";
    
    const plants = [
        { i: "🌱", c: 10, r: 20, t: 5 }, { i: "🌼", c: 25, r: 50, t: 12 },
        { i: "🌷", c: 120, r: 240, t: 30 }, { i: "🌳", c: 2000, r: 4000, t: 120 },
        { i: "🍌", c: 8000, r: 16000, t: 300 }, { i: "🍋", c: 25000, r: 50000, t: 600 }
    ];

    function openLicense() { document.getElementById('license-modal').style.display = 'flex'; }
    function closeLicense() { document.getElementById('license-modal').style.display = 'none'; }

    function init() {
        const g = document.getElementById('zen-grid');
        g.innerHTML = "";
        for(let i=0; i<16; i++) {
            let d = document.createElement('div');
            d.className='slot'; d.id='s'+i; d.onclick=()=>tap(i);
            g.appendChild(d);
        }
        load();
        if(quests.length === 0) genQuests();
        setInterval(tick, 100);
    }

    function log(msg) { document.getElementById('game-log').innerText = msg; }

    function tick() {
        eTime -= 0.1; qTime -= 0.1;
        if(eTime <= 0) cycle();
        if(qTime <= 0) genQuests();
        plots.forEach((p, i) => {
            if(p.stage > 0 && p.stage < 3) {
                p.time -= 0.1;
                if(p.time <= 0) { p.stage++; if(p.stage < 3) { p.time = plants[p.type].t; p.max = p.time; } }
            }
            render(i);
        });
        checkQuests();
        updateUI();
    }

    function updateUI() {
        document.getElementById('p-count').innerText = "🌸 " + Math.floor(petals).toLocaleString();
        document.getElementById('ev-label').innerText = (mode===""?"WAVE":mode.toUpperCase()) + ": " + fmt(eTime);
        document.getElementById('q-label').innerText = "REFRESH: " + fmt(qTime);
    }

    function fmt(s) { 
        let m = Math.max(0, Math.floor(s/60)); 
        let sec = Math.max(0, Math.floor(s%60)); 
        return m + ":" + sec.toString().padStart(2,'0'); 
    }

    function harvestAll() {
        let gain = 0, count = 0;
        plots.forEach((p, i) => {
            if(p.stage === 3) {
                gain += (plants[p.type].r * ((p.sur ? 3 : 1) + (p.dan ? 5 : 0)));
                harvested++;
                plots[i] = { stage: 0, type: -1, time: 0, max: 0, sur: false, dan: false };
                count++;
            }
        });
        if(count > 0) { petals += gain; log("HARVESTED " + count + " PLANTS!"); }
    }

    function plantAll() {
        if(tool === -1) return;
        plots.forEach((p, i) => {
            if(p.stage === 0 && petals >= plants[tool].c) {
                petals -= plants[tool].c;
                plots[i] = { stage: 1, type: tool, time: plants[tool].t, max: plants[tool].t, dan: (mode === "disco"), sur: (mode === "storm") };
            }
        });
    }

    function tap(i) {
        let p = plots[i];
        if(tool === -1) { plots[i] = { stage: 0, type: -1, time: 0, max: 0, sur: false, dan: false }; return; }
        if(p.stage === 3) {
            petals += (plants[p.type].r * ((p.sur ? 3 : 1) + (p.dan ? 5 : 0)));
            harvested++;
            plots[i] = { stage: 0, type: -1, time: 0, max: 0, sur: false, dan: false };
        } else if(p.stage === 0 && petals >= plants[tool].c) {
            petals -= plants[tool].c;
            plots[i] = { stage: 1, type: tool, time: plants[tool].t, max: plants[tool].t, dan: (mode === "disco"), sur: (mode === "storm") };
        }
    }

    function render(i) {
        let p = plots[i], el = document.getElementById('s'+i);
        el.className = 'slot' + (p.sur ? ' survivor' : '') + (p.dan ? ' lets-dance' : '');
        if(p.stage === 0) { el.innerHTML = ""; return; }
        let icon = p.stage === 3 ? plants[p.type].i : (p.stage === 2 ? "🌿" : "🍃");
        let bar = (p.stage < 3) ? `<div class="p-bar-wrap"><div class="p-bar-fill" style="width:${((p.max-p.time)/p.max)*100}%"></div></div>` : "";
        el.innerHTML = `<span>${icon}</span>${bar}`;
    }

    function genQuests() {
        qTime = 600;
        const pool = [
            { t: "Grow 3 Oaks 🌳", c: () => plots.filter(x => x.type === 3).length >= 3, r: 2500 },
            { t: "Harvest 10 Times", c: () => harvested >= 10, r: 1000 },
            { t: "Find a Dancer 🕺", c: () => plots.some(x => x.dan), r: 5000 },
            { t: "Grow 2 Bananas 🍌", c: () => plots.filter(x => x.type === 4).length >= 2, r: 8000 },
            { t: "Lemon Squeezer 🍋", c: () => plots.some(x => x.type === 5 && x.stage === 3), r: 20000 }
        ];
        quests = pool.sort(() => 0.5 - Math.random()).slice(0, 3).map(q => ({...q, done: false}));
        renderQuests();
    }

    function renderQuests() {
        let h = "";
        quests.forEach(q => h += `<div style="margin-bottom:8px; border-bottom:1px solid #eee; ${q.done?'color:#2ecc71;text-decoration:line-through':''}">• ${q.t} (+🌸${q.r})</div>`);
        document.getElementById('quest-list').innerHTML = h;
    }

    function checkQuests() { quests.forEach(q => { if(!q.done && q.c()) { petals += q.r; q.done = true; renderQuests(); log("QUEST DONE!"); } }); }
    function setTool(id) { tool = id; document.querySelectorAll('.zen-btn').forEach((b, i) => { if(i<6) b.classList.toggle('active', i===id); }); }
    function buyPack() { if(petals >= 1000) { petals -= 1000; let win = Math.floor(Math.random() * 3000) + 1200; petals += win; log("PACK: +🌸" + win); } }

    function cycle() {
        const wrap = document.getElementById('game-container');
        if(mode === "") { mode = "storm"; eTime = 60; wrap.className = "zen-site-wrapper storm"; plots.forEach(p => { if(p.stage > 0) p.sur = true; }); }
        else if(mode === "storm") { mode = "wait"; eTime = 1800; wrap.className = "zen-site-wrapper"; }
        else if(mode === "wait") { mode = "disco"; eTime = 300; wrap.className = "zen-site-wrapper disco"; }
        else { mode = ""; eTime = 2700; wrap.className = "zen-site-wrapper"; }
    }

    function applyCode() {
        let c = document.getElementById('code-in').value.toUpperCase().trim();
        if(c === "MANILOVETHISGAME") { mode = "disco"; eTime = 300; document.getElementById('game-container').className = "zen-site-wrapper disco"; log("DISCO MODE!"); }
        document.getElementById('code-in').value = "";
    }

    function save(manual) { localStorage.setItem('ZEN_ASHTON_V1', JSON.stringify({ p: petals, plots, ts: Date.now(), h: harvested, m: mode, e: eTime, qT: qTime, qs: quests })); if(manual) log("SAVED!"); }
    function load() {
        try {
            let s = localStorage.getItem('ZEN_ASHTON_V1');
            if(!s) return;
            let d = JSON.parse(s);
            petals = d.p; plots = d.plots; harvested = d.h; mode = d.m; eTime = d.e; qTime = d.qT; quests = d.qs; renderQuests();
        } catch(e) {}
    }
</script>
</body>
</html>
