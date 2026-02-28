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
<html>
<head>
    <title>ZEN GARDEN: BY ASHTON (QUEST UPDATE)</title>
    <style>
        :root { --dirt: #5d3a1a; --grass: #2d5a27; --gold: #ffd700; --banana: #f1c40f; --ui-bg: #ffffff; }
        
        body { 
            background: #a8dadc; 
            font-family: 'Segoe UI', sans-serif; 
            margin: 0; 
            display: flex; 
            flex-direction: column; 
            align-items: center; 
            transition: background 1s; 
            overflow-y: auto; 
            min-height: 100vh;
            user-select: none; 
        }
        
        #header { background: #fff; width: 100%; padding: 12px; text-align: center; box-shadow: 0 4px 10px rgba(0,0,0,0.1); position: sticky; top: 0; z-index: 100; display: flex; justify-content: center; gap: 10px; align-items: center; }
        #petal-display { color: #e84393; font-size: 22px; font-weight: 800; background: #fff0f5; padding: 4px 12px; border-radius: 20px; border: 2px solid #ffdeeb; }
        .timer-box { font-weight: bold; font-size: 11px; background: #f1f2f6; padding: 6px 10px; border-radius: 10px; color: #2f3542; border: 1px solid #dfe4ea; min-width: 100px;}
        
        #save-toast { position: fixed; top: 20%; background: #2ecc71; color: white; padding: 10px 25px; border-radius: 50px; font-weight: bold; box-shadow: 0 5px 15px rgba(0,0,0,0.2); transform: translateY(-100px); opacity: 0; transition: 0.4s; z-index: 2000; pointer-events: none; }
        #save-toast.show { transform: translateY(0); opacity: 1; }

        .main { display: flex; flex-direction: row; flex-wrap: nowrap; gap: 15px; padding: 20px; width: fit-content; max-width: 100vw; justify-content: center; align-items: flex-start; }
        
        /* SHOP SIDEBAR - NOW WHITE */
        .sidebar { background: var(--ui-bg); padding: 15px; border-radius: 15px; width: 160px; box-shadow: 0 8px 20px rgba(0,0,0,0.1); border: 1px solid #eee; display: flex; flex-direction: column; min-height: 480px; flex-shrink: 0; }
        .item { padding: 10px; margin: 4px 0; border-radius: 8px; cursor: pointer; transition: 0.2s; border: 1px solid #f0f0f0; font-weight: 500; font-size: 13px; background: #fff; }
        .item:hover { background: #f8f9fa; transform: translateX(5px); }
        .item.active { background: #e3f2fd; border-color: #2196f3; color: #1976d2; }

        #grid { 
            display: grid; grid-template-columns: repeat(6, 95px); grid-template-rows: repeat(3, 95px); grid-gap: 10px; 
            background: #4a2c12; padding: 15px; border-radius: 25px; border: 8px solid #3d240f; 
            box-shadow: inset 0 0 20px rgba(0,0,0,0.5); position: relative; overflow: hidden;
        }

        .row-dancer { position: absolute; font-size: 50px; z-index: 1000; pointer-events: none; left: -80px; transition: left 3s linear; display: none; }
        .slot { width: 95px; height: 95px; background: #795548; border-radius: 12px; display: flex; align-items: center; justify-content: center; font-size: 40px; cursor: pointer; position: relative; border: 3px solid transparent; transition: 0.1s; }
        
        .survivor { box-shadow: 0 0 15px #ffd700; border-color: #ffd700 !important; }
        .lets-dance { animation: rainbow 2s infinite linear, glow 0.5s infinite alternate; }
        .lets-dance span { display: inline-block; animation: dance 0.6s infinite linear; }

        @keyframes dance { 0% { transform: translateY(0); } 50% { transform: translateY(-8px); } 100% { transform: translateY(0); } }
        @keyframes glow { from { transform: scale(1); } to { transform: scale(1.05); } }
        @keyframes rainbow {
            0% { filter: hue-rotate(0deg) drop-shadow(0 0 10px red); }
            33% { filter: hue-rotate(120deg) drop-shadow(0 0 10px lime); }
            66% { filter: hue-rotate(240deg) drop-shadow(0 0 10px blue); }
            100% { filter: hue-rotate(360deg) drop-shadow(0 0 10px red); }
        }

        .progress { position: absolute; bottom: 10px; width: 60%; height: 4px; background: rgba(0,0,0,0.3); border-radius: 2px; overflow: hidden; }
        .bar { height: 100%; background: #00b894; width: 0%; transition: width 0.1s linear; }

        #modal-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.7); z-index: 1000; display: none; align-items: center; justify-content: center; backdrop-filter: blur(4px); }
        .modal-content { background: white; padding: 25px; border-radius: 20px; width: 260px; text-align: center; }
        .modal-btn { background: #6c5ce7; color: white; border: none; padding: 10px; border-radius: 10px; cursor: pointer; margin-top: 10px; font-weight: bold; width: 100%; }

        body.storm { background: #2d3436 !important; }
        body.disco { animation: disco-bg 1s infinite alternate; }
        @keyframes disco-bg { 0% { background: #ff00ff; } 50% { background: #00ffff; } 100% { background: #ffff00; } }
        
        .quest-item { background: white; border: 1px solid #eee; border-radius: 8px; padding: 8px; margin-bottom: 8px; transition: 0.3s; }
        .quest-done { background: #e8f5e9; border-color: #2ecc71; color: #27ae60; text-decoration: line-through; }
    </style>
</head>
<body onload="init()">

<div id="save-toast">Saved! =)</div>

<div id="modal-overlay" onclick="closeModal()">
    <div class="modal-content" id="modal-box" onclick="event.stopPropagation()">
        <h2 id="modal-title" style="margin:0"></h2>
        <div id="modal-body"></div>
        <button class="modal-btn" onclick="closeModal()">OK</button>
    </div>
</div>

<div id="header">
    <div id="petal-display">🌸 <span id="p-count">10</span></div>
    <div class="timer-box" id="timer-label">🌊 WAVE IN: <span id="timer-event">45:00</span></div>
    <div class="timer-box">📜 REFRESH: <span id="timer-quest">10:00</span></div>
    <button onclick="manualSave()" style="background:#2ecc71; color:white; border:none; padding:8px 15px; border-radius:12px; cursor:pointer; font-weight:bold;">💾 SAVE</button>
    <button onclick="openCodeApp()" style="background:#6c5ce7; color:white; border:none; padding:8px 12px; border-radius:12px; cursor:pointer;">🔑 CODE</button>
</div>

<div class="main">
    <div class="sidebar">
        <h3 style="margin:0 0 10px 0; text-align:center; color:#333; font-size: 16px;">GARDEN SHOP</h3>
        <div class="item active" onclick="setTool(0)">🌱 Grass (10)</div>
        <div class="item" onclick="setTool(1)">🌼 Daisy (25)</div>
        <div class="item" onclick="setTool(2)">🌷 Tulip (120)</div>
        <div class="item" onclick="setTool(3)">🌳 Oak (2k)</div>
        <div class="item" onclick="setTool(4)" style="color: #8a6d3b;">🍌 Banana (8k)</div>
        <div class="item" onclick="setTool(5)" style="color: #f1c40f;">🍋 Lemon (25k)</div>
        <div class="item" id="shovel-btn" style="background:#ff7675; color:white; margin-top: auto; text-align: center;" onclick="setTool(-1)">🗑️ SHOVEL</div>
    </div>

    <div id="grid">
        <div id="rd-0" class="row-dancer" style="top: 25px;">🕺</div>
        <div id="rd-1" class="row-dancer" style="top: 130px;">💃</div>
        <div id="rd-2" class="row-dancer" style="top: 235px;">🕺</div>
    </div>

    <div class="sidebar" style="width: 200px;">
        <h3 style="margin:0 0 10px 0; text-align:center; color:#333; font-size: 16px;">QUESTS</h3>
        <div id="quest-list" style="background:#fcfcfc; padding:8px; border-radius:12px; border:1px solid #ddd; min-height: 150px; font-size: 11px; overflow-y: auto; max-height: 300px;"></div>
        <button class="item" onclick="buyPack()" style="width:100%; background:linear-gradient(45deg, #f0932b, #ffbe76); color:white; border:none; position:relative; margin-top:10px; font-weight: bold;">
            🎁 GIFT PACK (1k)
        </button>
    </div>
</div>

<script>
    let petals = 10, tool = 0;
    let totalHarvested = 0;
    let plots = Array(18).fill(null).map(() => ({ stage: 0, type: -1, time: 0, max: 0, sur: false, dan: false }));
    let eTime = 2700, qTime = 600, subTimer = 0, activeQuests = [];
    let state = "WAIT_STORM";

    const plants = [
        { icon: "🌱", cost: 10, rev: 20, t: 5 },
        { icon: "🌼", cost: 25, rev: 50, t: 12 },
        { icon: "🌷", cost: 120, rev: 240, t: 25 },
        { icon: "🌳", cost: 2000, rev: 1000, t: 60 },
        { icon: "🍌", cost: 8000, rev: 3500, t: 45 },
        { icon: "🍋", cost: 25000, rev: 10000, t: 90 }
    ];

    function init() {
        const g = document.getElementById('grid');
        for(let i=0; i<18; i++) {
            let d = document.createElement('div');
            d.className = 'slot'; d.id = 'plot-'+i;
            d.onclick = () => doClick(i);
            g.appendChild(d);
        }
        load();
        if(activeQuests.length === 0) genQuests();
        setInterval(tick, 100);
        setInterval(save, 5000); 
    }

    function processOfflineTime(seconds) {
        if (seconds <= 0) return;
        eTime -= seconds;
        if (eTime < 0) eTime = 10;
        plots.forEach((p) => {
            let remaining = seconds;
            while(remaining > 0 && p.stage > 0 && p.stage < 3) {
                if (remaining >= p.time) {
                    remaining -= p.time;
                    p.stage++;
                    if (p.stage < 3) { p.time = plants[p.type].t; p.max = p.time; }
                    else { p.time = 0; }
                } else { p.time -= remaining; remaining = 0; }
            }
        });
        showAppModal("Welcome Back!", `You were away for ${Math.floor(seconds)}s. Your plants grew while you were gone!`);
    }

    function tick() {
        eTime -= 0.1; qTime -= 0.1;
        if(qTime <= 0) genQuests();
        
        if(eTime <= 0) {
            if(state === "WAIT_STORM") {
                state = "STORM"; eTime = 60; document.body.className = "storm";
                plots.forEach(p => { if(p.stage > 0) p.sur = true; });
            } else if(state === "STORM") {
                state = "WAIT_DISCO"; eTime = 1800; document.body.className = "";
            } else if(state === "WAIT_DISCO") {
                triggerDisco();
            } else if(state === "DISCO") {
                state = "WAIT_STORM"; eTime = 2700; document.body.className = "";
            }
        }

        if(state === "DISCO") {
            subTimer -= 0.1;
            if(subTimer <= 0) { triggerRowDancers(); subTimer = 60; }
        }

        checkQuests();
        updateUI();
        plots.forEach((p, i) => {
            if(p.stage > 0 && p.stage < 3) {
                p.time -= 0.1;
                if(p.time <= 0) { p.stage++; p.time = (p.stage < 3) ? plants[p.type].t : 0; p.max = (p.stage < 3) ? p.time : 0; }
            }
            renderPlot(i);
        });
    }

    function triggerDisco() {
        state = "DISCO"; eTime = 300;
        document.body.className = "disco";
        subTimer = 0;
    }

    function updateUI() {
        let lbl = document.getElementById('timer-label');
        if(state === "WAIT_STORM") lbl.innerHTML = `🌊 WAVE IN: <span id="timer-event"></span>`;
        if(state === "STORM") lbl.innerHTML = `⚠️ STORM: <span id="timer-event"></span>`;
        if(state === "WAIT_DISCO") lbl.innerHTML = `🕺 DISCO IN: <span id="timer-event"></span>`;
        if(state === "DISCO") lbl.innerHTML = `✨ PARTY!: <span id="timer-event"></span>`;

        document.getElementById('p-count').innerText = Math.floor(petals).toLocaleString();
        let displayE = Math.max(0, eTime);
        document.getElementById('timer-event').innerText = Math.floor(displayE/60) + ":" + Math.floor(displayE%60).toString().padStart(2, '0');
        document.getElementById('timer-quest').innerText = Math.max(0, Math.floor(qTime/60)) + ":" + Math.max(0, Math.floor(qTime%60)).toString().padStart(2, '0');
    }

    function triggerRowDancers() {
        for(let r=0; r<3; r++) {
            let rd = document.getElementById('rd-' + r);
            rd.style.display = "block"; rd.style.left = "-80px";
            setTimeout(() => { rd.style.left = "700px"; }, 50);
            setTimeout(() => {
                for(let c=0; c<6; c++) {
                    let idx = (r * 6) + c;
                    if(plots[idx].stage > 0 && !plots[idx].dan && Math.random() < 0.3) {
                        plots[idx].dan = true; renderPlot(idx);
                    }
                }
            }, 1500);
            setTimeout(() => { rd.style.display = "none"; }, 3000);
        }
    }

    function doClick(i) {
        let p = plots[i];
        if(tool === -1) { resetPlot(i); return; }
        if(p.stage === 3) {
            let mult = (p.sur ? 3 : 1) + (p.dan ? 7 : 0);
            petals += (plants[p.type].rev * mult);
            totalHarvested++;
            resetPlot(i);
            return;
        }
        if(p.stage === 0 && petals >= plants[tool].cost) {
            petals -= plants[tool].cost;
            p.type = tool; p.stage = 1; p.time = plants[tool].t; p.max = p.time;
        }
    }

    function renderPlot(i) {
        let p = plots[i], el = document.getElementById('plot-'+i);
        el.className = 'slot' + (p.sur ? ' survivor' : '') + (p.dan ? ' lets-dance' : '');
        if(p.stage === 0) { el.innerHTML = ""; return; }
        let icon = p.stage === 3 ? plants[p.type].icon : (p.stage === 2 ? "🌿" : "🍃");
        el.innerHTML = `<span>${icon}</span>` + (p.stage < 3 ? `<div class="progress"><div class="bar" style="width:${((p.max-p.time)/p.max)*100}%"></div></div>` : "");
    }

    function resetPlot(i) { plots[i] = { stage: 0, type: -1, time: 0, max: 0, sur: false, dan: false }; }
    function setTool(idx) { tool = idx; document.querySelectorAll('.sidebar .item').forEach((el, i) => { if(i < 7) el.classList.toggle('active', (idx === -1 && i === 6) || (idx === i)); }); }

    function genQuests() {
        qTime = 600;
        const pool = [
            { id: 1, text: "Fill the whole Garden", check: () => plots.every(p => p.stage > 0), reward: 500 },
            { id: 2, text: "Find 3 Dancers", check: () => plots.filter(p => p.dan).length >= 3, reward: 2000 },
            { id: 3, text: "Harvest 10 Plants", check: () => totalHarvested >= 10, reward: 300 },
            { id: 4, text: "Save up 5,000 Petals", check: () => petals >= 5000, reward: 1000 },
            { id: 5, text: "Survive a Storm", check: () => state === "WAIT_DISCO", reward: 800 }
        ];
        // Pick 3 random quests
        activeQuests = pool.sort(() => 0.5 - Math.random()).slice(0, 3).map(q => ({...q, done: false}));
        renderQuestsUI();
    }

    function checkQuests() {
        activeQuests.forEach(q => {
            if(!q.done && q.check()) {
                q.done = true;
                petals += q.reward;
                renderQuestsUI();
            }
        });
    }

    function renderQuestsUI() {
        let h = "";
        activeQuests.forEach(q => {
            h += `<div class="quest-item ${q.done?'quest-done':''}">
                    <b>${q.done?'✓':'•'} ${q.text}</b><br>
                    <span style="font-size:9px; color:#888;">Reward: 🌸 ${q.reward}</span>
                  </div>`;
        });
        document.getElementById('quest-list').innerHTML = h;
    }

    function openCodeApp() {
        showAppModal("SECRET CODE", `<input type="text" id="code-input" placeholder="..." style="width:80%; padding:8px; text-align:center; border:2px solid #ddd; border-radius:10px;"><button class="modal-btn" style="background:#2ecc71" onclick="submitCode()">GO</button>`);
    }

    function submitCode() {
        let val = document.getElementById('code-input').value.toUpperCase();
        if(val === "MANILOVETHISGAME") { triggerDisco(); closeModal(); save(); }
        if(val === "PEPSI6") { petals += 1000000; closeModal(); }
    }

    function showAppModal(title, body) { document.getElementById('modal-title').innerText = title; document.getElementById('modal-body').innerHTML = body; document.getElementById('modal-overlay').style.display = "flex"; }
    function closeModal() { document.getElementById('modal-overlay').style.display = "none"; }
    function manualSave() { save(); const t = document.getElementById('save-toast'); t.classList.add('show'); setTimeout(() => t.classList.remove('show'), 2000); }
    
    function save() { 
        localStorage.setItem('ZEN_FINAL_V1', JSON.stringify({ 
            p: petals, plots: plots, state: state, e: eTime, h: totalHarvested, q: activeQuests, last: Date.now() 
        })); 
    }

    function load() {
        let s = localStorage.getItem('ZEN_FINAL_V1');
        if(s) {
            let d = JSON.parse(s); 
            petals = d.p; eTime = d.e; totalHarvested = d.h || 0;
            state = d.state || "WAIT_STORM";
            if(d.plots) plots = d.plots;
            if(d.q) activeQuests = d.q;
            if(d.last) { processOfflineTime((Date.now() - d.last) / 1000); }
            if(state === "DISCO") document.body.className = "disco";
            if(state === "STORM") document.body.className = "storm";
            renderQuestsUI();
        }
    }
    function buyPack() { if(petals >= 1000) { petals -= 1000; alert("You got a lucky charm! (Quests will reset)"); genQuests(); } }
</script>
</body>
</html>
