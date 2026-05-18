# Kamala-p3

<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>KAMALA QUANT — A/D Engine</title>
<script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js"></script>
<style>
@import url('https://fonts.googleapis.com/css2?family=Space+Mono:wght@400;700&family=Syne:wght@400;700;800&display=swap');

:root {
  --bg:       #06090f;
  --bg2:      #0b1120;
  --bg3:      #101825;
  --card:     #111d2e;
  --border:   #1e3048;
  --border2:  #162540;
  --acc:      #00e5ff;
  --acc2:     #0099bb;
  --dist:     #ff4060;
  --dist2:    #bb1030;
  --manip:    #ffb800;
  --manip2:   #cc8800;
  --neutral:  #8899aa;
  --green:    #00e676;
  --green2:   #00994d;
  --red:      #ff3d5a;
  --gold:     #f0c040;
  --text:     #ccdde8;
  --text2:    #6688aa;
  --text3:    #334455;
  --font:     'Space Mono', monospace;
  --disp:     'Syne', sans-serif;
  --radius:   12px;
}

* { margin:0; padding:0; box-sizing:border-box; -webkit-tap-highlight-color:transparent; }

html { scroll-behavior: smooth; }

body {
  background: var(--bg);
  color: var(--text);
  font-family: var(--font);
  font-size: 12px;
  min-height: 100vh;
  overflow-x: hidden;
  background-image:
    radial-gradient(ellipse 60% 40% at 15% 10%, rgba(0,150,200,0.06) 0%, transparent 70%),
    radial-gradient(ellipse 50% 50% at 85% 90%, rgba(0,80,140,0.05) 0%, transparent 70%);
}

/* ── LOADING ── */
#loading {
  position: fixed; inset: 0; z-index: 9999;
  background: var(--bg);
  display: flex; flex-direction: column;
  align-items: center; justify-content: center;
  gap: 16px; transition: opacity 0.5s;
}
#loading.gone { opacity: 0; pointer-events: none; }
.load-ring {
  width: 56px; height: 56px; border-radius: 50%;
  border: 3px solid var(--border);
  border-top-color: var(--acc);
  animation: spin 1s linear infinite;
}
@keyframes spin { to { transform: rotate(360deg); } }
.load-brand { font-family: var(--disp); font-size: 20px; font-weight: 800; color: var(--acc); letter-spacing: 3px; }
.load-sub { font-size: 10px; color: var(--text2); letter-spacing: 2px; }

/* ── HEADER ── */
.header {
  position: sticky; top: 0; z-index: 100;
  background: rgba(6,9,15,0.96);
  backdrop-filter: blur(16px);
  border-bottom: 1px solid var(--border);
  padding: 10px 14px;
  display: flex; align-items: center; justify-content: space-between;
}
.header-brand { display: flex; align-items: center; gap: 8px; }
.brand-dot {
  width: 28px; height: 28px; border-radius: 8px;
  background: linear-gradient(135deg, var(--acc), var(--green));
  display: flex; align-items: center; justify-content: center;
  font-size: 14px; animation: pulse-dot 3s ease-in-out infinite;
}
@keyframes pulse-dot {
  0%,100% { box-shadow: 0 0 0 0 rgba(0,229,255,0.3); }
  50% { box-shadow: 0 0 0 8px rgba(0,229,255,0); }
}
.brand-name { font-family: var(--disp); font-size: 14px; font-weight: 800; color: var(--acc); letter-spacing: 2px; }
.brand-sub  { font-size: 8px; color: var(--text2); letter-spacing: 1px; margin-top: 1px; }
.conn-pill {
  display: flex; align-items: center; gap: 5px;
  padding: 4px 10px; border-radius: 20px;
  border: 1px solid var(--border); font-size: 9px; color: var(--text2);
  background: var(--bg2); letter-spacing: 1px;
}
.conn-pill.live { border-color: var(--green); color: var(--green); background: rgba(0,230,118,0.06); }
.conn-pill.err  { border-color: var(--red);   color: var(--red);   background: rgba(255,61,90,0.06);  }
.cdot { width: 5px; height: 5px; border-radius: 50%; background: currentColor; }
.conn-pill.live .cdot { animation: blink 1s infinite; }
@keyframes blink { 0%,100%{opacity:1} 50%{opacity:.2} }

/* ── CONTROLS ── */
.controls {
  display: flex; gap: 8px; padding: 10px 14px;
  overflow-x: auto; scrollbar-width: none;
  background: var(--bg2); border-bottom: 1px solid var(--border2);
}
.controls::-webkit-scrollbar { display: none; }
.ctrl-group { display: flex; gap: 4px; flex-shrink: 0; }
.ctrl-btn {
  padding: 6px 12px; border-radius: 6px;
  border: 1px solid var(--border); background: var(--bg3);
  color: var(--text2); font-family: var(--font); font-size: 10px;
  cursor: pointer; transition: all .18s; letter-spacing: .5px;
  white-space: nowrap;
}
.ctrl-btn.active { border-color: var(--acc); background: rgba(0,229,255,0.1); color: var(--acc); }
.ctrl-btn:active { transform: scale(.95); }
.ctrl-divider { width: 1px; background: var(--border); flex-shrink: 0; }
.refresh-btn {
  display: flex; align-items: center; gap: 6px;
  padding: 6px 12px; border-radius: 6px;
  border: 1px solid var(--acc2); background: rgba(0,229,255,0.08);
  color: var(--acc); font-family: var(--font); font-size: 10px;
  cursor: pointer; transition: all .18s; letter-spacing: 1px; flex-shrink: 0;
}
.refresh-btn:active { background: rgba(0,229,255,0.2); transform: scale(.95); }
.spin { animation: spin .8s linear infinite; }

/* ── PHASE BANNER ── */
.phase-banner {
  margin: 10px 14px 0;
  border-radius: var(--radius);
  padding: 14px 16px;
  border: 1px solid var(--border);
  background: var(--card);
  display: flex; align-items: center; justify-content: space-between;
  gap: 12px; transition: border-color .4s, background .4s;
}
.phase-banner.acc  { border-color: var(--acc);   background: rgba(0,229,255,0.05); }
.phase-banner.dist { border-color: var(--dist);  background: rgba(255,64,96,0.06); }
.phase-banner.manip{ border-color: var(--manip); background: rgba(255,184,0,0.05); }
.phase-left { display: flex; align-items: center; gap: 12px; }
.phase-icon { font-size: 28px; line-height: 1; }
.phase-label { font-family: var(--disp); font-size: 18px; font-weight: 800; letter-spacing: 1px; }
.phase-label.acc  { color: var(--acc);   }
.phase-label.dist { color: var(--dist);  }
.phase-label.manip{ color: var(--manip); }
.phase-label.neut { color: var(--neutral); }
.phase-sub { font-size: 9px; color: var(--text2); letter-spacing: 1px; margin-top: 3px; }
.phase-conf {
  text-align: right; flex-shrink: 0;
}
.conf-pct { font-family: var(--disp); font-size: 26px; font-weight: 800; color: var(--gold); }
.conf-lbl { font-size: 8px; color: var(--text2); letter-spacing: 1px; margin-top: 2px; }

