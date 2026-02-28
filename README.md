# ZEN-GARDEN
plant and grow =)
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ZEN GARDEN: BY ASHTON</title>
    <style>
        :root { --dirt: #5d3a1a; --grass: #2d5a27; --gold: #ffd700; --ui-bg: rgba(255,255,255,0.95); }
        body { background: #a8dadc; font-family: 'Segoe UI', sans-serif; margin: 0; display: flex; flex-direction: column; align-items: center; transition: background 0.5s; overflow: hidden; user-select: none; }
        
        #header { background: #fff; width: 100%; padding: 12px; text-align: center; box-shadow: 0 4px 10px rgba(0,0,0,0.1); position: sticky; top: 0; z-index: 1000; display: flex; justify-content: center; gap: 15px; align-items: center; }
        #petal-display { color: #e84393; font-size: 22px; font-weight: 800; background: #fff0f5; padding: 4px 12px; border-radius: 20px; border: 2px solid #ffdeeb; }
        .timer-box { font-weight: bold; font-size: 12px; background: #f1f2f6; padding: 6px 10px; border-radius: 10px; color: #2f3542; border: 1px solid #dfe4ea; min-width: 110px; }
        
        #save-toast { position: fixed; top: 20%; background: #2ecc71; color: white; padding: 10px 25px; border-radius: 50px; font-weight: bold; box-shadow: 0 5px 15px rgba(0,0,0,0.2); transform: translateY(-100px); opacity: 0; transition: 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275); z-index: 3000; pointer-events: none; }
        #save-toast.show { transform: translateY(0); opacity: 1; }

        .main { display: flex; gap: 15px; padding: 15px; height: 82vh; width: 95%; max-width: 1200px; justify-content: center; }
        .sidebar { background: var(--ui-bg); padding: 12px; border-radius: 15px; width: 180px; box-shadow: 0 8px 20px rgba(0,0,0,0.1); border: 1px solid rgba(0,0,0,0.05); display: flex; flex-direction: column; z-index: 1500; }
        
        #grid { display: grid; grid-template-columns: repeat(4, 100px); grid-template-rows: repeat(3, 100px); grid-gap: 12px; background: #4a2c12; padding: 20px; border-radius: 25px; border: 10px solid #3d240f; box-shadow: inset 0 0 20px rgba(0,0,0,0.5); position: relative; }
        .slot { width: 100px; height: 100px; background: #795548; border-radius: 15px; display: flex; align-items: center; justify-content: center; font-size: 45px; cursor: pointer; position: relative; border: 4px solid transparent; transition: 0.1s; z-index: 100; }

        .plant-visual { pointer-events: none; position: absolute; width: 100%; height: 100%; display: flex; flex-direction: column; align-items: center; justify-content: center; z-index: 1; }

        body.disco .lets-dance .plant-visual { animation: row-traveler 5s infinite linear; }
        @keyframes row-traveler { 0% { transform: translateY(-150px); opacity: 0; } 10% { opacity: 1; } 100% { transform: translateY(350px); opacity: 1; } }

        .big-pulse .plant-visual { animation: big-jump 0.5s 4 alternate ease-in-out !important; }
        @keyframes big-jump { 0% { transform: scale(1); } 100% { transform: scale(1.4); filter: hue-rotate(90deg); } }

        .item { padding: 6px; margin-bottom: 5px; border: 2px solid #eee; border-radius: 10px; cursor: pointer; font-weight: bold; text-align: center; font-size: 12px; background: white; }
        .item.active { border-color: #2ecc71; background: #e8f5e9; box-shadow: 0 3px 0 #27ae60; transform: translateY(-1px); }
        
        .survivor { border: 4px solid #ffd700 !important; box-shadow: 0 0 15px #ffd700; }
        .progress { width: 60%; height: 4px; background: rgba(0,0,0,0.3); border-radius: 2px; overflow: hidden; margin-top: 5px; }
        .bar { height: 100%; background: #00b894; width: 0%; transition: width 0.1s linear; }

        #modal-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.7); z-index: 4000; display: none; align-items: center; justify-content: center; backdrop-filter: blur(4px); }
        .modal-content { background: white; padding: 25px; border-radius: 20px; width: 260px; text-align: center; }
        .modal-btn { background: #6c5ce7; color: white; border: none; padding: 10px; border-radius: 10px; cursor: pointer; margin-top: 10px; font-weight: bold; width: 100%; }

        body.storm { background: #2d3436 !important; }
        body.disco { animation: disco-bg 0.4s infinite alternate; }
        @keyframes disco-bg { from { background: #fd79a8; } to { background: #74b9ff; } }
        
        #version-tag { position: fixed; bottom: 10px; right: 10px; font-size: 10px; color: #636e72; font-weight: bold; }
    </style>
</head>
<body onload="init()">

<div id="save-toast">Garden Saved! =)</div>
<div id="version-tag">v2.2 | Ashton | MIT License</div>

<div id="modal-overlay" onclick="closeModal()">
    <div class="modal-content" id="modal-box" onclick="event.stopPropagation()">
        <h2 id="modal-title" style="margin:0"></h2>
        <div id="modal-body" style="padding: 15px 0;"></div>
        <button class="modal-btn" onclick="closeModal()">OK</button>
    </div>
</div>

<div id="header">
    <div id="petal-display">🌸 <span id="p-count">10</span></div>
    <div class="timer-box" id="t-main">🌊 WAVE: <span id="timer-event">45:00</span></div>
    <div class="timer-box" id="t-disco" style="display:none; background:#ff9ff3;">⏳ DISCO: <span id="timer-disco-wait">30:00</span></div>
    <button onclick="manualSave()" style="background:#2ecc71; color:white; border:none; padding:8px 15px; border-radius:12px; cursor:pointer; font-weight:bold;">💾 SAVE</button>
    <button onclick="openCodeApp()" style="background:#6c5ce7; color:white; border:none; padding:8px 12px; border-radius:12px; cursor:pointer;">🔑 CODE</button>
</div>

<div class="main">
    <div class="sidebar">
        <h5 style="text-align:center; margin:5px;">SHOP</h5>
        <div class="item active" onclick="setTool(0)">🌱 Grass (10)</div>
        <div class="item" onclick="setTool(1)">🌼 Daisy (25)</div>
        <div class="item" onclick="setTool(2)">🌷 Tulip (120)</div>
        <div class="item" onclick="setTool(3)">🌳 Oak (2k)</div>
        <div class="item" onclick="setTool(4)" style="color: #8a6d3b;">🍌 Banana (8k)</div>
        <div class="item" onclick="setTool(5)">🍋 Lemon (25k)</div>
        <div class="item" style="background:#ff7675; color:white; margin-top: auto;" onclick="setTool(-1)">🗑️ SHOVEL</div>
    </div>

    <div id="grid"></div>

    <div class="sidebar" style="width: 220px;">
        <h5 style="text-align:center; margin:5px;">QUESTS</h5>
        <div id="quest-list" style="background:#f9f9f9; padding:8px; border-radius:12px; border:1px solid #ddd; min-height: 150px; font-size: 11px;"></div>
        <button class="item" onclick="buyPack()" style="width:100%; background:linear-gradient(45deg, #f0932b, #ffbe76); color:white; border:none; margin-top:10px">🎁 PACK (1k)</button>
        <button class="item" onclick="showCredits()" style="background:#dfe4ea; margin-top: 5px;">📜 CREDITS</button>
    </div>
</div>

<script>
    let petals = 10, tool = 0;
    let plots = Array(12).fill(null).map(() => ({ stage: 0, type: -1, time: 0, max: 0, sur: false, dan: false }));
    let eTime = 2700, qTime = 600, wTime = 0, pulseTimer = 120, state = "NORMAL";
    let activeQuests = [];

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
        for(let i=0; i<12; i++) {
            let d = document.createElement('div');
            d.className = 'slot'; d.id = 'plot-'+i;
            d.onclick = () => doClick(i);
            g.appendChild(d);
        }
        load();
        genQuests();
        setInterval(tick, 100);
    }

    function tick() {
        qTime -= 0.1; pulseTimer -= 0.1;
        if(qTime <= 0) genQuests();
        if(pulseTimer <= 0) { triggerPulse(); pulseTimer = 120; }

        if (state === "NORMAL") {
            eTime -= 0.1;
            if (eTime <= 0) { state = "STORM"; eTime = 300; document.body.className = "storm"; plots.forEach(p => { if(p.stage > 0) p.sur = true; }); }
        } else if (state === "STORM") {
            eTime -= 0.1;
            if (eTime <= 0) { state = "PRE_DISCO"; wTime = 1800; document.body.className = ""; updateHeaders(); }
        } else if (state === "PRE_DISCO") {
            wTime -= 0.1;
            if (wTime <= 0) { state = "DISCO"; eTime = 300; document.body.className = "disco"; updateHeaders(); }
        } else if (state === "DISCO") {
            eTime -= 0.1;
            if (eTime <= 0) { state = "NORMAL"; eTime = 2700; document.body.className = ""; updateHeaders(); }
        }

        plots.forEach((p, i) => {
            if(p.stage > 0 && p.stage < 3) {
                p.time -= 0.1;
                if(p.time <= 0) { p.stage++; p.time = (p.stage < 3) ? plants[p.type].t : 0; p.max = (p.stage < 3) ? p.time : 0; }
            }
            renderPlot(i);
        });

        activeQuests.forEach(q => { if(!q.done && q.check()) { q.done = true; petals += q.reward; renderQuestsUI(); } });
        updateUIVals();
    }

    function doClick(i) {
        let p = plots[i];
        if(tool === -1) { resetPlot(i); return; }
        if(p.stage === 3) {
            let mult = (p.sur ? 3 : 1) + (p.dan ? 4 : 0);
            petals += (plants[p.type].rev * mult);
            resetPlot(i); return;
        }
        if(p.stage === 0 && petals >= plants[tool].cost) {
            petals -= plants[tool].cost;
            p.type = tool; p.stage = 1; p.time = plants[tool].t; p.max = p.time;
            p.sur = (state === "STORM");
            p.dan = (state === "DISCO" || Math.random() < 0.15);
        }
    }

    function renderPlot(i) {
        let p = plots[i], el = document.getElementById('plot-'+i);
        el.className = 'slot' + (p.dan ? ' lets-dance' : '');
        if(p.stage === 0) { el.innerHTML = ""; return; }
        let icon = p.stage === 3 ? plants[p.type].icon : (p.stage === 2 ? "🌿" : "🍃");
        el.innerHTML = `<div class="plant-visual ${p.sur ? 'survivor' : ''}">
            <span>${icon}</span>
            ${p.stage < 3 ? `<div class="progress"><div class="bar" style="width:${((p.max-p.time)/p.max)*100}%"></div></div>` : ""}
        </div>`;
    }

    function triggerPulse() {
        plots.forEach((p, i) => { if(p.dan) { let el = document.getElementById('plot-'+i); el.classList.add('big-pulse'); setTimeout(() => el.classList.remove('big-pulse'), 2500); } });
    }

    function updateHeaders() {
        document.getElementById('t-main').style.display = (state === "NORMAL" || state === "STORM") ? "block" : "none";
        document.getElementById('t-disco').style.display = (state === "PRE_DISCO") ? "block" : "none";
    }

    function updateUIVals() {
        document.getElementById('p-count').innerText = Math.floor(petals).toLocaleString();
        if(document.getElementById('timer-event')) document.getElementById('timer-event').innerText = formatTime(eTime);
        document.getElementById('timer-disco-wait').innerText = formatTime(wTime);
    }

    function formatTime(t) {
        let m = Math.floor(t/60), s = Math.floor(t%60);
        return m + ":" + (s < 0 ? 0 : s).toString().padStart(2, '0');
    }

    function resetPlot(i) { plots[i] = { stage: 0, type: -1, time: 0, max: 0, sur: false, dan: false }; }

    function setTool(idx) {
        tool = idx;
        document.querySelectorAll('.sidebar:first-child .item').forEach((el, i) => { el.classList.toggle('active', (idx === -1 && i === 6) || (idx === i)); });
    }

    function buyPack() {
        if(petals >= 1000) { petals -= 1000; petals += (Math.random() > 0.8 ? 5000 : 500); showAppModal("REWARD", "Pack opened!"); }
    }

    function manualSave() { save(); const toast = document.getElementById('save-toast'); toast.classList.add('show'); setTimeout(() => toast.classList.remove('show'), 2000); }

    function save() { localStorage.setItem('ZEN_ASHTON_RELEASE', JSON.stringify({ p: petals, plots: plots, e: eTime, s: state, w: wTime })); }

    function load() {
        let s = localStorage.getItem('ZEN_ASHTON_RELEASE');
        if(s) { let d = JSON.parse(s); petals = d.p; plots = d.plots; eTime = d.e; state = d.s; wTime = d.w; updateHeaders(); }
    }

    function showCredits() {
        showAppModal("CREDITS", `<b>Lead Developer:</b> Ashton<br><b>License:</b> MIT 2026<br><br>Developed for the Zen Garden Community.`);
    }

    function showAppModal(title, body) { document.getElementById('modal-title').innerText = title; document.getElementById('modal-body').innerHTML = body; document.getElementById('modal-overlay').style.display = "flex"; }
    function closeModal() { document.getElementById('modal-overlay').style.display = "none"; }
    function openCodeApp() { showAppModal("CODE", `<input type="text" id="code-input" placeholder="..." style="width:80%; padding:8px; text-align:center;"><button class="modal-btn" onclick="submitCode()">GO</button>`); }
    function submitCode() { let val = document.getElementById('code-input').value.toUpperCase(); if(val === "MANILOVETHISGAME") { state = "DISCO"; eTime = 300; document.body.className = "disco"; closeModal(); save(); updateHeaders(); } }

    function genQuests() {
        qTime = 600;
        const possibleQuests = [
            { text: "Grow a Banana", check: () => plots.some(p => p.type === 4 && p.stage === 3), reward: 5000 },
            { text: "Survivor (Storm)", check: () => plots.filter(p => p.sur).length >= 1, reward: 3000 },
            { text: "Zen Master (100k)", check: () => petals >= 100000, reward: 50000 }
        ];
        activeQuests = possibleQuests.sort(() => 0.5 - Math.random()).slice(0, 3).map(q => ({...q, done: false}));
        renderQuestsUI();
    }

    function renderQuestsUI() {
        let h = "";
        activeQuests.forEach(q => h += `<div style="padding:6px; border-bottom:1px solid #eee; ${q.done?'color:#2ecc71; font-weight:bold;':''}">• ${q.text}<br><span style="color:#e84393; font-size:9px;">+${q.reward}</span></div>`);
        document.getElementById('quest-list').innerHTML = h;
    }
</script>
</body>
</html>
