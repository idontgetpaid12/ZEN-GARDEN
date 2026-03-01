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
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ZEN-GARDEN: Ashton Edition</title>
    <style>
        :root { --p-pink: #ff74b1; --p-purple: #6c5ce7; --gold: #ffd700; --dirt: #4a2c12; }
        * { box-sizing: border-box; }
        
        /* FIX: Ensure the body doesn't fight the game size */
        body { 
            background: #f0f2f5; 
            margin: 0; 
            padding: 10px; 
            display: flex; 
            justify-content: center; 
            align-items: flex-start;
            font-family: 'Arial Black', sans-serif; 
            min-height: 100vh;
        }
        
        #license-modal { display: none; position: fixed; z-index: 100; left: 0; top: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.8); align-items: center; justify-content: center; }
        .modal-content { background: white; padding: 25px; border-radius: 20px; width: 90%; max-width: 550px; max-height: 80vh; overflow-y: auto; font-family: monospace; font-size: 12px; line-height: 1.6; color: #333; }
        
        /* FIX: Added max-height and flexible display to fit any website container */
        .zen-site-wrapper { 
            width: 100%; 
            max-width: 1000px; 
            max-height: 95vh; 
            background: #a8dadc; 
            border-radius: 25px; 
            border: 6px solid #3d240f; 
            display: flex; 
            flex-direction: column; 
            overflow: hidden; 
            user-select: none; 
            box-shadow: 0 10px 30px rgba(0,0,0,0.1);
        }

        #zen-header { background: white; padding: 10px; display: flex; flex-wrap: wrap; justify-content: center; gap: 10px; align-items: center; border-bottom: 3px solid #ddd; flex-shrink: 0; }
        #p-count { color: var(--p-pink); font-size: clamp(16px, 4vw, 24px); background: #fff0f5; padding: 5px 20px; border-radius: 50px; border: 3px solid #ffdeeb; }
        
        .timer-box { background: #f1f2f6; padding: 5px 10px; border-radius: 12px; font-size: 10px; border: 1px solid #ccc; font-weight: bold; text-align: center; flex: 1; min-width: 90px; }
        
        #game-log { background: #2d3436; color: #00ff88; padding: 8px; font-family: monospace; font-size: 11px; text-align: center; text-transform: uppercase; height: 35px; display: flex; align-items: center; justify-content: center; flex-shrink: 0; }

        /* FIX: Added overflow-y: auto so the game scrolls internally if the website is small */
        .zen-main { 
            display: flex; 
            padding: 15px; 
            gap: 15px; 
            flex-wrap: wrap; 
            justify-content: center; 
            overflow-y: auto; 
            flex-grow: 1;
        }

        /* FIX: min-width: 0 allows the grid to shrink correctly in tight spaces */
        #zen-grid { 
            display: grid; 
            grid-template-columns: repeat(4, 1fr); 
            grid-gap: 8px; 
            background: var(--dirt); 
            padding: 15px; 
            border-radius: 20px; 
            border: 8px solid #3d240f; 
            flex: 2; 
            min-width: 0; 
            width: 100%;
            max-width: 500px; 
            box-shadow: inset 0 0 30px rgba(0,0,0,0.5); 
            height: fit-content;
        }

        .slot { aspect-ratio: 1/1; background: #795548; border-radius: 10px; display: flex; align-items: center; justify-content: center; font-size: clamp(20px, 8vw, 40px); cursor: pointer; position: relative; border: 3px solid transparent; transition: background 0.2s; }
        .slot:hover { background: #8d6e63; }

        .zen-side { background: white; padding: 12px; border-radius: 20px; width: 100%; max-width: 200px; display: flex; flex-direction: column; gap: 6px; flex: 1; min-width: 160px; height: fit-content; }
        
        .zen-btn { padding: 8px; border-radius: 10px; border: 3px solid #eee; cursor: pointer; font-weight: bold; font-size: 10px; background: white; transition: 0.1s; width: 100%; }
        .zen-btn:active { transform: scale(0.95); }
        .zen-btn.active { border-color: #2ecc71; background: #e8f5e9; box-shadow: 0 0 5px rgba(46, 204, 113, 0.3); }
        
        .p-bar-wrap { position: absolute; bottom: 5px; width: 80%; height: 6px; background: rgba(0,0,0,0.3); border-radius: 4px; overflow: hidden; pointer-events: none; }
        .p-bar-fill { height: 100%; background: #00ff88; width: 0%; }
        
        .credit-box { margin-top: auto; padding: 8px; background: #f8f9fa; border: 2px dashed #ddd; border-radius: 12px; text-align: center; }
        .owner-name { font-size: 12px; color: #2d3436; display: block; font-weight: bold; }
        
        .zen-site-wrapper.storm { background: #2d3436 !important; transition: 1s; }
        .zen-site-wrapper.disco { animation: disco-bg 0.5s infinite alternate; }
        @keyframes disco-bg { from { background: #ff9ff3; } to { background: #7ed6df; } }
        .survivor { border-color: var(--gold) !important; box-shadow: 0 0 10px var(--gold); }
        .lets-dance span { display: inline-block; animation: dance 0.5s infinite alternate; }
        @keyframes dance { from { transform: translateY(0); } to { transform: translateY(-8px); } }

        @media (max-width: 600px) { 
            .zen-main { flex-direction: column; align-items: center; } 
            .zen-side { max-width: 100%; order: 2; } 
            #zen-grid { width: 100%; order: 1; } 
        }
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
        <button class="zen-btn" onclick="closeLicense()" style="background:var(--p-purple); color:white; border:none; margin-top:10px;">CLOSE</button>
    </div>
</div>

<div class="zen-site-wrapper" id="game-container">
    <div id="zen-header">
        <div id="p-count">🌸 10</div>
        <div class="timer-box" id="ev-label">EVENT: --:--</div>
        <div class="timer-box" id="q-label" style="color:var(--p-purple)">REFRESH: --:--</div>
        
        <div style="display:flex; gap:5px;">
            <button onclick="save(true)" class="zen-btn" style="background:#2ecc71; color:white; border:none; width:60px;">SAVE</button>
            <input type="text" id="code-in" placeholder="CODE" style="width:60px; padding:5px; border-radius:10px; border:1px solid #ccc; font-size:10px;">
            <button onclick="applyCode()" class="zen-btn" style="background:var(--p-purple); color:white; border:none; width:40px;">OK</button>
        </div>
    </div>

    <div id="game-log">GARDEN INITIALIZING...</div>

    <div class="zen-main">
        <div class="zen-side">
            <div class="zen-btn active" id="t0" onclick="setTool(0)">🌱 Grass (10)</div>
            <div class="zen-btn" id="t1" onclick="setTool(1)">🌼 Daisy (25)</div>
            <div class="zen-btn" id="t2" onclick="setTool(2)">🌷 Tulip (120)</div>
            <div class="zen-btn" id="t3" onclick="setTool(3)">🌳 Oak (2k)</div>
            <div class="zen-btn" id="t4" onclick="setTool(4)">🍌 Banana (8k)</div>
            <div class="zen-btn" id="t5" onclick="setTool(5)">🍋 Lemon (25k)</div>
            <hr style="width:100%">
            <button class="zen-btn" onclick="plantAll()" style="background:#6c5ce7; color:white; border:none;">🚜 PLANT ALL</button>
            <button class="zen-btn" onclick="harvestAll()" style="background:#00ff88; color:#333; border:none;">✨ HARVEST ALL</button>
            <button class="zen-btn" onclick="setTool(-1)" style="background:#ff7675; color:white; border:none;">🗑️ SHOVEL</button>
        </div>

        <div id="zen-grid"></div>

        <div class="zen-side">
            <h4 style="margin:0; font-size:11px;">QUESTS</h4>
            <div id="quest-list" style="font-size:10px; color:#444; min-height: 100px;"></div>
            <button class="zen-btn" onclick="buyPack()" style="background:orange; color:white; border:none; margin-top:5px;">🎁 PACK (1k)</button>
            <div class="credit-box">
                <span style="font-size: 8px; color: #7f8c8d;">OWNER</span>
                <span class="owner-name">ASHTON</span>
                <button onclick="openLicense()" style="border:none; background:none; color:var(--p-purple); font-size:8px; text-decoration:underline; cursor:pointer;">📜 LICENSE</button>
            </div>
        </div>
    </div>
</div>

<script>
    let petals = 10, tool = 0, harvestedTotal = 0, eTime = 2700, qTime = 600, mode = "";
    let plots = Array(16).fill(null).map(() => ({ stage: 0, type: -1, time: 0, max: 0, sur: false, dan: false }));
    let quests = [];
    
    const plants = [
        { i: "🌱", c: 10, r: 20, t: 5 }, { i: "🌼", c: 25, r: 50, t: 12 },
        { i: "🌷", c: 120, r: 240, t: 30 }, { i: "🌳", c: 2000, r: 4000, t: 120 },
        { i: "🍌", c: 8000, r: 16000, t: 300 }, { i: "🍋", c: 25000, r: 50000, t: 600 }
    ];

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
        log("WELCOME BACK, ASHTON!");
    }

    function tick() {
        eTime = Math.max(0, eTime - 0.1);
        qTime = Math.max(0, qTime - 0.1);

        if(eTime <= 0) cycle();
        if(qTime <= 0) genQuests();

        plots.forEach((p, i) => {
            if(p.stage > 0 && p.stage < 3) {
                p.time -= 0.1;
                if(p.time <= 0) { 
                    p.stage++; 
                    if(p.stage < 3) { p.time = plants[p.type].t; p.max = p.time; } 
                }
            }
            render(i);
        });
        checkQuests();
        updateUI();
    }

    function updateUI() {
        document.getElementById('p-count').innerText = "🌸 " + Math.floor(petals).toLocaleString();
        let evDisplay = mode === "" ? "WAVE" : mode.toUpperCase();
        document.getElementById('ev-label').innerText = evDisplay + ": " + fmt(eTime);
        document.getElementById('q-label').innerText = "REFRESH: " + fmt(qTime);
    }

    function fmt(s) { 
        let m = Math.max(0, Math.floor(s/60)); 
        let sec = Math.max(0, Math.floor(s%60)); 
        return m + ":" + sec.toString().padStart(2,'0'); 
    }

    function tap(i) {
        let p = plots[i];
        if(tool === -1) { 
            plots[i] = { stage: 0, type: -1, time: 0, max: 0, sur: false, dan: false }; 
            return; 
        }
        if(p.stage === 3) {
            let mult = (p.sur ? 3 : 1) + (p.dan ? 5 : 0);
            petals += (plants[p.type].r * mult);
            harvestedTotal++;
            plots[i] = { stage: 0, type: -1, time: 0, max: 0, sur: false, dan: false };
        } else if(p.stage === 0 && petals >= plants[tool].c) {
            petals -= plants[tool].c;
            plots[i] = { stage: 1, type: tool, time: plants[tool].t, max: plants[tool].t, dan: (mode === "disco"), sur: (mode === "storm") };
        }
    }

    function plantAll() {
        if(tool === -1) return;
        let count = 0;
        plots.forEach((p, i) => {
            if(p.stage === 0 && petals >= plants[tool].c) {
                petals -= plants[tool].c;
                plots[i] = { stage: 1, type: tool, time: plants[tool].t, max: plants[tool].t, dan: (mode === "disco"), sur: (mode === "storm") };
                count++;
            }
        });
        if(count > 0) log("PLANTED " + count + " SEEDS");
    }

    function harvestAll() {
        let gain = 0;
        plots.forEach((p, i) => {
            if(p.stage === 3) {
                let mult = (p.sur ? 3 : 1) + (p.dan ? 5 : 0);
                gain += (plants[p.type].r * mult);
                harvestedTotal++;
                plots[i] = { stage: 0, type: -1, time: 0, max: 0, sur: false, dan: false };
            }
        });
        if(gain > 0) { petals += gain; log("HARVESTED: +🌸" + gain.toLocaleString()); }
    }

    function render(i) {
        let p = plots[i], el = document.getElementById('s'+i);
        el.className = 'slot' + (p.sur ? ' survivor' : '') + (p.dan ? ' lets-dance' : '');
        if(p.stage === 0) { el.innerHTML = ""; return; }
        let icon = p.stage === 3 ? plants[p.type].i : (p.stage === 2 ? "🌿" : "🍃");
        let bar = (p.stage < 3) ? `<div class="p-bar-wrap"><div class="p-bar-fill" style="width:${Math.max(0, Math.min(100, ((p.max-p.time)/p.max)*100))}%"></div></div>` : "";
        el.innerHTML = `<span>${icon}</span>${bar}`;
    }

    function genQuests() {
        qTime = 600;
        const pool = [
            { id: 1, t: "Grow 3 Oaks 🌳", c: () => plots.filter(x => x.type === 3).length >= 3, r: 2500 },
            { id: 2, t: "Total Harvests: 20", c: () => harvestedTotal >= 20, r: 1000 },
            { id: 3, t: "Find a Dancer 🕺", c: () => plots.some(x => x.dan), r: 5000 },
            { id: 4, t: "Grow 2 Bananas 🍌", c: () => plots.filter(x => x.type === 4).length >= 2, r: 8000 },
            { id: 5, t: "Harvest a Lemon 🍋", c: () => plots.some(x => x.type === 5 && x.stage === 3), r: 20000 }
        ];
        quests = pool.sort(() => 0.5 - Math.random()).slice(0, 3).map(q => ({...q, done: false}));
        renderQuests();
        log("NEW QUESTS AVAILABLE!");
    }

    function renderQuests() {
        let h = "";
        quests.forEach(q => h += `<div style="margin-bottom:8px; border-bottom:1px solid #eee; padding-bottom:2px; ${q.done?'color:#2ecc71;text-decoration:line-through':''}">• ${q.t}</div>`);
        document.getElementById('quest-list').innerHTML = h;
    }

    function checkQuests() { 
        quests.forEach(q => { 
            if(!q.done && q.c()) { 
                petals += q.r; 
                q.done = true; 
                renderQuests(); 
                log("QUEST COMPLETE: +🌸" + q.r); 
            } 
        }); 
    }

    function setTool(id) { 
        tool = id; 
        document.querySelectorAll('.zen-btn').forEach((b, i) => { 
            if(i<6) b.classList.toggle('active', i===id); 
            if(id === -1 && b.innerText.includes("SHOVEL")) b.classList.add('active');
            else if(id !== -1 && b.innerText.includes("SHOVEL")) b.classList.remove('active');
        }); 
    }

    function buyPack() { 
        if(petals >= 1000) { 
            petals -= 1000; 
            let win = Math.floor(Math.random() * 3000) + 1200; 
            petals += win; 
            log("GIFT PACK: +🌸" + win); 
        } else {
            log("NEED 1,000 PETALS!");
        }
    }

    function log(msg) { document.getElementById('game-log').innerText = msg; }

    function cycle() {
        const wrap = document.getElementById('game-container');
        if(mode === "") { mode = "storm"; eTime = 60; wrap.className = "zen-site-wrapper storm"; plots.forEach(p => { if(p.stage > 0) p.sur = true; }); log("STORM APPROACHING!"); }
        else if(mode === "storm") { mode = "wait"; eTime = 1200; wrap.className = "zen-site-wrapper"; log("STORM PASSED."); }
        else if(mode === "wait") { mode = "disco"; eTime = 300; wrap.className = "zen-site-wrapper disco"; log("DISCO TIME!"); }
        else { mode = ""; eTime = 2700; wrap.className = "zen-site-wrapper"; log("GARDEN IS CALM."); }
    }

    function applyCode() {
        let c = document.getElementById('code-in').value.toUpperCase().trim();
        if(c === "MANILOVETHISGAME") { petals += 50000; log("CODE: +🌸50k!"); }
        if(c === "DISCO") { mode = "disco"; eTime = 300; document.getElementById('game-container').className = "zen-site-wrapper disco"; log("DANCE PARTY!"); }
        document.getElementById('code-in').value = "";
    }

    function openLicense() { document.getElementById('license-modal').style.display = 'flex'; }
    function closeLicense() { document.getElementById('license-modal').style.display = 'none'; }

    function save(manual) { 
        const data = { p: petals, plots, h: harvestedTotal, m: mode, e: eTime, qT: qTime, qs: quests };
        localStorage.setItem('ZEN_ASHTON_SAVE', JSON.stringify(data)); 
        if(manual) log("GARDEN SAVED!"); 
    }

    function load() {
        try {
            let s = localStorage.getItem('ZEN_ASHTON_SAVE');
            if(!s) return;
            let d = JSON.parse(s);
            petals = d.p || 10; 
            plots = d.plots || plots; 
            harvestedTotal = d.h || 0; 
            mode = d.m || ""; 
            eTime = d.e || 2700; 
            qTime = d.qT || 600; 
            quests = d.qs || []; 
            renderQuests();
            if(mode === "storm") document.getElementById('game-container').className = "zen-site-wrapper storm";
            if(mode === "disco") document.getElementById('game-container').className = "zen-site-wrapper disco";
        } catch(e) { console.error("Load failed", e); }
    }
</script>
</body>
</html>
