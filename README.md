<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Juvenile League — Official Portal</title>
<link href="https://fonts.googleapis.com/css2?family=Bebas+Neue&family=Barlow:wght@300;400;500;600;700&family=Barlow+Condensed:wght@400;600;700&display=swap" rel="stylesheet">

<!-- Firebase SDKs -->
<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.0/firebase-app.js";
import { getFirestore, collection, doc, setDoc, getDoc, getDocs, deleteDoc, onSnapshot, writeBatch }
  from "https://www.gstatic.com/firebasejs/10.12.0/firebase-firestore.js";
import { getStorage, ref, uploadBytes, getDownloadURL }
  from "https://www.gstatic.com/firebasejs/10.12.0/firebase-storage.js";

const firebaseConfig = {
  apiKey: "AIzaSyCsZrHcpJgGoTHeW0Ex4Hv20KLtDopPq4",
  authDomain: "llfc-4d2df.firebaseapp.com",
  projectId: "llfc-4d2df",
  storageBucket: "llfc-4d2df.firebasestorage.app",
  messagingSenderId: "697058785471",
  appId: "1:697058785471:web:7481cae8fe6b682d762e0a"
};

const app = initializeApp(firebaseConfig);
const db  = getFirestore(app);
const storage = getStorage(app);

// ─── expose to window ───────────────────────────────────────
window._FB = { db, storage, collection, doc, setDoc, getDoc, getDocs, deleteDoc, onSnapshot, writeBatch, ref, uploadBytes, getDownloadURL };

// ─── realtime listeners ─────────────────────────────────────
function listen(colName, setter){
  onSnapshot(collection(db, colName), snap => {
    const data = {};
    snap.forEach(d => { data[d.id] = d.data(); });
    setter(data);
  });
}

// After page load wire up
window.addEventListener('load', () => {
  listen('teams',   d => { window.fbTeams   = d; rebuildLocal(); });
  listen('players', d => { window.fbPlayers = d; rebuildLocal(); });
  listen('fixtures',d => { window.fbFixtures= d; rebuildLocal(); });
  listen('matches', d => { window.fbMatches = d; rebuildLocal(); });
  listen('stats',   d => { window.fbStats   = d; rebuildLocal(); });
  listen('manual_standings', d => { window.fbManualStandings = d; rebuildLocal(); });
});
</script>

<style>
:root{
  --green:#00C853;--gd:#009624;--glow:#00FF6A;
  --dark:#080D0A;--card:#0F1A11;--card2:#142016;
  --border:#1A2E1E;--text:#E8F5E9;--muted:#5A8465;
  --acc:#FFD600;--red:#FF3D3D;--blue:#2979FF;
  --purple:#AA00FF;
}
*{margin:0;padding:0;box-sizing:border-box;}
body{background:var(--dark);color:var(--text);font-family:'Barlow',sans-serif;min-height:100vh;overflow-x:hidden;}

/* ── LOADER ── */
#loader{position:fixed;inset:0;background:var(--dark);display:flex;flex-direction:column;align-items:center;justify-content:center;z-index:999;transition:opacity .5s;}
#loader.gone{opacity:0;pointer-events:none;}
.spin{width:52px;height:52px;border:3px solid var(--border);border-top-color:var(--green);border-radius:50%;animation:spin .8s linear infinite;margin-bottom:1rem;}
@keyframes spin{to{transform:rotate(360deg)}}

