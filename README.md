<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>The analog design octagon — field notes</title>
<style>
@import url('https://fonts.googleapis.com/css2?family=IBM+Plex+Mono:wght@400;500;600&family=IBM+Plex+Sans:wght@400;500;600&display=swap');

:root{
  --navy:#0b2036;
  --navy-2:#0e2740;
  --grid:rgba(127,219,255,0.08);
  --grid-strong:rgba(127,219,255,0.16);
  --paper-text:#dce9f2;
  --paper-text-dim:#8fa8bd;
  --cyan:#7fdbff;
  --cyan-dim:#3d7a92;
  --amber:#f2b544;
  --amber-dim:#7a5c22;
  --coral:#ff8a65;
  --coral-dim:#7a3a28;
  --card:#0f2c47;
  --card-border:#1f4b6e;
  --mono:'IBM Plex Mono', monospace;
  --sans:'IBM Plex Sans', sans-serif;
}
*{box-sizing:border-box;}
body{
  margin:0;
  background:
    linear-gradient(var(--grid) 1px, transparent 1px) 0 0 / 28px 28px,
    linear-gradient(90deg, var(--grid) 1px, transparent 1px) 0 0 / 28px 28px,
    linear-gradient(var(--grid-strong) 1px, transparent 1px) 0 0 / 140px 140px,
    linear-gradient(90deg, var(--grid-strong) 1px, transparent 1px) 0 0 / 140px 140px,
    var(--navy);
  color:var(--paper-text);
  font-family:var(--sans);
  line-height:1.6;
}
.sheet{max-width:980px;margin:0 auto;padding:48px 24px 80px;}
header.title-block{border-bottom:1px solid var(--card-border);padding-bottom:28px;margin-bottom:40px;}
.eyebrow{
  font-family:var(--mono);font-size:12px;letter-spacing:0.14em;text-transform:uppercase;
  color:var(--cyan);display:flex;align-items:center;gap:10px;margin-bottom:14px;
}
.eyebrow::before{content:"§";color:var(--amber);}
h1{font-family:var(--mono);font-weight:600;font-size:clamp(26px,4vw,38px);margin:0 0 10px;color:#fff;letter-spacing:-0.01em;}
.sub{color:var(--paper-text-dim);font-size:15px;max-width:640px;}
.tag-row{display:flex;gap:8px;margin-top:18px;flex-wrap:wrap;}
.tag{font-family:var(--mono);font-size:11px;border:1px solid var(--card-border);color:var(--paper-text-dim);padding:4px 10px;border-radius:2px;}

section{margin-bottom:56px;}
h2{
  font-family:var(--mono);font-size:14px;letter-spacing:0.06em;text-transform:uppercase;
  color:var(--cyan);border-bottom:1px dashed var(--card-border);padding-bottom:10px;margin-bottom:22px;
  display:flex;align-items:baseline;gap:10px;
}
h2 .num{color:var(--amber);}
p.lead{color:var(--paper-text-dim);font-size:14.5px;max-width:680px;margin-top:-8px;margin-bottom:20px;}

/* explorer layout */
.explorer-grid{display:grid;grid-template-columns:1.3fr 1fr;gap:24px;align-items:start;}
@media(max-width:780px){.explorer-grid{grid-template-columns:1fr;}}
.diagram-card,.panel-card{
  background:var(--card);border:1px solid var(--card-border);border-radius:6px;padding:18px;
}
svg{display:block;width:100%;height:auto;}
.node-box{fill:#0d2740;stroke:var(--cyan-dim);stroke-width:1.2;transition:fill .2s, stroke .2s;cursor:pointer;}
.node-box:hover{stroke:var(--cyan);}
.node.active .node-box{fill:var(--amber-dim);stroke:var(--amber);}
.node.lit-neighbor .node-box{stroke:var(--cyan);}
.node.lit-diag .node-box{stroke:var(--coral);}
.node-label{font-family:var(--mono);font-size:12.5px;fill:var(--paper-text);text-anchor:middle;dominant-baseline:central;pointer-events:none;}
.node.active .node-label{fill:#fff;}
.edge{stroke:var(--cyan-dim);stroke-width:1.2;opacity:0.55;transition:stroke .2s, opacity .2s, stroke-width .2s;}
.edge.diag{stroke:var(--coral-dim);stroke-dasharray:5 4;opacity:0.4;}
.edge.lit{stroke:var(--cyan);opacity:1;stroke-width:2;}
.edge.diag.lit{stroke:var(--coral);opacity:1;stroke-width:2;}
.legend-row{display:flex;gap:18px;margin-top:14px;flex-wrap:wrap;font-family:var(--mono);font-size:11px;color:var(--paper-text-dim);}
.legend-row span{display:inline-flex;align-items:center;gap:6px;}
.swatch{width:18px;height:2px;display:inline-block;}
.swatch.outer{background:var(--cyan);}
.swatch.diag{background:var(--coral);background-image:linear-gradient(90deg,var(--coral) 60%,transparent 40%);background-size:6px 2px;}

.panel-card h3{margin:0 0 6px;font-family:var(--mono);font-size:16px;color:#fff;}
.panel-hint{color:var(--paper-text-dim);font-size:13.5px;}
.panel-block{margin-top:16px;padding-top:14px;border-top:1px dashed var(--card-border);}
.panel-block .k{font-family:var(--mono);font-size:11px;text-transform:uppercase;letter-spacing:.08em;color:var(--cyan);margin-bottom:6px;}
.panel-block .k.diag{color:var(--coral);}
.panel-block ul{margin:0;padding-left:18px;font-size:13.5px;color:var(--paper-text-dim);}
.panel-block li{margin-bottom:8px;}
.panel-block li b{color:var(--paper-text);font-weight:500;}

/* walkthrough */
.step-wrap{display:grid;grid-template-columns:1.3fr 1fr;gap:24px;align-items:start;}
@media(max-width:780px){.step-wrap{grid-template-columns:1fr;}}
.step-text{background:var(--card);border:1px solid var(--card-border);border-radius:6px;padding:20px;}
.step-count{font-family:var(--mono);font-size:11px;color:var(--amber);margin-bottom:8px;letter-spacing:.08em;}
.step-title{font-family:var(--mono);font-size:16px;color:#fff;margin:0 0 10px;}
.step-body{font-size:14px;color:var(--paper-text-dim);}
.step-nav{display:flex;gap:10px;margin-top:18px;}
button.nav-btn{
  font-family:var(--mono);font-size:12px;background:transparent;color:var(--cyan);
  border:1px solid var(--cyan-dim);border-radius:3px;padding:8px 14px;cursor:pointer;letter-spacing:.05em;
}
button.nav-btn:hover{border-color:var(--cyan);}
button.nav-btn:disabled{opacity:.35;cursor:default;}
.dots{display:flex;gap:6px;margin-left:auto;align-items:center;}
.dot{width:7px;height:7px;border-radius:50%;background:var(--card-border);}
.dot.on{background:var(--amber);}

/* simulator */
.sim-card{background:var(--card);border:1px solid var(--card-border);border-radius:6px;padding:24px;}
.sim-formula{font-family:var(--mono);font-size:12px;color:var(--paper-text-dim);background:#081a2c;border:1px solid var(--card-border);border-radius:4px;padding:10px 14px;margin-bottom:22px;letter-spacing:.02em;}
.sim-formula b{color:var(--cyan);font-weight:500;}
input[type=range]{width:100%;accent-color:var(--amber);margin-bottom:6px;}
.sim-label-row{display:flex;justify-content:space-between;font-family:var(--mono);font-size:12px;color:var(--paper-text-dim);margin-bottom:22px;}
.meter{margin-bottom:16px;}
.meter-top{display:flex;justify-content:space-between;font-size:13px;margin-bottom:6px;}
.meter-top .name{color:var(--paper-text);}
.meter-top .val{font-family:var(--mono);color:var(--cyan);}
.meter-track{height:8px;background:#081a2c;border:1px solid var(--card-border);border-radius:4px;overflow:hidden;}
.meter-fill{height:100%;border-radius:4px 0 0 4px;transition:width .12s linear;}
.fill-teal{background:linear-gradient(90deg,var(--cyan-dim),var(--cyan));}
.fill-amber{background:linear-gradient(90deg,var(--amber-dim),var(--amber));}
.fill-coral{background:linear-gradient(90deg,var(--coral-dim),var(--coral));}
.sim-caption{margin-top:18px;font-size:13.5px;color:var(--paper-text-dim);border-top:1px dashed var(--card-border);padding-top:14px;}
.sim-caption b{color:var(--paper-text);}

/* reference accordion */
.ref-group-title{font-family:var(--mono);font-size:12px;color:var(--amber);text-transform:uppercase;letter-spacing:.08em;margin:22px 0 10px;}
details{
  background:var(--card);border:1px solid var(--card-border);border-radius:5px;margin-bottom:8px;overflow:hidden;
}
summary{
  cursor:pointer;padding:12px 16px;font-family:var(--mono);font-size:13.5px;color:var(--paper-text);
  display:flex;align-items:center;gap:10px;list-style:none;
}
summary::-webkit-details-marker{display:none;}
summary::before{content:"+";color:var(--cyan);width:14px;flex:none;}
details[open] summary::before{content:"–";color:var(--amber);}
details p{margin:0;padding:0 16px 16px 40px;font-size:13.5px;color:var(--paper-text-dim);}

footer{color:var(--paper-text-dim);font-size:12px;font-family:var(--mono);border-top:1px solid var(--card-border);padding-top:18px;}
</style>
</head>
<body>
<div class="sheet">

  <header class="title-block">
    <div class="eyebrow">field notes / analog IC design</div>
    <h1>The octagon of trade-offs</h1>
    <p class="sub">Eight performance specs — Gain, Supply voltage, Voltage swings, Speed, I/O impedance, Power dissipation, Noise, Linearity — locked in a ring of mutual trade-offs. Click through the diagram below to internalize which corners fight which, and why.</p>
    <div class="tag-row">
      <span class="tag">gm ∝ √I</span><span class="tag">noise ∝ 1/gm</span><span class="tag">P = V·I</span><span class="tag">swing ≤ Vdd</span>
    </div>
  </header>

  <section id="explorer">
    <h2><span class="num">01</span> the octagon — click any corner</h2>
    <p class="lead">Solid cyan lines are <b>direct</b> trade-offs between neighbors. Dashed coral lines are <b>indirect</b> trade-offs between opposite corners, usually mediated through bias current.</p>
    <div class="explorer-grid">
      <div class="diagram-card">
        <svg id="svg-explorer" viewBox="0 0 640 640" role="img" aria-label="Interactive octagon of analog design trade-offs"></svg>
        <div class="legend-row">
          <span><span class="swatch outer"></span> direct (neighbor)</span>
          <span><span class="swatch diag"></span> indirect (opposite)</span>
        </div>
      </div>
      <div class="panel-card" id="panel">
        <!-- filled by JS -->
      </div>
    </div>
  </section>

  <section id="walkthrough">
    <h2><span class="num">02</span> worked example — a low-noise, high-speed front end</h2>
    <p class="lead">Watch one design requirement ripple around the entire octagon. Step through it.</p>
    <div class="step-wrap">
      <div class="diagram-card">
        <svg id="svg-walk" viewBox="0 0 640 640" role="img" aria-label="Octagon showing the worked example ripple"></svg>
      </div>
      <div class="step-text">
        <div class="step-count" id="step-count">STEP 1 / 5</div>
        <h3 class="step-title" id="step-title"></h3>
        <div class="step-body" id="step-body"></div>
        <div class="step-nav">
          <button class="nav-btn" id="btn-prev">← prev</button>
          <button class="nav-btn" id="btn-next">next →</button>
          <div class="dots" id="dots"></div>
        </div>
      </div>
    </div>
  </section>

  <section id="simulator">
    <h2><span class="num">03</span> the bias-current lever — try it</h2>
    <p class="lead">Bias current is the one knob that touches almost every corner at once. Drag it and watch four specs move together (illustrative model, not exact device physics).</p>
    <div class="sim-card">
      <div class="sim-formula"><b>gm</b> ∝ √I_bias &nbsp;·&nbsp; <b>noise</b> ∝ 1/gm &nbsp;·&nbsp; <b>speed</b> ∝ gm &nbsp;·&nbsp; <b>power</b> = Vdd · I_bias</div>
      <input type="range" id="current-slider" min="1" max="100" value="35">
      <div class="sim-label-row"><span>low bias current</span><span>high bias current</span></div>

      <div class="meter">
        <div class="meter-top"><span class="name">Noise (lower is better)</span><span class="val" id="v-noise"></span></div>
        <div class="meter-track"><div class="meter-fill fill-coral" id="m-noise"></div></div>
      </div>
      <div class="meter">
        <div class="meter-top"><span class="name">Speed / bandwidth</span><span class="val" id="v-speed"></span></div>
        <div class="meter-track"><div class="meter-fill fill-teal" id="m-speed"></div></div>
      </div>
      <div class="meter">
        <div class="meter-top"><span class="name">Linearity</span><span class="val" id="v-lin"></span></div>
        <div class="meter-track"><div class="meter-fill fill-teal" id="m-lin"></div></div>
      </div>
      <div class="meter">
        <div class="meter-top"><span class="name">Power dissipation</span><span class="val" id="v-power"></span></div>
        <div class="meter-track"><div class="meter-fill fill-amber" id="m-power"></div></div>
      </div>
      <div class="sim-caption" id="sim-caption"></div>
    </div>
  </section>

  <section id="reference">
    <h2><span class="num">04</span> full relationship reference</h2>
    <p class="lead">All twelve links, for review. Click to expand.</p>

    <div class="ref-group-title">Direct — adjacent corners (8)</div>
    <details><summary>Gain ↔ Supply voltage</summary><p>Higher gain usually needs cascoding or extra stages, and each stacked device eats voltage headroom — pushing up the minimum supply. Example: a cascode load boosts gain but needs its own VDS.</p></details>
    <details><summary>Supply voltage ↔ Voltage swings</summary><p>Output swing is capped by the rail: swing ≤ Vdd − headroom. More supply directly buys more swing.</p></details>
    <details><summary>Voltage swings ↔ Speed</summary><p>Large swings into a fixed load capacitance become slew-rate limited (SR = I/C) — chasing swing without more current costs speed.</p></details>
    <details><summary>Speed ↔ Input/output impedance</summary><p>Bandwidth is set by RC time constants; low output impedance for driving loads, or the parasitics tied to high input impedance, both fight speed.</p></details>
    <details><summary>I/O impedance ↔ Power dissipation</summary><p>Achieving low output impedance to drive a load needs more standing current — impedance choices trade directly with power.</p></details>
    <details><summary>Power dissipation ↔ Noise</summary><p>Input-referred noise scales roughly as 1/gm, and gm costs current. Lower noise always costs more power.</p></details>
    <details><summary>Noise ↔ Linearity</summary><p>More overdrive voltage improves linearity but reduces gm-per-mA efficiency, quietly raising relative noise.</p></details>
    <details><summary>Linearity ↔ Gain</summary><p>Loop gain with feedback is what actually linearizes a circuit, but squeezing more raw gain often biases devices near their limits, hurting intrinsic linearity before feedback helps.</p></details>

    <div class="ref-group-title">Indirect — opposite corners (4)</div>
    <details><summary>Gain ↔ I/O impedance</summary><p>A high-gain stage is, by construction, a high-output-impedance node — wanting both at once is nearly contradictory, which is why op-amps split into a gain stage plus a buffer.</p></details>
    <details><summary>Supply voltage ↔ Power dissipation</summary><p>P = V·I — raising the rail to buy swing or gain directly taxes power at the same bias current.</p></details>
    <details><summary>Voltage swings ↔ Noise</summary><p>Dynamic range = swing ÷ noise floor. Chasing swing alone doesn't buy dynamic range without also fighting noise.</p></details>
    <details><summary>Speed ↔ Linearity</summary><p>Pushing bandwidth drives devices toward velocity saturation or crossover regions — exactly where distortion spikes.</p></details>
  </section>

  <footer>
    field notes · analog IC design octagon · illustrative models, not exact device equations
  </footer>

</div>

<script>
const NS = "http://www.w3.org/2000/svg";

const nodes = [
  {id:"gain",     label:"Gain",              cx:180, cy:50,  w:100, h:46},
  {id:"supply",   label:"Supply voltage",    cx:460, cy:50,  w:150, h:46},
  {id:"swings",   label:"Voltage swings",    cx:555, cy:230, w:120, h:46},
  {id:"speed",    label:"Speed",             cx:555, cy:410, w:90,  h:46},
  {id:"io",       label:"I/O impedance",     cx:460, cy:590, w:140, h:46},
  {id:"power",    label:"Power dissipation", cx:180, cy:590, w:150, h:46},
  {id:"noise",    label:"Noise",             cx:90,  cy:410, w:90,  h:46},
  {id:"lin",      label:"Linearity",         cx:85,  cy:230, w:110, h:46},
];
const order = ["gain","supply","swings","speed","io","power","noise","lin"];
const idx = id => order.indexOf(id);
const outerPairs = order.map((id,i)=>[id, order[(i+1)%8]]);
const diagPairs = [["gain","io"],["supply","power"],["swings","noise"],["speed","lin"]];

const direct = {
  "gain-supply":["Gain","Supply voltage","Higher gain often means cascoding or extra stages — each stacked device eats voltage headroom, pushing up the minimum supply."],
  "supply-swings":["Supply voltage","Voltage swings","Output swing is capped by the rail (swing ≤ Vdd − headroom). More supply directly buys more swing."],
  "swings-speed":["Voltage swings","Speed","Large swings into a fixed capacitance become slew-rate limited (SR = I/C) — swing without more current costs speed."],
  "speed-io":["Speed","I/O impedance","Bandwidth is set by RC time constants; low Zout for driving loads, or parasitics from high Zin, both fight speed."],
  "io-power":["I/O impedance","Power dissipation","Low output impedance to drive a load needs more standing current — a direct tax on power."],
  "power-noise":["Power dissipation","Noise","Input-referred noise scales roughly as 1/gm, and gm costs current — lower noise always costs more power."],
  "noise-lin":["Noise","Linearity","More overdrive improves linearity but lowers gm-per-mA efficiency, quietly raising relative noise."],
  "lin-gain":["Linearity","Gain","Loop gain plus feedback is what linearizes a circuit, but chasing raw gain biases devices near their limits, hurting intrinsic linearity."],
};
const diag = {
  "gain-io":"A high-gain stage is, by construction, high-impedance — wanting high gain and low Zout on one node is nearly contradictory.",
  "supply-power":"P = V·I — raising the rail to buy swing or gain directly taxes power at the same bias current.",
  "swings-noise":"Dynamic range = swing ÷ noise floor. Chasing swing alone doesn't buy dynamic range without also fighting noise.",
  "speed-lin":"Pushing bandwidth drives devices toward velocity saturation or crossover regions — exactly where distortion spikes.",
};

function edgeKey(a,b){
  for(const k in direct){ const [n1,n2]=[k.split("-")[0],k.split("-")[1]]; if((n1===a&&n2===b)||(n1===b&&n2===a)) return k; }
  return null;
}
function diagKey(a,b){
  for(const k in diag){ const [n1,n2]=[k.split("-")[0],k.split("-")[1]]; if((n1===a&&n2===b)||(n1===b&&n2===a)) return k; }
  return null;
}

function buildSvg(svgEl, interactive){
  outerPairs.forEach(([a,b])=>{
    const na=nodes.find(n=>n.id===a), nb=nodes.find(n=>n.id===b);
    const line=document.createElementNS(NS,"line");
    line.setAttribute("x1",na.cx);line.setAttribute("y1",na.cy);
    line.setAttribute("x2",nb.cx);line.setAttribute("y2",nb.cy);
    line.setAttribute("class","edge outer");
    line.dataset.a=a; line.dataset.b=b;
    svgEl.appendChild(line);
  });
  diagPairs.forEach(([a,b])=>{
    const na=nodes.find(n=>n.id===a), nb=nodes.find(n=>n.id===b);
    const line=document.createElementNS(NS,"line");
    line.setAttribute("x1",na.cx);line.setAttribute("y1",na.cy);
    line.setAttribute("x2",nb.cx);line.setAttribute("y2",nb.cy);
    line.setAttribute("class","edge diag");
    line.dataset.a=a; line.dataset.b=b;
    svgEl.appendChild(line);
  });
  nodes.forEach(n=>{
    const g=document.createElementNS(NS,"g");
    g.setAttribute("class","node");
    g.dataset.id=n.id;
    if(interactive){ g.style.cursor="pointer"; g.addEventListener("click",()=>selectNode(n.id)); g.setAttribute("tabindex","0"); }
    const rect=document.createElementNS(NS,"rect");
    rect.setAttribute("x",n.cx-n.w/2); rect.setAttribute("y",n.cy-n.h/2);
    rect.setAttribute("width",n.w); rect.setAttribute("height",n.h);
    rect.setAttribute("rx",6); rect.setAttribute("class","node-box");
    const text=document.createElementNS(NS,"text");
    text.setAttribute("x",n.cx); text.setAttribute("y",n.cy);
    text.setAttribute("class","node-label");
    text.textContent=n.label;
    g.appendChild(rect); g.appendChild(text);
    svgEl.appendChild(g);
  });
}

const svgExplorer=document.getElementById("svg-explorer");
const svgWalk=document.getElementById("svg-walk");
buildSvg(svgExplorer,true);
buildSvg(svgWalk,false);

function clearHighlights(svgEl){
  svgEl.querySelectorAll(".edge").forEach(e=>e.classList.remove("lit"));
  svgEl.querySelectorAll(".node").forEach(n=>n.classList.remove("active","lit-neighbor","lit-diag"));
}

function selectNode(id){
  clearHighlights(svgExplorer);
  const n = nodes.find(x=>x.id===id);
  const i = idx(id);
  const prev = order[(i+7)%8], next = order[(i+1)%8];
  const oppo = order[(i+4)%8];

  svgExplorer.querySelector('.node[data-id="'+id+'"]').classList.add("active");
  [prev,next].forEach(nb=>{
    svgExplorer.querySelector('.node[data-id="'+nb+'"]').classList.add("lit-neighbor");
  });
  svgExplorer.querySelector('.node[data-id="'+oppo+'"]').classList.add("lit-diag");

  svgExplorer.querySelectorAll(".edge.outer").forEach(e=>{
    if((e.dataset.a===id||e.dataset.b===id)) e.classList.add("lit");
  });
  svgExplorer.querySelectorAll(".edge.diag").forEach(e=>{
    if((e.dataset.a===id||e.dataset.b===id)) e.classList.add("lit");
  });

  const label = n2=>nodes.find(x=>x.id===n2).label;
  const kPrev = edgeKey(id,prev), kNext = edgeKey(id,next), kDiag = diagKey(id,oppo);
  const dPrev = direct[kPrev], dNext = direct[kNext];

  document.getElementById("panel").innerHTML = `
    <h3>${n.label}</h3>
    <p class="panel-hint">Click another corner to trace its links.</p>
    <div class="panel-block">
      <div class="k">Direct neighbors</div>
      <ul>
        <li><b>${label(prev)}</b> — ${dPrev[2]}</li>
        <li><b>${label(next)}</b> — ${dNext[2]}</li>
      </ul>
    </div>
    <div class="panel-block">
      <div class="k diag">Opposite corner</div>
      <ul><li><b>${label(oppo)}</b> — ${diag[kDiag]}</li></ul>
    </div>
  `;
}
selectNode("gain");

// ---- walkthrough ----
const steps = [
  {title:"Start: the noise spec", nodes:["noise","power"], edges:[["power","noise"]],
   body:"You need low noise for a photodiode front end. Input-referred noise scales as 1/gm, so hitting the noise target forces gm up, which forces bias current — and power — up. <b>Noise → Power</b> (direct link)."},
  {title:"Current helps speed too", nodes:["power","speed"], edges:[["speed","io"]],
   body:"Handily, the extra current also raises gm, which helps bandwidth — a rare case where two specs move the same direction. But wider, faster devices bring more input capacitance."},
  {title:"Impedance pays the price", nodes:["speed","io"], edges:[["speed","io"]],
   body:"That extra input capacitance hurts the impedance match to the photodiode. <b>Speed ↔ I/O impedance</b> (direct link) — the bandwidth win isn't free."},
  {title:"Swing needs more rail", nodes:["swings","supply","power"], edges:[["supply","swings"],["supply","power"]],
   body:"For good SNR you also want more output swing, so you raise the supply voltage — which raises power again through the <b>Supply ↔ Power</b> diagonal."},
  {title:"Gain still needs a buffer", nodes:["gain","io"], edges:[],
   body:"Your gain stage's cascode load is a high-impedance node by construction (<b>Gain ↔ I/O impedance</b> diagonal), so it still needs a buffer — more current again. One spec rippled through five corners."},
];
let stepI=0;
function renderStep(){
  clearHighlights(svgWalk);
  const s=steps[stepI];
  s.nodes.forEach(id=>svgWalk.querySelector('.node[data-id="'+id+'"]').classList.add("active"));
  s.edges.forEach(([a,b])=>{
    svgWalk.querySelectorAll(".edge").forEach(e=>{
      if((e.dataset.a===a&&e.dataset.b===b)||(e.dataset.a===b&&e.dataset.b===a)) e.classList.add("lit");
    });
  });
  document.getElementById("step-count").textContent = `STEP ${stepI+1} / ${steps.length}`;
  document.getElementById("step-title").textContent = s.title;
  document.getElementById("step-body").innerHTML = s.body;
  document.getElementById("btn-prev").disabled = stepI===0;
  document.getElementById("btn-next").disabled = stepI===steps.length-1;
  document.getElementById("dots").innerHTML = steps.map((_,i)=>`<div class="dot ${i===stepI?'on':''}"></div>`).join("");
}
document.getElementById("btn-prev").addEventListener("click",()=>{ if(stepI>0){stepI--;renderStep();} });
document.getElementById("btn-next").addEventListener("click",()=>{ if(stepI<steps.length-1){stepI++;renderStep();} });
renderStep();

// ---- simulator ----
const slider=document.getElementById("current-slider");
function updateSim(){
  const I=Number(slider.value);
  const gm=Math.sqrt(I);
  const noise=Math.max(4, 100 - gm*8.2);
  const speed=Math.min(100, gm*8.2);
  const lin=Math.max(10, Math.min(100, 40 + I*0.9 - I*I*0.005));
  const power=I;

  document.getElementById("m-noise").style.width=noise+"%";
  document.getElementById("m-speed").style.width=speed+"%";
  document.getElementById("m-lin").style.width=lin+"%";
  document.getElementById("m-power").style.width=power+"%";
  document.getElementById("v-noise").textContent=Math.round(noise);
  document.getElementById("v-speed").textContent=Math.round(speed);
  document.getElementById("v-lin").textContent=Math.round(lin);
  document.getElementById("v-power").textContent=Math.round(power);

  const cap = I<30
    ? "Low bias: quiet on power, but noisy and slow. Fine for a low-speed, battery-constrained stage."
    : I>70
    ? "High bias: fast and quiet, but power-hungry. Typical of a high-performance front end that can afford the current."
    : "Mid bias: a balanced compromise — the default starting point before you optimize toward a spec.";
  document.getElementById("sim-caption").innerHTML = `<b>At I = ${I}%:</b> ${cap}`;
}
slider.addEventListener("input",updateSim);
updateSim();
</script>
</body>
</html>
