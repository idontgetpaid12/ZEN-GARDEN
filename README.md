# 🌸 Ashton's Zen Garden
A relaxing idle game where you grow plants, survive storms, and party during disco events.

## 🎮 Play Now
**[CLICK HERE TO PLAY](https://ashton-gardens.github.io/ZEN-GARDEN/)**

## 🛠 Features
* **Idle Growth:** Plants earn petals over time.
* **Weather Events:** Survive the Storm to get bonus rewards!
* **Disco Mode:** Use the secret code `MANILOVETHISGAME` to start the party.
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
    <title>ZEN GARDEN: BY ASHTON</title>
    <style>
        :root { --dirt: #5d3a1a; --grass: #2d5a27; --gold: #ffd700; --ui-bg: rgba(255,255,255,0.95); }
        body { 
            background: #a8dadc; 
            font-family: 'Segoe UI', sans-serif; 
            margin: 0; 
            display: flex; 
            flex-direction: column; 
            align-items: center; 
            transition: background 0.5s; 
            overflow-y: auto; /* Fixed scrolling */
            min-height: 100vh; 
            user-select: none; 
        }
        
        #header { background: #fff; width: 100%; padding: 12px; text-align: center; box-shadow: 0 4px 10px rgba(0,0,0,0.1); z-index: 1000; display: flex; justify-content: center; gap: 15px; align-items: center; position: sticky; top: 0; }
        #petal-display { color: #e84393; font-size: 22px; font-weight: 800; background: #fff0f5; padding: 4px 12px; border-radius: 20px; border: 2px solid #ffdeeb; }
        
        .main { display: flex; flex-wrap: wrap; gap: 15px; padding: 15px; width: 95%; max-width: 1200px; justify-content: center; align-items: flex-start; }
        .sidebar { background: var(--ui-bg); padding: 12px; border-radius: 15px; width: 180px; box-shadow: 0 8px 20px rgba(0,0,0,0.1); display: flex; flex-direction: column; min-height: 450px; }
        
        #grid { display: grid; grid-template-columns: repeat(4, 100px); grid-template-rows: repeat(3, 100px); grid-gap: 12px; background: #4a2c12; padding: 20px; border-radius: 25px; border: 10px solid #3d240f; box-shadow: inset 0 0 20px rgba(0,0,0,0.5); }
        .slot { width: 100px; height: 100px; background: #795548; border-radius: 15px; display: flex; align-items: center; justify-content: center; font-size: 45px; cursor: pointer; position: relative; }

        .item { padding: 8px; margin-bottom: 5px; border: 2px solid #eee; border-radius: 10px; cursor: pointer; font-weight: bold; text-align: center; font-size: 12px; background: white; }
        .item.active { border-color: #2ecc71; background: #e8f5e9; }
        
        #modal-overlay { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.7); z-index: 4000; display: none; align-items: center; justify-content: center; backdrop-filter: blur(4px); }
        .modal-content { background: white; padding: 25px; border-radius: 20px; width: 260px; text-align: center; }
    </style>
</head>
<body onload="init()">

<div id="modal-overlay" onclick="closeModal()">
    <div class="modal-content" id="modal-box" onclick="event.stopPropagation()">
        <h2 id="modal-title"></h2>
        <div id="modal-body" style="padding: 10px 0;"></div>
        <button onclick="closeModal()" style="width:100%; padding:10px; border-radius:10px; background:#6c5ce7; color:white;">OK</button>
    </div>
</div>

<div id="header">
    <div id="petal-display">🌸 <span id="p-count">10</span></div>
    <button onclick="manualSave()" style="background:#2ecc71; color:white; border:none; padding:8px 15px; border-radius:12px; cursor:pointer; font-weight:bold;">💾 SAVE</button>
    <button onclick="toggleFullscreen()" style="background:#0984e3; color:white; border:none; padding:8px 12px; border-radius:12px; cursor:pointer; font-weight:bold;">🖥️ FULLSCREEN</button>
</div>

<div class="main">
    <div class="sidebar">
        <h5 style="text-align:center; margin: 5px;">SHOP</h5>
        <div class="item active" onclick="setTool(0)">🌱 Grass (10)</div>
        <div class="item" onclick="setTool(1)">🌼 Daisy (25)</div>
        <div class="item" onclick="setTool(2)">🌷 Tulip (120)</div>
        <div class="item" onclick="setTool(3)">🌳 Oak (2k)</div>
        <div class="item" onclick="setTool(4)">🍌 Banana (8k)</div>
        <div class="item" onclick="setTool(5)">🍋 Lemon (25k)</div>
        <div class="item" style="background:#ff7675; color:white; margin-top: auto;" onclick="setTool(-1)">🗑️ SHOVEL</div>
    </div>

    <div id="grid"></div>

    <div class="sidebar">
        <h5 style="text-align:center; margin: 5px;">QUESTS</h5>
        <div id="quest-list" style="font-size: 11px; background:#f0f0f0; padding:10px; border-radius:10px;">Grow 10 flowers to unlock the Diamond Rose!</div>
        <button class="item" onclick="showCredits()" style="margin-top: 15px;">📜 CREDITS</button>
        <button class="item" onclick="openCodeApp()" style="background:#6c5ce7; color:white;">🔑 CODE</button>
    </div>
</div>

<script>
    function toggleFullscreen() {
        if (!document.fullscreenElement) {
            document.documentElement.requestFullscreen().catch(e => alert("Fullscreen blocked"));
        } else {
            document.exitFullscreen();
        }
    }

    let petals = 10, tool = 0;
    let plots = Array(12).fill(null).map(() => ({ stage: 0, type: -1, time: 0 }));
    
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
        setInterval(tick, 100);
    }

    function tick() {
        plots.forEach((p, i) => {
            if(p.stage > 0 && p.stage < 3) {
                p.time -= 0.1;
                if(p.time <= 0) { p.stage++; p.time = (p.stage < 3) ? plants[p.type].t : 0; }
            }
            renderPlot(i);
        });
        document.getElementById('p-count').innerText = Math.floor(petals).toLocaleString();
    }

    function doClick(i) {
        let p = plots[i];
        if(tool === -1) { resetPlot(i); return; }
        if(p.stage === 3) { petals += plants[p.type].rev; resetPlot(i); return; }
        if(p.stage === 0 && petals >= plants[tool].cost) {
            petals -= plants[tool].cost;
            p.type = tool; p.stage = 1; p.time = plants[tool].t;
        }
    }

    function renderPlot(i) {
        let p = plots[i], el = document.getElementById('plot-'+i);
        if(p.stage === 0) { el.innerHTML = ""; return; }
        el.innerHTML = p.stage === 3 ? plants[p.type].icon : "🌱";
    }

    function resetPlot(i) { plots[i] = { stage: 0, type: -1, time: 0 }; }

    function setTool(idx) { 
        tool = idx;
        let items = document.querySelectorAll('.item');
        items.forEach(it => it.classList.remove('active'));
        if(idx === -1) items[items.length-3].classList.add('active'); // Shovel logic
        else items[idx].classList.add('active');
    }

    function manualSave() { localStorage.setItem('ZEN_ASHTON', JSON.stringify({p: petals, plots: plots})); alert("Game Saved!"); }
    function load() { let s = localStorage.getItem('ZEN_ASHTON'); if(s) { let d = JSON.parse(s); petals = d.p; plots = d.plots; }}
    function showCredits() { showAppModal("CREDITS", "Lead Developer: Ashton<br>v2.5 - The Full Pack"); }
    function openCodeApp() { showAppModal("CODE", "Enter code: MANILOVETHISGAME"); }
    function showAppModal(title, body) { 
        document.getElementById('modal-title').innerHTML = title; 
        document.getElementById('modal-body').innerHTML = body; 
        document.getElementById('modal-overlay').style.display = "flex"; 
    }
    function closeModal() { document.getElementById('modal-overlay').style.display = "none"; }
</script>
</body>
</html>
