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
    <title>Zen Garden: Quest Master</title>
    <style>
        :root { --p-pink: #ff74b1; --p-purple: #6c5ce7; --gold: #ffd700; --dirt: #4a2c12; }
        .zen-site-wrapper {
            width: 100%; max-width: 1000px; margin: 10px auto; background: #a8dadc;
            font-family: 'Arial Black', sans-serif; border-radius: 25px; border: 6px solid #3d240f;
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
        .zen-btn { padding: 10px; border-radius: 12px; border: 3px solid #eee; cursor: pointer; font-weight: bold; font-size: 11px; background: white; transition: 0.2s; }
        .zen-btn.active { border-color: #2ecc71; background: #e8f5e9; }
        .p-bar-wrap { position: absolute; bottom: 10px; width: 70%; height: 7px; background: rgba(0,0,0,0.5); border-radius: 4px; overflow: hidden; pointer-events: none; }
        .p-bar-fill { height: 100%; background: #00ff88; width: 0%; }
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

<div class="zen-site-wrapper" id="game-container">
    <div id="zen-header">
        <div id="p-count">🌸 10</div>
        <div class="timer-box" id="ev-label">EVENT: --:--</div>
        <div class="timer-box" id="q-label" style="color:var(--p-purple)">QUESTS: --:--</div>
        <button onclick="save(true)" class="zen-btn" style="background:#2ecc71; color:white; border:none; padding:10px 15px;">SAVE</button>
        <input type="text" id="code-in" placeholder="CODE" style="width:70px; padding:8px; border-radius:10px; border:1px solid #ccc; font-size:10px;">
        <button onclick="applyCode()" class="zen-btn" style="background:var(--p-purple); color:white; border:none; padding:10px 15px;">OK</button>
    </div>

    <div id="game-log">WELCOME BACK TO THE GARDEN</div>

    <div class="zen-main">
        <div class="zen-side">
            <div class="zen-btn active" id="t0" onclick="setTool(0)">🌱 Grass (10)</div>
            <div class="zen-btn" id="t1" onclick="setTool(1)">🌼 Daisy (25)</div>
            <div class="zen-btn" id="t2" onclick="setTool(2)">🌷 Tulip (120)</div>
            <div class="zen-btn" id="t3" onclick="setTool(3)">🌳 Oak (2k)</div>
            <div class="zen-btn" id="t4" onclick="setTool(4)">🍌 Banana (8k)</div>
            <div class="zen-btn" id="t5" onclick="setTool(5)">🍋 Lemon (25k)</div>
            <button class="zen-btn" onclick="setTool(-1)" style="background:#ff7675; color:white; border:none;">🗑️ SHOVEL</button>
        </div>

        <div id="zen-grid"></div>

        <div class="zen-side">
            <h4 style="margin:0; font-size:12px;">ACTIVE QUESTS</h4>
            <div id="quest-list" style="font-size:10px; color:#444;"></div>
            <button class="zen-btn" onclick="buyPack()" style="background:orange; color:white; border:none; margin-top:10px;">🎁 OPEN PACK (1k)</button>
        </div>
    </div>
</div>

<script>
    let petals = 10, tool = 0, harvested = 0;
    let plots = Array(16).fill(null).map(() => ({ stage: 0, type: -1, time: 0, max: 0, sur: false, dan: false }));
    let eTime = 2700, qTime = 600, quests = [], mode = "";
    const plants = [
        { i: "🌱", c: 10, r: 25, t: 5 }, { i: "🌼", c: 25, r: 60, t: 12 },
        { i: "🌷", c: 120, r: 300, t: 30 }, { i: "🌳", c: 2000, r: 5000, t: 120 },
        { i: "🍌", c: 8000, r: 18000, t: 300 }, { i: "🍋", c: 25000, r: 60000, t: 600 }
    ];

    function init() {
        const g = document.getElementById('zen-grid');
        for(let i=0; i<16; i++) {
            let d = document.createElement('div');
            d.className='slot'; d.id='s'+i; d.onclick=()=>tap(i);
            g.appendChild(d);
        }
        load();
        if(quests.length === 0) genQuests();
        setInterval(tick, 100);
        setInterval(()=>save(false), 30000);
    }

    function log(msg) { document.getElementById('game-log').innerText = msg; }

    function tick() {
        eTime -= 0.1; qTime -= 0.1;
        if(eTime <= 0) cycle();
        if(qTime <= 0) genQuests();
        plots.forEach((p, i) => {
            if(p.stage > 0 && p.stage < 3) {
                p.time -= 0.1;
                if(p.time <= 0) { p.stage++; p.time = (p.stage < 3) ? plants[p.type].t : 0; p.max = p.time; }
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

    function fmt(s) { let m=Math.max(0, Math.floor(s/60)); let sec=Math.max(0, Math.floor(s%60)); return m+":"+sec.toString().padStart(2,'0'); }

    function tap(i) {
        let p = plots[i];
        if(tool === -1) { plots[i] = { stage: 0, type: -1, time: 0, max: 0, sur: false, dan: false }; return; }
        
        if(p.stage === 3) {
            let mult = (p.sur ? 3 : 1) + (p.dan ? 5 : 0);
            petals += (plants[p.type].r * mult);
            harvested++;
            plots[i] = { stage: 0, type: -1, time: 0, max: 0, sur: false, dan: false };
            log("HARVESTED: +🌸" + (plants[p.type].r * mult));
        } else if(p.stage === 0) {
            if(petals >= plants[tool].c) {
                petals -= plants[tool].c;
                p.type = tool; p.stage = 1; p.time = plants[tool].t; p.max = p.time;
                p.dan = (mode === "disco"); p.sur = (mode === "storm");
            } else { log("NOT ENOUGH PETALS!"); }
        }
    }

    function render(i) {
        let p = plots[i], el = document.getElementById('s'+i);
        el.className = 'slot' + (p.sur ? ' survivor' : '') + (p.dan ? ' lets-dance' : '');
        if(p.stage === 0) { el.innerHTML = ""; return; }
        let icon = p.stage === 3 ? plants[p.type].i : (p.stage === 2 ? "🌿" : "🍃");
        el.innerHTML = `<span>${icon}</span>` + (p.stage < 3 ? `<div class="p-bar-wrap"><div class="p-bar-fill" style="width:${((p.max-p.time)/p.max)*100}%"></div></div>` : "");
    }

    function genQuests() {
        qTime = 600;
        const pool = [
            { t: "Grow 3 Oaks 🌳", c: () => plots.filter(x => x.type === 3).length >= 3, r: 2500 },
            { t: "Harvest 10 Times", c: () => harvested >= 10, r: 1000 },
            { t: "Find a Dancer 🕺", c: () => plots.some(x => x.dan), r: 5000 },
            { t: "Grow 2 Bananas 🍌", c: () => plots.filter(x => x.type === 4).length >= 2, r: 8000 },
            { t: "Garden Full 🌸", c: () => plots.every(x => x.stage > 0), r: 1500 },
            // --- 5 NEW QUESTS ADDED HERE ---
            { t: "Lemon Squeezer 🍋", c: () => plots.some(x => x.type === 5 && x.stage === 3), r: 20000 },
            { t: "Storm Survivor ⛈️", c: () => plots.some(x => x.sur && x.stage === 3), r: 4000 },
            { t: "Grass Field 🌱", c: () => plots.filter(x => x.type === 0 && x.stage === 3).length >= 6, r: 500 },
            { t: "Petal Millionaire", c: () => petals >= 1000000, r: 50000 },
            { t: "Tulip Collector 🌷", c: () => plots.filter(x => x.type === 2).length >= 4, r: 3000 }
        ];
        quests = pool.sort(() => 0.5 - Math.random()).slice(0, 3).map(q => ({...q, done: false}));
        renderQuests();
        log("NEW QUESTS ARRIVED!");
    }

    function renderQuests() {
        let h = "";
        quests.forEach(q => h += `<div style="margin-bottom:8px; padding-bottom:4px; border-bottom:1px solid #eee; ${q.done?'color:#2ecc71;text-decoration:line-through':''}">• ${q.t}<br><b>+🌸${q.r}</b></div>`);
        document.getElementById('quest-list').innerHTML = h;
    }

    function checkQuests() { quests.forEach(q => { if(!q.done && q.c()) { petals += q.r; q.done = true; renderQuests(); log("QUEST COMPLETE: +🌸"+q.r); } }); }
    function setTool(id) { tool = id; document.querySelectorAll('.zen-btn').forEach((b, i) => { if(i<6) b.classList.toggle('active', i===id); }); }
    function buyPack() { if(petals >= 1000) { petals -= 1000; let win = Math.floor(Math.random() * 3000) + 1200; petals += win; log("PACK OPENED: +🌸"+win); } }

    function cycle() {
        const wrap = document.getElementById('game-container');
        if(mode === "") { mode = "storm"; eTime = 60; wrap.className = "zen-site-wrapper storm"; plots.forEach(p => { if(p.stage > 0) p.sur = true; }); log("STORM WARNING!"); }
        else if(mode === "storm") { mode = "wait"; eTime = 1800; wrap.className = "zen-site-wrapper"; log("STORM PASSED"); }
        else if(mode === "wait") { mode = "disco"; eTime = 300; wrap.className = "zen-site-wrapper disco"; log("DISCO TIME!"); }
        else { mode = ""; eTime = 2700; wrap.className = "zen-site-wrapper"; log("CALM WEATHER"); }
    }

    function applyCode() {
        let c = document.getElementById('code-in').value.toUpperCase();
        if(c === "PEPSI6") { petals += 1000000; log("CODE: 1,000,000 PETALS!"); }
        else if(c === "MANILOVETHISGAME") { mode = "disco"; eTime = 300; document.getElementById('game-container').className = "zen-site-wrapper disco"; log("CODE: DISCO ACTIVE"); }
        else { log("INVALID CODE"); }
        document.getElementById('code-in').value = "";
    }

    function save(manual) { 
        localStorage.setItem('ZEN_V17', JSON.stringify({ p: petals, plots, ts: Date.now(), h: harvested, m: mode, e: eTime, qT: qTime, qs: quests })); 
        if(manual) log("saved =)"); 
    }

    function load() {
        let s = localStorage.getItem('ZEN_V17');
        if(!s) return;
        let d = JSON.parse(s);
        petals = d.p; plots = d.plots; harvested = d.h; mode = d.m; eTime = d.e; qTime = d.qT || 600; quests = d.qs || [];
        if(d.ts) {
            let diff = Math.floor((Date.now() - d.ts) / 1000);
            if(diff > 10) {
                let grown = 0;
                plots.forEach(p => {
                    if(p.stage > 0 && p.stage < 3) {
                        let rem = diff;
                        while(rem > 0 && p.stage < 3) {
                            if(rem >= p.time) { rem -= p.time; p.stage++; grown++; p.time = (p.stage < 3) ? plants[p.type].t : 0; p.max = p.time; }
                            else { p.time -= rem; rem = 0; }
                        }
                    }
                });
                log("OFFLINE: " + grown + " STAGES GROWN!");
            }
        }
        renderQuests();
    }
</script>
</body>
</html>