/* ── SIGNAL GRID ── */
.signal-grid {
  display: grid; grid-template-columns: 1fr 1fr;
  gap: 8px; padding: 10px 14px;
}
.sig-card {
  background: var(--card); border: 1px solid var(--border);
  border-radius: 10px; padding: 10px 12px;
  position: relative; overflow: hidden;
}
.sig-card::before {
  content: ''; position: absolute; top: 0; left: 0; right: 0; height: 2px;
}
.sig-card.acc::before  { background: linear-gradient(90deg, var(--acc), transparent); }
.sig-card.dist::before { background: linear-gradient(90deg, var(--dist), transparent); }
.sig-card.warn::before { background: linear-gradient(90deg, var(--manip), transparent); }
.sig-card.neut::before { background: linear-gradient(90deg, var(--neutral), transparent); }
.sig-name { font-size: 8px; color: var(--text2); letter-spacing: 1.5px; margin-bottom: 4px; text-transform: uppercase; }
.sig-val  { font-family: var(--disp); font-size: 14px; font-weight: 700; }
.sig-val.green { color: var(--green);   }
.sig-val.red   { color: var(--red);     }
.sig-val.gold  { color: var(--gold);    }
.sig-val.cyan  { color: var(--acc);     }
.sig-val.white { color: var(--text);    }
.sig-val.neut  { color: var(--neutral); }
.sig-sub { font-size: 9px; color: var(--text2); margin-top: 3px; }

/* ── SECTION HEADER ── */
.sec-hdr {
  padding: 10px 14px 6px;
  display: flex; align-items: center; gap: 8px;
  font-size: 9px; color: var(--text2); letter-spacing: 2px; text-transform: uppercase;
}
.sec-hdr::before { content:''; width:3px; height:12px; border-radius:2px; background:linear-gradient(var(--acc),var(--green)); flex-shrink:0; }
.sec-hdr .hi { color: var(--acc); }

/* ── CHART WRAPPER ── */
.chart-wrap {
  margin: 0 14px 10px;
  background: var(--card); border: 1px solid var(--border);
  border-radius: var(--radius); padding: 14px 12px;
}
.chart-title { font-size: 10px; color: var(--text2); letter-spacing: 1px; margin-bottom: 10px; display: flex; align-items: center; justify-content: space-between; }
.chart-legend { display: flex; gap: 10px; }
.leg-item { display: flex; align-items: center; gap: 4px; font-size: 8px; color: var(--text2); }
.leg-dot  { width: 8px; height: 8px; border-radius: 50%; }
canvas { border-radius: 8px; }

/* ── WYCKOFF PHASES ── */
.wyckoff-panel {
  margin: 0 14px 10px;
  background: var(--card); border: 1px solid var(--border);
  border-radius: var(--radius); padding: 14px;
}
.wyckoff-title { font-size: 10px; color: var(--text2); letter-spacing: 1px; margin-bottom: 12px; }
.wyckoff-phases { display: flex; gap: 4px; }
.wy-phase {
  flex: 1; text-align: center; padding: 8px 4px;
  border-radius: 8px; border: 1px solid var(--border);
  background: var(--bg3); transition: all .3s;
  cursor: default;
}
.wy-phase.active-acc  { border-color: var(--acc);   background: rgba(0,229,255,0.1);  }
.wy-phase.active-dist { border-color: var(--dist);  background: rgba(255,64,96,0.1);  }
.wy-phase.active-manip{ border-color: var(--manip); background: rgba(255,184,0,0.1);  }
.wy-phase-name { font-size: 8px; color: var(--text2); letter-spacing: .5px; }
.wy-phase.active-acc  .wy-phase-name { color: var(--acc);   }
.wy-phase.active-dist .wy-phase-name { color: var(--dist);  }
.wy-phase.active-manip .wy-phase-name{ color: var(--manip); }
.wy-phase-letter { font-family: var(--disp); font-size: 16px; font-weight: 800; color: var(--text3); margin-bottom: 2px; }
.wy-phase.active-acc  .wy-phase-letter { color: var(--acc);   }
.wy-phase.active-dist .wy-phase-letter { color: var(--dist);  }
.wy-phase.active-manip .wy-phase-letter{ color: var(--manip); }

/* ── VSA SIGNALS ── */
.vsa-panel {
  margin: 0 14px 10px;
  background: var(--card); border: 1px solid var(--border);
  border-radius: var(--radius); overflow: hidden;
}
.vsa-hdr {
  padding: 10px 14px; background: var(--bg3);
  display: grid; grid-template-columns: 1fr 1fr 1fr 1fr;
  font-size: 8px; color: var(--text2); letter-spacing: 1px;
  border-bottom: 1px solid var(--border);
}
.vsa-row {
  display: grid; grid-template-columns: 1fr 1fr 1fr 1fr;
  padding: 8px 14px; border-bottom: 1px solid rgba(30,48,72,0.5);
  align-items: center; font-size: 10px;
  animation: row-in .3s ease;
}
@keyframes row-in { from{opacity:0;transform:translateY(-4px)} to{opacity:1;transform:none} }
.vsa-row:last-child { border-bottom: none; }
.vsa-row.acc-row  { border-left: 2px solid var(--acc);  }
.vsa-row.dist-row { border-left: 2px solid var(--dist); }
.vsa-row.manip-row{ border-left: 2px solid var(--manip);}
.vsa-signal { font-size: 9px; font-weight: bold; }
.vsa-signal.acc   { color: var(--acc);   }
.vsa-signal.dist  { color: var(--dist);  }
.vsa-signal.manip { color: var(--manip); }
.vsa-price { color: var(--text); }
.vsa-spread { color: var(--text2); font-size: 9px; }
.vsa-time  { color: var(--text3); font-size: 9px; text-align: right; }

/* ── SCORE METERS ── */
.score-grid {
  display: grid; grid-template-columns: 1fr 1fr;
  gap: 8px; margin: 0 14px 10px;
}
.score-card {
  background: var(--card); border: 1px solid var(--border);
  border-radius: 10px; padding: 12px;
}
.score-label { font-size: 8px; color: var(--text2); letter-spacing: 1.5px; margin-bottom: 8px; }
.score-num   { font-family: var(--disp); font-size: 22px; font-weight: 800; margin-bottom: 6px; }
.score-track { height: 5px; background: var(--bg3); border-radius: 3px; overflow: hidden; }
.score-fill  { height: 100%; border-radius: 3px; transition: width .6s cubic-bezier(.34,1.56,.64,1); }

/* ── DIVERGENCE ── */
.div-card {
  margin: 0 14px 10px;
  background: var(--card); border: 1px solid var(--border);
  border-radius: var(--radius); padding: 14px;
}
.div-title { font-size: 10px; color: var(--text2); letter-spacing: 1px; margin-bottom: 10px; }
.div-row {
  display: flex; align-items: center; justify-content: space-between;
  padding: 8px 0; border-bottom: 1px solid rgba(30,48,72,0.5);
}
.div-row:last-child { border-bottom: none; }
.div-name { font-size: 10px; color: var(--text2); }
.div-badge { padding: 3px 10px; border-radius: 20px; font-size: 9px; font-weight: bold; letter-spacing: .5px; }
.div-badge.bull  { background: rgba(0,230,118,0.12); color: var(--green); border: 1px solid var(--green2); }
.div-badge.bear  { background: rgba(255,61,90,0.12);  color: var(--red);   border: 1px solid var(--dist2);  }
.div-badge.neut  { background: rgba(136,153,170,0.1); color: var(--neutral);border: 1px solid var(--border); }
.div-badge.warn  { background: rgba(255,184,0,0.12);  color: var(--manip);  border: 1px solid var(--manip2); }

