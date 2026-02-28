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
    <title>ZEN GARDEN: BY ASHTON</title>
    <style>
        :root { --dirt: #5d3a1a; --grass: #2d5a27; --gold: #ffd700; --banana: #f1c40f; --ui-bg: rgba(255,255,255,0.95); }
        
        body { 
            background: #a8dadc; 
            font-family: 'Segoe UI', sans-serif; 
            margin: 0; 
            display: flex; 
            flex-direction: column; 
            align-items: center; 
            transition: background 0.5s; 
            overflow-y: auto; 
            min-height: 100vh;
            user-select: none; 
        }
        
        #header { background: #fff; width: 100%; padding: 12px; text-align: center; box-shadow: 0 4px 10px rgba(0,0,0,0.1); position: sticky; top: 0; z-index: 100; display: flex; justify-content: center; gap: 10px; align-items: center; }
        #petal-display { color: #e84393; font-size: 22px; font-weight: 800; background: #fff0f5; padding: 4px 12px; border-radius: 20px; border: 2px solid #ffdeeb; }
        .timer-box { font-weight: bold; font-size: 11px; background: #f1f2f6; padding: 6px 10px; border-radius: 10px; color: #2f3542; border: 1px solid #dfe4ea; }
        
        #save-toast { position: fixed; top: 20%; background: #2ecc71; color: white; padding: 10px 25px; border-radius: 50px; font-weight: bold; box-shadow: 0 5px 15px rgba(0,0,0,0.2); transform: translateY(-100px); opacity: 0; transition: 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275); z-index: 2000; pointer-events: none; }
        #save-toast.show { transform: translateY(0); opacity: 1; }

        /* FIXED: Added white-space and flex-wrap control to keep sidebars in line */
        .main { 
            display: flex; 
            flex-direction: row; /* Forces side-by-side */
            flex-wrap: nowrap; /* Prevents Quest bar from dropping down */
            gap: 15px; 
            padding: 20px; 
            width: fit-content; /* Adjusts to fit the wide garden */
            max-width: 100vw;
            justify-content: center; 
            align-items: flex-start;
        }

        .sidebar { background: var(--ui-bg); padding: 12px; border-radius: 15px; width: 160px; box-shadow: 0 8px 20px rgba(0,0,0,0.1); border: 1px solid rgba(0,0,0,0.05); display: flex; flex-direction: column; min-height: 480px; flex-shrink: 0; }
        
        /* GRID: 6 columns wide */
        #grid { display: grid; grid-template-columns: repeat(6, 95px); grid-template-rows: repeat(3, 95px); grid-gap: 10px; background: #4a2c12; padding: 15px; border-radius: 25px; border: 8px solid #3d240f; box-shadow: inset 0 0 20px rgba(0,0,0,0.5); flex-shrink: 0; }
        .slot { width: 95px; height: 95px; background: #795548; border-radius: 12px; display: flex; align-items: center; justify-content: center; font-size: 40px; cursor: pointer; position: relative; border: 3px solid transparent; transition: 0.1s; }
        .slot span { pointer-events: none; } 

        .item { padding: 6px; margin-bottom: 5px; border: 2px solid #eee; border-radius: 10px; cursor: pointer; font-weight: bold; text-align: center; font-size: 11px; background: white; }
        .item.active { border-color: #2ecc71; background: #e8f5e9; box-shadow: 0 3px 0 #27ae60; transform: translateY(-1px); }
        
        .survivor { box-shadow: 0 0 15px #ffd700; border-color: #ffd700 !important; }
        .lets-dance span { display: inline-block; animation: dance 0.6s infinite linear; }
        @keyframes dance { 0% { transform: translateY(0); } 50% { transform: translateY(-8px); } 100% { transform: translateY(0); } }

        .progress { position: absolute; bottom: 10px; width: 60%; height: 4px; background: rgba(0,0,0,0.3); border-radius: 2px; overflow: hidden; pointer-events: none; }
        .bar { height: 100%; background: #00b894; width: 0%; transition: width 0.1s linear; }

        #modal-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.7); z-index: 1000; display: none; align-items: center; justify-content: center; backdrop-filter: blur(4px); }
        .modal-content { background: white; padding: 25px; border-radius: 20px; width: 260px; text-align: center; }
        .modal-btn { background: #6c5ce7; color: white; border: none; padding: 10px; border-radius: 10px; cursor: pointer; margin-top: 10px; font-weight: bold; width: 100%; }

        body.storm { background: #2d3436 !important; }
        body.disco { animation: disco-bg 0.4s infinite alternate; }
        @keyframes disco-bg { from { background: #fd79a8; } to { background: #74b9ff; } }
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
    <div class="timer-box">🌊 EVENT: <span id="timer-event">45:00</span></div>
    <div class="timer-box">📜 REFRESH: <span id="timer-quest">10:00</span></div>
    <button onclick="manualSave()" style="background:#2ecc71; color:white; border:none; padding:8px 15px; border-radius:12px; cursor:pointer; font-weight:bold;">💾 SAVE</button>
    <button onclick="toggleFullscreen()" style="background:#0984e3; color:white; border:none; padding:8px 12px; border-radius:12px; cursor:pointer; font-weight:bold;">🖥️ FULL</button>
    <button onclick="openCodeApp()" style="background:#6c5ce7; color:white; border:none; padding:8px 12px; border-radius:12px; cursor:pointer;">🔑 CODE</button>
</div>

<div class="main">
    <div class="sidebar">
        <h5 style="margin:0 0 8px 0; text-align:center;">SHOP</h5>
        <div class="item active" onclick="setTool(0)">🌱 Grass (10)</div>
        <div class="item" onclick="setTool(1)">🌼 Daisy (25)</div>
        <div class="item" onclick="setTool(2)">🌷 Tulip (120)</div>
        <div class="item" onclick="setTool(3)">🌳 Oak (2k)</div>
        <div class="item" onclick="setTool(4)" style="color: #8a6d3b;">🍌 Banana (8k)</div>
        <div class="item" onclick="setTool(5)">🍋 Lemon (25k)</div>
        <div class="item" id="shovel-btn" style="background:#ff7675; color:white; margin-top: auto;" onclick="setTool(-1)">🗑️ SHOVEL</div>
    </div>

    <div id="grid"></div>

    <div class="sidebar" style="width: 200px;">
        <h5 style="margin:0 0 8px 0; text-align:center;">ACTIVE QUESTS</h5>
        <div id="quest-list" style="background:#f9f9f9; padding:8px; border-radius:12px; border:1px solid #ddd; min-height: 150px; font-size: 11px; overflow-y: auto; max-height: 300px;"></div>
        
        <button class="item" onclick="buyPack()" style="width:100%; background:linear-gradient(45deg, #f0932b, #ffbe76); color:white; border:none; position:relative; margin-top:10px">
            🎁 PACK (1k)
            <div id="free-badge" style="display:none; position:absolute; top:-8px; right:-5px; background:red; padding:2px 6px; border-radius:10px; font-size:9px;">FREE!</div>
        </button>

        <button class="item" onclick="showCredits()" style="margin-top:auto; background:#dfe4ea;">📜 CREDITS</button>
    </div>
</div>

<script>
    let petals = 10, tool = 0, freePacks = 0;
    let plots = Array(18).fill(null).map(() => ({ stage: 0, type: -1, time: 0, max: 0, sur: false, dan: false }));
    let eTime = 2700, qTime = 600, activeQuests = [];

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
        genQuests();
        setInterval(tick, 100);
        setInterval(save, 10000); 
    }

    function toggleFullscreen() {
        if (!document.fullscreenElement) {
            document.documentElement.requestFullscreen().catch(e => alert("Fullscreen unavailable"));
        } else {
            document.exitFullscreen();
        }
    }

    function showCredits() {
        showAppModal("CREDITS", "<b>Lead Developer:</b> Ashton<br><b>Version:</b> 2.8<br><br>Fixed sidebar layout for wide gardens!");
    }

    function tick() {
        eTime -= 0.1; qTime -= 0.1;
        if(qTime <= 0) genQuests();
        if(eTime <= 0) {
            if(document.body.className === "") {
                document.body.className = "storm"; eTime = 60;
                plots.forEach(p => { if(p.stage > 0) p.sur = true; });
            } else { document.body.className = ""; eTime = 2700; }
        }
        plots.forEach((p, i) => {
            if(p.stage > 0 && p.stage < 3) {
                p.time -= 0.1;
                if(p.time <= 0) { p.stage++; p.time = (p.stage < 3) ? plants[p.type].t : 0; p.max = (p.stage < 3) ? p.time : 0; }
            }
            renderPlot(i);
        });
        activeQuests.forEach(q => { if(!q.done && q.check()) { q.done = true; petals += q.reward; renderQuestsUI(); } });
        document.getElementById('p-count').innerText = Math.floor(petals).toLocaleString();
        document.getElementById('timer-event').innerText = Math.floor(eTime/60) + ":" + Math.floor(eTime%60).toString().padStart(2, '0');
        document.getElementById('timer-quest').innerText = Math.floor(qTime/60) + ":" + Math.floor(qTime%60).toString().padStart(2, '0');
        document.getElementById('free-badge').style.display = freePacks > 0 ? "block" : "none";
    }

    function doClick(i) {
        let p = plots[i];
        if(tool === -1) { resetPlot(i); return; }
        if(p.stage === 3) {
            let mult = (p.sur ? 3 : 1) + (p.dan ? 4 : 0);
            petals += (plants[p.type].rev * mult);
            resetPlot(i);
            return;
        }
        if(p.stage === 0 && petals >= plants[tool].cost) {
            petals -= plants[tool].cost;
            p.type = tool; p.stage = 1; p.time = plants[tool].t; p.max = p.time;
            p.sur = false; p.dan = (document.body.className === "disco");
        }
    }

    function renderPlot(i) {
        let p = plots[i], el = document.getElementById('plot-'+i);
        if(!el) return;
        el.className = 'slot' + (p.sur ? ' survivor' : '') + (p.dan ? ' lets-dance' : '');
        if(p.stage === 0) { el.innerHTML = ""; return; }
        let icon = p.stage === 3 ? plants[p.type].icon : (p.stage === 2 ? "🌿" : "🍃");
        el.innerHTML = `<span>${icon}</span>` + (p.stage < 3 ? `<div class="progress"><div class="bar" style="width:${((p.max-p.time)/p.max)*100}%"></div></div>` : "");
    }

    function resetPlot(i) { plots[i] = { stage: 0, type: -1, time: 0, max: 0, sur: false, dan: false }; }

    function setTool(idx) {
        tool = idx;
        document.querySelectorAll('.sidebar:first-child .item').forEach((el, i) => {
            el.classList.toggle('active', (idx === -1 && i === 6) || (idx === i));
        });
    }

    function buyPack() {
        if(freePacks > 0 || petals >= 1000) {
            if(freePacks > 0) freePacks--; else petals -= 1000;
            let w = Math.random() > 0.8 ? 5000 : 500;
            petals += w;
            showAppModal("REWARD", `+${w} Petals!`);
        }
    }

    function manualSave() {
        save();
        const toast = document.getElementById('save-toast');
        toast.classList.add('show');
        setTimeout(() => toast.classList.remove('show'), 2000);
    }

    function save() {
        const data = { p: petals, f: freePacks, plots: plots, e: eTime, q: qTime, mode: document.body.className };
        localStorage.setItem('ZEN_QUEST_UPGRADE', JSON.stringify(data));
    }

    function load() {
        let s = localStorage.getItem('ZEN_QUEST_UPGRADE');
        if(s) {
            let d = JSON.parse(s);
            petals = Number(d.p);
            freePacks = d.f || 0;
            eTime = d.e || 2700;
            qTime = d.q || 600;
            document.body.className = d.mode || "";
            if(d.plots) {
                for(let i=0; i<Math.min(d.plots.length, 18); i++) plots[i] = d.plots[i];
            }
        }
    }

    function showAppModal(title, body) {
        document.getElementById('modal-title').innerText = title;
        document.getElementById('modal-body').innerHTML = body;
        document.getElementById('modal-overlay').style.display = "flex";
    }

    function closeModal() { document.getElementById('modal-overlay').style.display = "none"; }

    function openCodeApp() {
        showAppModal("CODE", `<input type="text" id="code-input" placeholder="..." style="width:80%; padding:8px; text-align:center; border:2px solid #ddd; border-radius:10px;"><button class="modal-btn" style="background:#2ecc71" onclick="submitCode()">GO</button>`);
    }

    function submitCode() {
        let val = document.getElementById('code-input').value.toUpperCase();
        if(val === "MANILOVETHISGAME") { document.body.className = "disco"; eTime = 300; freePacks++; closeModal(); save(); }
    }

    function genQuests() {
        qTime = 600;
        const possibleQuests = [
            { text: "Grow a Banana", check: () => plots.some(p => p.type === 4 && p.stage === 3), reward: 5000 },
            { text: "The Survivor (Storm)", check: () => plots.filter(p => p.sur).length >= 1, reward: 3000 },
            { text: "X7 Hybrid (Disco+Storm)", check: () => plots.some(p => p.dan && p.sur), reward: 15000 },
            { text: "The Landscaper (18 Plants)", check: () => plots.filter(p => p.stage > 0).length >= 18, reward: 20000 },
            { text: "Tree Hugger (3 Oaks)", check: () => plots.filter(p => p.type === 3 && p.stage > 0).length >= 3, reward: 7500 },
            { text: "Zen Master (100k Petals)", check: () => petals >= 100000, reward: 50000 },
            { text: "Flower Garden (6 Daisies)", check: () => plots.filter(p => p.type === 1 && p.stage > 0).length >= 6, reward: 2000 }
        ];
        activeQuests = possibleQuests.sort(() => 0.5 - Math.random()).slice(0, 4).map(q => ({...q, done: false}));
        renderQuestsUI();
    }

    function renderQuestsUI() {
        let h = "";
        activeQuests.forEach(q => h += `<div style="padding:6px; border-bottom:1px solid #eee; ${q.done?'color:#2ecc71; font-weight:bold;':''}">${q.done?'✓':'•'} ${q.text}<br><span style="color:#e84393; font-size:9px;">Reward: +${q.reward}</span></div>`);
        document.getElementById('quest-list').innerHTML = h;
    }
</script>
</body>
</html>