/* ── HEADER ── */
header{background:linear-gradient(135deg,#0b1a0d,#080d0a);border-bottom:2px solid var(--green);padding:0 1.5rem;display:flex;align-items:center;justify-content:space-between;position:sticky;top:0;z-index:100;box-shadow:0 2px 40px rgba(0,200,83,.12);}
.logo-area{display:flex;align-items:center;gap:.8rem;padding:.7rem 0;}
.logo-icon{width:46px;height:46px;background:linear-gradient(135deg,var(--green),var(--gd));border-radius:50%;display:flex;align-items:center;justify-content:center;font-size:1.4rem;box-shadow:0 0 20px rgba(0,200,83,.35);}
.logo-text{font-family:'Bebas Neue';font-size:1.55rem;letter-spacing:2px;line-height:1;}
.logo-text span{display:block;font-family:'Barlow Condensed';font-size:.68rem;letter-spacing:4px;color:var(--green);font-weight:600;}
nav{display:flex;gap:.15rem;flex-wrap:wrap;}
nav button{background:transparent;border:none;color:var(--muted);padding:.42rem .8rem;font-family:'Barlow Condensed';font-size:.85rem;font-weight:600;letter-spacing:1px;cursor:pointer;border-radius:4px;transition:all .2s;text-transform:uppercase;}
nav button:hover,nav button.active{color:var(--green);background:rgba(0,200,83,.08);}
.admin-btn{background:linear-gradient(135deg,var(--green),var(--gd))!important;color:#000!important;font-weight:700!important;}
.admin-btn.aa{background:linear-gradient(135deg,var(--acc),#d49600)!important;color:#000!important;}

/* ── SECTIONS ── */
.section{display:none;padding:1.5rem;max-width:1200px;margin:0 auto;animation:fi .3s;}
.section.active{display:block;}
@keyframes fi{from{opacity:0;transform:translateY(8px)}to{opacity:1;transform:translateY(0)}}

/* ── HERO ── */
.hero{text-align:center;padding:2.5rem 1rem;background:radial-gradient(ellipse at center,rgba(0,200,83,.08) 0%,transparent 70%);border-radius:16px;margin-bottom:1.5rem;border:1px solid var(--border);}
.hero h1{font-family:'Bebas Neue';font-size:3.5rem;letter-spacing:4px;color:var(--green);}
.hero p{color:var(--muted);font-size:1rem;margin-top:.4rem;}
.stats-row{display:flex;gap:.8rem;justify-content:center;flex-wrap:wrap;margin-top:1.5rem;}
.stat-box{background:var(--card);border:1px solid var(--border);border-radius:10px;padding:.8rem 1.2rem;min-width:110px;text-align:center;}
.stat-box .num{font-family:'Bebas Neue';font-size:2rem;color:var(--green);}
.stat-box .lbl{font-size:.7rem;color:var(--muted);text-transform:uppercase;letter-spacing:1px;}

/* ── TITLES & GRIDS ── */
.stitle{font-family:'Bebas Neue';font-size:1.8rem;letter-spacing:3px;color:var(--green);border-bottom:2px solid var(--border);padding-bottom:.4rem;margin-bottom:1.2rem;}
.grid2{display:grid;grid-template-columns:repeat(auto-fill,minmax(290px,1fr));gap:1rem;}
.card{background:var(--card);border:1px solid var(--border);border-radius:14px;padding:1.1rem;transition:all .2s;cursor:pointer;}
.card:hover{border-color:var(--green);transform:translateY(-2px);box-shadow:0 8px 30px rgba(0,200,83,.1);}

/* ── BADGES ── */
.badge{display:inline-block;padding:.12rem .48rem;border-radius:4px;font-size:.68rem;font-weight:700;letter-spacing:.5px;text-transform:uppercase;}
.bl{background:rgba(0,200,83,.12);color:var(--green);border:1px solid rgba(0,200,83,.3);}
.by{background:rgba(255,214,0,.12);color:var(--acc);border:1px solid rgba(255,214,0,.3);}
.br{background:rgba(255,61,61,.12);color:var(--red);border:1px solid rgba(255,61,61,.3);}
.bpu{background:rgba(170,0,255,.12);color:var(--purple);border:1px solid rgba(170,0,255,.3);}

/* ── CONDITION BADGES ── */
.cond{display:inline-flex;align-items:center;gap:.25rem;padding:.12rem .5rem;border-radius:20px;font-size:.7rem;font-weight:700;letter-spacing:.5px;}
.cond-ap{background:rgba(0,255,106,.15);color:#00FF6A;border:1px solid rgba(0,255,106,.4);}
.cond-a {background:rgba(0,200,83,.15);color:var(--green);border:1px solid rgba(0,200,83,.4);}
.cond-bp{background:rgba(41,121,255,.15);color:#7CB9FF;border:1px solid rgba(41,121,255,.4);}
.cond-bm{background:rgba(100,181,246,.12);color:#90CAF9;border:1px solid rgba(100,181,246,.3);}
.cond-c {background:rgba(255,214,0,.12);color:var(--acc);border:1px solid rgba(255,214,0,.3);}
.cond-d {background:rgba(255,100,0,.12);color:#FF8A50;border:1px solid rgba(255,100,0,.3);}
.cond-e {background:rgba(255,61,61,.15);color:var(--red);border:1px solid rgba(255,61,61,.4);}

/* ── TABS ── */
.tabs{display:flex;gap:.4rem;margin-bottom:1rem;border-bottom:1px solid var(--border);padding-bottom:.7rem;flex-wrap:wrap;}
.tab{padding:.33rem .85rem;border:1px solid transparent;border-radius:20px;font-size:.78rem;font-weight:600;cursor:pointer;transition:all .2s;background:transparent;color:var(--muted);font-family:'Barlow Condensed';text-transform:uppercase;letter-spacing:.5px;}
.tab.active{background:rgba(0,200,83,.1);color:var(--green);border-color:var(--green);}

/* ── TEAM LOGO ── */
.t-logo-wrap{width:52px;height:52px;border-radius:50%;background:var(--card2);border:2px solid var(--border);display:flex;align-items:center;justify-content:center;font-size:1.5rem;overflow:hidden;flex-shrink:0;}
.t-logo-wrap img{width:100%;height:100%;object-fit:cover;border-radius:50%;}
.mini-logo{width:28px;height:28px;border-radius:50%;background:var(--card2);border:1px solid var(--border);display:flex;align-items:center;justify-content:center;font-size:.9rem;overflow:hidden;flex-shrink:0;}
.mini-logo img{width:100%;height:100%;object-fit:cover;border-radius:50%;}

/* ── PHOTO ── */
.p-photo{border-radius:50%;background:var(--card2);border:2px solid var(--border);display:flex;align-items:center;justify-content:center;overflow:hidden;flex-shrink:0;}
.p-photo img{width:100%;height:100%;object-fit:cover;border-radius:50%;}

/* ── BEST PLAYER ── */
.best-card{background:linear-gradient(135deg,rgba(255,214,0,.07),rgba(0,200,83,.04));border:1px solid rgba(255,214,0,.2);border-radius:12px;padding:.85rem 1rem;display:flex;align-items:center;gap:.8rem;margin-top:.8rem;}

/* ── FIXTURE ── */
.fx-card{background:var(--card);border:1px solid var(--border);border-radius:14px;padding:1rem 1.2rem;display:flex;align-items:center;gap:.8rem;flex-wrap:wrap;}
.vs-block{flex:1;display:flex;align-items:center;gap:.7rem;justify-content:center;min-width:200px;}
.t-side{text-align:center;min-width:72px;}
.t-side .tl{width:38px;height:38px;border-radius:50%;background:var(--card2);border:2px solid var(--border);display:flex;align-items:center;justify-content:center;font-size:1.1rem;margin:0 auto .25rem;overflow:hidden;}
.t-side .tl img{width:100%;height:100%;object-fit:cover;border-radius:50%;}
.t-side .tn{font-size:.72rem;font-weight:600;line-height:1.2;}
.vs-txt{font-family:'Bebas Neue';font-size:1.6rem;color:var(--muted);}
.score-txt{font-family:'Bebas Neue';font-size:2rem;color:var(--green);letter-spacing:3px;}
.fx-info{text-align:right;min-width:130px;}
.sbadge{padding:.18rem .55rem;border-radius:20px;font-size:.68rem;font-weight:700;text-transform:uppercase;}
.s-up{background:rgba(255,214,0,.1);color:var(--acc);border:1px solid rgba(255,214,0,.3);}
.s-pl{background:rgba(0,200,83,.1);color:var(--green);border:1px solid rgba(0,200,83,.3);}
.s-lv{background:rgba(255,61,61,.16);color:var(--red);border:1px solid rgba(255,61,61,.4);animation:pulse 1.5s infinite;}
@keyframes pulse{0%,100%{opacity:1}50%{opacity:.5}}

/* ── TABLE ── */
.twrap{overflow-x:auto;border-radius:12px;border:1px solid var(--border);}
table{width:100%;border-collapse:collapse;font-size:.88rem;}
thead{background:rgba(0,200,83,.07);}
th{padding:.7rem .9rem;text-align:left;font-family:'Barlow Condensed';font-size:.76rem;letter-spacing:1px;text-transform:uppercase;color:var(--green);font-weight:600;white-space:nowrap;}
td{padding:.6rem .9rem;border-top:1px solid var(--border);}
tr:hover td{background:rgba(0,200,83,.03);}
.pts-val{font-family:'Bebas Neue';font-size:1.3rem;color:var(--green);}
.pos-num{font-family:'Bebas Neue';font-size:1.3rem;}
.team-cell{display:flex;align-items:center;gap:.5rem;}
.form-dots{display:flex;gap:.2rem;}
.fd{width:8px;height:8px;border-radius:50%;}
.fw{background:var(--green);}.ld{background:var(--red);}.dr{background:var(--muted);}

/* ── RANKING ── */
.rank-card{background:var(--card);border:1px solid var(--border);border-radius:12px;padding:.9rem 1.1rem;display:flex;align-items:center;gap:.9rem;}
.rnum{font-family:'Bebas Neue';font-size:2rem;width:34px;text-align:center;}
.rinfo{flex:1;}
.rinfo .rn{font-weight:700;font-size:.95rem;}
.rinfo .rt{font-size:.73rem;color:var(--muted);}
.rstat{text-align:right;}
.rstat .rv{font-family:'Bebas Neue';font-size:1.6rem;color:var(--green);}
.rstat .rl{font-size:.67rem;color:var(--muted);text-transform:uppercase;}
.rank-tabs{display:flex;gap:.4rem;margin-bottom:1rem;flex-wrap:wrap;}
.rtab{padding:.33rem .85rem;border:1px solid var(--border);border-radius:20px;font-size:.76rem;font-weight:600;cursor:pointer;transition:all .2s;background:transparent;color:var(--muted);font-family:'Barlow Condensed';}
.rtab.active{background:var(--green);color:#000;border-color:var(--green);}

/* ── ADMIN ── */
.apanel{background:rgba(255,214,0,.025);border:1px solid rgba(255,214,0,.12);border-radius:16px;padding:1.3rem;margin-bottom:1.3rem;}
.apanel.hidden{display:none;}
.apanel h3{font-family:'Barlow Condensed';font-size:1rem;font-weight:700;color:var(--acc);margin-bottom:.9rem;text-transform:uppercase;letter-spacing:1px;}
.fgrid{display:grid;grid-template-columns:repeat(auto-fill,minmax(180px,1fr));gap:.7rem;}
.fg{display:flex;flex-direction:column;gap:.25rem;}
.fg label{font-size:.7rem;color:var(--muted);text-transform:uppercase;letter-spacing:.5px;font-weight:600;}
.fg input,.fg select,.fg textarea{background:var(--dark);border:1px solid var(--border);border-radius:8px;padding:.52rem .72rem;color:var(--text);font-family:'Barlow';font-size:.87rem;outline:none;transition:border-color .2s;width:100%;}
.fg input:focus,.fg select:focus,.fg textarea:focus{border-color:var(--green);}
.fg select option{background:var(--dark);}
.fg textarea{resize:vertical;min-height:140px;font-family:monospace;font-size:.79rem;}
.btn{padding:.52rem 1.15rem;border:none;border-radius:8px;font-family:'Barlow Condensed';font-size:.88rem;font-weight:700;letter-spacing:.5px;cursor:pointer;transition:all .2s;text-transform:uppercase;}
.bg{background:var(--green);color:#000;}.bg:hover{background:var(--glow);}
.ba{background:var(--acc);color:#000;}
.bd{background:rgba(255,61,61,.1);color:var(--red);border:1px solid rgba(255,61,61,.25);font-size:.76rem;padding:.28rem .65rem;border-radius:6px;cursor:pointer;font-family:'Barlow Condensed';font-weight:700;}
.alist{display:flex;flex-direction:column;gap:.35rem;margin-top:.8rem;}
.aitem{background:var(--dark);border:1px solid var(--border);border-radius:9px;padding:.52rem .88rem;display:flex;align-items:center;gap:.6rem;flex-wrap:wrap;}
.aitem .ai{flex:1;min-width:100px;}
.aitem .an{font-weight:600;font-size:.88rem;}
.aitem .am{font-size:.7rem;color:var(--muted);}
.admin-header{display:flex;align-items:center;gap:1rem;margin-bottom:1.3rem;background:rgba(255,214,0,.03);border:1px solid rgba(255,214,0,.1);border-radius:12px;padding:.9rem 1.3rem;flex-wrap:wrap;}
.admin-badge{background:var(--acc);color:#000;padding:.25rem .7rem;border-radius:6px;font-weight:700;font-size:.76rem;font-family:'Barlow Condensed';letter-spacing:1px;}
.lbtn{padding:.32rem .78rem;background:rgba(255,61,61,.08);border:1px solid rgba(255,61,61,.25);color:var(--red);border-radius:6px;cursor:pointer;font-size:.76rem;font-weight:700;font-family:'Barlow Condensed';}
.atabs{display:flex;gap:.4rem;margin-bottom:1.2rem;flex-wrap:wrap;}
.atab{padding:.42rem 1.05rem;border:1px solid var(--border);border-radius:8px;font-size:.8rem;font-weight:700;cursor:pointer;transition:all .2s;background:transparent;color:var(--muted);font-family:'Barlow Condensed';text-transform:uppercase;}
.atab.active{background:rgba(255,214,0,.08);color:var(--acc);border-color:var(--acc);}

/* ── MODAL ── */
.moverlay{position:fixed;inset:0;background:rgba(0,0,0,.85);display:flex;align-items:center;justify-content:center;z-index:200;backdrop-filter:blur(6px);}
.moverlay.hidden{display:none;}
.modal{background:var(--card);border:1px solid var(--green);border-radius:16px;padding:1.8rem;width:min(92vw,540px);max-height:87vh;overflow-y:auto;box-shadow:0 0 70px rgba(0,200,83,.18);}
.modal::-webkit-scrollbar{width:4px;}
.modal::-webkit-scrollbar-thumb{background:var(--green);border-radius:2px;}
.modal h2{font-family:'Bebas Neue';font-size:1.9rem;color:var(--green);margin-bottom:.3rem;}
.modal p.sub{font-size:.82rem;color:var(--muted);margin-bottom:1.2rem;}
.modal input,.modal select{width:100%;background:var(--dark);border:1px solid var(--border);border-radius:8px;padding:.62rem .88rem;color:var(--text);font-family:'Barlow';font-size:.9rem;outline:none;margin-bottom:.85rem;transition:border-color .2s;}
.modal input:focus,.modal select:focus{border-color:var(--green);}
.modal select option{background:var(--dark);}
.merr{color:var(--red);font-size:.78rem;margin-bottom:.5rem;display:none;}
.mbtns{display:flex;gap:.7rem;}

/* ── LOGO INPUT GROUP ── */
.logo-input-group{display:flex;flex-direction:column;gap:.4rem;}
.logo-tabs-sm{display:flex;gap:.3rem;margin-bottom:.3rem;}
.logo-tab-sm{padding:.22rem .65rem;border:1px solid var(--border);border-radius:12px;font-size:.7rem;cursor:pointer;color:var(--muted);font-family:'Barlow Condensed';font-weight:600;background:transparent;}
.logo-tab-sm.active{background:rgba(0,200,83,.1);color:var(--green);border-color:var(--green);}
.logo-preview-circle{width:52px;height:52px;border-radius:50%;background:var(--card2);border:2px dashed var(--border);display:flex;align-items:center;justify-content:center;font-size:1.3rem;overflow:hidden;flex-shrink:0;}
.logo-preview-circle img{width:100%;height:100%;object-fit:cover;border-radius:50%;}

/* ── EXPAND ── */
.expand-sec{display:none;background:var(--dark);border:1px solid var(--border);border-left:3px solid var(--green);border-radius:0 0 9px 9px;padding:.9rem;margin-top:-1px;}

/* ── WIN RATIO BAR ── */
.wr-bar{height:6px;border-radius:3px;background:var(--border);overflow:hidden;margin-top:.3rem;}
.wr-fill{height:100%;border-radius:3px;transition:width .5s;}

/* ── FIREBASE STATUS ── */
.fb-status{display:flex;align-items:center;gap:.4rem;font-size:.7rem;color:var(--muted);padding:.2rem .6rem;border-radius:20px;border:1px solid var(--border);margin-left:auto;}
.fb-dot{width:7px;height:7px;border-radius:50%;background:var(--muted);}
.fb-dot.connected{background:var(--green);box-shadow:0 0 6px var(--green);}

@media(max-width:600px){
  header{flex-direction:column;padding:.8rem;gap:.4rem;}
  nav{flex-wrap:wrap;justify-content:center;}
  .hero h1{font-size:2.2rem;}
  .fx-card{flex-direction:column;text-align:center;}
  .fx-info{text-align:center;}
}
</style>
</head>
<body>

<!-- LOADER -->
<div id="loader">
  <div class="spin"></div>
  <div style="font-family:'Bebas Neue';font-size:1.4rem;color:var(--green);letter-spacing:3px">JUVENILE LEAGUE</div>
  <div style="font-size:.75rem;color:var(--muted);margin-top:.3rem">Connecting to Firebase…</div>
</div>

<!-- LOGIN -->
<div class="moverlay hidden" id="loginModal">
  <div class="modal" style="width:min(92vw,360px)">
    <h2>⚽ Admin Login</h2>
    <p class="sub">Enter admin password.</p>
    <p class="merr" id="loginErr">❌ Wrong password.</p>
    <input type="password" id="adminPwd" placeholder="Password…" onkeydown="if(event.key==='Enter')doLogin()">
    <div class="mbtns">
      <button class="btn bg" onclick="doLogin()">Login</button>
      <button class="btn" style="background:var(--border);color:var(--text)" onclick="closeLogin()">Cancel</button>
    </div>
  </div>
</div>

<!-- TEAM DETAIL -->
<div class="moverlay hidden" id="teamModal">
  <div class="modal" id="teamModalContent"></div>
</div>

<header>
  <div class="logo-area">
    <div class="logo-icon">⚽</div>
    <div class="logo-text">Juvenile League<span>Official Portal</span></div>
  </div>
  <nav>
    <button class="active" onclick="go('home',this)">Home</button>
    <button onclick="go('teams',this)">Teams</button>
    <button onclick="go('fixtures',this)">Fixtures</button>
    <button onclick="go('points',this)">Points</button>
    <button onclick="go('ranking',this)">Rankings</button>
    <button class="admin-btn" id="adminNavBtn" onclick="handleAdmin()">⚙ Admin</button>
  </nav>
  <div class="fb-status" id="fbStatus"><div class="fb-dot" id="fbDot"></div><span id="fbTxt">Connecting</span></div>
</header>

<!-- SECTIONS -->
<div class="section active" id="section-home">
  <div class="hero">
    <h1>⚽ Juvenile League</h1>
    <p>The Official Portal for Youth Football Excellence</p>
    <div class="stats-row" id="heroStats"></div>
  </div>
  <h2 class="stitle">Latest Fixtures</h2>
  <div id="homeFixtures" style="display:flex;flex-direction:column;gap:.8rem;"></div>
</div>

<div class="section" id="section-teams">
  <h2 class="stitle">Team Database</h2>
  <div class="grid2" id="teamsGrid"></div>
</div>

<div class="section" id="section-fixtures">
  <h2 class="stitle">Fixtures &amp; Results</h2>
  <div class="tabs">
    <button class="tab active" onclick="filterFx('all',this)">All</button>
    <button class="tab" onclick="filterFx('upcoming',this)">Upcoming</button>
    <button class="tab" onclick="filterFx('played',this)">Played</button>
    <button class="tab" onclick="filterFx('live',this)">Live</button>
  </div>
  <div id="fxList" style="display:flex;flex-direction:column;gap:.8rem;"></div>
</div>

<div class="section" id="section-points">
  <h2 class="stitle">Points Table</h2>
  <div class="twrap">
    <table><thead><tr>
      <th>#</th><th>Team</th><th>P</th><th>W</th><th>D</th><th>L</th><th>WR%</th><th>GF</th><th>GA</th><th>GD</th><th>PTS</th><th>Form</th>
    </tr></thead><tbody id="ptBody"></tbody></table>
  </div>
</div>

<div class="section" id="section-ranking">
  <h2 class="stitle">Player Rankings</h2>
  <div class="rank-tabs">
    <button class="rtab active" onclick="showRank('total',this)">🏆 Points</button>
    <button class="rtab" onclick="showRank('goals',this)">⚽ Goals</button>
    <button class="rtab" onclick="showRank('motm',this)">👑 MOTM</button>
    <button class="rtab" onclick="showRank('cs',this)">🧤 Clean Sheets</button>
    <button class="rtab" onclick="showRank('wr',this)">📈 Win Ratio</button>
  </div>
  <div id="rankList" style="display:flex;flex-direction:column;gap:.6rem;"></div>
</div>

<div class="section" id="section-admin">
  <div class="admin-header">
    <div><div style="font-family:'Bebas Neue';font-size:1.4rem;color:var(--acc)">Admin Control Panel</div><div style="font-size:.76rem;color:var(--muted)">Full league management — Firebase synced</div></div>
    <span class="admin-badge">🔑 ADMIN</span>
    <button class="lbtn" style="margin-left:auto" onclick="adminLogout()">Logout</button>
  </div>
  <div class="atabs">
    <button class="atab active" onclick="showATab('teams',this)">Teams</button>
    <button class="atab" onclick="showATab('players',this)">Players</button>
    <button class="atab" onclick="showATab('fixtures',this)">Fixtures</button>
    <button class="atab" onclick="showATab('matches',this)">Matches</button>
    <button class="atab" onclick="showATab('standings',this)">Standings</button>
  </div>
  <div id="adminContent"><div style="color:var(--muted);padding:2rem;text-align:center">Loading Firebase data…</div></div>
</div>

<script>
// ══════════════════════════════════════════════════
// LOCAL STATE (mirrors Firebase)
// ══════════════════════════════════════════════════
var state = { teams:{}, players:{}, fixtures:{}, matches:{}, stats:{} };
var isAdmin = false;
var pendingMatch = null;
var currentRankType = 'total';
var currentFxFilter = 'all';
var fbReady = false;

// ── Firebase helpers ──────────────────────────────
function fb(){ return window._FB || null; }

window.fbTeams   = {};
window.fbPlayers = {};
window.fbFixtures= {};
window.fbMatches = {};
window.fbStats   = {};
window.fbManualStandings = {};

function rebuildLocal(){
  state.teams    = window.fbTeams;
  state.players  = window.fbPlayers;
  state.fixtures = window.fbFixtures;
  state.matches  = window.fbMatches;
  state.stats    = window.fbStats;
  state.manual_standings = window.fbManualStandings;
  if(!fbReady){
    fbReady = true;
    document.getElementById('fbDot').classList.add('connected');
    document.getElementById('fbTxt').textContent = 'Live';
    document.getElementById('loader').classList.add('gone');
    renderHome();
  } else {
    refreshCurrentSection();
  }
}

function refreshCurrentSection(){
  var active = document.querySelector('.section.active');
  if(!active) return;
  var id = active.id.replace('section-','');
  if(id==='home') renderHome();
  else if(id==='teams') renderTeams();
  else if(id==='fixtures') renderFxList(currentFxFilter);
  else if(id==='points') renderPoints();
  else if(id==='ranking') renderRank(currentRankType);
  else if(id==='admin') renderATab(document.querySelector('.atab.active')&&document.querySelector('.atab.active').textContent.toLowerCase().trim());
}

// ── Save helpers ──────────────────────────────────
function fsSet(col, id, data){
  var F=fb(); if(!F) return Promise.resolve();
  return F.setDoc(F.doc(F.db,col,String(id)), data);
}
function fsDel(col, id){
  var F=fb(); if(!F) return Promise.resolve();
  return F.deleteDoc(F.doc(F.db,col,String(id)));
}

// ══════════════════════════════════════════════════
// CONDITION SYSTEM
// ══════════════════════════════════════════════════
function getCondition(wr){
  if(wr>=80) return {label:'A+',cls:'cond-ap',boost:1.8,icon:'🔥'};
  if(wr>=70) return {label:'A', cls:'cond-a', boost:1.5,icon:'⚡'};
  if(wr>=60) return {label:'B+',cls:'cond-bp',boost:1.2,icon:'💪'};
  if(wr>=50) return {label:'B-',cls:'cond-bm',boost:1.1,icon:'👍'};
  if(wr>=40) return {label:'C', cls:'cond-c', boost:1.0,icon:'➖'};
  if(wr>=30) return {label:'D', cls:'cond-d', boost:-1.2,icon:'📉'};
  return       {label:'E', cls:'cond-e', boost:-1.5,icon:'💀'};
}
function condBadge(wr){
  var c=getCondition(wr);
  return '<span class="cond '+c.cls+'">'+c.icon+' '+c.label+'</span>';
}

// ══════════════════════════════════════════════════
// POINTS FORMULA  Win=10 Loss=-10 Draw=5 GD×1 MOTM×5 × conditionBoost
// ══════════════════════════════════════════════════
function calcPts(s){
  if(!s) return 0;
  var w=s.wins||0, l=s.losses||0, d=s.draws||0;
  var total=w+l+d;
  var wr=total>0?Math.round((w/total)*100):0;
  var cond=getCondition(wr);
  var raw = w*10 + l*(-10) + d*5 + (s.gd||0)*1 + (s.motm||0)*5;
  var boosted = Math.round(raw * (cond.boost > 0 ? cond.boost : 1));
  return boosted + (cond.boost < 0 ? Math.round(raw * cond.boost) - raw : 0);
}
function realCalcPts(s){
  if(!s) return 0;
  var w=s.wins||0,l=s.losses||0,d=s.draws||0;
  var total=w+l+d;
  var wr=total>0?Math.round((w/total)*100):0;
  var cond=getCondition(wr);
  var raw=w*10+l*(-10)+d*5+(s.gd||0)+(s.motm||0)*5;
  if(cond.boost>0) return Math.round(raw*cond.boost);
  return Math.round(raw+raw*Math.abs(cond.boost)*-1);
}
function winRatio(s){
  if(!s) return 0;
  var t=(s.wins||0)+(s.losses||0)+(s.draws||0);
  return t>0?Math.round(((s.wins||0)/t)*100):0;
}

// ══════════════════════════════════════════════════
// HELPERS
// ══════════════════════════════════════════════════
function esc(s){ return String(s||'').replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;'); }
function uid(){ return Date.now().toString(36)+Math.random().toString(36).slice(2); }

function teamLogoEl(t,size){
  size=size||28;
  var src=t&&(t.logoUrl||t.logo);
  if(src&&src.startsWith('http')){
    return '<div class="mini-logo" style="width:'+size+'px;height:'+size+'px;"><img src="'+esc(src)+'" onerror="this.parentNode.innerHTML=\'⚽\'"></div>';
  }
  var em=src||'⚽';
  return '<div class="mini-logo" style="width:'+size+'px;height:'+size+'px;font-size:'+(size*.48)+'px;">'+em+'</div>';
}
function playerPhotoEl(p,size){
  size=size||34;
  var src=p&&(p.photoUrl||p.photo);
  if(src&&src.startsWith('http')){
    return '<div class="p-photo" style="width:'+size+'px;height:'+size+'px;"><img src="'+esc(src)+'" onerror="this.parentNode.innerHTML=\'👤\'"></div>';
  }
  return '<div class="p-photo" style="width:'+size+'px;height:'+size+'px;font-size:'+(size*.42)+'px;">👤</div>';
}
function teamBigLogo(t){
  var src=t&&(t.logoUrl||t.logo);
  if(src&&src.startsWith('http')){
    return '<div class="t-logo-wrap" style="width:56px;height:56px;border-color:'+(t.color||'var(--border)')+'"><img src="'+esc(src)+'" onerror="this.innerHTML=\'⚽\'"></div>';
  }
  return '<div class="t-logo-wrap" style="width:56px;height:56px;font-size:1.6rem;border-color:'+(t.color||'var(--border)')+'">'+esc(src||'⚽')+'</div>';
}

function getTeams(){ return Object.values(state.teams); }
function getPlayers(){ return Object.values(state.players); }
function getFixtures(){ return Object.values(state.fixtures).sort(function(a,b){return (a.date||'').localeCompare(b.date||'');}).reverse(); }
function getMatches(){ return Object.values(state.matches); }
function getStat(pid){ return state.stats[pid]||{wins:0,losses:0,draws:0,gd:0,goals:0,cs:0,motm:0}; }
function getTeamById(id){ return state.teams[id]||null; }
function getPlayersByTeam(tid){ return getPlayers().filter(function(p){return p.teamId===tid;}); }

// ══════════════════════════════════════════════════
// NAV
// ══════════════════════════════════════════════════
function go(sec,btn){
  document.querySelectorAll('.section').forEach(function(s){s.classList.remove('active');});
  document.querySelectorAll('nav button').forEach(function(b){b.classList.remove('active');});
  document.getElementById('section-'+sec).classList.add('active');
  if(btn) btn.classList.add('active');
  if(sec==='home') renderHome();
  if(sec==='teams') renderTeams();
  if(sec==='fixtures'){ currentFxFilter='all'; renderFxList('all'); }
  if(sec==='points') renderPoints();
  if(sec==='ranking') renderRank(currentRankType);
  if(sec==='admin') renderATab('teams');
}
function handleAdmin(){
  if(isAdmin){ go('admin',document.getElementById('adminNavBtn')); document.getElementById('adminNavBtn').classList.add('aa'); }
  else{
    document.getElementById('loginModal').classList.remove('hidden');
    document.getElementById('adminPwd').value='';
    document.getElementById('loginErr').style.display='none';
    setTimeout(function(){document.getElementById('adminPwd').focus();},50);
  }
}
function doLogin(){
  if(document.getElementById('adminPwd').value==='fardous'){
    isAdmin=true;
    document.getElementById('loginModal').classList.add('hidden');
    document.getElementById('adminNavBtn').classList.add('aa');
    document.querySelectorAll('nav button').forEach(function(b){b.classList.remove('active');});
    go('admin',document.getElementById('adminNavBtn'));
  } else { document.getElementById('loginErr').style.display='block'; }
}
function closeLogin(){ document.getElementById('loginModal').classList.add('hidden'); }
function adminLogout(){
  isAdmin=false;
  document.getElementById('adminNavBtn').classList.remove('aa');
  go('home',document.querySelector('nav button'));
}
function toggle(id){
  var el=document.getElementById(id);
  if(el) el.style.display=(el.style.display==='none'||el.style.display==='')?'block':'none';
}

// ══════════════════════════════════════════════════
// LOGO UPLOAD HELPER
// ══════════════════════════════════════════════════
function buildLogoInput(idPrefix, label){
  label=label||'Logo';
  return '<div class="fg" style="grid-column:span 2"><label>'+label+' (URL or Upload)</label>'+
    '<div class="logo-input-group">'+
    '<div class="logo-tabs-sm">'+
    '<span class="logo-tab-sm active" onclick="switchLogoTab(\'url\',\''+idPrefix+'\',this)">🔗 URL</span>'+
    '<span class="logo-tab-sm" onclick="switchLogoTab(\'upload\',\''+idPrefix+'\',this)">📤 Upload</span>'+
    '</div>'+
    '<div id="'+idPrefix+'_url_wrap">'+
    '<input id="'+idPrefix+'_url" placeholder="https://… or emoji like 🦅" oninput="previewLogo(\''+idPrefix+'\')">'+
    '</div>'+
    '<div id="'+idPrefix+'_upload_wrap" style="display:none">'+
    '<input id="'+idPrefix+'_file" type="file" accept="image/*" style="padding:.3rem" onchange="previewLogoFile(\''+idPrefix+'\')">'+
    '</div>'+
    '<div style="display:flex;align-items:center;gap:.7rem;margin-top:.4rem">'+
    '<div class="logo-preview-circle" id="'+idPrefix+'_prev">⚽</div>'+
    '<div style="font-size:.7rem;color:var(--muted)">Preview</div></div>'+
    '</div></div>';
}
function switchLogoTab(type, prefix, btn){
  document.querySelectorAll('[id^="'+prefix+'_url_wrap"],[id^="'+prefix+'_upload_wrap"]').forEach(function(el){el.style.display='none';});
  document.getElementById(prefix+'_'+type+'_wrap').style.display='block';
  if(btn&&btn.parentNode) btn.parentNode.querySelectorAll('.logo-tab-sm').forEach(function(b){b.classList.remove('active');});
  if(btn) btn.classList.add('active');
}
function previewLogo(prefix){
  var val=document.getElementById(prefix+'_url').value.trim();
  var prev=document.getElementById(prefix+'_prev'); if(!prev) return;
  if(val.startsWith('http')){ prev.innerHTML='<img src="'+esc(val)+'" style="width:100%;height:100%;object-fit:cover;border-radius:50%;" onerror="this.parentNode.innerHTML=\'❌\'">'; }
  else { prev.innerHTML=val||'⚽'; }
}
function previewLogoFile(prefix){
  var inp=document.getElementById(prefix+'_file');
  var prev=document.getElementById(prefix+'_prev'); if(!prev||!inp||!inp.files[0]) return;
  var r=new FileReader(); r.onload=function(e){ prev.innerHTML='<img src="'+e.target.result+'" style="width:100%;height:100%;object-fit:cover;border-radius:50%;">'; }; r.readAsDataURL(inp.files[0]);
}
async function resolveLogoUrl(prefix){
  // Returns the final URL string
  var urlInp=document.getElementById(prefix+'_url');
  var fileInp=document.getElementById(prefix+'_file');
  var F=fb();
  if(fileInp&&fileInp.files&&fileInp.files[0]&&F){
    try{
      var file=fileInp.files[0];
      var storRef=F.ref(F.storage,'logos/'+uid()+'_'+file.name);
      await F.uploadBytes(storRef,file);
      return await F.getDownloadURL(storRef);
    }catch(e){ console.warn('Upload failed',e); }
  }
  if(urlInp) return urlInp.value.trim();
  return '';
}
// Same for player photos
function buildPhotoInput(idPrefix){
  return '<div class="fg" style="grid-column:span 2"><label>Photo (optional — URL or Upload)</label>'+
    '<div class="logo-input-group">'+
    '<div class="logo-tabs-sm">'+
    '<span class="logo-tab-sm active" onclick="switchLogoTab(\'url\',\''+idPrefix+'\',this)">🔗 URL</span>'+
    '<span class="logo-tab-sm" onclick="switchLogoTab(\'upload\',\''+idPrefix+'\',this)">📤 Upload</span>'+
    '</div>'+
    '<div id="'+idPrefix+'_url_wrap"><input id="'+idPrefix+'_url" placeholder="https://…"></div>'+
    '<div id="'+idPrefix+'_upload_wrap" style="display:none"><input id="'+idPrefix+'_file" type="file" accept="image/*" style="padding:.3rem" onchange="previewLogoFile(\''+idPrefix+'\')"></div>'+
    '<div style="display:flex;align-items:center;gap:.7rem;margin-top:.4rem">'+
    '<div class="logo-preview-circle" id="'+idPrefix+'_prev">👤</div>'+
    '<div style="font-size:.7rem;color:var(--muted)">Preview</div></div>'+
    '</div></div>';
}
async function resolvePhotoUrl(prefix){
  var urlInp=document.getElementById(prefix+'_url');
  var fileInp=document.getElementById(prefix+'_file');
  var F=fb();
  if(fileInp&&fileInp.files&&fileInp.files[0]&&F){
    try{
      var file=fileInp.files[0];
      var storRef=F.ref(F.storage,'photos/'+uid()+'_'+file.name);
      await F.uploadBytes(storRef,file);
      return await F.getDownloadURL(storRef);
    }catch(e){ console.warn('Upload failed',e); }
  }
  if(urlInp) return urlInp.value.trim();
  return '';
}

// ══════════════════════════════════════════════════
// HOME
// ══════════════════════════════════════════════════
function renderHome(){
  var teams=getTeams(), players=getPlayers();
  var played=getFixtures().filter(function(f){return f.status==='played';}).length;
  var up=getFixtures().filter(function(f){return f.status==='upcoming';}).length;
  document.getElementById('heroStats').innerHTML=
    '<div class="stat-box"><div class="num">'+teams.length+'</div><div class="lbl">Teams</div></div>'+
    '<div class="stat-box"><div class="num">'+players.length+'</div><div class="lbl">Players</div></div>'+
    '<div class="stat-box"><div class="num">'+played+'</div><div class="lbl">Played</div></div>'+
    '<div class="stat-box"><div class="num">'+up+'</div><div class="lbl">Upcoming</div></div>';
  document.getElementById('homeFixtures').innerHTML=getFixtures().slice(0,4).map(fxHTML).join('');
}

// ══════════════════════════════════════════════════
// TEAMS (viewer)
// ══════════════════════════════════════════════════
function getBestPlayer(tid){
  var ps=getPlayersByTeam(tid);
  if(!ps.length) return null;
  return ps.slice().sort(function(a,b){ return realCalcPts(getStat(b.id))-realCalcPts(getStat(a.id)); })[0];
}
function renderTeams(){
  var teams=getTeams();
  if(!teams.length){document.getElementById('teamsGrid').innerHTML='<p style="color:var(--muted)">No teams yet.</p>';return;}
  document.getElementById('teamsGrid').innerHTML=teams.map(function(t){
    var ps=getPlayersByTeam(t.id);
    var local=ps.filter(function(p){return p.cat==='local';});
    var youth=ps.filter(function(p){return p.cat==='youth';});
    var inv=ps.filter(function(p){return p.cat==='invited';});
    var best=getBestPlayer(t.id);
    var bestH='';
    if(best){
      var bs=getStat(best.id); var wr=winRatio(bs);
      bestH='<div class="best-card">'+playerPhotoEl(best,38)+
        '<div><div style="font-size:.64rem;color:var(--acc);font-weight:700;letter-spacing:1px">👑 BEST PLAYER</div>'+
        '<div style="font-weight:700;font-size:.88rem">'+esc(best.name)+'</div>'+
        '<div style="display:flex;gap:.3rem;align-items:center;margin-top:.2rem">'+condBadge(wr)+'<span style="font-size:.7rem;color:var(--muted)">'+realCalcPts(bs)+' pts</span></div></div></div>';
    }
    return '<div class="card" onclick="showTeamDetail(\''+t.id+'\')">'+
      '<div style="display:flex;align-items:center;gap:.8rem;margin-bottom:.8rem">'+
      teamBigLogo(t)+
      '<div><div style="font-family:\'Barlow Condensed\';font-size:1.15rem;font-weight:700">'+esc(t.name)+'</div>'+
      '<div style="font-size:.74rem;color:var(--muted)">👔 '+esc(t.president||'—')+'</div></div></div>'+
      '<div style="display:flex;gap:.4rem;flex-wrap:wrap;margin-bottom:.7rem">'+
      '<span class="badge bl">Local: '+local.length+'</span>'+
      '<span class="badge by">Youth: '+youth.length+'</span>'+
      '<span class="badge br">Invited: '+inv.length+'</span></div>'+
      bestH+'</div>';
  }).join('');
}

function showTeamDetail(tid){
  var t=getTeamById(tid); if(!t) return;
  var rows=calcStandings(); var tp=rows.find(function(r){return r.id===tid;})||{};
  var ps=getPlayersByTeam(tid);
  var best=getBestPlayer(tid);
  var catGrps={local:[],youth:[],invited:[]};
  ps.forEach(function(p){ (catGrps[p.cat]||catGrps.local).push(p); });
  var sq='';
  var catLabels={local:'🟢 Local Players',youth:'🟡 Youth Players',invited:'🔴 Invited Players'};
  Object.keys(catGrps).forEach(function(c){
    var arr=catGrps[c]; if(!arr.length) return;
    sq+='<div style="font-family:\'Barlow Condensed\';font-size:.78rem;color:var(--muted);text-transform:uppercase;letter-spacing:1px;margin:.7rem 0 .3rem">'+catLabels[c]+' ('+arr.length+')</div>';
    arr.forEach(function(p){
      var s=getStat(p.id); var wr=winRatio(s);
      sq+='<div style="display:flex;align-items:center;gap:.5rem;padding:.35rem 0;border-bottom:1px solid var(--border);font-size:.84rem">'+
        playerPhotoEl(p,28)+'<span style="flex:1">'+esc(p.name)+'</span>'+
        condBadge(wr)+
        '<span style="font-size:.7rem;color:var(--green);margin-left:.3rem">'+realCalcPts(s)+'pts</span></div>';
    });
  });
  var bestH='';
  if(best){ var bs2=getStat(best.id); var wr2=winRatio(bs2);
    bestH='<div class="best-card" style="margin-bottom:.8rem">'+playerPhotoEl(best,44)+
      '<div><div style="font-size:.64rem;color:var(--acc);font-weight:700;letter-spacing:1px">👑 BEST PLAYER</div>'+
      '<div style="font-weight:700">'+esc(best.name)+'</div>'+
      condBadge(wr2)+'<span style="font-size:.7rem;color:var(--muted);margin-left:.4rem">'+realCalcPts(bs2)+' ranking pts</span></div></div>'; }
  document.getElementById('teamModalContent').innerHTML=
    '<div style="display:flex;align-items:center;gap:.9rem;margin-bottom:1rem">'+
    teamBigLogo(t)+
    '<div style="flex:1"><div style="font-family:\'Bebas Neue\';font-size:1.8rem;color:var(--green)">'+esc(t.name)+'</div>'+
    '<div style="font-size:.78rem;color:var(--muted)">👔 '+esc(t.president||'—')+'</div></div>'+
    '<button onclick="document.getElementById(\'teamModal\').classList.add(\'hidden\')" style="background:none;border:none;color:var(--muted);font-size:1.4rem;cursor:pointer;padding:.2rem .5rem">✕</button></div>'+
    '<div style="display:flex;gap:.7rem;flex-wrap:wrap;margin-bottom:1rem">'+
    '<div class="stat-box" style="padding:.6rem 1rem"><div class="num">'+(tp.pts||0)+'</div><div class="lbl">Pts</div></div>'+
    '<div class="stat-box" style="padding:.6rem 1rem"><div class="num">'+(tp.w||0)+'</div><div class="lbl">Wins</div></div>'+
    '<div class="stat-box" style="padding:.6rem 1rem"><div class="num">'+(tp.d||0)+'</div><div class="lbl">Draws</div></div>'+
    '<div class="stat-box" style="padding:.6rem 1rem"><div class="num">'+(tp.l||0)+'</div><div class="lbl">Loss</div></div>'+
    '<div class="stat-box" style="padding:.6rem 1rem"><div class="num">'+(tp.wr||0)+'%</div><div class="lbl">Win %</div></div></div>'+
    bestH+'<div style="font-family:\'Barlow Condensed\';font-size:.84rem;font-weight:700;color:var(--text);text-transform:uppercase;letter-spacing:1px">Squad — '+ps.length+' Players</div>'+sq;
  document.getElementById('teamModal').classList.remove('hidden');
}

// ══════════════════════════════════════════════════
// FIXTURES (viewer)
// ══════════════════════════════════════════════════
function teamTlEl(t){
  if(!t) return '<div class="tl">⚽</div>';
  var src=t.logoUrl||t.logo;
  if(src&&src.startsWith('http')) return '<div class="tl"><img src="'+esc(src)+'" onerror="this.parentNode.innerHTML=\'⚽\'"></div>';
  return '<div class="tl">'+(src||'⚽')+'</div>';
}
function fxHTML(f){
  var ht=getTeamById(f.home), at=getTeamById(f.away);
  if(!ht||!at) return '';
  var sc=(f.homeScore!=null)?'<div class="score-txt">'+f.homeScore+' — '+f.awayScore+'</div>':'<div class="vs-txt">VS</div>';
  var scls=f.status==='played'?'s-pl':f.status==='live'?'s-lv':'s-up';
  return '<div class="fx-card">'+
    '<div class="vs-block">'+
    '<div class="t-side">'+teamTlEl(ht)+'<div class="tn">'+esc(ht.name)+'</div></div>'+
    sc+
    '<div class="t-side">'+teamTlEl(at)+'<div class="tn">'+esc(at.name)+'</div></div></div>'+
    '<div class="fx-info">'+
    '<div style="font-size:.82rem;color:var(--acc);font-weight:600">📅 '+esc(f.date||'TBD')+'</div>'+
    (f.round?'<div style="font-size:.7rem;color:var(--green);font-weight:700">'+esc(f.round)+'</div>':'')+
    '<div style="font-size:.72rem;color:var(--muted)">📍 '+esc(f.venue||'TBD')+'</div>'+
    '<div style="margin-top:.35rem"><span class="sbadge '+scls+'">'+(f.status==='live'?'🔴 LIVE':f.status.toUpperCase())+'</span></div></div></div>';
}
function renderFxList(filter){
  currentFxFilter=filter;
  var list=getFixtures().filter(function(f){return filter==='all'||f.status===filter;});
  document.getElementById('fxList').innerHTML=list.length?list.map(fxHTML).join(''):'<p style="color:var(--muted)">No fixtures.</p>';
}
function filterFx(c,btn){
  document.querySelectorAll('#section-fixtures .tab').forEach(function(b){b.classList.remove('active');});
  btn.classList.add('active'); renderFxList(c);
}

// ══════════════════════════════════════════════════
// POINTS TABLE
// ══════════════════════════════════════════════════
function calcStandings(){
  var map={};
  getTeams().forEach(function(t){ map[t.id]={id:t.id,name:t.name,logo:t.logoUrl||t.logo,p:0,w:0,d:0,l:0,gf:0,ga:0,pts:0,form:[]}; });
  getFixtures().filter(function(f){return f.status==='played'&&f.homeScore!=null;}).forEach(function(f){
    var h=map[f.home],a=map[f.away]; if(!h||!a) return;
    h.p++;a.p++;h.gf+=f.homeScore;h.ga+=f.awayScore;a.gf+=f.awayScore;a.ga+=f.homeScore;
    if(f.homeScore>f.awayScore){h.w++;h.pts+=3;h.form.push('fw');a.l++;a.form.push('ld');}
    else if(f.homeScore<f.awayScore){a.w++;a.pts+=3;a.form.push('fw');h.l++;h.form.push('ld');}
    else{h.d++;h.pts+=1;h.form.push('dr');a.d++;a.pts+=1;a.form.push('dr');}
  });
  // Apply manual overrides
  var ms=state.manual_standings||{};
  Object.keys(ms).forEach(function(tid){
    if(!map[tid]) return;
    var ov=ms[tid];
    if(ov.pointsOverride!=null){
      // Points-only override from scorecard paste
      map[tid].pts=ov.pointsOverride;
    } else {
      // Full W/D/L/GF/GA override
      if(ov.w!=null) map[tid].w=ov.w;
      if(ov.d!=null) map[tid].d=ov.d;
      if(ov.l!=null) map[tid].l=ov.l;
      if(ov.gf!=null) map[tid].gf=ov.gf;
      if(ov.ga!=null) map[tid].ga=ov.ga;
      map[tid].p=map[tid].w+map[tid].d+map[tid].l;
      map[tid].pts=map[tid].w*3+map[tid].d;
    }
  });
  return Object.values(map).map(function(r){
    var total=r.w+r.l+r.d; r.wr=total>0?Math.round((r.w/total)*100):0; return r;
  }).sort(function(a,b){ return b.pts-a.pts||(b.gf-b.ga)-(a.gf-a.ga); });
}
function renderPoints(){
  var rows=calcStandings().filter(function(r){
    // hide teams flagged as hidden in manual_standings
    var ms=state.manual_standings&&state.manual_standings[r.id];
    return !(ms&&ms.hidden);
  });
  document.getElementById('ptBody').innerHTML=rows.map(function(r,i){
    var col=i===0?'var(--acc)':i<3?'var(--green)':'var(--muted)';
    var logoSrc=r.logo;
    var logoHtml=logoSrc&&logoSrc.startsWith('http')?
      '<div class="mini-logo" style="width:26px;height:26px;"><img src="'+esc(logoSrc)+'" onerror="this.parentNode.innerHTML=\'⚽\'"></div>':
      '<div class="mini-logo" style="width:26px;height:26px;font-size:.9rem;">'+(logoSrc||'⚽')+'</div>';
    var wrColor=r.wr>=60?'var(--green)':r.wr>=40?'var(--acc)':'var(--red)';
    return '<tr><td><span class="pos-num" style="color:'+col+'">'+(i+1)+'</span></td>'+
      '<td><div class="team-cell">'+logoHtml+esc(r.name)+'</div></td>'+
      '<td>'+r.p+'</td><td>'+r.w+'</td><td>'+r.d+'</td><td>'+r.l+'</td>'+
      '<td><span style="color:'+wrColor+';font-weight:700">'+r.wr+'%</span></td>'+
      '<td>'+r.gf+'</td><td>'+r.ga+'</td><td>'+(r.gf-r.ga>=0?'+':'')+(r.gf-r.ga)+'</td>'+
      '<td><span class="pts-val">'+r.pts+'</span></td>'+
      '<td><div class="form-dots">'+r.form.slice(-5).map(function(c){return '<div class="fd '+c+'"></div>';}).join('')+'</div></td></tr>';
  }).join('');
}

// Condition circle (CSS circle with text, no emoji)
function condCircle(wr, size){
  size=size||36;
  var c=getCondition(wr);
  var bgMap={
    'cond-ap':'linear-gradient(135deg,#FFD700,#FFA500)',
    'cond-a':'linear-gradient(135deg,#00C853,#009624)',
    'cond-bp':'linear-gradient(135deg,#2979FF,#1565C0)',
    'cond-bm':'linear-gradient(135deg,#4FC3F7,#0288D1)',
    'cond-c':'linear-gradient(135deg,#78909C,#546E7A)',
    'cond-d':'linear-gradient(135deg,#FF6D00,#E65100)',
    'cond-e':'linear-gradient(135deg,#FF3D3D,#B71C1C)'
  };
  var bg=bgMap[c.cls]||'linear-gradient(135deg,#78909C,#546E7A)';
  return '<div style="width:'+size+'px;height:'+size+'px;border-radius:50%;background:'+bg+';display:flex;align-items:center;justify-content:center;flex-shrink:0;box-shadow:0 2px 8px rgba(0,0,0,.4)">'+
    '<span style="font-family:\'Barlow Condensed\';font-weight:900;font-size:'+(size*.32)+'px;color:#fff;letter-spacing:-.5px">'+c.label+'</span>'+
    '</div>';
}
// Get player match history from saved matches (last 5)
function getPlayerMatchHistory(pid){
  var history=[];
  getMatches().forEach(function(m){
    var allResults=(m.homeResults||[]).concat(m.awayResults||[]);
    allResults.forEach(function(r){
      if(r.playerId===pid){
        var isHome=(m.homeResults||[]).some(function(hr){return hr.playerId===pid;});
        var playerSideScore=isHome?m.homeScore:m.awayScore;
        var oppSideScore=isHome?m.awayScore:m.homeScore;
        var result=playerSideScore>oppSideScore?'W':playerSideScore<oppSideScore?'L':'D';
        var htName=m.detHomeName||'Home', atName=m.detAwayName||'Away';
        history.push({result:result,ts:r.ts,os:r.os,motm:(m.motmId===pid),round:m.round||'',htName:htName,atName:atName,isHome:isHome,matchId:m.id,playerName:r.playerName||''});
      }
    });
  });
  return history.slice(-5).reverse();
}
function formCircle(result){
  var cfg={
    W:{bg:'linear-gradient(135deg,#00C853,#009624)',txt:'W'},
    L:{bg:'linear-gradient(135deg,#FF3D3D,#B71C1C)',txt:'L'},
    D:{bg:'linear-gradient(135deg,#FFD600,#F57F17)',txt:'D'}
  };
  var c=cfg[result]||cfg.D;
  return '<div style="width:20px;height:20px;border-radius:50%;background:'+c.bg+';display:flex;align-items:center;justify-content:center;flex-shrink:0">'+
    '<span style="font-family:\'Barlow Condensed\';font-weight:900;font-size:10px;color:#fff">'+c.txt+'</span></div>';
}

function renderRank(type){
  currentRankType=type;
  var ps=getPlayers();
  ps.sort(function(a,b){
    var sa=getStat(a.id), sb=getStat(b.id);
    if(type==='total') return realCalcPts(sb)-realCalcPts(sa);
    if(type==='goals') return (sb.goals||0)-(sa.goals||0);
    if(type==='motm') return (sb.motm||0)-(sa.motm||0);
    if(type==='cs') return (sb.cs||0)-(sa.cs||0);
    if(type==='wr') return winRatio(sb)-winRatio(sa);
    return 0;
  });
  var medals={0:'linear-gradient(135deg,#FFD700,#FFA000)',1:'linear-gradient(135deg,#C0C0C0,#9E9E9E)',2:'linear-gradient(135deg,#CD7F32,#8D4E1A)'};
  document.getElementById('rankList').innerHTML=ps.map(function(p,i){
    var t=getTeamById(p.teamId);
    var s=getStat(p.id);
    var wr=winRatio(s);
    var mp=(s.mp||0)||(s.wins||0)+(s.draws||0)+(s.losses||0);
    var pts=realCalcPts(s);
    var history=getPlayerMatchHistory(p.id);
    var rankNumBg=medals[i]||'var(--card2)';
    var rankNumColor=i<3?'#000':'var(--muted)';
    // form circles (last 5, no emoji)
    var formHtml='<div style="display:flex;gap:.25rem;align-items:center">';
    if(history.length===0){formHtml+='<span style="font-size:.65rem;color:var(--muted)">No matches</span>';}
    else{history.forEach(function(h){formHtml+=formCircle(h.result);});}
    formHtml+='</div>';
    // condition — only show boost label if 3+ matches played
    var condHtml='';
    if(mp>=3){ condHtml=condCircle(wr,32); }
    else { condHtml='<div style="width:32px;height:32px;border-radius:50%;background:var(--card2);border:1px dashed var(--border);display:flex;align-items:center;justify-content:center;flex-shrink:0"><span style="font-size:8px;color:var(--muted);text-align:center;line-height:1">NEW</span></div>'; }
    return '<div style="background:var(--card);border:1px solid var(--border);border-radius:14px;padding:.9rem 1rem;margin-bottom:.7rem">'+
      // Top row: rank + photo + name + condition + pts
      '<div style="display:flex;align-items:center;gap:.7rem">'+
      '<div style="width:28px;height:28px;border-radius:50%;background:'+rankNumBg+';display:flex;align-items:center;justify-content:center;flex-shrink:0">'+
        '<span style="font-family:\'Bebas Neue\';font-size:.95rem;color:'+rankNumColor+'">'+(i+1)+'</span>'+
      '</div>'+
      playerPhotoEl(p,40)+
      '<div style="flex:1;min-width:0">'+
        '<div style="font-weight:700;font-size:.95rem;white-space:nowrap;overflow:hidden;text-overflow:ellipsis">'+esc(p.name)+'</div>'+
        '<div style="font-size:.72rem;color:var(--muted)">'+(t?esc(t.name):'')+' · '+esc(p.cat||'')+'</div>'+
      '</div>'+
      condHtml+
      '<div style="text-align:right;flex-shrink:0">'+
        '<div style="font-family:\'Bebas Neue\';font-size:1.7rem;color:var(--green);line-height:1">'+pts+'</div>'+
        '<div style="font-size:.62rem;color:var(--muted);text-transform:uppercase">Pts</div>'+
      '</div>'+
      '</div>'+
      // Stats grid
      '<div style="display:grid;grid-template-columns:repeat(6,1fr);gap:.3rem;margin-top:.7rem;background:var(--card2);border-radius:8px;padding:.5rem .4rem">'+
      statCell('MP', mp)+
      statCell('W', s.wins||0)+
      statCell('WR', wr+'%')+
      statCell('GF', s.gf||s.goals||0)+
      statCell('MOTM', s.motm||0)+
      statCell('PTS', pts)+
      '</div>'+
      // Form row
      '<div style="display:flex;align-items:center;gap:.5rem;margin-top:.55rem">'+
      '<span style="font-size:.65rem;color:var(--muted);text-transform:uppercase;letter-spacing:.5px;width:34px">Form</span>'+
      formHtml+
      '</div>'+
      // Match history
      (history.length?'<div style="margin-top:.5rem;display:flex;flex-direction:column;gap:.2rem">'+
        history.slice(0,3).map(function(h){
          var rCol=h.result==='W'?'var(--green)':h.result==='L'?'var(--red)':'var(--acc)';
          return '<div style="display:flex;align-items:center;gap:.4rem;font-size:.72rem;color:var(--muted)">'+
            formCircle(h.result)+
            '<span style="color:var(--text);font-weight:600">'+esc(h.isHome?h.htName:h.atName)+'</span>'+
            '<span style="color:var(--muted)">vs</span>'+
            '<span>'+esc(h.isHome?h.atName:h.htName)+'</span>'+
            '<span style="color:'+rCol+';font-weight:700;margin-left:auto">'+h.ts+' — '+h.os+'</span>'+
            (h.motm?'<span style="font-size:.6rem;color:var(--acc);font-weight:900;margin-left:.3rem">MOTM</span>':'')+
            '</div>';
        }).join('')+
      '</div>':'')
      +'</div>';
  }).join('');
}
function statCell(lbl,val){
  return '<div style="text-align:center">'+
    '<div style="font-family:\'Bebas Neue\';font-size:1.1rem;color:var(--text);line-height:1.1">'+val+'</div>'+
    '<div style="font-size:.58rem;color:var(--muted);text-transform:uppercase;letter-spacing:.4px">'+lbl+'</div>'+
    '</div>';
}
function showRank(t,btn){ document.querySelectorAll('.rtab').forEach(function(b){b.classList.remove('active');}); btn.classList.add('active'); renderRank(t); }

// ══════════════════════════════════════════════════
// ADMIN TABS
// ══════════════════════════════════════════════════
function showATab(tab,btn){
  document.querySelectorAll('.atab').forEach(function(b){b.classList.remove('active');});
  if(btn) btn.classList.add('active');
  renderATab(tab);
}
function renderATab(tab){
  var el=document.getElementById('adminContent');
  if(!el) return;
  if(tab==='teams'||tab==='⚙ admin'||tab==='') el.innerHTML=aTeamsHTML();
  else if(tab==='players') el.innerHTML=aPlayersHTML();
  else if(tab==='fixtures') el.innerHTML=aFixturesHTML();
  else if(tab==='matches') el.innerHTML=aMatchesHTML();
  else if(tab==='standings') el.innerHTML=aStandingsHTML();
  else el.innerHTML=aTeamsHTML();
}

// ══════════════════════════════════════════════════
// ADMIN — TEAMS
// ══════════════════════════════════════════════════
function aTeamsHTML(){
  var teams=getTeams();
  return '<div class="apanel"><h3>➕ Register New Team</h3>'+
    '<div class="fgrid">'+
    '<div class="fg"><label>Team Name *</label><input id="nt_nm" placeholder="e.g. Fire Wolves"></div>'+
    '<div class="fg"><label>President / Manager</label><input id="nt_pr" placeholder="Full name"></div>'+
    '<div class="fg"><label>Team Color</label><input id="nt_cl" type="color" value="#00C853"></div>'+
    buildLogoInput('nt_lg','Team Logo')+
    '</div>'+
    '<div style="margin-top:.9rem"><button class="btn bg" onclick="addTeamAsync()">Register Team</button></div></div>'+
    '<div class="apanel"><h3>📋 All Teams ('+teams.length+')</h3>'+
    '<div class="alist">'+teams.map(function(t){
      var ps=getPlayersByTeam(t.id);
      return '<div><div class="aitem">'+
        teamLogoEl(t,32)+
        '<div class="ai"><div class="an">'+esc(t.name)+'</div><div class="am">👔 '+esc(t.president||'—')+' | '+ps.length+' players</div></div>'+
        '<button class="btn" style="background:rgba(0,200,83,.1);color:var(--green);border:1px solid rgba(0,200,83,.3);font-size:.72rem;padding:.27rem .6rem" onclick="openEditTeam(\''+t.id+'\')">Edit</button>'+
        '<button class="btn" style="background:rgba(41,121,255,.1);color:#6AB0FF;border:1px solid rgba(41,121,255,.3);font-size:.72rem;padding:.27rem .6rem" onclick="toggle(\'pf_'+t.id+'\')">+ Player</button>'+
        '<button class="bd" onclick="deleteTeamAsync(\''+t.id+'\')">Delete</button></div>'+
        '<div id="pf_'+t.id+'" class="expand-sec">'+
        '<div style="font-size:.78rem;color:var(--green);font-weight:700;margin-bottom:.8rem;text-transform:uppercase;letter-spacing:1px">Add Players to '+esc(t.name)+'</div>'+
        // BULK paste section
        '<div style="background:rgba(0,200,83,.04);border:1px solid rgba(0,200,83,.15);border-radius:8px;padding:.8rem;margin-bottom:.8rem">'+
        '<div style="font-size:.72rem;color:var(--acc);font-weight:700;margin-bottom:.4rem;text-transform:uppercase;letter-spacing:1px">📋 Bulk Add (paste multiple names)</div>'+
        '<div style="font-size:.7rem;color:var(--muted);margin-bottom:.5rem">One player per line. Format: <code style="color:var(--green);background:rgba(0,200,83,.1);padding:.1rem .3rem;border-radius:3px">Name, category</code> — category optional (local/youth/invited)</div>'+
        '<textarea id="bulk_'+t.id+'" style="width:100%;background:var(--dark);border:1px solid var(--border);border-radius:8px;padding:.55rem .75rem;color:var(--text);font-family:monospace;font-size:.8rem;resize:vertical;min-height:100px;outline:none;" placeholder="Ahmed Ali, local&#10;Tariq Nasser, invited&#10;Karim Said, youth&#10;Yusuf Nour&#10;Hassan Omar"></textarea>'+
        '<button class="btn bg" onclick="bulkAddPlayersAsync(\''+t.id+'\')" style="font-size:.8rem;padding:.4rem .9rem;margin-top:.5rem">➕ Add All Players</button>'+
        '</div>'+
        // Single player section
        '<div style="font-size:.72rem;color:var(--muted);font-weight:700;margin-bottom:.5rem;text-transform:uppercase;letter-spacing:1px">Or add single player with photo</div>'+
        '<div class="fgrid">'+
        '<div class="fg"><label>Name *</label><input id="pp_n_'+t.id+'" placeholder="Full name"></div>'+
        '<div class="fg"><label>Category</label><select id="pp_c_'+t.id+'"><option value="local">Local</option><option value="youth">Youth</option><option value="invited">Invited</option></select></div>'+
        buildPhotoInput('pp_ph_'+t.id)+
        '</div>'+
        '<div style="margin-top:.6rem;display:flex;gap:.5rem;flex-wrap:wrap">'+
        '<button class="btn bg" onclick="addPlayerToTeamAsync(\''+t.id+'\')" style="font-size:.82rem;padding:.4rem .9rem">Add Single Player</button>'+
        '<button onclick="toggle(\'pf_'+t.id+'\')" style="background:none;border:1px solid var(--border);color:var(--muted);padding:.4rem .9rem;border-radius:6px;cursor:pointer;font-size:.8rem">Close</button>'+
        '</div></div></div>';
    }).join('')+'</div></div>';
}
async function addTeamAsync(){
  var name=document.getElementById('nt_nm').value.trim();
  if(!name){alert('Name required!');return;}
  var logoUrl=await resolveLogoUrl('nt_lg');
  var id=uid();
  var data={id:id,name:name,president:document.getElementById('nt_pr').value.trim(),color:document.getElementById('nt_cl').value,logoUrl:logoUrl};
  await fsSet('teams',id,data);
}
async function deleteTeamAsync(tid){
  if(!confirm('Delete team and ALL its players? This cannot be undone.')) return;
  var F=fb(); if(!F){alert('Firebase not ready');return;}
  try{
    // 1. Delete team doc
    await F.deleteDoc(F.doc(F.db,'teams',tid));
    // 2. Query players collection directly from Firestore for this team
    var snap=await F.getDocs(F.collection(F.db,'players'));
    var batch=F.writeBatch(F.db);
    var batchCount=0;
    snap.forEach(function(d){
      var pl=d.data();
      if(pl.teamId===tid){
        batch.delete(F.doc(F.db,'players',d.id));
        batch.delete(F.doc(F.db,'stats',d.id));
        batchCount++;
      }
    });
    if(batchCount>0) await batch.commit();
    // 3. Delete fixtures involving this team
    var fsnap=await F.getDocs(F.collection(F.db,'fixtures'));
    var fbatch=F.writeBatch(F.db);
    var fc=0;
    fsnap.forEach(function(d){
      var fx=d.data();
      if(fx.home===tid||fx.away===tid){fbatch.delete(F.doc(F.db,'fixtures',d.id));fc++;}
    });
    if(fc>0) await fbatch.commit();
    alert('✅ Team deleted successfully.');
  }catch(e){alert('Error deleting team: '+e.message);console.error(e);}
}
async function addPlayerToTeamAsync(tid){
  var name=document.getElementById('pp_n_'+tid).value.trim();
  if(!name){alert('Name required!');return;}
  var photoUrl=await resolvePhotoUrl('pp_ph_'+tid);
  var pid=uid();
  var data={id:pid,name:name,teamId:tid,cat:document.getElementById('pp_c_'+tid).value,photoUrl:photoUrl};
  await fsSet('players',pid,data);
  await fsSet('stats',pid,{wins:0,losses:0,draws:0,gd:0,goals:0,cs:0,motm:0});
  toggle('pf_'+tid);
}

async function bulkAddPlayersAsync(tid){
  var textarea=document.getElementById('bulk_'+tid);
  if(!textarea){alert('Could not find input');return;}
  var text=textarea.value.trim();
  if(!text){alert('Paste at least one player name!');return;}
  var lines=text.split('\n').map(function(l){return l.trim();}).filter(Boolean);
  if(!lines.length){alert('No players found in the text.');return;}
  var catMap={'local':'local','youth':'youth','invited':'invited','l':'local','y':'youth','i':'invited'};
  var added=0, skipped=0;
  var F=fb();
  for(var line of lines){
    var parts=line.split(',');
    var name=(parts[0]||'').trim();
    if(!name){skipped++;continue;}
    var catRaw=(parts[1]||'local').trim().toLowerCase();
    var cat=catMap[catRaw]||'local';
    var pid=uid();
    var data={id:pid,name:name,teamId:tid,cat:cat,photoUrl:''};
    try{
      if(F){
        await F.setDoc(F.doc(F.db,'players',pid),data);
        await F.setDoc(F.doc(F.db,'stats',pid),{wins:0,losses:0,draws:0,gd:0,goals:0,cs:0,motm:0});
      }
      added++;
    }catch(e){console.error('Failed to add '+name,e);skipped++;}
  }
  textarea.value='';
  alert('✅ Added '+added+' player(s)'+(skipped?' | ⚠️ Skipped '+skipped:'')+'.');
}

// ══════════════════════════════════════════════════
// ADMIN — PLAYERS
// ══════════════════════════════════════════════════
function aPlayersHTML(){
  var ps=getPlayers();
  return '<div class="apanel"><h3>➕ Register Player</h3>'+
    '<div class="fgrid">'+
    '<div class="fg"><label>Name *</label><input id="gp_n" placeholder="Full name"></div>'+
    '<div class="fg"><label>Team *</label><select id="gp_t">'+getTeams().map(function(t){return '<option value="'+t.id+'">'+esc(t.name)+'</option>';}).join('')+'</select></div>'+
    '<div class="fg"><label>Category</label><select id="gp_c"><option value="local">Local</option><option value="youth">Youth</option><option value="invited">Invited</option></select></div>'+
    buildPhotoInput('gp_ph')+
    '</div><div style="margin-top:.9rem"><button class="btn bg" onclick="addPlayerAsync()">Register Player</button></div></div>'+
    '<div class="apanel"><h3>📋 All Players ('+ps.length+')</h3>'+
    '<div class="alist">'+ps.map(function(p){
      var t=getTeamById(p.teamId); var s=getStat(p.id); var wr=winRatio(s);
      return '<div class="aitem">'+playerPhotoEl(p,30)+
        '<div class="ai"><div class="an">'+esc(p.name)+' '+condBadge(wr)+'</div>'+
        '<div class="am">'+(t?esc(t.name):'')+' | ⚽'+(s.goals||0)+' 👑'+(s.motm||0)+' 🧤'+(s.cs||0)+' WR:'+wr+'% = '+realCalcPts(s)+'pts</div></div>'+
        '<button class="btn" style="background:rgba(41,121,255,.1);color:#6AB0FF;border:1px solid rgba(41,121,255,.3);font-size:.72rem;padding:.27rem .6rem" onclick="openEditPlayer(\''+p.id+'\')">✏️ Edit</button>'+
        '<button class="bd" onclick="removePlayerAsync(\''+p.id+'\')">Remove</button></div>';
    }).join('')+'</div></div>';
}
async function addPlayerAsync(){
  var name=document.getElementById('gp_n').value.trim();
  var tid=document.getElementById('gp_t').value;
  if(!name){alert('Name required!');return;}
  if(!tid){alert('Select a team!');return;}
  var photoUrl=await resolvePhotoUrl('gp_ph');
  var pid=uid();
  var data={id:pid,name:name,teamId:tid,cat:document.getElementById('gp_c').value,photoUrl:photoUrl};
  await fsSet('players',pid,data);
  await fsSet('stats',pid,{wins:0,losses:0,draws:0,gd:0,goals:0,cs:0,motm:0});
}
async function removePlayerAsync(pid){
  if(!confirm('Remove player?')) return;
  await fsDel('players',pid); await fsDel('stats',pid);
}

function openEditPlayer(pid){
  var p=findPlayerInState(pid); if(!p){alert('Player not found');return;}
  var s=getStat(pid);
  var teams=getTeams();
  var teamOpts=teams.map(function(t){
    return '<option value="'+t.id+'"'+(t.id===p.teamId?' selected':'')+'>'+esc(t.name)+'</option>';
  }).join('');

  var modal=document.getElementById('editPlayerModal');
  var content=document.getElementById('editPlayerContent');
  if(!modal){
    // Create modal if it doesn't exist
    var div=document.createElement('div');
    div.id='editPlayerModal';
    div.className='moverlay';
    div.innerHTML='<div class="modal" style="width:min(92vw,560px)"><div id="editPlayerContent"></div></div>';
    document.body.appendChild(div);
    modal=div;
    content=div.querySelector('#editPlayerContent');
  }

  content.innerHTML=
    '<div style="display:flex;align-items:center;gap:.8rem;margin-bottom:1.2rem">'+
    '<div style="flex:1"><div style="font-family:\'Bebas Neue\';font-size:1.6rem;color:var(--green)">✏️ Edit Player</div>'+
    '<div style="font-size:.75rem;color:var(--muted)">Changes save directly to Firebase</div></div>'+
    '<button onclick="document.getElementById(\'editPlayerModal\').classList.add(\'hidden\')" style="background:none;border:none;color:var(--muted);font-size:1.5rem;cursor:pointer">✕</button>'+
    '</div>'+

    // Current photo preview
    '<div style="display:flex;align-items:center;gap:1rem;margin-bottom:1rem;background:var(--card2);border:1px solid var(--border);border-radius:10px;padding:.8rem">'+
    playerPhotoEl(p,56)+
    '<div><div style="font-weight:700">'+esc(p.name)+'</div>'+
    '<div style="font-size:.75rem;color:var(--muted)">'+(getTeamById(p.teamId)?esc(getTeamById(p.teamId).name):'')+' · '+esc(p.cat)+'</div></div>'+
    '</div>'+

    // Edit fields
    '<div class="fgrid" style="margin-bottom:1rem">'+
    '<div class="fg"><label>Name *</label><input id="ep_name" value="'+esc(p.name)+'" placeholder="Full name"></div>'+
    '<div class="fg"><label>Team</label><select id="ep_team">'+teamOpts+'</select></div>'+
    '<div class="fg"><label>Category</label><select id="ep_cat">'+
      '<option value="local"'+(p.cat==='local'?' selected':'')+'>Local</option>'+
      '<option value="youth"'+(p.cat==='youth'?' selected':'')+'>Youth</option>'+
      '<option value="invited"'+(p.cat==='invited'?' selected':'')+'>Invited</option>'+
    '</select></div>'+
    '</div>'+

    // Photo update
    '<div style="background:rgba(0,200,83,.04);border:1px solid rgba(0,200,83,.12);border-radius:10px;padding:.9rem;margin-bottom:1rem">'+
    '<div style="font-size:.72rem;color:var(--green);font-weight:700;text-transform:uppercase;letter-spacing:1px;margin-bottom:.6rem">📷 Update Photo</div>'+
    buildPhotoInput('ep_ph')+
    '<div style="font-size:.68rem;color:var(--muted);margin-top:.3rem">Leave empty to keep current photo</div>'+
    '</div>'+

    // Stats edit
    '<div style="background:rgba(255,214,0,.04);border:1px solid rgba(255,214,0,.12);border-radius:10px;padding:.9rem;margin-bottom:1rem">'+
    '<div style="font-size:.72rem;color:var(--acc);font-weight:700;text-transform:uppercase;letter-spacing:1px;margin-bottom:.6rem">📊 Edit Stats (manual override)</div>'+
    '<div class="fgrid">'+
    '<div class="fg"><label>⚽ Goals</label><input id="ep_goals" type="number" value="'+(s.goals||0)+'" min="0"></div>'+
    '<div class="fg"><label>👑 MOTM</label><input id="ep_motm" type="number" value="'+(s.motm||0)+'" min="0"></div>'+
    '<div class="fg"><label>🧤 Clean Sheets</label><input id="ep_cs" type="number" value="'+(s.cs||0)+'" min="0"></div>'+
    '<div class="fg"><label>🏆 Wins</label><input id="ep_wins" type="number" value="'+(s.wins||0)+'" min="0"></div>'+
    '<div class="fg"><label>🤝 Draws</label><input id="ep_draws" type="number" value="'+(s.draws||0)+'" min="0"></div>'+
    '<div class="fg"><label>❌ Losses</label><input id="ep_losses" type="number" value="'+(s.losses||0)+'" min="0"></div>'+
    '<div class="fg"><label>⚡ GD</label><input id="ep_gd" type="number" value="'+(s.gd||0)+'"></div>'+
    '</div></div>'+

    '<div style="display:flex;gap:.7rem;flex-wrap:wrap">'+
    '<button class="btn bg" onclick="saveEditPlayer(\''+pid+'\')">💾 Save Changes</button>'+
    '<button class="btn" style="background:var(--border);color:var(--text)" onclick="document.getElementById(\'editPlayerModal\').classList.add(\'hidden\')">Cancel</button>'+
    '</div>';

  modal.classList.remove('hidden');
}

async function saveEditPlayer(pid){
  var name=document.getElementById('ep_name').value.trim();
  if(!name){alert('Name is required!');return;}

  // Resolve new photo (empty = keep existing)
  var newPhotoUrl=await resolvePhotoUrl('ep_ph');
  var existing=findPlayerInState(pid);
  var photoUrl=newPhotoUrl||(existing?existing.photoUrl||'':'');

  // Save player doc
  var playerData={
    id:pid,
    name:name,
    teamId:document.getElementById('ep_team').value,
    cat:document.getElementById('ep_cat').value,
    photoUrl:photoUrl
  };
  await fsSet('players',pid,playerData);

  // Save stats
  var statsData={
    goals:parseInt(document.getElementById('ep_goals').value)||0,
    motm:parseInt(document.getElementById('ep_motm').value)||0,
    cs:parseInt(document.getElementById('ep_cs').value)||0,
    wins:parseInt(document.getElementById('ep_wins').value)||0,
    draws:parseInt(document.getElementById('ep_draws').value)||0,
    losses:parseInt(document.getElementById('ep_losses').value)||0,
    gd:parseInt(document.getElementById('ep_gd').value)||0
  };
  await fsSet('stats',pid,statsData);

  document.getElementById('editPlayerModal').classList.add('hidden');
  alert('✅ Player updated!');
}

// ══════════════════════════════════════════════════
// ADMIN — FIXTURES
// ══════════════════════════════════════════════════
function aFixturesHTML(){
  var teams=getTeams(); var fxs=getFixtures();
  var teamOpts=teams.map(function(t){return '<option value="'+t.id+'">'+esc(t.name)+'</option>';}).join('');
  return '<div class="apanel"><h3>➕ Create Fixture</h3>'+
    '<div class="fgrid">'+
    '<div class="fg"><label>Home Team</label><select id="nf_h">'+teamOpts+'</select></div>'+
    '<div class="fg"><label>Away Team</label><select id="nf_a">'+teamOpts+'</select></div>'+
    '<div class="fg"><label>Date</label><input id="nf_d" type="date"></div>'+
    '<div class="fg"><label>Venue</label><input id="nf_v" placeholder="Main Stadium"></div>'+
    '<div class="fg"><label>Round / Label</label><input id="nf_r" placeholder="Round 1"></div>'+
    '<div class="fg"><label>Status</label><select id="nf_st"><option value="upcoming">Upcoming</option><option value="live">Live</option><option value="played">Played</option></select></div>'+
    '<div class="fg"><label>Home Score</label><input id="nf_hs" type="number" placeholder="0" min="0"></div>'+
    '<div class="fg"><label>Away Score</label><input id="nf_as" type="number" placeholder="0" min="0"></div>'+
    '</div><div style="margin-top:.9rem"><button class="btn bg" onclick="addFixtureAsync()">Create Fixture</button></div></div>'+
    '<div class="apanel"><h3>📋 All Fixtures ('+fxs.length+')</h3>'+
    '<div class="alist">'+fxs.map(function(f){
      var ht=getTeamById(f.home), at=getTeamById(f.away);
      var scls=f.status==='played'?'s-pl':f.status==='live'?'s-lv':'s-up';
      return '<div><div class="aitem">'+
        '<div class="ai"><div class="an">'+(ht?esc(ht.name):'?')+' vs '+(at?esc(at.name):'?')+'</div>'+
        '<div class="am">'+esc(f.date||'')+(f.round?' | '+esc(f.round):'')+(f.venue?' | '+esc(f.venue):'')+' <span class="sbadge '+scls+'">'+f.status+'</span>'+(f.homeScore!=null?' | '+f.homeScore+'-'+f.awayScore:'')+'</div></div>'+
        '<button class="btn ba" style="font-size:.72rem;padding:.27rem .6rem" onclick="toggle(\'ef_'+f.id+'\')">Edit</button>'+
        '<button class="bd" onclick="deleteFixtureAsync(\''+f.id+'\')">Del</button></div>'+
        '<div id="ef_'+f.id+'" class="expand-sec" style="border-left-color:var(--acc)">'+
        '<div class="fgrid">'+
        '<div class="fg"><label>Status</label><select id="ef_s_'+f.id+'"><option value="upcoming"'+(f.status==='upcoming'?' selected':'')+'>Upcoming</option><option value="live"'+(f.status==='live'?' selected':'')+'>Live</option><option value="played"'+(f.status==='played'?' selected':'')+'>Played</option></select></div>'+
        '<div class="fg"><label>Home Score</label><input id="ef_h_'+f.id+'" type="number" value="'+(f.homeScore||0)+'"></div>'+
        '<div class="fg"><label>Away Score</label><input id="ef_a_'+f.id+'" type="number" value="'+(f.awayScore||0)+'"></div>'+
        '</div><div style="margin-top:.6rem;display:flex;gap:.5rem">'+
        '<button class="btn bg" onclick="saveFxAsync(\''+f.id+'\')" style="font-size:.82rem;padding:.4rem .9rem">Save</button>'+
        '<button onclick="toggle(\'ef_'+f.id+'\')" style="background:none;border:1px solid var(--border);color:var(--muted);padding:.4rem .9rem;border-radius:6px;cursor:pointer;font-size:.8rem">Cancel</button>'+
        '</div></div></div>';
    }).join('')+'</div></div>';
}
async function addFixtureAsync(){
  var hi=document.getElementById('nf_h').value, ai=document.getElementById('nf_a').value;
  if(hi===ai){alert('Home and away must differ!');return;}
  var st=document.getElementById('nf_st').value;
  var hs=parseInt(document.getElementById('nf_hs').value)||0;
  var as_=parseInt(document.getElementById('nf_as').value)||0;
  var id=uid();
  await fsSet('fixtures',id,{id:id,home:hi,away:ai,date:document.getElementById('nf_d').value,venue:document.getElementById('nf_v').value||'TBD',round:document.getElementById('nf_r').value,status:st,homeScore:st!=='upcoming'?hs:null,awayScore:st!=='upcoming'?as_:null});
}
async function saveFxAsync(fid){
  var f=state.fixtures[fid]; if(!f) return;
  var updated=Object.assign({},f,{status:document.getElementById('ef_s_'+fid).value,homeScore:parseInt(document.getElementById('ef_h_'+fid).value)||0,awayScore:parseInt(document.getElementById('ef_a_'+fid).value)||0});
  await fsSet('fixtures',fid,updated);
}

// ══════════════════════════════════════════════════
// ADMIN — MATCHES (Parse & Submit)
// ══════════════════════════════════════════════════
function aMatchesHTML(){
  var fxs=getFixtures();
  var fxOpts=fxs.map(function(f){
    var ht=getTeamById(f.home), at=getTeamById(f.away);
    return '<option value="'+f.id+'">'+(ht?esc(ht.name):'?')+' vs '+(at?esc(at.name):'?')+' ('+esc(f.date||'')+')</option>';
  }).join('');
  var matchList=getMatches().map(function(m,i){
    var f=state.fixtures[m.fixtureId];
    var ht=f?getTeamById(f.home):null, at=f?getTeamById(f.away):null;
    return '<div class="aitem">'+
      '<div class="ai"><div class="an">'+(ht?esc(ht.name):'?')+' '+m.homeScore+' — '+m.awayScore+' '+(at?esc(at.name):'?')+'</div>'+
      '<div class="am">'+(m.round?esc(m.round)+' | ':'')+(m.homeResults?m.homeResults.length+m.awayResults.length:0)+' players | 👑 '+(m.motm?esc(m.motm):'—')+'</div></div>'+
      '<button class="btn" style="background:rgba(0,200,83,.1);color:var(--green);border:1px solid rgba(0,200,83,.3);font-size:.7rem;padding:.27rem .6rem" onclick="viewMatch(\''+m.id+'\')">View</button>'+
      '<button class="bd" onclick="deleteMatchAsync(\''+m.id+'\')">Del</button></div>';
  }).join('');
  return '<div class="apanel"><h3>📋 Submit Match Result</h3>'+
    '<p style="font-size:.76rem;color:var(--muted);margin-bottom:.9rem">Select fixture → paste result text → Parse → Submit.<br>'+
    '<strong style="color:var(--acc)">🛠️</strong> separates teams. <strong style="color:var(--green)">👑</strong>=MOTM  <strong style="color:var(--green)">🔑</strong>=Key Player  <strong style="color:var(--green)">✈️</strong>=Away marker  <strong style="color:var(--green)">🆚</strong>=score divider</p>'+
    '<div class="fgrid" style="margin-bottom:.8rem"><div class="fg" style="grid-column:1/-1"><label>Select Fixture *</label><select id="mr_fx">'+fxOpts+'</select></div></div>'+
    '<div class="fg" style="margin-bottom:.8rem"><label>Paste Match Result Text</label>'+
    '<textarea id="mr_txt" style="background:var(--dark);border:1px solid var(--border);border-radius:8px;padding:.6rem .8rem;color:var(--text);font-family:monospace;font-size:.79rem;resize:vertical;min-height:180px;outline:none;width:100%;margin-top:.3rem" placeholder="Paste here…&#10;&#10;🔥 Thriller Loading…&#10;🏆 JPL 2026 LEAGUE STAGE&#10;ROUND 1&#10;Team A 🛠️ Team B&#10;&#10;Owasikur Rahman 👑 🔑 2🆚0 Md Tasin&#10;Afsan Gazi 🔑 1🆚1 Raiful ✈️"></textarea></div>'+
    '<div style="display:flex;gap:.7rem;flex-wrap:wrap"><button class="btn bg" onclick="parseAndPreview()">🔍 Parse &amp; Preview</button></div></div>'+
    '<div class="apanel hidden" id="previewPanel"><h3>👁 Preview — Verify Before Submitting</h3><div id="previewContent"></div>'+
    '<div style="margin-top:1rem;display:flex;gap:.7rem;flex-wrap:wrap">'+
    '<button class="btn bg" onclick="submitMatchAsync()">✅ Submit</button>'+
    '<button class="btn" style="background:var(--border);color:var(--text)" onclick="document.getElementById(\'previewPanel\').classList.add(\'hidden\')">Cancel</button>'+
    '</div></div>'+
    '<div class="apanel hidden" id="viewPanel"><h3>📄 Match Detail</h3><div id="viewContent"></div>'+
    '<button class="btn" style="background:var(--border);color:var(--text);margin-top:.8rem" onclick="document.getElementById(\'viewPanel\').classList.add(\'hidden\')">Close</button></div>'+
    (matchList?'<div class="apanel"><h3>📂 Submitted Matches ('+getMatches().length+')</h3><div class="alist">'+matchList+'</div></div>':'');
}

// ── Parse helpers ─────────────────────────────────
function cleanName(s){
  return String(s||'')
    .replace(/[👑🔑✈️👤🔥🏆⭐⛔🛠️📌]/gu,'')
    .replace(/@/g,'').replace(/\s+/g,' ').trim();
}
function fuzzyFind(name, players){
  if(!name||!players||!players.length) return null;
  var n=name.toLowerCase().replace(/[^a-z0-9 ]/g,'').trim();
  if(n.length<2) return null;
  var ex=players.find(function(p){return p.name.toLowerCase()===n;});
  if(ex) return ex;
  var co=players.find(function(p){
    var pn=p.name.toLowerCase();
    return pn.indexOf(n)>=0||n.indexOf(pn)>=0;
  });
  if(co) return co;
  var tokens=n.split(' ').filter(function(t){return t.length>=3;});
  for(var i=0;i<tokens.length;i++){
    var tm=players.find(function(p){return p.name.toLowerCase().indexOf(tokens[i])>=0;});
    if(tm) return tm;
  }
  return null;
}
function fuzzyTeam(name){
  if(!name) return null;
  var n=name.toLowerCase().replace(/[^a-z0-9 ]/g,'').trim();
  if(n.length<2) return null;
  return getTeams().find(function(t){
    var tn=t.name.toLowerCase().replace(/[^a-z0-9 ]/g,'');
    if(tn===n||tn.indexOf(n)>=0||n.indexOf(tn)>=0) return true;
    var abbr=t.name.toLowerCase().split(' ').map(function(w){return w[0]||'';}).join('');
    return abbr===n;
  })||null;
}

function parseMatchText(text, fx){
  var ht=getTeamById(fx.home)||{id:fx.home,name:'Home'};
  var at=getTeamById(fx.away)||{id:fx.away,name:'Away'};
  var lines=text.split('\n').map(function(l){return l.trim();}).filter(Boolean);
  var homeResults=[], awayResults=[], unmatched=[];
  var motm=null, motmId=null, round='';
  var detHome=ht, detAway=at;
  var finalHomeScore=null, finalAwayScore=null;

  // Round
  var rm=text.match(/ROUND\s+(\d+)/i);
  if(rm) round='Round '+rm[1];

  // Team detect via 🛠️
  var teamLine=lines.find(function(l){return l.includes('🛠️');});
  if(teamLine){
    var tparts=teamLine.split('🛠️');
    var tA=fuzzyTeam(cleanName(tparts[0]||''));
    var tB=fuzzyTeam(cleanName(tparts[1]||''));
    if(tA) detHome=tA;
    if(tB) detAway=tB;
  }

  var homePlayers=getPlayersByTeam(detHome.id);
  var awayPlayers=getPlayersByTeam(detAway.id);

  // POINTS block — lines like "LLRS :25" or "TR :07"
  var inPtsSection=false;
  lines.forEach(function(l){
    if(/POINTS\s*:/i.test(l)||/^POINTS\s*$/i.test(l)){inPtsSection=true;return;}
    // also try any line matching "WORD(S) :NUMBER" pattern
    var clean=l.replace(/[^\w\s:]/g,'').trim();
    var m2=clean.match(/^([A-Za-z][A-Za-z0-9\s]{0,30})\s*:\s*(\d+)\s*$/);
    if((inPtsSection||/^[A-Z]/.test(l))&&m2){
      var nm=m2[1].trim(); var pts=parseInt(m2[2]);
      if(nm.length<2||pts<0||pts>9999) return;
      var matched=fuzzyTeam(nm);
      if(matched){
        if(matched.id===detHome.id) finalHomeScore=pts;
        else if(matched.id===detAway.id) finalAwayScore=pts;
      } else {
        // abbreviation fallback
        var hn=detHome.name.toLowerCase(), an=detAway.name.toLowerCase(), nml=nm.toLowerCase();
        if(hn.indexOf(nml)>=0||nml.indexOf(hn.split(' ')[0])>=0) finalHomeScore=pts;
        else if(an.indexOf(nml)>=0||nml.indexOf(an.split(' ')[0])>=0) finalAwayScore=pts;
      }
    }
  });

  // Player lines — each contains 🆚
  lines.forEach(function(line){
    if(!line.includes('🆚')) return;
    var parts=line.split('🆚');
    if(parts.length<2) return;
    var lp=parts[0];
    var rp=parts.slice(1).join('');

    // Scores adjacent to 🆚
    var lsm=lp.match(/(\d+)\s*$/);
    var rsm=rp.match(/^\s*(\d+)/);
    var ls=lsm?parseInt(lsm[1]):0;
    var rs=rsm?parseInt(rsm[1]):0;

    // Names
    var lName=cleanName(lp.replace(/\d+\s*$/,''));
    var rName=cleanName(rp.replace(/^\s*\d+/,''));

    // Side: ✈️ = away side
    var lIsAway=lp.includes('✈️');
    var rIsAway=rp.includes('✈️');

    var homeN, awayN, hS, aS;
    if(rIsAway&&!lIsAway){homeN=lName;awayN=rName;hS=ls;aS=rs;}
    else if(lIsAway&&!rIsAway){homeN=rName;awayN=lName;hS=rs;aS=ls;}
    else{homeN=lName;awayN=rName;hS=ls;aS=rs;}

    // MOTM (👑)
    var hMOTM=(lIsAway?rp:lp).includes('👑');
    var aMOTM=(lIsAway?lp:rp).includes('👑');

    // Match home player
    if(homeN&&homeN.length>1){
      var hMatch=fuzzyFind(homeN, homePlayers);
      if(hMatch){
        homeResults.push({playerId:hMatch.id,playerName:hMatch.name,rawName:homeN,ts:hS,os:aS,isMOTM:hMOTM,matched:true});
        if(hMOTM&&!motmId){motm=hMatch.name;motmId=hMatch.id;}
      } else {
        var unmIdx=homeResults.length;
        homeResults.push({playerId:null,playerName:homeN,rawName:homeN,ts:hS,os:aS,isMOTM:false,matched:false});
        unmatched.push({rawName:homeN,side:'home',ts:hS,os:aS,resultIdx:unmIdx,inHome:true});
      }
    }
    // Match away player
    if(awayN&&awayN.length>1){
      var aMatch=fuzzyFind(awayN, awayPlayers);
      if(aMatch){
        awayResults.push({playerId:aMatch.id,playerName:aMatch.name,rawName:awayN,ts:aS,os:hS,isMOTM:aMOTM,matched:true});
        if(aMOTM&&!motmId){motm=aMatch.name;motmId=aMatch.id;}
      } else {
        var unmIdxA=awayResults.length;
        awayResults.push({playerId:null,playerName:awayN,rawName:awayN,ts:aS,os:hS,isMOTM:false,matched:false});
        unmatched.push({rawName:awayN,side:'away',ts:aS,os:hS,resultIdx:unmIdxA,inHome:false});
      }
    }
  });

  var totalH=finalHomeScore!==null?finalHomeScore:homeResults.reduce(function(s,r){return s+(r.ts||0);},0);
  var totalA=finalAwayScore!==null?finalAwayScore:awayResults.reduce(function(s,r){return s+(r.ts||0);},0);

  return {
    homeScore:totalH,awayScore:totalA,
    homeResults:homeResults,awayResults:awayResults,
    unmatched:unmatched,motm:motm,motmId:motmId,round:round,
    detHomeId:detHome.id,detAwayId:detAway.id,
    detHomeName:detHome.name,detAwayName:detAway.name,
    pointsBlockFound:(finalHomeScore!==null||finalAwayScore!==null)
  };
}

function parseAndPreview(){
  var fxId=document.getElementById('mr_fx').value;
  var text=document.getElementById('mr_txt').value.trim();
  if(!text){alert('Please paste match result text first!');return;}
  if(!fxId){alert('Please select a fixture!');return;}
  var fx=state.fixtures[fxId];
  if(!fx){alert('Fixture not found in database!');return;}

  try{
    var r=parseMatchText(text,fx);
    pendingMatch=Object.assign({},r,{fixtureId:fxId});

    var ht=getTeamById(r.detHomeId);
    var at=getTeamById(r.detAwayId);
    var htName=ht?ht.name:r.detHomeName;
    var atName=at?at.name:r.detAwayName;

    var html='';

    // Scoreboard header — team match points (W=3, D=1, L=0)
    var homeMatchPts = r.homeScore > r.awayScore ? 3 : r.homeScore === r.awayScore ? 1 : 0;
    var awayMatchPts = r.awayScore > r.homeScore ? 3 : r.homeScore === r.awayScore ? 1 : 0;
    var homeResult   = r.homeScore > r.awayScore ? 'WIN' : r.homeScore === r.awayScore ? 'DRAW' : 'LOSS';
    var awayResult   = r.awayScore > r.homeScore ? 'WIN' : r.homeScore === r.awayScore ? 'DRAW' : 'LOSS';
    var homeResColor = homeResult==='WIN'?'var(--green)':homeResult==='DRAW'?'var(--acc)':'var(--red)';
    var awayResColor = awayResult==='WIN'?'var(--green)':awayResult==='DRAW'?'var(--acc)':'var(--red)';

    html+='<div style="background:var(--card2);border-radius:14px;padding:1.2rem;margin-bottom:1rem;text-align:center;border:1px solid var(--border)">';
    html+='<div style="display:flex;align-items:center;justify-content:center;gap:1.5rem;flex-wrap:wrap">';
    // Home side
    html+='<div style="text-align:center;min-width:100px">'+
      teamLogoEl(ht||{},44)+
      '<div style="font-weight:700;font-size:.88rem;margin-top:.3rem">'+esc(htName)+'</div>'+
      '<div style="font-family:\'Bebas Neue\';font-size:2.2rem;color:'+homeResColor+'">'+homeMatchPts+'</div>'+
      '<div style="font-size:.65rem;color:'+homeResColor+';font-weight:700;letter-spacing:1px">'+homeResult+' ('+r.homeScore+' game pts)</div>'+
      '</div>';
    // VS divider
    html+='<div style="font-family:\'Bebas Neue\';font-size:1.4rem;color:var(--muted)">MATCH<br>PTS</div>';
    // Away side
    html+='<div style="text-align:center;min-width:100px">'+
      teamLogoEl(at||{},44)+
      '<div style="font-weight:700;font-size:.88rem;margin-top:.3rem">'+esc(atName)+'</div>'+
      '<div style="font-family:\'Bebas Neue\';font-size:2.2rem;color:'+awayResColor+'">'+awayMatchPts+'</div>'+
      '<div style="font-size:.65rem;color:'+awayResColor+';font-weight:700;letter-spacing:1px">'+awayResult+' ('+r.awayScore+' game pts)</div>'+
      '</div>';
    html+='</div>';
    if(r.round) html+='<div style="font-size:.76rem;color:var(--acc);margin-top:.5rem;font-weight:700">'+esc(r.round)+'</div>';
    if(r.motm) html+='<div style="font-size:.8rem;color:var(--acc);margin-top:.3rem">👑 MOTM: <strong>'+esc(r.motm)+'</strong></div>';
    // Points legend
    html+='<div style="display:flex;gap:.5rem;justify-content:center;flex-wrap:wrap;margin-top:.5rem">';
    html+='<span style="font-size:.68rem;color:var(--muted);background:var(--card);padding:.2rem .5rem;border-radius:8px;border:1px solid var(--border)">🏆 Win = 3 pts</span>';
    html+='<span style="font-size:.68rem;color:var(--muted);background:var(--card);padding:.2rem .5rem;border-radius:8px;border:1px solid var(--border)">🤝 Draw = 1 pt</span>';
    html+='<span style="font-size:.68rem;color:var(--muted);background:var(--card);padding:.2rem .5rem;border-radius:8px;border:1px solid var(--border)">❌ Loss = 0 pts</span>';
    html+='</div>';
    html+='<div style="font-size:.67rem;color:var(--muted);margin-top:.3rem">'+(r.pointsBlockFound?'✅ Game totals from POINTS block':'⚠️ Game totals auto-summed — include POINTS: section for accuracy')+'</div>';
    html+='</div>';

    // Match summary badges
    var matchedCount=r.homeResults.filter(function(x){return x.matched;}).length+r.awayResults.filter(function(x){return x.matched;}).length;
    var totalCount=r.homeResults.length+r.awayResults.length;
    html+='<div style="display:flex;gap:.5rem;flex-wrap:wrap;margin-bottom:.8rem">';
    html+='<span style="font-size:.72rem;background:rgba(0,200,83,.1);color:var(--green);border:1px solid rgba(0,200,83,.3);padding:.2rem .6rem;border-radius:10px">✅ '+matchedCount+'/'+totalCount+' matched</span>';
    if(r.unmatched.length) html+='<span style="font-size:.72rem;background:rgba(255,61,61,.1);color:var(--red);border:1px solid rgba(255,61,61,.3);padding:.2rem .6rem;border-radius:10px">⚠️ '+r.unmatched.length+' need manual fix</span>';
    html+='</div>';

    // Player scorecard — two-sided table
    html+='<div style="margin:.4rem 0 .2rem;font-family:\'Barlow Condensed\';font-size:.8rem;font-weight:700;color:var(--muted);text-transform:uppercase;letter-spacing:1px;display:grid;grid-template-columns:1fr auto 1fr;padding:0 .7rem">';
    html+='<div style="text-align:right">'+esc(htName)+'</div><div></div><div>'+esc(atName)+'</div></div>';
    html+=buildScorecardTable(r.homeResults, r.awayResults, r);

    // Unmatched resolution UI
    if(r.unmatched.length){
      html+='<div style="background:rgba(255,214,0,.04);border:1px solid rgba(255,214,0,.2);border-radius:12px;padding:1rem;margin-top:.8rem">';
      html+='<div style="color:var(--acc);font-weight:700;font-size:.82rem;margin-bottom:.6rem">⚠️ Fix Unmatched Players</div>';
      r.unmatched.forEach(function(u,i){
        var sideTeam=u.side==='home'?ht:at;
        var sidePlayers=sideTeam?getPlayersByTeam(sideTeam.id):[];
        var opts='<option value="">❌ Skip</option>'+sidePlayers.map(function(p){return '<option value="'+p.id+'">'+esc(p.name)+'</option>';}).join('');
        html+='<div style="display:flex;align-items:center;gap:.6rem;flex-wrap:wrap;padding:.4rem 0;border-bottom:1px solid var(--border);font-size:.8rem">';
        html+='<div style="min-width:130px"><span style="color:var(--red)">❓</span> <strong>'+esc(u.rawName)+'</strong></div>';
        html+='<span style="color:var(--muted);font-size:.7rem">'+(u.side==='home'?esc(htName):esc(atName))+' | '+u.ts+' — '+u.os+'</span>';
        html+='<select id="um_'+i+'" style="flex:1;min-width:140px;background:var(--dark);border:1px solid var(--border);border-radius:6px;padding:.3rem .5rem;color:var(--text);font-size:.78rem">'+opts+'</select>';
        html+='</div>';
      });
      html+='</div>';
    }

    document.getElementById('previewContent').innerHTML=html;
    document.getElementById('previewPanel').classList.remove('hidden');
    setTimeout(function(){document.getElementById('previewPanel').scrollIntoView({behavior:'smooth',block:'start'});},50);

  }catch(e){
    console.error('Parse error:',e);
    alert('Parse error: '+e.message+'\n\nCheck console for details.');
  }
}
function findPlayerInState(pid){
  if(!pid) return null;
  var found=null;
  getPlayers().forEach(function(pl){ if(pl.id===pid) found=pl; });
  return found;
}

function buildPreviewSection(title, results, fullR){
  if(!results||!results.length) return '';
  var html='<div style="margin:.8rem 0 .3rem;font-family:\'Barlow Condensed\';font-size:.8rem;font-weight:700;color:var(--muted);text-transform:uppercase;letter-spacing:1px;border-left:3px solid var(--green);padding-left:.6rem">'+title+'</div>';
  return html;
}
// Two-sided scorecard: home results paired with away results by index
function buildScorecardTable(homeResults, awayResults, fullR){
  var maxRows=Math.max(homeResults.length, awayResults.length);
  if(maxRows===0) return '';
  var html='<div style="border:1px solid var(--border);border-radius:12px;overflow:hidden;margin:.8rem 0">';
  for(var i=0;i<maxRows;i++){
    var hr=homeResults[i]||null;
    var ar=awayResults[i]||null;
    var hp=hr?findPlayerInState(hr.playerId):null;
    var ap=ar?findPlayerInState(ar.playerId):null;
    var hMOTM=hr&&fullR&&fullR.motmId&&hr.playerId===fullR.motmId;
    var aMOTM=ar&&fullR&&fullR.motmId&&ar.playerId===fullR.motmId;
    var hWin=hr&&ar?(hr.ts>ar.ts):false;
    var aWin=hr&&ar?(ar.ts>hr.ts):false;
    var rowBg=i%2===0?'background:rgba(255,255,255,.015)':'';
    html+='<div style="display:grid;grid-template-columns:1fr auto 1fr;align-items:center;gap:0;padding:.45rem .7rem;border-bottom:1px solid var(--border);'+rowBg+'">';
    // HOME SIDE
    html+='<div style="display:flex;align-items:center;gap:.4rem;justify-content:flex-end">';
    if(hr){
      // MOTM crown (text, no emoji in form but crown here is fine for player indicator)
      if(hMOTM) html+='<span style="font-size:.62rem;color:var(--acc);font-weight:900;letter-spacing:.5px">MOTM</span>';
      html+='<span style="font-size:.84rem;font-weight:'+(hWin?'700':'500')+';color:'+(hWin?'var(--text)':'var(--muted)')+'">'+esc(hr.playerName||hr.rawName||'?')+'</span>';
      if(!hp) html+='<span style="font-size:.58rem;color:var(--red);background:rgba(255,61,61,.1);border:1px solid rgba(255,61,61,.3);padding:.05rem .28rem;border-radius:3px">?</span>';
      html+=playerPhotoEl(hp,22);
    } else { html+='<span style="color:var(--muted);font-size:.78rem">—</span>'; }
    html+='</div>';
    // SCORE CENTER
    var hScore=hr?hr.ts:'-';
    var aScore=ar?ar.ts:'-';
    var hScoreCol=hWin?'var(--green)':aWin?'var(--red)':'var(--acc)';
    var aScoreCol=aWin?'var(--green)':hWin?'var(--red)':'var(--acc)';
    html+='<div style="text-align:center;padding:0 .7rem;white-space:nowrap">';
    html+='<span style="font-family:\'Bebas Neue\';font-size:1.3rem;color:'+hScoreCol+'">'+hScore+'</span>';
    html+='<span style="font-family:\'Bebas Neue\';font-size:.9rem;color:var(--muted);margin:0 .2rem">VS</span>';
    html+='<span style="font-family:\'Bebas Neue\';font-size:1.3rem;color:'+aScoreCol+'">'+aScore+'</span>';
    html+='</div>';
    // AWAY SIDE
    html+='<div style="display:flex;align-items:center;gap:.4rem">';
    if(ar){
      html+=playerPhotoEl(ap,22);
      html+='<span style="font-size:.84rem;font-weight:'+(aWin?'700':'500')+';color:'+(aWin?'var(--text)':'var(--muted)')+'">'+esc(ar.playerName||ar.rawName||'?')+'</span>';
      if(!ap) html+='<span style="font-size:.58rem;color:var(--red);background:rgba(255,61,61,.1);border:1px solid rgba(255,61,61,.3);padding:.05rem .28rem;border-radius:3px">?</span>';
      if(aMOTM) html+='<span style="font-size:.62rem;color:var(--acc);font-weight:900;letter-spacing:.5px">MOTM</span>';
    } else { html+='<span style="color:var(--muted);font-size:.78rem">—</span>'; }
    html+='</div>';
    html+='</div>';
  }
  html+='</div>';
  return html;
}

async function submitMatchAsync(){
  if(!pendingMatch){alert('Nothing to submit!');return;}
  var m=pendingMatch;
  var fx=state.fixtures[m.fixtureId];
  // resolve manual unmatched
  if(m.unmatched){
    m.unmatched.forEach(function(u,i){
      var sel=document.getElementById('um_'+i);
      if(sel&&sel.value){
        var pid=sel.value;
        var found=null; getPlayers().forEach(function(pl){if(pl.id===pid) found=pl;});
        if(found){
          var entry={playerId:pid,playerName:found.name,ts:u.ts,os:u.os,isMOTM:false};
          if(u.side==='home') m.homeResults.push(entry);
          else m.awayResults.push(entry);
        }
      }
    });
  }
  // update fixture
  if(fx){
    var updFx=Object.assign({},fx,{homeScore:m.homeScore,awayScore:m.awayScore,status:'played'});
    if(m.round) updFx.round=m.round;
    await fsSet('fixtures',m.fixtureId,updFx);
  }
  // apply stats — only for matched players (playerId not null)
  var homeWin=m.homeScore>m.awayScore, awayWin=m.awayScore>m.homeScore;
  var absGD=Math.abs(m.homeScore-m.awayScore);
  async function applyStats(results,win,lose,opponentResults){
    for(var idx=0;idx<results.length;idx++){
      var r=results[idx];
      if(!r.playerId) continue;
      var s=Object.assign({wins:0,losses:0,draws:0,gd:0,goals:0,cs:0,motm:0,gf:0,ga:0,mp:0},state.stats[r.playerId]||{});
      s.mp=(s.mp||0)+1;
      if(win){s.wins++;s.gd+=absGD;} else if(lose){s.losses++;s.gd-=absGD;} else{s.draws++;}
      s.gf=(s.gf||0)+(r.ts||0);
      // GA = opponent's score at same index
      var oppR=opponentResults[idx]||null;
      s.ga=(s.ga||0)+(oppR?oppR.ts:0);
      s.goals=(s.goals||0)+(r.ts||0);
      // Clean sheet: won and conceded 0
      var conceded=oppR?oppR.ts:999;
      if(conceded===0) s.cs=(s.cs||0)+1;
      if(r.isMOTM||(m.motmId&&r.playerId===m.motmId)) s.motm=(s.motm||0)+1;
      await fsSet('stats',r.playerId,s);
    }
  }
  await applyStats(m.homeResults,homeWin,awayWin,m.awayResults);
  await applyStats(m.awayResults,awayWin,homeWin,m.homeResults);
  // save match record
  var mid=uid();
  await fsSet('matches',mid,{id:mid,fixtureId:m.fixtureId,homeScore:m.homeScore,awayScore:m.awayScore,homeResults:m.homeResults,awayResults:m.awayResults,motm:m.motm||null,motmId:m.motmId||null,round:m.round||null,detHomeId:m.detHomeId,detAwayId:m.detAwayId});
  pendingMatch=null;
  document.getElementById('previewPanel').classList.add('hidden');
  document.getElementById('mr_txt').value='';
  alert('✅ Match submitted and stats updated!');
}
async function deleteMatchAsync(mid){
  if(!confirm('Delete match? This will REVERT all player stats from this match.')) return;
  var m=state.matches[mid]; if(!m){await fsDel('matches',mid);return;}
  // Revert stats
  var homeWin=m.homeScore>m.awayScore, awayWin=m.awayScore>m.homeScore;
  var absGD=Math.abs(m.homeScore-m.awayScore);
  async function revertStats(results,win,lose,oppResults){
    for(var idx=0;idx<results.length;idx++){
      var r=results[idx]; if(!r.playerId) continue;
      var s=Object.assign({wins:0,losses:0,draws:0,gd:0,goals:0,cs:0,motm:0,gf:0,ga:0,mp:0},state.stats[r.playerId]||{});
      s.mp=Math.max(0,(s.mp||0)-1);
      if(win){s.wins=Math.max(0,(s.wins||0)-1);s.gd-=absGD;} else if(lose){s.losses=Math.max(0,(s.losses||0)-1);s.gd+=absGD;} else{s.draws=Math.max(0,(s.draws||0)-1);}
      s.gf=Math.max(0,(s.gf||0)-(r.ts||0));
      var oppR=oppResults[idx]||null;
      s.ga=Math.max(0,(s.ga||0)-(oppR?oppR.ts:0));
      s.goals=Math.max(0,(s.goals||0)-(r.ts||0));
      var conceded=oppR?oppR.ts:999;
      if(conceded===0) s.cs=Math.max(0,(s.cs||0)-1);
      if(r.isMOTM||(m.motmId&&r.playerId===m.motmId)) s.motm=Math.max(0,(s.motm||0)-1);
      await fsSet('stats',r.playerId,s);
    }
  }
  await revertStats(m.homeResults||[],homeWin,awayWin,m.awayResults||[]);
  await revertStats(m.awayResults||[],awayWin,homeWin,m.homeResults||[]);
  await fsDel('matches',mid);
  alert('✅ Match deleted and stats reverted.');
}
async function deleteFixtureAsync(fid){
  if(!confirm('Delete fixture? Any submitted matches for this fixture will also be deleted and stats reverted.')) return;
  // Find and delete related matches first
  var relatedMatches=getMatches().filter(function(m){return m.fixtureId===fid;});
  for(var m of relatedMatches){ await deleteMatchAsync(m.id); }
  await fsDel('fixtures',fid);
}
function viewMatch(mid){
  var m=state.matches[mid]; if(!m) return;
  var ht=getTeamById(m.detHomeId), at=getTeamById(m.detAwayId);
  var htName=ht?ht.name:'Home', atName=at?at.name:'Away';
  var homeMatchPts=m.homeScore>m.awayScore?3:m.homeScore===m.awayScore?1:0;
  var awayMatchPts=m.awayScore>m.homeScore?3:m.homeScore===m.awayScore?1:0;
  var hCol=homeMatchPts===3?'var(--green)':homeMatchPts===1?'var(--acc)':'var(--red)';
  var aCol=awayMatchPts===3?'var(--green)':awayMatchPts===1?'var(--acc)':'var(--red)';
  document.getElementById('viewContent').innerHTML=
    '<div style="background:var(--card2);border-radius:12px;padding:1rem;margin-bottom:.8rem;border:1px solid var(--border)">'+
    '<div style="display:flex;align-items:center;justify-content:center;gap:1.5rem;flex-wrap:wrap;text-align:center">'+
    '<div>'+teamLogoEl(ht||{},40)+'<div style="font-weight:700;font-size:.85rem;margin-top:.25rem">'+esc(htName)+'</div>'+
    '<div style="font-family:\'Bebas Neue\';font-size:2rem;color:'+hCol+'">'+homeMatchPts+'</div>'+
    '<div style="font-size:.62rem;color:'+hCol+';font-weight:700">'+(homeMatchPts===3?'WIN':homeMatchPts===1?'DRAW':'LOSS')+' ('+m.homeScore+' pts)</div></div>'+
    '<div style="font-family:\'Bebas Neue\';font-size:1.2rem;color:var(--muted)">MATCH</div>'+
    '<div>'+teamLogoEl(at||{},40)+'<div style="font-weight:700;font-size:.85rem;margin-top:.25rem">'+esc(atName)+'</div>'+
    '<div style="font-family:\'Bebas Neue\';font-size:2rem;color:'+aCol+'">'+awayMatchPts+'</div>'+
    '<div style="font-size:.62rem;color:'+aCol+';font-weight:700">'+(awayMatchPts===3?'WIN':awayMatchPts===1?'DRAW':'LOSS')+' ('+m.awayScore+' pts)</div></div>'+
    '</div>'+
    (m.round?'<div style="text-align:center;font-size:.74rem;color:var(--acc);margin-top:.4rem;font-weight:700">'+esc(m.round)+'</div>':'')+
    (m.motm?'<div style="text-align:center;font-size:.76rem;color:var(--acc);margin-top:.2rem">MOTM: <strong>'+esc(m.motm)+'</strong></div>':'')+
    '</div>'+
    '<div style="display:grid;grid-template-columns:1fr auto 1fr;padding:0 .7rem;margin-bottom:.2rem;font-family:\'Barlow Condensed\';font-size:.78rem;font-weight:700;color:var(--muted);text-transform:uppercase">'+
    '<div style="text-align:right">'+esc(htName)+'</div><div></div><div>'+esc(atName)+'</div></div>'+
    buildScorecardTable(m.homeResults||[], m.awayResults||[], m);
  document.getElementById('viewPanel').classList.remove('hidden');
}

// ══════════════════════════════════════════════════
// ADMIN — STANDINGS (Manual + Auto)
// ══════════════════════════════════════════════════
function aStandingsHTML(){
  var rows=calcStandings();
  var teams=getTeams();
  var teamOpts=teams.map(function(t){return '<option value="'+t.id+'">'+esc(t.name)+'</option>';}).join('');
  var topPlayers=getPlayers().slice().sort(function(a,b){return realCalcPts(getStat(b.id))-realCalcPts(getStat(a.id));}).slice(0,15);

  // Manual override rows
  var manualRows='';
  rows.forEach(function(r){
    manualRows+='<div class="aitem" style="flex-wrap:wrap;gap:.4rem">'+
      '<div style="min-width:120px;font-weight:700;font-size:.88rem">'+esc(r.name)+'</div>'+
      '<div style="display:flex;gap:.4rem;flex-wrap:wrap;align-items:center">'+
      '<div class="fg" style="min-width:52px"><label style="font-size:.62rem">W</label><input id="mo_w_'+r.id+'" type="number" value="'+r.w+'" min="0" style="width:52px;padding:.3rem .4rem;font-size:.82rem"></div>'+
      '<div class="fg" style="min-width:52px"><label style="font-size:.62rem">D</label><input id="mo_d_'+r.id+'" type="number" value="'+r.d+'" min="0" style="width:52px;padding:.3rem .4rem;font-size:.82rem"></div>'+
      '<div class="fg" style="min-width:52px"><label style="font-size:.62rem">L</label><input id="mo_l_'+r.id+'" type="number" value="'+r.l+'" min="0" style="width:52px;padding:.3rem .4rem;font-size:.82rem"></div>'+
      '<div class="fg" style="min-width:52px"><label style="font-size:.62rem">GF</label><input id="mo_gf_'+r.id+'" type="number" value="'+r.gf+'" min="0" style="width:52px;padding:.3rem .4rem;font-size:.82rem"></div>'+
      '<div class="fg" style="min-width:52px"><label style="font-size:.62rem">GA</label><input id="mo_ga_'+r.id+'" type="number" value="'+r.ga+'" min="0" style="width:52px;padding:.3rem .4rem;font-size:.82rem"></div>'+
      '<button class="btn bg" onclick="saveManualStanding(\''+r.id+'\')" style="font-size:.72rem;padding:.3rem .7rem;align-self:flex-end">Save</button>'+
      '</div></div>';
  });

  return '<div class="apanel"><h3>📊 Auto Standings</h3>'+
    '<p style="font-size:.72rem;color:var(--muted);margin-bottom:.7rem">Hide removes a team from the public table without deleting them.</p>'+
    rows.map(function(r,i){
      var col=i===0?'var(--acc)':i<3?'var(--green)':'var(--muted)';
      var wrColor=r.wr>=60?'var(--green)':r.wr>=40?'var(--acc)':'var(--red)';
      var logo=r.logo;
      var logoH=logo&&logo.startsWith('http')?'<div class="mini-logo" style="width:26px;height:26px;"><img src="'+esc(logo)+'" onerror="this.parentNode.innerHTML=\'⚽\'"></div>':'<div class="mini-logo" style="width:26px;height:26px;font-size:.9rem;">'+(logo||'⚽')+'</div>';
      var hidden=state.manual_standings&&state.manual_standings[r.id]&&state.manual_standings[r.id].hidden;
      return '<div class="aitem"'+(hidden?' style="opacity:.45"':'')+'>'+
        '<div style="font-family:\'Bebas Neue\';font-size:1.3rem;width:24px;color:'+col+'">'+(hidden?'—':(i+1))+'</div>'+
        logoH+
        '<div class="ai"><div class="an">'+esc(r.name)+(hidden?' <span style="font-size:.62rem;color:var(--red)">[hidden]</span>':'')+'</div>'+
        '<div class="am">W'+r.w+' D'+r.d+' L'+r.l+' Pts:'+r.pts+' WR:<span style="color:'+wrColor+'">'+r.wr+'%</span></div></div>'+
        '<div style="font-family:\'Bebas Neue\';font-size:1.7rem;color:var(--green)">'+r.pts+'</div>'+
        (hidden?
          '<button class="btn bg" onclick="restoreTeamInTable(\''+r.id+'\')" style="font-size:.68rem;padding:.24rem .5rem">Restore</button>':
          '<button class="bd" onclick="removeTeamFromTable(\''+r.id+'\')" style="font-size:.68rem;padding:.24rem .5rem">Hide</button>')+
        '</div>';
    }).join('')+'</div>'+

    '<div class="apanel"><h3>✏️ Manual Standing Override</h3>'+
    '<p style="font-size:.73rem;color:var(--muted);margin-bottom:.8rem">Manually set W/D/L/GF/GA per team. This saves an override to Firebase that gets applied on top of auto-calc.</p>'+
    '<div style="display:flex;flex-direction:column;gap:.5rem">'+manualRows+'</div></div>'+

    '<div class="apanel"><h3>➕ Manual Entry: Add New Team to Table</h3>'+
    '<div class="fgrid">'+
    '<div class="fg"><label>Team</label><select id="mn_team">'+teamOpts+'</select></div>'+
    '<div class="fg"><label>W</label><input id="mn_w" type="number" value="0" min="0"></div>'+
    '<div class="fg"><label>D</label><input id="mn_d" type="number" value="0" min="0"></div>'+
    '<div class="fg"><label>L</label><input id="mn_l" type="number" value="0" min="0"></div>'+
    '<div class="fg"><label>GF</label><input id="mn_gf" type="number" value="0" min="0"></div>'+
    '<div class="fg"><label>GA</label><input id="mn_ga" type="number" value="0" min="0"></div>'+
    '</div><div style="margin-top:.8rem"><button class="btn bg" onclick="saveManualEntry()">Save Entry</button></div></div>'+

    '<div class="apanel"><h3>📋 Update Points via Scorecard Paste</h3>'+
    '<p style="font-size:.73rem;color:var(--muted);margin-bottom:.6rem">Paste scorecard text with POINTS block. System reads team totals and updates standings.</p>'+
    '<textarea id="st_txt" style="width:100%;background:var(--dark);border:1px solid var(--border);border-radius:8px;padding:.6rem .8rem;color:var(--text);font-family:monospace;font-size:.79rem;resize:vertical;min-height:120px;outline:none;" placeholder="Paste scorecard with POINTS: section&#10;&#10;POINTS:&#10;LLRS :25&#10;TR :07"></textarea>'+
    '<div style="margin-top:.7rem;display:flex;gap:.7rem;flex-wrap:wrap">'+
    '<button class="btn bg" onclick="parseStandingsPaste()">🔍 Parse &amp; Preview Points</button>'+
    '</div>'+
    '<div id="stPreview" style="margin-top:.8rem;display:none"></div></div>'+

    '<div class="apanel"><h3>🏆 Player Rankings</h3>'+
    '<div style="font-size:.72rem;color:var(--muted);margin-bottom:.8rem;background:rgba(0,200,83,.04);border:1px solid rgba(0,200,83,.12);padding:.5rem .8rem;border-radius:8px">'+
    'Formula: Win×10 | Loss×-10 | Draw×5 | GD×1 | MOTM×5 → ×Condition Boost<br>'+
    '<span class="cond cond-ap" style="margin-right:.3rem">🔥 A+×1.8</span>'+
    '<span class="cond cond-a" style="margin-right:.3rem">⚡ A×1.5</span>'+
    '<span class="cond cond-bp" style="margin-right:.3rem">💪 B+×1.2</span>'+
    '<span class="cond cond-bm" style="margin-right:.3rem">👍 B-×1.1</span>'+
    '<span class="cond cond-c" style="margin-right:.3rem">➖ C×1.0</span>'+
    '<span class="cond cond-d" style="margin-right:.3rem">📉 D×-1.2</span>'+
    '<span class="cond cond-e">💀 E×-1.5</span></div>'+
    topPlayers.map(function(p,i){
      var s=getStat(p.id); var t=getTeamById(p.teamId); var wr=winRatio(s);
      return '<div class="aitem">'+
        '<div style="font-family:\'Bebas Neue\';font-size:1.2rem;width:22px;color:'+(i===0?'var(--acc)':'var(--muted)')+'">'+(i+1)+'</div>'+
        playerPhotoEl(p,30)+
        '<div class="ai"><div class="an">'+esc(p.name)+' '+condBadge(wr)+'</div>'+
        '<div class="am">'+(t?esc(t.name):'')+' W'+(s.wins||0)+' D'+(s.draws||0)+' L'+(s.losses||0)+' ⚽'+(s.goals||0)+' 👑'+(s.motm||0)+' 🧤'+(s.cs||0)+' GD'+(s.gd||0)+' WR:'+wr+'%</div></div>'+
        '<div style="font-family:\'Bebas Neue\';font-size:1.5rem;color:var(--green)">'+realCalcPts(s)+'</div></div>';
    }).join('')+'</div>';
}

async function saveManualStanding(tid){
  var w=parseInt(document.getElementById('mo_w_'+tid).value)||0;
  var d=parseInt(document.getElementById('mo_d_'+tid).value)||0;
  var l=parseInt(document.getElementById('mo_l_'+tid).value)||0;
  var gf=parseInt(document.getElementById('mo_gf_'+tid).value)||0;
  var ga=parseInt(document.getElementById('mo_ga_'+tid).value)||0;
  // Save as a manual_standing doc
  await fsSet('manual_standings',tid,{teamId:tid,w:w,d:d,l:l,gf:gf,ga:ga,updatedAt:Date.now()});
  alert('✅ Standing saved for team.');
}

async function saveManualEntry(){
  var tid=document.getElementById('mn_team').value;
  if(!tid){alert('Select a team!');return;}
  var w=parseInt(document.getElementById('mn_w').value)||0;
  var d=parseInt(document.getElementById('mn_d').value)||0;
  var l=parseInt(document.getElementById('mn_l').value)||0;
  var gf=parseInt(document.getElementById('mn_gf').value)||0;
  var ga=parseInt(document.getElementById('mn_ga').value)||0;
  await fsSet('manual_standings',tid,{teamId:tid,w:w,d:d,l:l,gf:gf,ga:ga,updatedAt:Date.now()});
  alert('✅ Manual entry saved!');
}

function parseStandingsPaste(){
  var text=document.getElementById('st_txt').value.trim();
  if(!text){alert('Paste scorecard text first!');return;}
  var lines=text.split('\n').map(function(l){return l.trim();}).filter(Boolean);
  var found=[];
  lines.forEach(function(l){
    var clean=l.replace(/[🔥🏆⭐⛔👑🔑✈️👤🛠️📌]/gu,'').trim();
    // Match "TEAMNAME :25" or "TEAMNAME: 25"
    var m=clean.match(/^(.+?)\s*:\s*(\d+)\s*$/);
    if(m){
      var nm=m[1].trim();
      var pts=parseInt(m[2]);
      if(nm&&!isNaN(pts)&&pts>=0&&nm.length>1){
        // try fuzzy match to team
        var matched=fuzzyTeam(nm);
        found.push({raw:nm,pts:pts,team:matched});
      }
    }
  });
  if(!found.length){
    document.getElementById('stPreview').style.display='block';
    document.getElementById('stPreview').innerHTML='<p style="color:var(--red);font-size:.82rem">⚠️ No POINTS lines detected. Make sure format is: <code style="color:var(--green)">TEAMNAME :25</code></p>';
    return;
  }
  // Build preview with confirm buttons
  var html='<div style="background:var(--card2);border-radius:10px;padding:.9rem;border:1px solid var(--border)">';
  html+='<div style="font-size:.78rem;font-weight:700;color:var(--acc);margin-bottom:.6rem">Detected Point Totals:</div>';
  found.forEach(function(f,i){
    var teamOpts='<option value="">❌ Skip</option>'+getTeams().map(function(t){return '<option value="'+t.id+'"'+(f.team&&f.team.id===t.id?' selected':'')+'>'+esc(t.name)+'</option>';}).join('');
    html+='<div style="display:flex;align-items:center;gap:.6rem;flex-wrap:wrap;padding:.3rem 0;border-bottom:1px solid var(--border);font-size:.82rem">'+
      '<span style="min-width:80px;font-weight:700;color:var(--green)">'+f.pts+' pts</span>'+
      '<span style="color:var(--muted)">→ "'+esc(f.raw)+'"</span>'+
      '<select id="spteam_'+i+'" style="background:var(--dark);border:1px solid var(--border);border-radius:6px;padding:.2rem .4rem;color:var(--text);font-size:.78rem;flex:1">'+teamOpts+'</select>'+
      '</div>';
  });
  html+='</div><button class="btn bg" onclick="applyStandingsPaste('+found.length+')" style="margin-top:.7rem;font-size:.82rem">✅ Apply Points</button>';
  document.getElementById('stPreview').innerHTML=html;
  document.getElementById('stPreview').style.display='block';
  // store found for apply
  window._stFound=found;
}

async function applyStandingsPaste(count){
  var found=window._stFound||[];
  var applied=0;
  for(var i=0;i<count;i++){
    var sel=document.getElementById('spteam_'+i);
    if(!sel||!sel.value) continue;
    var tid=sel.value;
    var pts=found[i]?found[i].pts:0;
    // We only have total points from paste, derive a plausible W/D/L
    // Save as override with just pts (special field)
    var existing=state.manual_standings&&state.manual_standings[tid]||{};
    await fsSet('manual_standings',tid,Object.assign({},existing,{teamId:tid,pointsOverride:pts,updatedAt:Date.now()}));
    applied++;
  }
  alert('✅ Applied '+applied+' team points!');
  document.getElementById('stPreview').style.display='none';
  document.getElementById('st_txt').value='';
}

// ══════════════════════════════════════════════════
// STANDINGS — Hide/Restore team from table
// ══════════════════════════════════════════════════
async function removeTeamFromTable(tid){
  var existing=Object.assign({},state.manual_standings&&state.manual_standings[tid]||{});
  existing.teamId=tid; existing.hidden=true; existing.updatedAt=Date.now();
  await fsSet('manual_standings',tid,existing);
}
async function restoreTeamInTable(tid){
  var existing=Object.assign({},state.manual_standings&&state.manual_standings[tid]||{});
  existing.teamId=tid; existing.hidden=false; existing.updatedAt=Date.now();
  await fsSet('manual_standings',tid,existing);
}

// ══════════════════════════════════════════════════
// EDIT TEAM MODAL
// ══════════════════════════════════════════════════
function openEditTeam(tid){
  var t=getTeamById(tid); if(!t){alert('Team not found');return;}
  var modal=document.getElementById('editTeamModal');
  var content=document.getElementById('editTeamContent');
  if(!modal){
    var div=document.createElement('div');
    div.id='editTeamModal';
    div.className='moverlay';
    div.innerHTML='<div class="modal" style="width:min(92vw,520px)"><div id="editTeamContent"></div></div>';
    document.body.appendChild(div);
    modal=div; content=div.querySelector('#editTeamContent');
  }
  content.innerHTML=
    '<div style="display:flex;align-items:center;gap:.8rem;margin-bottom:1.2rem">'+
    '<div style="flex:1"><div style="font-family:\'Bebas Neue\';font-size:1.6rem;color:var(--green)">Edit Team</div>'+
    '<div style="font-size:.75rem;color:var(--muted)">Changes sync to Firebase instantly</div></div>'+
    '<button onclick="document.getElementById(\'editTeamModal\').classList.add(\'hidden\')" style="background:none;border:none;color:var(--muted);font-size:1.5rem;cursor:pointer">✕</button>'+
    '</div>'+
    // Current preview
    '<div style="display:flex;align-items:center;gap:1rem;padding:.8rem;background:var(--card2);border-radius:10px;border:1px solid var(--border);margin-bottom:1rem">'+
    teamBigLogo(t)+
    '<div><div style="font-weight:700;font-size:1rem">'+esc(t.name)+'</div>'+
    '<div style="font-size:.75rem;color:var(--muted)">👔 '+esc(t.president||'—')+'</div></div>'+
    '</div>'+
    // Fields
    '<div class="fgrid" style="margin-bottom:1rem">'+
    '<div class="fg"><label>Team Name *</label><input id="et_name" value="'+esc(t.name)+'" placeholder="Team name"></div>'+
    '<div class="fg"><label>President / Manager</label><input id="et_pres" value="'+esc(t.president||'')+'" placeholder="Full name"></div>'+
    '<div class="fg"><label>Team Color</label><input id="et_color" type="color" value="'+(t.color||'#00C853')+'"></div>'+
    '</div>'+
    // Logo update
    '<div style="background:rgba(0,200,83,.04);border:1px solid rgba(0,200,83,.12);border-radius:10px;padding:.9rem;margin-bottom:1rem">'+
    '<div style="font-size:.72rem;color:var(--green);font-weight:700;text-transform:uppercase;letter-spacing:1px;margin-bottom:.5rem">Logo (URL or Upload)</div>'+
    '<div class="logo-input-group">'+
    '<div class="logo-tabs-sm">'+
    '<span class="logo-tab-sm active" onclick="switchLogoTab(\'url\',\'et_lg\',this)">🔗 URL / Emoji</span>'+
    '<span class="logo-tab-sm" onclick="switchLogoTab(\'upload\',\'et_lg\',this)">📤 Upload</span>'+
    '</div>'+
    '<div id="et_lg_url_wrap"><input id="et_lg_url" placeholder="https://… or emoji" oninput="previewLogo(\'et_lg\')" value="'+esc(t.logoUrl&&t.logoUrl.startsWith('http')?t.logoUrl:t.logo||'')+'"></div>'+
    '<div id="et_lg_upload_wrap" style="display:none"><input id="et_lg_file" type="file" accept="image/*" style="padding:.3rem" onchange="previewLogoFile(\'et_lg\')"></div>'+
    '<div style="display:flex;align-items:center;gap:.7rem;margin-top:.4rem">'+
    '<div class="logo-preview-circle" id="et_lg_prev">'+(t.logoUrl&&t.logoUrl.startsWith('http')?'<img src="'+esc(t.logoUrl)+'" style="width:100%;height:100%;object-fit:cover;border-radius:50%">':esc(t.logo||'⚽'))+'</div>'+
    '<div style="font-size:.7rem;color:var(--muted)">Current / Preview</div></div>'+
    '</div></div>'+
    '<div style="display:flex;gap:.7rem;flex-wrap:wrap">'+
    '<button class="btn bg" onclick="saveEditTeam(\''+tid+'\')">💾 Save Changes</button>'+
    '<button class="btn" style="background:var(--border);color:var(--text)" onclick="document.getElementById(\'editTeamModal\').classList.add(\'hidden\')">Cancel</button>'+
    '</div>';
  modal.classList.remove('hidden');
}

async function saveEditTeam(tid){
  var name=document.getElementById('et_name').value.trim();
  if(!name){alert('Team name required!');return;}
  var newLogoUrl=await resolveLogoUrl('et_lg');
  var t=getTeamById(tid)||{};
  var logoUrl=newLogoUrl||(t.logoUrl||t.logo||'⚽');
  var data=Object.assign({},t,{
    id:tid,
    name:name,
    president:document.getElementById('et_pres').value.trim(),
    color:document.getElementById('et_color').value,
    logoUrl:logoUrl
  });
  await fsSet('teams',tid,data);
  document.getElementById('editTeamModal').classList.add('hidden');
  alert('✅ Team updated!');
}

// ══════════════════════════════════════════════════
// FALLBACK: if Firebase not loaded after 5s, show demo
// ══════════════════════════════════════════════════
setTimeout(function(){
  if(!fbReady){
    document.getElementById('fbDot').style.background='var(--acc)';
    document.getElementById('fbTxt').textContent='Offline';
    document.getElementById('loader').classList.add('gone');
    fbReady=true;
    renderHome();
  }
},5000);
</script>
</body>
</html>