/* ── EXPLANATION ── */
.info-card {
  margin: 0 14px 14px;
  background: rgba(11,17,32,0.6); border: 1px solid var(--border2);
  border-radius: var(--radius); padding: 14px;
}
.info-title { font-size: 10px; color: var(--acc); letter-spacing: 1px; margin-bottom: 8px; }
.info-text  { font-size: 10px; color: var(--text2); line-height: 1.7; }
.info-text li { margin-left: 14px; margin-top: 4px; }

/* ── PRICE TAG ── */
.price-display {
  margin: 0 14px 10px;
  display: grid; grid-template-columns: 1fr 1fr 1fr;
  gap: 8px;
}
.price-cell {
  background: var(--card); border: 1px solid var(--border);
  border-radius: 10px; padding: 10px 12px;
}
.price-lbl { font-size: 8px; color: var(--text2); letter-spacing: 1px; margin-bottom: 4px; }
.price-num { font-family: var(--disp); font-size: 13px; font-weight: 700; color: var(--text); }
.price-num.green { color: var(--green); }
.price-num.red   { color: var(--red);   }

/* ── ERROR ── */
.err-banner {
  margin: 8px 14px; padding: 10px 14px;
  background: rgba(255,61,90,0.08); border: 1px solid var(--dist2);
  border-radius: 8px; font-size: 10px; color: var(--red);
  display: none;
}

.footer {
  text-align: center; padding: 14px; font-size: 9px; color: var(--text3);
  border-top: 1px solid var(--border2); margin-top: 4px; line-height: 2;
}
</style>
</head>
<body>

<!-- LOADING -->
<div id="loading">
  <div class="load-ring"></div>
  <div class="load-brand">KAMALA QUANT</div>
  <div class="load-sub">A/D ENGINE · INITIALIZING</div>
</div>

<!-- HEADER -->
<div class="header">
  <div class="header-brand">
    <div class="brand-dot">⚡</div>
    <div>
      <div class="brand-name">KAMALA QUANT</div>
      <div class="brand-sub">ACCUMULATION / DISTRIBUTION ENGINE</div>
    </div>
  </div>
  <div class="conn-pill" id="connPill">
    <div class="cdot"></div><span id="connLbl">CONNECTING</span>
  </div>
</div>

<!-- CONTROLS -->
<div class="controls">
  <div class="ctrl-group" id="pairBtns">
    <button class="ctrl-btn active" data-pair="BTCUSDT">BTC</button>
    <button class="ctrl-btn" data-pair="ETHUSDT">ETH</button>
    <button class="ctrl-btn" data-pair="SOLUSDT">SOL</button>
    <button class="ctrl-btn" data-pair="BNBUSDT">BNB</button>
    <button class="ctrl-btn" data-pair="PAXGUSDT">PAXG</button>
    <button class="ctrl-btn" data-pair="EURUSDT">EUR</button>
  </div>
  <div class="ctrl-divider"></div>
  <div class="ctrl-group" id="tfBtns">
    <button class="ctrl-btn" data-tf="15m">15m</button>
    <button class="ctrl-btn" data-tf="1h">1h</button>
    <button class="ctrl-btn active" data-tf="4h">4h</button>
    <button class="ctrl-btn" data-tf="1d">1D</button>
  </div>
  <div class="ctrl-divider"></div>
  <button class="refresh-btn" id="refreshBtn" onclick="loadData()">
    <span id="refreshIcon">↻</span> REFRESH
  </button>
</div>

<!-- ERROR BANNER -->
<div class="err-banner" id="errBanner"></div>

<!-- PHASE BANNER -->
<div class="phase-banner" id="phaseBanner">
  <div class="phase-left">
    <div class="phase-icon" id="phaseIcon">⏳</div>
    <div>
      <div class="phase-label" id="phaseLabel">LOADING…</div>
      <div class="phase-sub" id="phaseSub">Fetching market data</div>
    </div>
  </div>
  <div class="phase-conf">
    <div class="conf-pct" id="confPct">—</div>
    <div class="conf-lbl">CONFIDENCE</div>
  </div>
</div>

<!-- PRICE BAR -->
<div class="sec-hdr"><span class="hi">PRICE</span>&nbsp;SNAPSHOT</div>
<div class="price-display">
  <div class="price-cell"><div class="price-lbl">LAST CLOSE</div><div class="price-num" id="priceClose">—</div></div>
  <div class="price-cell"><div class="price-lbl">24H CHANGE</div><div class="price-num" id="priceChg">—</div></div>
  <div class="price-cell"><div class="price-lbl">SPREAD %</div><div class="price-num" id="priceSpread">—</div></div>
</div>

<!-- SIGNAL GRID -->
<div class="sec-hdr"><span class="hi">OHLC</span>&nbsp;STRUCTURE SIGNALS</div>
<div class="signal-grid">
  <div class="sig-card" id="sigAD">
    <div class="sig-name">A/D LINE</div>
    <div class="sig-val" id="sigADVal">—</div>
    <div class="sig-sub" id="sigADSub">Acc/Dist cumulative</div>
  </div>
  <div class="sig-card" id="sigVol">
    <div class="sig-name">VOLUME BIAS</div>
    <div class="sig-val" id="sigVolVal">—</div>
    <div class="sig-sub" id="sigVolSub">Buy vs Sell vol</div>
  </div>
  <div class="sig-card" id="sigClose">
    <div class="sig-name">CLOSE LOCATION</div>
    <div class="sig-val" id="sigCloseVal">—</div>
    <div class="sig-sub" id="sigCloseSub">Where price closed</div>
  </div>
  <div class="sig-card" id="sigSpread">
    <div class="sig-name">SPREAD × VOL</div>
    <div class="sig-val" id="sigSpreadVal">—</div>
    <div class="sig-sub" id="sigSpreadSub">VSA effort score</div>
  </div>
  <div class="sig-card" id="sigWick">
    <div class="sig-name">WICK ANALYSIS</div>
    <div class="sig-val" id="sigWickVal">—</div>
    <div class="sig-sub" id="sigWickSub">Rejection footprint</div>
  </div>
  <div class="sig-card" id="sigMom">
    <div class="sig-name">PRICE MOMENTUM</div>
    <div class="sig-val" id="sigMomVal">—</div>
    <div class="sig-sub" id="sigMomSub">10-period change</div>
  </div>
</div>

