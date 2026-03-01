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
        :root { --p-pink: #ff74b1; --p-purple: #6c5ce7; --gold: #ffd700; --dirt: #4a2c12; --ui-bg: rgba(255,255,255,0.95); }
        * { box-sizing: border-box; }
        
        body { 
            background: #a8dadc; 
            margin: 0; 
            padding: 10px; 
            display: flex; 
            flex-direction: column;
            align-items: center;
            font-family: 'Segoe UI', 'Arial Black', sans-serif; 
            min-height: 100vh;
            user-select: none;
            transition: background 0.5s;
        }
        
        /* MODALS */
        #modal-overlay { position: fixed; z-index: 4000; left: 0; top: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.8); display: none; align-items: center; justify-content: center; backdrop-filter: blur(4px); }
        .modal-content { background: white; padding: 25px; border-radius: 20px; width: 90%; max-width: 500px; max-height: 80vh; overflow-y: auto; text-align: center; }

        /* MAIN CONTAINER */
        .zen-site-wrapper { 
            width: 100%; 
            max-width: 1100px; 
            max-height: 98vh; 
            background: #f0f2f5; 
            border-radius: 25px; 
            border: 6px solid #3d240f; 
            display: flex; 
            flex-direction: column; 
            overflow: hidden; 
            box-shadow: 0 10px 30px rgba(0,0,0,0.2);
        }

        /* HEADER */
        #zen-header { background: white; padding: 10px; display: flex; flex-wrap: wrap; justify-content: center; gap: 10px; align-items: center; border-bottom: 3px solid #ddd; }
        #p-count-display { color: var(--p-pink); font-size: clamp(18px, 4vw, 24px); font-weight: 800; background: #fff0f5; padding: 5px 20px; border-radius: 50px; border: 3px solid #ffdeeb; }
        .timer-box { background: #f1f2f6; padding: 6px 12px; border-radius: 12px; font-size: 11px; border: 1px solid #ccc; font-weight: bold; text-align: center; min-width: 100px; }
        
        /* LOG */
        #game-log { background: #2d3436; color: #00ff88; padding: 8px; font-family: monospace; font-size: 11px; text-align: center; text-transform: uppercase; height: 35px; display: flex; align-items: center; justify-content: center; flex-shrink: 0; }

        /* GAME AREA */
        .zen-main { display: flex; padding: 15px; gap: 15px; flex-wrap: wrap; justify-content: center; overflow-y: auto; flex-grow: 1; }

        /* SIDEBARS */
        .zen-side { background: var(--ui-bg); padding: 12px; border-radius: 20px; width: 100%; max-width: 190px; display: flex; flex-direction: column; gap: 6px; box-shadow: 0 4px 10px rgba(0,0,0,0.05); }
        
        .zen-btn { padding: 8px; border-radius: 10px; border: 2px solid #eee; cursor: pointer; font-weight: bold; font-size: 11px; background: white; transition: 0.1s; width: 100%; }
        .zen-btn:active { transform: scale(0.95); }
        .zen-btn.active { border-color: #2ecc71; background: #e8f5e9; }
        
        /* GRID */
        #zen-grid { 
            display: grid; 
            grid-template-columns: repeat(4, clamp(60px, 9vw, 95px)); 
            grid-template-rows: repeat(4, clamp(60px, 9vw, 95px));
            grid-gap: 10px; 
            background: var(--dirt); 
            padding: 15px; 
            border-radius: 20px; 
            border: 8px solid #3d240f; 
            box-shadow: inset 0 0 30px rgba(0,0,0,0.5); 
            height: fit-content;
        }

        .slot { background: #795548; border-radius: 12px; display: flex; align-items: center; justify-content: center; font-size: clamp(25px, 6vw, 45px); cursor: pointer; position: relative; transition: 0.2s; }
        .slot:hover { background: #8d6e63; }

        /* PROGRESS BAR */
        .p-bar-wrap { position: absolute; bottom: 5px; width: 80%; height: 5px; background: rgba(0,0,0,0.3); border-radius: 4px; overflow: hidden; pointer-events: none; }
        .p-bar-fill { height: 100%; background: #00ff88; width: 0%; }
        
        /* SPECIAL EFFECTS */
        .zen-site-wrapper.storm { background: #2d3436 !important; }
        .zen-site-wrapper.disco { animation: disco-bg 0.5s infinite alternate; }
        @keyframes disco-bg { from { background: #ff9ff3; } to { background: #7ed6df; } }
        .survivor { border-color: var(--gold) !important; box-shadow: 0 0 10px var(--gold); border: 3px solid; }
        .lets-dance span { display: inline-block; animation: dance 0.5s infinite alternate; }
        @keyframes dance { from { transform: translateY(0); } to { transform: translateY(-6px); } }

        @media (max-width: 850px) {
            .zen-main { flex-direction: column; align-items: center; }
            .zen-side { max-width: 100%; order: 2; }
            #zen-grid { order: 1; }
        }
    </style>
</head>
<body onload="init()">

<div id="modal-overlay" onclick="closeModal()">
    <div class="modal-content" id="modal-box" onclick="event.stopPropagation()">
        <h2 id="modal-title" style="margin:0"></h2>
        <div id="modal-body" style="padding: 15px 0; font-size: 14px; text-align: left; line-height: 1.5; white-space: pre-wrap;"></div>
        <button class="zen-btn" onclick="closeModal()" style="background:var(--p-purple); color:white; border:none; padding:10px;">OK</button>
    </div>
</div>

<div class="zen-site-wrapper" id="game-container">
    <div id="zen-header">
        <div id="p-count-display">🌸 <span id="p-count">10</span></div>
        <div class="timer-box" id="ev-label">WAVE: 45:00</div>
        <div class="timer-box" id="q-label" style="color:var(--p-purple)">REFRESH: 10:00</div>
        <button onclick="save(true)" class="zen-btn" style="background:#2ecc71; color:white; border:none; width:70px;">SAVE</button>
        <button onclick="toggleFullscreen()" class="zen-btn" style="background:#0984e3; color:white; border:none; width:50px;">🖥️</button>
    </div>

    <div id="game-log">GARDEN INITIALIZING...</div>

    <div class="zen-main">
        <div class="zen-side">
            <h5 style="text-align:center; margin:0;">SHOP</h5>
            <div class="zen-btn active" id="t0" onclick="setTool(0)">🌱 Grass (10)</div>
            <div class="zen-btn" id="t1" onclick="setTool(1)">🌼 Daisy (25)</div>
            <div class="zen-btn" id="t2" onclick="setTool(2)">🌷 Tulip (120)</div>
            <div class="zen-btn" id="t3" onclick="setTool(3)">🌳 Oak (2k)</div>
            <div class="zen-btn" id="t4" onclick="setTool(4)">🍌 Banana (8k)</div>
            <div class="zen-btn" id="t5" onclick="setTool(5)">🍋 Lemon (25k)</div>
            <hr style="width:100%; opacity:0.2;">
            <button class="zen-btn" onclick="plantAll()" style="background:#6c5ce7; color:white; border:none;">🚜 PLANT ALL</button>
            <button class="zen-btn" onclick="harvestAll()" style="background:#00ff88; color:#333; border:none;">✨ HARVEST ALL</button>
            <button class="zen-btn" id="t-1" onclick="setTool(-1)" style="background:#ff7675; color:white; border:none;">🗑️ SHOVEL</button>
        </div>

        <div id="zen-grid"></div>

        <div class="zen-side">
            <h5 style="text-align:center; margin:0;">QUESTS</h5>
            <div id="quest-list" style="background:#f9f9f9; padding:8px; border-radius:12px; border:1px solid #ddd; min-height: 120px; font-size: 11px;"></div>
            <button class="zen-btn" onclick="buyPack()" style="background:orange; color:white; border:none; margin-top:5px;">🎁 PACK (1k)</button>
            <button class="zen-btn" onclick="openCodeApp()" style="background:#dfe4ea; margin-top:5px;">🔑 CODE</button>
            <div style="margin-top:auto; text-align:center;">
                <span style="font-size: 9px; color: #999;">OWNER</span><br>
                <strong style="font-size: 12px;">ASHTON</strong><br>
                <button onclick="showCredits()" style="border:none; background:none; color:var(--p-purple); font-size:9px; text-decoration:underline; cursor:pointer;">LICENSE & CREDITS</button>
            </div>
        </div>
    </div>
</div>

<script>
    /*
     * MIT License
     * Copyright (c) 2026 Ashton
     * Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:
     * The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.
     */

    let petals = 10, tool = 0, harvestedTotal = 0, eTime = 2700, qTime = 600, mode = "";
    let plots = Array(16).fill(null).map(() => ({ stage: 0, type: -1, time: 0, max: 0, sur: false, dan: false }));
    let quests = [];
    
    const plants = [
        { i: "🌱", c: 10, r: 20, t: 5 }, { i: "🌼", c: 25, r: 50, t: 12 },
        { i: "🌷", c: 120, r: 240, t: 25 }, { i: "🌳", c: 2000, r: 4000, t: 60 },
        { i: "🍌", c: 8000, r: 16000, t: 45 }, { i: "🍋", c: 25000, r: 50000, t: 90 }
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

    function toggleFullscreen() {
        if (!document.fullscreenElement) {
            document.documentElement.requestFullscreen().catch(err => { alert(`Error: ${err.message}`); });
        } else { document.exitFullscreen(); }
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
        document.getElementById('p-count').innerText = Math.floor(petals).toLocaleString();
        let evDisplay = mode === "" ? "WAVE" : mode.toUpperCase();
        document.getElementById('ev-label').innerText = evDisplay + ": " + fmt(eTime);
        document.getElementById('q-label').innerText = "REFRESH: " + fmt(qTime);
    }

    function fmt(s) { 
        let m = Math.floor(s/60); let sec = Math.floor(s%60); 
        return m + ":" + sec.toString().padStart(2,'0'); 
    }

    function tap(i) {
        let p = plots[i];
        if(tool === -1) { resetPlot(i); return; }
        if(p.stage === 3) {
            let mult = (p.sur ? 3 : 1) + (p.dan ? 5 : 0);
            petals += (plants[p.type].r * mult);
            harvestedTotal++;
            resetPlot(i);
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
                resetPlot(i);
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
            { id: 2, t: "Harvest 20 plants", c: () => harvestedTotal >= 20, r: 1000 },
            { id: 3, t: "Find a Dancer 🕺", c: () => plots.some(x => x.dan), r: 5000 },
            { id: 4, t: "Grow 2 Bananas 🍌", c: () => plots.filter(x => x.type === 4).length >= 2, r: 8000 },
            { id: 5, t: "Harvest a Lemon 🍋", c: () => plots.some(x => x.type === 5 && x.stage === 3), r: 20000 }
        ];
        quests = pool.sort(() => 0.5 - Math.random()).slice(0, 3).map(q => ({...q, done: false}));
        renderQuests();
    }

    function renderQuests() {
        let h = "";
        quests.forEach(q => h += `<div style="margin-bottom:8px; border-bottom:1px solid #eee; ${q.done?'color:#2ecc71;text-decoration:line-through':''}">• ${q.t}</div>`);
        document.getElementById('quest-list').innerHTML = h;
    }

    function checkQuests() { 
        quests.forEach(q => { if(!q.done && q.c()) { petals += q.r; q.done = true; renderQuests(); log("QUEST DONE: +🌸" + q.r); } }); 
    }

    function setTool(id) { 
        tool = id; 
        document.querySelectorAll('.zen-btn').forEach(b => b.classList.remove('active'));
        if(id === -1) document.getElementById('t-1').classList.add('active');
        else if (id >= 0) document.getElementById('t'+id).classList.add('active');
    }

    function resetPlot(i) { plots[i] = { stage: 0, type: -1, time: 0, max: 0, sur: false, dan: false }; }
    function log(msg) { document.getElementById('game-log').innerText = msg; }

    function buyPack() { 
        if(petals >= 1000) { petals -= 1000; let win = Math.floor(Math.random() * 3000) + 1200; petals += win; log("GIFT: +🌸" + win); }
        else { log("NEED 1,000 PETALS!"); }
    }

    function cycle() {
        const wrap = document.getElementById('game-container');
        if(mode === "") { mode = "storm"; eTime = 60; wrap.classList.add("storm"); plots.forEach(p => { if(p.stage > 0) p.sur = true; }); log("STORM!"); }
        else if(mode === "storm") { mode = "wait"; eTime = 1200; wrap.classList.remove("storm"); log("STORM PASSED."); }
        else if(mode === "wait") { mode = "disco"; eTime = 300; wrap.classList.add("disco"); log("DISCO!"); }
        else { mode = ""; eTime = 2700; wrap.classList.remove("disco"); log("CALM."); }
    }

    function save(manual) { 
        localStorage.setItem('ZEN_ASHTON_V2', JSON.stringify({ p: petals, plots, h: harvestedTotal, m: mode, e: eTime, q: quests }));
        if(manual) log("GARDEN SAVED!"); 
    }

    function load() {
        let s = localStorage.getItem('ZEN_ASHTON_V2');
        if(!s) return;
        let d = JSON.parse(s);
        petals = d.p || 10; plots = d.plots || plots; harvestedTotal = d.h || 0; mode = d.m || ""; eTime = d.e || 2700; quests = d.q || [];
        renderQuests();
    }

    function showCredits() { 
        const licenseText = `MIT License\nCopyright (c) 2026 Ashton\n\nPermission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:\n\nThe above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.\n\nTHE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.`;
        showAppModal("LICENSE & CREDITS", "<strong>Lead Developer:</strong> Ashton<br><br>" + licenseText); 
    }
    
    function openCodeApp() { showAppModal("ENTER CODE", "<input type='text' id='sc' placeholder='SECRET' style='padding:8px; border-radius:8px;'><br><button onclick='applyCode()' class='zen-btn' style='margin-top:10px; background:var(--p-purple); color:white;'>APPLY</button>"); }
    
    function applyCode() {
        let c = document.getElementById('sc').value.toUpperCase();
        if(c === "MANILOVETHISGAME") { petals += 50000; log("CODE: +50k!"); closeModal(); }
        else if(c === "DISCO") { mode = "disco"; eTime = 300; log("DANCE!"); closeModal(); }
    }
    
    function showAppModal(title, body) { document.getElementById('modal-title').innerHTML = title; document.getElementById('modal-body').innerHTML = body; document.getElementById('modal-overlay').style.display = "flex"; }
    function closeModal() { document.getElementById('modal-overlay').style.display = "none"; }
</script>
</body>
</html>