<!-- SCORE METERS -->
<div class="sec-hdr"><span class="hi">PRESSURE</span>&nbsp;METERS</div>
<div class="score-grid">
  <div class="score-card">
    <div class="score-label">ACCUMULATION PRESSURE</div>
    <div class="score-num" id="accScore" style="color:var(--acc)">0</div>
    <div class="score-track"><div class="score-fill" id="accBar" style="width:0%;background:linear-gradient(90deg,var(--acc2),var(--acc))"></div></div>
  </div>
  <div class="score-card">
    <div class="score-label">DISTRIBUTION PRESSURE</div>
    <div class="score-num" id="distScore" style="color:var(--dist)">0</div>
    <div class="score-track"><div class="score-fill" id="distBar" style="width:0%;background:linear-gradient(90deg,var(--dist2),var(--dist))"></div></div>
  </div>
  <div class="score-card">
    <div class="score-label">MANIPULATION INDEX</div>
    <div class="score-num" id="manipScore" style="color:var(--manip)">0</div>
    <div class="score-track"><div class="score-fill" id="manipBar" style="width:0%;background:linear-gradient(90deg,var(--manip2),var(--manip))"></div></div>
  </div>
  <div class="score-card">
    <div class="score-label">TREND STRENGTH</div>
    <div class="score-num" id="trendScore" style="color:var(--gold)">0</div>
    <div class="score-track"><div class="score-fill" id="trendBar" style="width:0%;background:linear-gradient(90deg,#996600,var(--gold))"></div></div>
  </div>
</div>

<!-- WYCKOFF PHASES -->
<div class="sec-hdr"><span class="hi">WYCKOFF</span>&nbsp;PHASE DETECTION</div>
<div class="wyckoff-panel">
  <div class="wyckoff-title">CURRENT PHASE SIGNATURE · Based on OHLC structure + Volume</div>
  <div class="wyckoff-phases">
    <div class="wy-phase" id="wyPS">
      <div class="wy-phase-letter">PS</div>
      <div class="wy-phase-name">Prelim Support</div>
    </div>
    <div class="wy-phase" id="wySC">
      <div class="wy-phase-letter">SC</div>
      <div class="wy-phase-name">Selling Climax</div>
    </div>
    <div class="wy-phase" id="wyAR">
      <div class="wy-phase-letter">AR</div>
      <div class="wy-phase-name">Auto Rally</div>
    </div>
    <div class="wy-phase" id="wyLPS">
      <div class="wy-phase-letter">LPS</div>
      <div class="wy-phase-name">Last Point Sup</div>
    </div>
    <div class="wy-phase" id="wySOS">
      <div class="wy-phase-letter">SOS</div>
      <div class="wy-phase-name">Sign of Str</div>
    </div>
  </div>
</div>

<!-- DIVERGENCE PANEL -->
<div class="sec-hdr"><span class="hi">DIVERGENCE</span>&nbsp;ANALYSIS</div>
<div class="div-card">
  <div class="div-title">PRICE vs INDICATOR DIVERGENCE · Hidden Institutional Moves</div>
  <div class="div-row">
    <div class="div-name">Price vs A/D Line</div>
    <div class="div-badge" id="divAD">—</div>
  </div>
  <div class="div-row">
    <div class="div-name">Price vs Volume</div>
    <div class="div-badge" id="divVol">—</div>
  </div>
  <div class="div-row">
    <div class="div-name">Taker Buy vs Price</div>
    <div class="div-badge" id="divTaker">—</div>
  </div>
  <div class="div-row">
    <div class="div-name">Close Loc vs Trend</div>
    <div class="div-badge" id="divClose">—</div>
  </div>
  <div class="div-row">
    <div class="div-name">Manipulation Signal</div>
    <div class="div-badge" id="divManip">—</div>
  </div>
</div>

<!-- PRICE CHART -->
<div class="sec-hdr"><span class="hi">PRICE</span>&nbsp;ACTION CHART</div>
<div class="chart-wrap">
  <div class="chart-title">
    <span>CLOSE PRICE · Last 100 Candles</span>
    <div class="chart-legend">
      <div class="leg-item"><div class="leg-dot" style="background:var(--acc)"></div>Price</div>
      <div class="leg-item"><div class="leg-dot" style="background:var(--manip)"></div>EMA20</div>
    </div>
  </div>
  <canvas id="priceChart" height="180"></canvas>
</div>

<!-- AD CHART -->
<div class="sec-hdr"><span class="hi">ACCUMULATION</span>&nbsp;/ DISTRIBUTION LINE</div>
<div class="chart-wrap">
  <div class="chart-title">
    <span>A/D CUMULATIVE · Money Flow</span>
    <div class="chart-legend">
      <div class="leg-item"><div class="leg-dot" style="background:var(--green)"></div>A/D Line</div>
      <div class="leg-item"><div class="leg-dot" style="background:var(--gold)"></div>SMA14</div>
    </div>
  </div>
  <canvas id="adChart" height="150"></canvas>
</div>

<!-- VOLUME CHART -->
<div class="sec-hdr"><span class="hi">VOLUME</span>&nbsp;DELTA · BUY vs SELL</div>
<div class="chart-wrap">
  <div class="chart-title">
    <span>TAKER BUY RATIO · Last 50 Candles</span>
  </div>
  <canvas id="volChart" height="130"></canvas>
</div>

<!-- VSA TABLE -->
<div class="sec-hdr"><span class="hi">VSA</span>&nbsp;SIGNAL LOG · Recent Events</div>
<div class="vsa-panel">
  <div class="vsa-hdr">
    <span>SIGNAL</span><span>PRICE</span><span>SPREAD%</span><span style="text-align:right">TIME</span>
  </div>
  <div id="vsaBody"></div>
</div>

<!-- INFO -->
<div class="sec-hdr"><span class="hi">HOW</span>&nbsp;IT WORKS</div>
<div class="info-card">
  <div class="info-title">MULTI-LAYER ACCUMULATION / DISTRIBUTION DETECTION</div>
  <div class="info-text">
    This engine analyzes <strong style="color:var(--acc)">real OHLC candle data</strong> — not just the A/D formula — using 5 independent methods:
    <ul>
      <li><strong style="color:var(--acc)">A/D Line:</strong> [(Close−Low)−(High−Close)] / (High−Low) × Volume — cumulative institutional money flow.</li>
      <li><strong style="color:var(--acc)">VSA (Volume Spread Analysis):</strong> Detects effort vs result. High volume + narrow spread = absorption (accumulation). High volume + wide spread down = selling climax or distribution.</li>
      <li><strong style="color:var(--acc)">Close Location Value (CLV):</strong> Where price closes in its candle range reveals who won the battle. Top = buyers. Bottom = sellers.</li>
      <li><strong style="color:var(--acc)">Wyckoff Phases:</strong> PS, SC, AR, LPS, SOS patterns derived from volume spikes and price structure.</li>
      <li><strong style="color:var(--acc)">Divergence:</strong> Price falling but A/D rising = hidden accumulation. Price rising but A/D falling = hidden distribution / smart money exiting.</li>
    </ul>
  </div>
</div>

<div class="footer">
  ⚡ KAMALA QUANT · A/D ENGINE · Powered by Binance Public API<br>
  Live OHLCV · VSA · Wyckoff · Divergence · Multi-factor Scoring<br>
  <span style="color:var(--dist)">⚠ EDUCATIONAL ONLY — NOT FINANCIAL ADVICE</span>
</div>

<script>
// ══════════════════════════════════════════
// CONFIG
// ══════════════════════════════════════════
const PROXIES = [
  '',
  'https://corsproxy.io/?',
  'https://api.allorigins.win/raw?url=',
];
let activeProxy = 0;
let activePair  = 'BTCUSDT';
let activeTF    = '4h';
let rawData     = [];
let priceChart, adChart, volChart;
let autoTimer   = null;

// ══════════════════════════════════════════
// API FETCH WITH PROXY FALLBACK
// ══════════════════════════════════════════
async function apiFetch(path) {
  let lastErr;
  for (let i = 0; i < PROXIES.length; i++) {
    try {
      const base = 'https://api.binance.com';
      const url  = PROXIES[i]
        ? PROXIES[i] + encodeURIComponent(base + path)
        : base + path;
      const res  = await fetch(url, { cache: 'no-store' });
      if (!res.ok) throw new Error('HTTP ' + res.status);
      const data = await res.json();
      activeProxy = i;
      return data;
    } catch (e) { lastErr = e; }
  }
  throw lastErr;
}

// ══════════════════════════════════════════
// MATH HELPERS
// ══════════════════════════════════════════
const clamp = (n,a,b) => Math.min(Math.max(n,a),b);
const avg   = arr => arr.reduce((s,x)=>s+x,0)/arr.length;

function calcEMA(values, period) {
  const k = 2 / (period + 1);
  let ema = values[0];
  return values.map(v => { ema = v * k + ema * (1-k); return ema; });
}

function calcSMA(values, period) {
  return values.map((_, i, arr) => {
    if (i < period - 1) return null;
    return avg(arr.slice(i - period + 1, i + 1));
  });
}

function calcAD(candles) {
  let ad = 0;
  return candles.map(c => {
    const hl = c.high - c.low;
    const clv = hl !== 0 ? ((c.close - c.low) - (c.high - c.close)) / hl : 0;
    const mfv = clv * c.volume;
    ad += mfv;
    return { ad, mfv, clv };
  });
}

// Close Location Value (0=bottom, 1=top of range)
function clv(c) {
  const hl = c.high - c.low;
  return hl !== 0 ? (c.close - c.low) / hl : 0.5;
}

// Spread % = (high-low)/low*100
function spreadPct(c) { return ((c.high - c.low) / c.low) * 100; }

// ══════════════════════════════════════════
// PARSE BINANCE KLINES
// ══════════════════════════════════════════
function parseKlines(raw) {
  return raw.map(k => ({
    ts:         k[0],
    time:       new Date(k[0]).toLocaleTimeString([],{hour:'2-digit',minute:'2-digit'}),
    open:       parseFloat(k[1]),
    high:       parseFloat(k[2]),
    low:        parseFloat(k[3]),
    close:      parseFloat(k[4]),
    volume:     parseFloat(k[5]),
    takerBuy:   parseFloat(k[9]),   // taker buy base volume
    totalVol:   parseFloat(k[5]),
    trades:     parseInt(k[8]),
  }));
}

// ══════════════════════════════════════════
// CORE ENGINE — multi-factor A/D analysis
// ══════════════════════════════════════════
function analyze(candles) {
  const n   = candles.length;
  if (n < 20) return null;

  // ── A/D LINE
  const adVals   = calcAD(candles);
  const adLine   = adVals.map(x => x.ad);
  const mfvLine  = adVals.map(x => x.mfv);
  const clvLine  = adVals.map(x => x.clv);
  const adSMA14  = calcSMA(adLine, 14);
  const adSMA5   = calcSMA(adLine, 5);

  // ── PRICE DATA
  const closes   = candles.map(c => c.close);
  const volumes  = candles.map(c => c.volume);
  const ema20    = calcEMA(closes, 20);
  const avgVol   = avg(volumes.slice(-30));

  // ── TAKER BUY RATIO per candle
  const takerRatio = candles.map(c => c.takerBuy / c.totalVol);

  // Use last N candles for scoring
  const L = 10; // lookback
  const last     = candles.slice(-L);
  const lastAD   = adLine.slice(-L);
  const lastMFV  = mfvLine.slice(-L);
  const lastCLV  = clvLine.slice(-L);
  const lastTaker= takerRatio.slice(-L);
  const lastVols = volumes.slice(-L);

  const cur   = candles[n-1];
  const prev10= candles[n-L-1] || candles[0];
  const curAD = adLine[n-1];
  const prvAD = adLine[n-L-1] || 0;

  // ─────────────────────────────────────
  // ACCUMULATION SCORE (0–100)
  // ─────────────────────────────────────
  let accScore = 0;

  // 1. A/D Line rising
  if (curAD > prvAD) accScore += 20;
  // 2. A/D above SMA14
  const curSMA14 = adSMA14[n-1];
  if (curSMA14 && curAD > curSMA14) accScore += 10;
  // 3. Average CLV > 0.5 (closing upper half)
  const avgCLV = avg(lastCLV);
  if (avgCLV > 0.5) accScore += clamp((avgCLV - 0.5) * 80, 0, 20);
  // 4. Taker buy ratio > 0.5
  const avgTaker = avg(lastTaker);
  if (avgTaker > 0.5) accScore += clamp((avgTaker - 0.5) * 80, 0, 20);
  // 5. Volume on up candles > down candles
  const upVol   = last.filter(c=>c.close>=c.open).reduce((s,c)=>s+c.volume,0);
  const downVol = last.filter(c=>c.close<c.open).reduce((s,c)=>s+c.volume,0);
  if (upVol > downVol) accScore += 15;
  // 6. Lower wicks large (buying at lows)
  const avgLowerWick = avg(last.map(c => (c.close - c.low) / (c.high - c.low + 0.0001)));
  if (avgLowerWick > 0.35) accScore += 15;

  accScore = clamp(Math.round(accScore), 0, 100);

  // ─────────────────────────────────────
  // DISTRIBUTION SCORE (0–100)
  // ─────────────────────────────────────
  let distScore = 0;

  // 1. A/D Line falling
  if (curAD < prvAD) distScore += 20;
  // 2. A/D below SMA14
  if (curSMA14 && curAD < curSMA14) distScore += 10;
  // 3. Average CLV < 0.5 (closing lower half)
  if (avgCLV < 0.5) distScore += clamp((0.5 - avgCLV) * 80, 0, 20);
  // 4. Taker buy ratio < 0.5
  if (avgTaker < 0.5) distScore += clamp((0.5 - avgTaker) * 80, 0, 20);
  // 5. Volume on down candles > up candles
  if (downVol > upVol) distScore += 15;
  // 6. Upper wicks large (selling at highs)
  const avgUpperWick = avg(last.map(c => (c.high - c.close) / (c.high - c.low + 0.0001)));
  if (avgUpperWick > 0.35) distScore += 15;

  distScore = clamp(Math.round(distScore), 0, 100);

  // ─────────────────────────────────────
  // MANIPULATION INDEX (0–100)
  // VSA: High volume + narrow spread = absorption (hidden acc/dist)
  // ─────────────────────────────────────
  let manipScore = 0;

  // High volume narrow spread = absorption
  const spreadArr = last.map(c => spreadPct(c));
  const avgSpread = avg(spreadArr);
  const volRatio  = avg(lastVols) / avgVol;
  if (volRatio > 1.5 && avgSpread < avg(candles.slice(-30).map(spreadPct)) * 0.8) {
    manipScore += 35; // volume coming in, price not moving = absorption
  }
  // Price rising but A/D falling = distribution disguised as rally
  const priceChgPct = (cur.close - prev10.close) / prev10.close * 100;
  const adChgPct    = prvAD !== 0 ? (curAD - prvAD) / Math.abs(prvAD) * 100 : 0;
  if (priceChgPct > 1 && adChgPct < -5)  manipScore += 40; // bear divergence
  if (priceChgPct < -1 && adChgPct > 5)  manipScore += 30; // bull divergence
  // Sudden volume spike
  const lastVolSpike = cur.volume > avgVol * 2.5;
  if (lastVolSpike) manipScore += 25;

  manipScore = clamp(Math.round(manipScore), 0, 100);

  // ─────────────────────────────────────
  // TREND STRENGTH (0–100)
  // ─────────────────────────────────────
  const ema = ema20[n-1];
  const priceDist = Math.abs(cur.close - ema) / ema * 100;
  let trendScore  = clamp(Math.round(priceDist * 8), 0, 50);
  // Consecutive candles same direction
  let streak = 0;
  for (let i = n-1; i >= Math.max(0,n-8); i--) {
    if (candles[i].close > candles[i].open) streak++;
    else if (candles[i].close < candles[i].open) streak--;
  }
  trendScore += clamp(Math.abs(streak) * 6, 0, 50);
  trendScore = clamp(trendScore, 0, 100);

  // ─────────────────────────────────────
  // PHASE DETERMINATION
  // ─────────────────────────────────────
  let phase = 'NEUTRAL', phaseClass = 'neut', confidence = 0, phaseIcon = '⏸', phaseSub = 'No clear phase detected';

  const isAccDominant  = accScore > distScore + 15;
  const isDistDominant = distScore > accScore + 15;
  const isManip        = manipScore > 50;

  if (isManip) {
    if (priceChgPct > 0 && adChgPct < 0) {
      phase = 'DISTRIBUTION'; phaseClass = 'dist'; phaseIcon = '🔴';
      phaseSub = 'Smart money exiting into retail buying'; confidence = Math.round((distScore+manipScore)/2);
    } else if (priceChgPct < 0 && adChgPct > 0) {
      phase = 'ACCUMULATION'; phaseClass = 'acc'; phaseIcon = '🟢';
      phaseSub = 'Institutions buying into weakness'; confidence = Math.round((accScore+manipScore)/2);
    } else {
      phase = 'MANIPULATION'; phaseClass = 'manip'; phaseIcon = '⚠️';
      phaseSub = 'High volume absorption — no clear direction'; confidence = manipScore;
    }
  } else if (isAccDominant) {
    phase = 'ACCUMULATION'; phaseClass = 'acc'; phaseIcon = '🟢';
    phaseSub = accScore > 75 ? 'Strong institutional buying pressure' : 'Moderate accumulation detected';
    confidence = accScore;
  } else if (isDistDominant) {
    phase = 'DISTRIBUTION'; phaseClass = 'dist'; phaseIcon = '🔴';
    phaseSub = distScore > 75 ? 'Heavy institutional selling pressure' : 'Moderate distribution detected';
    confidence = distScore;
  } else {
    phase = 'NEUTRAL'; phaseClass = 'neut'; phaseIcon = '⏸';
    phaseSub = 'No dominant institutional footprint'; confidence = 100 - Math.abs(accScore - distScore);
  }
  confidence = clamp(confidence, 0, 100);

  // ─────────────────────────────────────
  // WYCKOFF PHASE DETECTION
  // ─────────────────────────────────────
  const recentHighVol = candles.slice(-5).some(c => c.volume > avgVol * 2);
  const priceFalling  = priceChgPct < -3;
  const priceRising   = priceChgPct > 3;
  const narrowAfterSpike = last.slice(-3).some(c => spreadPct(c) < avgSpread * 0.6);

  const wyckoff = {
    PS:   priceFalling && recentHighVol && avgCLV > 0.45,
    SC:   priceFalling && recentHighVol && avgCLV < 0.4,
    AR:   priceRising && !recentHighVol && phaseClass === 'acc',
    LPS:  phaseClass === 'acc' && !recentHighVol && avgUpperWick < 0.25,
    SOS:  phaseClass === 'acc' && priceRising && recentHighVol,
  };

  // ─────────────────────────────────────
  // DIVERGENCE
  // ─────────────────────────────────────
  function divLabel(cond1, cond2, bull, bear, neut) {
    if (cond1) return { label: bull, cls: 'bull' };
    if (cond2) return { label: bear, cls: 'bear' };
    return { label: neut, cls: 'neut' };
  }

  const divAD     = divLabel(priceChgPct<-1&&adChgPct>3,  priceChgPct>1&&adChgPct<-3,  'HIDDEN BULL DIV','BEAR DIV','NO DIV');
  const divVol    = divLabel(priceChgPct<0&&volRatio>1.3,  priceChgPct>0&&volRatio<0.7, 'BULL DIV','BEAR DIV','INLINE');
  const divTaker  = divLabel(avgTaker>0.58, avgTaker<0.42, `BUY HEAVY ${(avgTaker*100).toFixed(0)}%`, `SELL HEAVY ${((1-avgTaker)*100).toFixed(0)}%`, 'BALANCED');
  const divClose  = divLabel(avgCLV>0.62, avgCLV<0.38, 'STRONG UPPER', 'STRONG LOWER', 'MID CLOSE');
  const manipDiv  = manipScore > 60 ? { label: 'DETECTED ⚠', cls:'warn' } : manipScore > 35 ? { label: 'POSSIBLE', cls:'warn' } : { label: 'NONE', cls:'neut' };

  // ─────────────────────────────────────
  // VSA SIGNALS (last 10 candles)
  // ─────────────────────────────────────
  const vsaSignals = [];
  const last20 = candles.slice(-20);
  const avg20Vol = avg(candles.slice(-40).map(c=>c.volume));
  const avg20Spr = avg(candles.slice(-40).map(c=>spreadPct(c)));

  last20.forEach(c => {
    const sp = spreadPct(c);
    const vr = c.volume / avg20Vol;
    const cv = clv(c);
    let signal = null;

    if (vr > 2.0 && sp < avg20Spr * 0.7 && cv > 0.5) signal = { type: 'acc', label: 'STOPPING VOLUME (ACC)' };
    else if (vr > 2.0 && sp < avg20Spr * 0.7 && cv < 0.5) signal = { type: 'dist', label: 'SUPPLY ABSORPTION (DIST)' };
    else if (vr > 2.0 && sp > avg20Spr * 1.4 && cv < 0.35) signal = { type: 'dist', label: 'SELLING CLIMAX' };
    else if (vr > 2.0 && sp > avg20Spr * 1.4 && cv > 0.65) signal = { type: 'acc', label: 'BUYING CLIMAX (TRAP)' };
    else if (vr < 0.5 && sp < avg20Spr * 0.5) signal = { type: 'manip', label: 'NO DEMAND / NO SUPPLY' };
    else if (vr > 1.5 && cv > 0.65 && c.close > c.open) signal = { type: 'acc', label: 'EFFORT UP (BULL BAR)' };
    else if (vr > 1.5 && cv < 0.35 && c.close < c.open) signal = { type: 'dist', label: 'EFFORT DOWN (BEAR BAR)' };

    if (signal) vsaSignals.push({ ...signal, price: c.close, spread: sp.toFixed(2), time: c.time });
  });

  // ─────────────────────────────────────
  // RETURN ALL
  // ─────────────────────────────────────
  return {
    phase, phaseClass, phaseIcon, phaseSub, confidence,
    accScore, distScore, manipScore, trendScore,
    adLine, adSMA14, clvLine, takerRatio,
    curAD, curSMA14, avgCLV, avgTaker, avgSpread,
    priceChgPct, adChgPct, volRatio,
    divAD, divVol, divTaker, divClose, manipDiv,
    wyckoff, vsaSignals,
    ema20,
    cur, avgVol,
    priceUp: closes[n-1] > closes[n-2],
    curSpread: spreadPct(cur),
  };
}

// ══════════════════════════════════════════
// UI UPDATE
// ══════════════════════════════════════════
function setConn(live) {
  const p = document.getElementById('connPill');
  const l = document.getElementById('connLbl');
  p.className = 'conn-pill ' + (live ? 'live' : 'err');
  l.textContent = live ? 'LIVE' : 'OFFLINE';
}

function showErr(msg) {
  const b = document.getElementById('errBanner');
  b.textContent = '⚠ ' + msg;
  b.style.display = 'block';
  setTimeout(() => b.style.display = 'none', 8000);
}

function fmtPrice(p, dec) {
  dec = dec ?? (p > 1000 ? 2 : p > 1 ? 4 : 6);
  return Number(p).toLocaleString('en-US', { minimumFractionDigits: dec, maximumFractionDigits: dec });
}

function fmtAD(v) {
  if (Math.abs(v) > 1e9) return (v/1e9).toFixed(2)+'B';
  if (Math.abs(v) > 1e6) return (v/1e6).toFixed(2)+'M';
  if (Math.abs(v) > 1e3) return (v/1e3).toFixed(2)+'K';
  return v.toFixed(0);
}

function updateUI(result, candles) {
  const { phase, phaseClass, phaseIcon, phaseSub, confidence,
    accScore, distScore, manipScore, trendScore,
    divAD, divVol, divTaker, divClose, manipDiv,
    wyckoff, vsaSignals,
    curAD, avgCLV, avgTaker, curSpread, volRatio,
    priceChgPct, adChgPct, cur, avgVol, priceUp } = result;

  // Phase banner
  const banner = document.getElementById('phaseBanner');
  banner.className = 'phase-banner ' + (phaseClass === 'acc' ? 'acc' : phaseClass === 'dist' ? 'dist' : phaseClass === 'manip' ? 'manip' : '');
  document.getElementById('phaseIcon').textContent = phaseIcon;
  const pl = document.getElementById('phaseLabel');
  pl.textContent  = phase;
  pl.className    = 'phase-label ' + phaseClass;
  document.getElementById('phaseSub').textContent  = phaseSub;
  document.getElementById('confPct').textContent   = confidence + '%';

  // Price display
  document.getElementById('priceClose').textContent = fmtPrice(cur.close);
  document.getElementById('priceClose').className = 'price-num ' + (priceUp ? 'green' : 'red');
  document.getElementById('priceChg').textContent  = (priceChgPct >= 0 ? '+' : '') + priceChgPct.toFixed(2) + '%';
  document.getElementById('priceChg').className    = 'price-num ' + (priceChgPct >= 0 ? 'green' : 'red');
  document.getElementById('priceSpread').textContent = curSpread.toFixed(3) + '%';

  // Signal cards
  function setSig(cardId, valId, subId, val, sub, colorCls, cardCls) {
    document.getElementById(valId).textContent = val;
    document.getElementById(valId).className   = 'sig-val ' + colorCls;
    document.getElementById(subId).textContent = sub;
    document.getElementById(cardId).className  = 'sig-card ' + cardCls;
  }

  setSig('sigAD',  'sigADVal',    'sigADSub',
    fmtAD(curAD),
    curAD > 0 ? 'Positive — accumulation' : 'Negative — distribution',
    curAD > 0 ? 'green' : 'red', curAD > 0 ? 'acc' : 'dist');

  setSig('sigVol', 'sigVolVal',   'sigVolSub',
    (avgTaker * 100).toFixed(1) + '% BUY',
    avgTaker > 0.52 ? 'Buyers dominant' : avgTaker < 0.48 ? 'Sellers dominant' : 'Balanced',
    avgTaker > 0.52 ? 'green' : avgTaker < 0.48 ? 'red' : 'neut',
    avgTaker > 0.52 ? 'acc' : avgTaker < 0.48 ? 'dist' : 'neut');

  const clvPct = (avgCLV * 100).toFixed(0) + '%';
  setSig('sigClose','sigCloseVal', 'sigCloseSub',
    clvPct + ' UP',
    avgCLV > 0.55 ? 'Closing near highs' : avgCLV < 0.45 ? 'Closing near lows' : 'Mid-range close',
    avgCLV > 0.55 ? 'green' : avgCLV < 0.45 ? 'red' : 'neut',
    avgCLV > 0.55 ? 'acc' : avgCLV < 0.45 ? 'dist' : 'neut');

  const svLabel = volRatio > 1.5 ? 'HIGH EFFORT' : volRatio > 1 ? 'NORMAL' : 'LOW EFFORT';
  setSig('sigSpread','sigSpreadVal','sigSpreadSub',
    svLabel,
    volRatio > 1.5 ? `${volRatio.toFixed(1)}× avg vol — institutional` : `${volRatio.toFixed(1)}× avg vol`,
    volRatio > 1.5 ? 'gold' : 'white', volRatio > 1.5 ? 'warn' : 'neut');

  const lastC = candles[candles.length-1];
  const lowerW = (lastC.close - lastC.low);
  const upperW = (lastC.high - lastC.close);
  const wickBias = lowerW > upperW * 1.5 ? 'LOWER WICK' : upperW > lowerW * 1.5 ? 'UPPER WICK' : 'BALANCED';
  const wickMng  = lowerW > upperW * 1.5 ? 'acc' : upperW > lowerW * 1.5 ? 'dist' : 'neut';
  setSig('sigWick','sigWickVal','sigWickSub',
    wickBias,
    lowerW > upperW * 1.5 ? 'Buyers rejected lows' : upperW > lowerW * 1.5 ? 'Sellers rejected highs' : 'No clear rejection',
    wickMng === 'acc' ? 'green' : wickMng === 'dist' ? 'red' : 'neut', wickMng);

  setSig('sigMom','sigMomVal','sigMomSub',
    (priceChgPct >= 0 ? '+' : '') + priceChgPct.toFixed(2) + '%',
    Math.abs(priceChgPct) > 3 ? 'Strong directional move' : 'Moderate drift',
    priceChgPct > 1 ? 'green' : priceChgPct < -1 ? 'red' : 'neut',
    priceChgPct > 1 ? 'acc' : priceChgPct < -1 ? 'dist' : 'neut');

  // Score meters
  function setMeter(scoreId, barId, score) {
    document.getElementById(scoreId).textContent = score;
    document.getElementById(barId).style.width   = score + '%';
  }
  setMeter('accScore',   'accBar',   accScore);
  setMeter('distScore',  'distBar',  distScore);
  setMeter('manipScore', 'manipBar', manipScore);
  setMeter('trendScore', 'trendBar', trendScore);

  // Wyckoff
  Object.entries(wyckoff).forEach(([key, active]) => {
    const el = document.getElementById('wy' + key);
    if (!el) return;
    el.className = 'wy-phase' + (active ? ' active-' + phaseClass : '');
  });

  // Divergence
  function setDiv(id, obj) {
    const el = document.getElementById(id);
    el.textContent = obj.label;
    el.className = 'div-badge ' + obj.cls;
  }
  setDiv('divAD',    divAD);
  setDiv('divVol',   divVol);
  setDiv('divTaker', divTaker);
  setDiv('divClose', divClose);
  setDiv('divManip', manipDiv);

  // VSA table
  const body = document.getElementById('vsaBody');
  const show = vsaSignals.slice(-8).reverse();
  if (show.length === 0) {
    body.innerHTML = '<div style="padding:12px 14px;font-size:10px;color:var(--text3)">No significant VSA signals in recent candles.</div>';
  } else {
    body.innerHTML = show.map(s => `
      <div class="vsa-row ${s.type}-row">
        <div class="vsa-signal ${s.type}">${s.label}</div>
        <div class="vsa-price">${fmtPrice(s.price)}</div>
        <div class="vsa-spread">${s.spread}%</div>
        <div class="vsa-time">${s.time}</div>
      </div>`).join('');
  }
}

// ══════════════════════════════════════════
// CHARTS
// ══════════════════════════════════════════
const CHART_OPTS = {
  responsive: true,
  animation:  false,
  plugins: { legend: { display: false }, tooltip: { enabled: true, mode: 'index', intersect: false } },
  scales: {
    x: { display: true, ticks: { color:'#334455', font:{size:9}, maxTicksLimit:8 }, grid: { color:'#0e1e30' } },
    y: { display: true, ticks: { color:'#334455', font:{size:9} }, grid: { color:'#0e1e30' }, position:'right' },
  },
  interaction: { mode: 'nearest', axis: 'x', intersect: false },
};

function buildPriceChart(candles, ema20) {
  const labels = candles.map(c => c.time);
  const prices = candles.map(c => c.close);
  const ctx    = document.getElementById('priceChart').getContext('2d');
  if (priceChart) priceChart.destroy();
  priceChart = new Chart(ctx, {
    type: 'line',
    data: {
      labels,
      datasets: [
        { label:'Price', data: prices, borderColor:'#00e5ff', borderWidth:1.5,
          pointRadius:0, fill: false, tension: 0.2 },
        { label:'EMA20', data: ema20, borderColor:'#ffb800', borderWidth:1,
          pointRadius:0, fill: false, tension: 0.4, borderDash:[4,3] },
      ]
    },
    options: { ...CHART_OPTS,
      plugins: { ...CHART_OPTS.plugins,
        tooltip: { ...CHART_OPTS.plugins.tooltip,
          callbacks: { label: ctx => ctx.dataset.label + ': ' + fmtPrice(ctx.parsed.y) }
        }
      }
    }
  });
}

function buildADChart(adLine, adSMA14, labels) {
  const ctx = document.getElementById('adChart').getContext('2d');
  if (adChart) adChart.destroy();

  // Color A/D segments
  const adColors = adLine.map((v, i) => i === 0 ? '#00e676' : (adLine[i] >= adLine[i-1] ? '#00e676' : '#ff3d5a'));

  adChart = new Chart(ctx, {
    type: 'line',
    data: {
      labels,
      datasets: [
        { label:'A/D', data: adLine, borderColor:'#00e676', borderWidth:1.5,
          pointRadius:0, fill: false, tension:0 },
        { label:'SMA14', data: adSMA14, borderColor:'#f0c040', borderWidth:1,
          pointRadius:0, fill: false, tension:0.4, borderDash:[5,4] },
      ]
    },
    options: { ...CHART_OPTS,
      plugins: { ...CHART_OPTS.plugins,
        tooltip: { ...CHART_OPTS.plugins.tooltip,
          callbacks: { label: ctx => ctx.dataset.label + ': ' + fmtAD(ctx.parsed.y) }
        }
      }
    }
  });
}

function buildVolChart(candles) {
  const recent = candles.slice(-50);
  const labels  = recent.map(c => c.time);
  const buyRatio= recent.map(c => (c.takerBuy / c.totalVol) * 100);
  const ctx     = document.getElementById('volChart').getContext('2d');
  if (volChart) volChart.destroy();

  volChart = new Chart(ctx, {
    type: 'bar',
    data: {
      labels,
      datasets: [{
        label: 'Taker Buy %',
        data: buyRatio,
        backgroundColor: buyRatio.map(v => v > 52 ? 'rgba(0,230,118,0.6)' : 'rgba(255,61,90,0.6)'),
        borderRadius: 2,
        borderSkipped: false,
      }]
    },
    options: { ...CHART_OPTS,
      scales: {
        ...CHART_OPTS.scales,
        y: { ...CHART_OPTS.scales.y, min: 30, max: 70,
          ticks: { ...CHART_OPTS.scales.y.ticks, callback: v => v + '%' } }
      },
      plugins: { ...CHART_OPTS.plugins,
        annotation: { annotations: { line50: { type:'line', yMin:50, yMax:50, borderColor:'rgba(255,255,255,0.15)', borderWidth:1 } } }
      }
    }
  });
}

// ══════════════════════════════════════════
// MAIN LOAD
// ══════════════════════════════════════════
async function loadData() {
  const icon = document.getElementById('refreshIcon');
  icon.classList.add('spin');

  try {
    const raw     = await apiFetch(`/api/v3/klines?symbol=${activePair}&interval=${activeTF}&limit=100`);
    const candles = parseKlines(raw);
    rawData       = candles;
    const result  = analyze(candles);
    if (!result) { showErr('Not enough data'); return; }

    updateUI(result, candles);
    buildPriceChart(candles, result.ema20);
    buildADChart(result.adLine, result.adSMA14, candles.map(c=>c.time));
    buildVolChart(candles);
    setConn(true);
  } catch(e) {
    showErr('API Error: ' + e.message);
    setConn(false);
  } finally {
    icon.classList.remove('spin');
  }
}

// ══════════════════════════════════════════
// CONTROLS
// ══════════════════════════════════════════
document.querySelectorAll('#pairBtns .ctrl-btn').forEach(btn => {
  btn.addEventListener('click', () => {
    document.querySelectorAll('#pairBtns .ctrl-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    activePair = btn.dataset.pair;
    loadData();
  });
});

document.querySelectorAll('#tfBtns .ctrl-btn').forEach(btn => {
  btn.addEventListener('click', () => {
    document.querySelectorAll('#tfBtns .ctrl-btn').forEach(b => b.classList.remove('active'));
    btn.classList.add('active');
    activeTF = btn.dataset.tf;
    loadData();
  });
});

// ══════════════════════════════════════════
// INIT
// ══════════════════════════════════════════
async function init() {
  await loadData();
  // Auto-refresh every 60s
  autoTimer = setInterval(loadData, 60000);
  setTimeout(() => document.getElementById('loading').classList.add('gone'), 1200);
}

init();
</script>
</body>
</html>
