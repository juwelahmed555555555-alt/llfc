<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
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

window._FB = { db, storage, collection, doc, setDoc, getDoc, getDocs, deleteDoc, onSnapshot, writeBatch, ref, uploadBytes, getDownloadURL };

function listen(colName, setter){
  onSnapshot(collection(db, colName), snap => {
    const data = {};
    snap.forEach(d => { data[d.id] = d.data(); });
    setter(data);
  });
}

window.addEventListener('load', () => {
  listen('teams',   d => { window.fbTeams   = d; rebuildLocal(); });
  listen('players', d => { window.fbPlayers = d; rebuildLocal(); });
  listen('fixtures',d => { window.fbFixtures= d; rebuildLocal(); });
  listen('matches', d => { window.fbMatches = d; rebuildLocal(); });
  listen('stats',   d => { window.fbStats   = d; rebuildLocal(); });
  listen('manual_standings', d => { window.fbManualStandings = d; rebuildLocal(); });
  listen('news',    d => { window.fbNews    = d; rebuildLocal(); });
  listen('player_matches',    d => { window.fbPlayerMatches    = d; rebuildLocal(); });
  listen('squad_submissions', d => { window.fbSquadSubmissions = d; rebuildLocal(); });
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
html{color-scheme:dark;background:#080D0A;}
body{background:var(--dark);color:var(--text);font-family:'Barlow',sans-serif;min-height:100vh;overflow-x:hidden;color-scheme:dark;}

/* LOADER */
#loader{position:fixed;inset:0;background:var(--dark);display:flex;flex-direction:column;align-items:center;justify-content:center;z-index:999;transition:opacity .5s;}
#loader.gone{opacity:0;pointer-events:none;}
.spin{width:52px;height:52px;border:3px solid var(--border);border-top-color:var(--green);border-radius:50%;animation:spin .8s linear infinite;margin-bottom:1rem;}
@keyframes spin{to{transform:rotate(360deg)}}

/* HEADER */
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

/* SECTIONS */
.section{display:none;padding:1.5rem;max-width:1200px;margin:0 auto;animation:fi .3s;background:transparent;}
.section.active{display:block;}
@keyframes fi{from{opacity:0;transform:translateY(8px)}to{opacity:1;transform:translateY(0)}}

/* HERO */
.hero{text-align:center;padding:2.5rem 1rem;background:radial-gradient(ellipse at center,rgba(0,200,83,.08) 0%,transparent 70%);border-radius:16px;margin-bottom:1.5rem;border:1px solid var(--border);}
.hero h1{font-family:'Bebas Neue';font-size:3.5rem;letter-spacing:4px;color:var(--green);}
.hero p{color:var(--muted);font-size:1rem;margin-top:.4rem;}
.stats-row{display:flex;gap:.8rem;justify-content:center;flex-wrap:wrap;margin-top:1.5rem;}
.stat-box{background:var(--card);border:1px solid var(--border);border-radius:10px;padding:.8rem 1.2rem;min-width:110px;text-align:center;}
.stat-box .num{font-family:'Bebas Neue';font-size:2rem;color:var(--green);}
.stat-box .lbl{font-size:.7rem;color:var(--muted);text-transform:uppercase;letter-spacing:1px;}

/* NEWS TICKER */
.news-ticker{background:rgba(255,214,0,.05);border:1px solid rgba(255,214,0,.2);border-radius:10px;padding:.55rem .9rem;margin-bottom:1rem;display:flex;align-items:center;gap:.7rem;overflow:hidden;}
.news-ticker-label{background:var(--acc);color:#000;font-family:'Barlow Condensed';font-size:.72rem;font-weight:900;padding:.15rem .5rem;border-radius:4px;letter-spacing:1px;white-space:nowrap;}
.news-ticker-track{flex:1;overflow:hidden;position:relative;}
.news-ticker-inner{display:flex;gap:2rem;animation:ticker 28s linear infinite;white-space:nowrap;}
.news-ticker-inner:hover{animation-play-state:paused;}
@keyframes ticker{from{transform:translateX(0)}to{transform:translateX(-50%)}}
.news-item-tick{font-size:.8rem;color:var(--text);cursor:pointer;transition:color .2s;white-space:nowrap;}
.news-item-tick:hover{color:var(--acc);}
.news-item-tick .ni-icon{margin-right:.3rem;}

/* NEWS GRID */
.news-grid{display:grid;grid-template-columns:repeat(auto-fill,minmax(280px,1fr));gap:.9rem;margin-bottom:1.5rem;}
.news-card{background:var(--card);border:1px solid var(--border);border-radius:14px;padding:1rem;cursor:pointer;transition:all .2s;}
.news-card:hover{border-color:var(--acc);transform:translateY(-2px);}
.news-card.hot{border-color:rgba(255,61,61,.4);background:linear-gradient(135deg,rgba(255,61,61,.04),var(--card));}
.news-card.result{border-color:rgba(0,200,83,.3);}
.news-card.table{border-color:rgba(255,214,0,.3);}
.nc-tag{display:inline-block;padding:.1rem .45rem;border-radius:4px;font-size:.64rem;font-weight:800;letter-spacing:.5px;text-transform:uppercase;margin-bottom:.4rem;}
.nc-hot{background:rgba(255,61,61,.15);color:var(--red);border:1px solid rgba(255,61,61,.35);}
.nc-result{background:rgba(0,200,83,.12);color:var(--green);border:1px solid rgba(0,200,83,.3);}
.nc-table{background:rgba(255,214,0,.12);color:var(--acc);border:1px solid rgba(255,214,0,.3);}
.nc-special{background:rgba(170,0,255,.12);color:#CC66FF;border:1px solid rgba(170,0,255,.3);}
.nc-title{font-family:'Barlow Condensed';font-size:1rem;font-weight:700;line-height:1.3;margin-bottom:.25rem;}
.nc-body{font-size:.78rem;color:var(--muted);line-height:1.45;}
.nc-time{font-size:.65rem;color:var(--muted);margin-top:.4rem;}

/* FEATURED SECTION */
.featured-row{display:grid;grid-template-columns:repeat(auto-fill,minmax(260px,1fr));gap:.9rem;margin-bottom:1.5rem;}
.feat-card{background:linear-gradient(135deg,rgba(0,200,83,.07),rgba(0,200,83,.02));border:1px solid rgba(0,200,83,.25);border-radius:14px;padding:1rem;display:flex;align-items:center;gap:.85rem;}
.feat-card.gold{background:linear-gradient(135deg,rgba(255,214,0,.07),rgba(255,214,0,.02));border-color:rgba(255,214,0,.3);}
.feat-rank-badge{width:36px;height:36px;border-radius:50%;display:flex;align-items:center;justify-content:center;font-family:'Bebas Neue';font-size:1.2rem;flex-shrink:0;}

/* STITLE */
.stitle{font-family:'Bebas Neue';font-size:1.8rem;letter-spacing:3px;color:var(--green);border-bottom:2px solid var(--border);padding-bottom:.4rem;margin-bottom:1.2rem;}
.grid2{display:grid;grid-template-columns:repeat(auto-fill,minmax(290px,1fr));gap:1rem;}
.card{background:var(--card);border:1px solid var(--border);border-radius:14px;padding:1.1rem;transition:all .2s;cursor:pointer;}
.card:hover{border-color:var(--green);transform:translateY(-2px);box-shadow:0 8px 30px rgba(0,200,83,.1);}

/* BADGES */
.badge{display:inline-block;padding:.12rem .48rem;border-radius:4px;font-size:.68rem;font-weight:700;letter-spacing:.5px;text-transform:uppercase;}
.bl{background:rgba(0,200,83,.12);color:var(--green);border:1px solid rgba(0,200,83,.3);}
.by{background:rgba(255,214,0,.12);color:var(--acc);border:1px solid rgba(255,214,0,.3);}
.br{background:rgba(255,61,61,.12);color:var(--red);border:1px solid rgba(255,61,61,.3);}

/* CATEGORY BADGES — SVG icon, no emoji */
.cat-badge{display:inline-flex;align-items:center;gap:.28rem;padding:.14rem .5rem .14rem .32rem;border-radius:5px;font-family:'Barlow Condensed';font-size:.67rem;font-weight:800;letter-spacing:.7px;text-transform:uppercase;vertical-align:middle;flex-shrink:0;line-height:1;}
.cat-local{background:rgba(0,200,83,.1);color:#00C853;border:1px solid rgba(0,200,83,.35);}
.cat-youth{background:rgba(41,121,255,.1);color:#5B9BFF;border:1px solid rgba(41,121,255,.35);}
.cat-invited{background:rgba(255,100,30,.1);color:#FF7A40;border:1px solid rgba(255,100,30,.35);}
.cat-icon{width:10px;height:10px;display:inline-block;flex-shrink:0;}
/* COIN BADGE */
.coin-badge{display:inline-flex;align-items:center;gap:.28rem;padding:.14rem .5rem .14rem .32rem;border-radius:5px;background:rgba(255,214,0,.1);border:1px solid rgba(255,214,0,.38);color:var(--acc);font-family:'Barlow Condensed';font-size:.7rem;font-weight:800;letter-spacing:.5px;vertical-align:middle;flex-shrink:0;line-height:1;}
.coin-icon{width:10px;height:10px;display:inline-block;flex-shrink:0;}
.coin-badge-lg{display:inline-flex;align-items:center;gap:.38rem;padding:.22rem .7rem .22rem .42rem;border-radius:8px;background:linear-gradient(135deg,rgba(255,214,0,.14),rgba(255,160,0,.07));border:1px solid rgba(255,214,0,.4);color:var(--acc);font-family:'Bebas Neue';font-size:.95rem;letter-spacing:1px;vertical-align:middle;}
.coin-icon-lg{width:14px;height:14px;display:inline-block;flex-shrink:0;}
.squad-total-value{background:linear-gradient(135deg,rgba(255,214,0,.08),rgba(255,160,0,.04));border:1px solid rgba(255,214,0,.25);border-radius:10px;padding:.55rem .9rem;display:flex;align-items:center;gap:.6rem;margin-bottom:.8rem;}

/* CONDITION BADGES */
.cond{display:inline-flex;align-items:center;gap:.25rem;padding:.12rem .5rem;border-radius:20px;font-size:.7rem;font-weight:700;letter-spacing:.5px;}
.cond-ap{background:rgba(0,255,106,.15);color:#00FF6A;border:1px solid rgba(0,255,106,.4);}
.cond-a {background:rgba(0,200,83,.15);color:var(--green);border:1px solid rgba(0,200,83,.4);}
.cond-bp{background:rgba(41,121,255,.15);color:#7CB9FF;border:1px solid rgba(41,121,255,.4);}
.cond-bm{background:rgba(100,181,246,.12);color:#90CAF9;border:1px solid rgba(100,181,246,.3);}
.cond-c {background:rgba(255,214,0,.12);color:var(--acc);border:1px solid rgba(255,214,0,.3);}
.cond-d {background:rgba(255,100,0,.12);color:#FF8A50;border:1px solid rgba(255,100,0,.3);}
.cond-e {background:rgba(255,61,61,.15);color:var(--red);border:1px solid rgba(255,61,61,.4);}

/* TABS */
.tabs{display:flex;gap:.4rem;margin-bottom:1rem;border-bottom:1px solid var(--border);padding-bottom:.7rem;flex-wrap:wrap;}
.tab{padding:.33rem .85rem;border:1px solid transparent;border-radius:20px;font-size:.78rem;font-weight:600;cursor:pointer;transition:all .2s;background:transparent;color:var(--muted);font-family:'Barlow Condensed';text-transform:uppercase;letter-spacing:.5px;}
.tab.active{background:rgba(0,200,83,.1);color:var(--green);border-color:var(--green);}

/* TEAM LOGO */
.t-logo-wrap{width:52px;height:52px;border-radius:50%;background:var(--card2);border:2px solid var(--border);display:flex;align-items:center;justify-content:center;font-size:1.5rem;overflow:hidden;flex-shrink:0;}
.t-logo-wrap img{width:100%;height:100%;object-fit:cover;border-radius:50%;}
.mini-logo{width:28px;height:28px;border-radius:50%;background:var(--card2);border:1px solid var(--border);display:flex;align-items:center;justify-content:center;font-size:.9rem;overflow:hidden;flex-shrink:0;}
.mini-logo img{width:100%;height:100%;object-fit:cover;border-radius:50%;}

/* PHOTO */
.p-photo{border-radius:50%;background:var(--card2);border:2px solid var(--border);display:flex;align-items:center;justify-content:center;overflow:hidden;flex-shrink:0;}
.p-photo img{width:100%;height:100%;object-fit:cover;border-radius:50%;}

/* BEST PLAYER */
.best-card{background:linear-gradient(135deg,rgba(255,214,0,.07),rgba(0,200,83,.04));border:1px solid rgba(255,214,0,.2);border-radius:12px;padding:.85rem 1rem;display:flex;align-items:center;gap:.8rem;margin-top:.8rem;}

/* FIXTURE */
.fx-card{background:var(--card);border:1px solid var(--border);border-radius:14px;padding:1rem 1.2rem;display:flex;align-items:center;gap:.8rem;flex-wrap:wrap;cursor:pointer;transition:all .2s;}
.fx-card:hover{border-color:var(--green);box-shadow:0 4px 20px rgba(0,200,83,.1);}
.vs-block{flex:1;display:flex;align-items:center;gap:.7rem;justify-content:center;min-width:200px;}
.t-side{text-align:center;min-width:72px;}
.t-side .tl{width:38px;height:38px;border-radius:50%;background:var(--card2);border:2px solid var(--border);display:flex;align-items:center;justify-content:center;font-size:1.1rem;margin:0 auto .25rem;overflow:hidden;}
.t-side .tl img{width:100%;height:100%;object-fit:cover;border-radius:50%;}
.t-side .tn{font-size:.72rem;font-weight:600;line-height:1.2;}
.vs-txt{font-family:'Bebas Neue';font-size:1.6rem;color:var(--muted);}
.score-txt{font-family:'Bebas Neue';font-size:2rem;color:var(--green);letter-spacing:3px;cursor:pointer;}
.score-txt:hover{color:var(--glow);}
.fx-info{text-align:right;min-width:130px;}
.fx-meta{font-size:.7rem;color:var(--muted);margin-top:.3rem;}
.sbadge{padding:.18rem .55rem;border-radius:20px;font-size:.68rem;font-weight:700;text-transform:uppercase;}
.s-up{background:rgba(255,214,0,.1);color:var(--acc);border:1px solid rgba(255,214,0,.3);}
.s-pl{background:rgba(0,200,83,.1);color:var(--green);border:1px solid rgba(0,200,83,.3);}
.s-lv{background:rgba(255,61,61,.16);color:var(--red);border:1px solid rgba(255,61,61,.4);animation:pulse 1.5s infinite;}
@keyframes pulse{0%,100%{opacity:1}50%{opacity:.5}}

/* WIN PROBABILITY BAR */
.prob-bar{height:8px;border-radius:4px;background:var(--border);overflow:hidden;display:flex;}
.prob-home{height:100%;background:var(--green);transition:width .5s;}
.prob-draw{height:100%;background:var(--acc);}
.prob-away{height:100%;background:var(--red);}

/* TABLE */
.twrap{overflow-x:auto;border-radius:12px;border:1px solid var(--border);background:var(--card);}
table{width:100%;border-collapse:collapse;font-size:.88rem;background:var(--card);color:var(--text);}
thead{background:#0d1f10;}
thead tr{background:#0d1f10;}
th{padding:.7rem .9rem;text-align:left;font-family:'Barlow Condensed';font-size:.76rem;letter-spacing:1px;text-transform:uppercase;color:var(--green);font-weight:600;white-space:nowrap;background:#0d1f10;}
tbody{background:var(--card);}
tbody tr{background:var(--card);}
td{padding:.6rem .9rem;border-top:1px solid var(--border);color:var(--text);background:var(--card);}
tr:hover td{background:#142016 !important;}
.pts-val{font-family:'Bebas Neue';font-size:1.3rem;color:var(--green);}
.pos-num{font-family:'Bebas Neue';font-size:1.3rem;}
.team-cell{display:flex;align-items:center;gap:.5rem;}
.form-dots{display:flex;gap:.2rem;}
.fd{width:8px;height:8px;border-radius:50%;}
.fw{background:var(--green);}.ld{background:var(--red);}.dr{background:var(--muted);}

/* RANKING */
.rank-tabs{display:flex;gap:.4rem;margin-bottom:1rem;flex-wrap:wrap;}
.rtab{padding:.33rem .85rem;border:1px solid var(--border);border-radius:20px;font-size:.76rem;font-weight:600;cursor:pointer;transition:all .2s;background:transparent;color:var(--muted);font-family:'Barlow Condensed';}
.rtab.active{background:var(--green);color:#000;border-color:var(--green);}

/* ADMIN */
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

/* MODAL */
.moverlay{position:fixed;inset:0;background:rgba(0,0,0,.85);display:flex;align-items:center;justify-content:center;z-index:200;backdrop-filter:blur(6px);}
.moverlay.hidden{display:none;}
.modal{background:var(--card);border:1px solid var(--green);border-radius:16px;padding:1.8rem;width:min(92vw,620px);max-height:88vh;overflow-y:auto;box-shadow:0 0 70px rgba(0,200,83,.18);}
.modal::-webkit-scrollbar{width:4px;}
.modal::-webkit-scrollbar-thumb{background:var(--green);border-radius:2px;}
.modal h2{font-family:'Bebas Neue';font-size:1.9rem;color:var(--green);margin-bottom:.3rem;}
.modal p.sub{font-size:.82rem;color:var(--muted);margin-bottom:1.2rem;}
.modal input,.modal select{width:100%;background:var(--dark);border:1px solid var(--border);border-radius:8px;padding:.62rem .88rem;color:var(--text);font-family:'Barlow';font-size:.9rem;outline:none;margin-bottom:.85rem;transition:border-color .2s;}
.modal input:focus,.modal select:focus{border-color:var(--green);}
.modal select option{background:var(--dark);}
.merr{color:var(--red);font-size:.78rem;margin-bottom:.5rem;display:none;}
.mbtns{display:flex;gap:.7rem;}

/* LOGO INPUT */
.logo-input-group{display:flex;flex-direction:column;gap:.4rem;}
.logo-tabs-sm{display:flex;gap:.3rem;margin-bottom:.3rem;}
.logo-tab-sm{padding:.22rem .65rem;border:1px solid var(--border);border-radius:12px;font-size:.7rem;cursor:pointer;color:var(--muted);font-family:'Barlow Condensed';font-weight:600;background:transparent;}
.logo-tab-sm.active{background:rgba(0,200,83,.1);color:var(--green);border-color:var(--green);}
.logo-preview-circle{width:52px;height:52px;border-radius:50%;background:var(--card2);border:2px dashed var(--border);display:flex;align-items:center;justify-content:center;font-size:1.3rem;overflow:hidden;flex-shrink:0;}
.logo-preview-circle img{width:100%;height:100%;object-fit:cover;border-radius:50%;}

/* EXPAND */
.expand-sec{display:none;background:var(--dark);border:1px solid var(--border);border-left:3px solid var(--green);border-radius:0 0 9px 9px;padding:.9rem;margin-top:-1px;}

/* FIREBASE STATUS */
.fb-status{display:flex;align-items:center;gap:.4rem;font-size:.7rem;color:var(--muted);padding:.2rem .6rem;border-radius:20px;border:1px solid var(--border);margin-left:auto;}
.fb-dot{width:7px;height:7px;border-radius:50%;background:var(--muted);}
.fb-dot.connected{background:var(--green);box-shadow:0 0 6px var(--green);}

/* PLAYER PROFILE MODAL */
.profile-modal{background:var(--card);border:1px solid var(--green);border-radius:16px;padding:0;width:min(92vw,680px);max-height:90vh;overflow-y:auto;box-shadow:0 0 70px rgba(0,200,83,.18);}
.profile-header{background:linear-gradient(135deg,rgba(0,200,83,.12),rgba(0,200,83,.03));padding:1.5rem;border-bottom:1px solid var(--border);display:flex;align-items:center;gap:1rem;}
.profile-body{padding:1.2rem;}
.profile-stat-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:.6rem;margin:.8rem 0;}
.pstat{background:var(--card2);border:1px solid var(--border);border-radius:10px;padding:.6rem;text-align:center;}
.pstat .pv{font-family:'Bebas Neue';font-size:1.6rem;color:var(--green);}
.pstat .pl{font-size:.62rem;color:var(--muted);text-transform:uppercase;letter-spacing:.5px;}
.match-hist-item{background:var(--dark);border:1px solid var(--border);border-radius:10px;padding:.65rem .9rem;display:flex;align-items:center;gap:.7rem;margin-bottom:.4rem;}
.mh-result{width:24px;height:24px;border-radius:50%;display:flex;align-items:center;justify-content:center;font-family:'Barlow Condensed';font-weight:900;font-size:.82rem;flex-shrink:0;}

/* H2H MODAL */
.h2h-modal{background:var(--card);border:1px solid rgba(41,121,255,.4);border-radius:16px;padding:1.5rem;width:min(92vw,680px);max-height:90vh;overflow-y:auto;}

/* RANK INFO BOX */
.rank-info-box{background:rgba(0,200,83,.04);border:1px solid rgba(0,200,83,.15);border-radius:10px;padding:.7rem 1rem;margin-bottom:1rem;font-size:.76rem;color:var(--muted);line-height:1.6;}
.rank-info-box strong{color:var(--green);}
.rank-info-box .sep{color:var(--border);margin:0 .3rem;}

/* BEST XI — 4-3-3 formation */
.pitch{background:linear-gradient(180deg,#0a2010 0%,#0d2814 50%,#0a2010 100%);border:1px solid rgba(0,200,83,.15);border-radius:16px;padding:1.5rem .8rem 1rem;position:relative;overflow:hidden;margin-bottom:.5rem;}
.pitch::before{content:'';position:absolute;inset:0;background:repeating-linear-gradient(0deg,transparent,transparent 48px,rgba(0,200,83,.06) 48px,rgba(0,200,83,.06) 49px),repeating-linear-gradient(90deg,transparent,transparent 48px,rgba(0,200,83,.04) 48px,rgba(0,200,83,.04) 49px);}
.pitch-center{position:absolute;top:50%;left:50%;transform:translate(-50%,-50%);width:80px;height:80px;border-radius:50%;border:1px solid rgba(0,200,83,.15);pointer-events:none;}
.pitch-line{position:absolute;left:10%;right:10%;border-top:1px solid rgba(0,200,83,.12);}
.formation-row{display:flex;justify-content:center;gap:.5rem;margin-bottom:.9rem;position:relative;z-index:1;flex-wrap:wrap;}
.xi-player{display:flex;flex-direction:column;align-items:center;gap:.25rem;min-width:58px;max-width:72px;cursor:pointer;transition:transform .2s;}
.xi-player:hover{transform:translateY(-3px);}
.xi-avatar{width:46px;height:46px;border-radius:50%;border:2px solid var(--green);background:var(--card2);display:flex;align-items:center;justify-content:center;overflow:hidden;position:relative;box-shadow:0 0 12px rgba(0,200,83,.25);}
.xi-avatar img{width:100%;height:100%;object-fit:cover;border-radius:50%;}
.xi-avatar.gold{border-color:var(--acc);box-shadow:0 0 14px rgba(255,214,0,.3);}
.xi-name{font-family:'Barlow Condensed';font-size:.65rem;font-weight:700;color:var(--text);text-align:center;line-height:1.2;max-width:68px;overflow:hidden;text-overflow:ellipsis;white-space:nowrap;}
.xi-pts{font-size:.58rem;color:var(--green);font-family:'Barlow Condensed';font-weight:600;}
.xi-pos-badge{position:absolute;bottom:-2px;right:-2px;width:16px;height:16px;border-radius:50%;background:var(--green);display:flex;align-items:center;justify-content:center;font-size:7px;font-weight:900;color:#000;font-family:'Barlow Condensed';}
.coach-row{display:flex;justify-content:center;margin-top:.2rem;position:relative;z-index:1;}
.coach-card{background:rgba(255,214,0,.07);border:1px solid rgba(255,214,0,.2);border-radius:10px;padding:.5rem 1rem;display:flex;align-items:center;gap:.6rem;}

@media(max-width:600px){
  header{flex-direction:column;padding:.8rem;gap:.4rem;}
  nav{flex-wrap:wrap;justify-content:center;}
  .hero h1{font-size:2.2rem;}
  .fx-card{flex-direction:column;text-align:center;}
  .fx-info{text-align:center;}
  .profile-stat-grid{grid-template-columns:repeat(2,1fr);}
}
</style>
<script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
</head>
<body>

<!-- LOADER -->
<div id="loader">
  <div class="spin"></div>
  <div style="font-family:'Bebas Neue';font-size:1.4rem;color:var(--green);letter-spacing:3px">JUVENILE LEAGUE</div>
  <div style="font-size:.75rem;color:var(--muted);margin-top:.3rem">Connecting to Firebase…</div>
</div>

<!-- CAPTAIN LOGIN MODAL -->
<div class="moverlay hidden" id="captainLoginModal">
  <div class="modal" style="width:min(92vw,380px)">
    <div style="font-family:'Bebas Neue';font-size:1.6rem;color:var(--green);margin-bottom:.3rem">Captain Login</div>
    <p class="sub">Enter your team password to access the captain dashboard.</p>
    <p class="merr" id="capLoginErr">Wrong password. Try again.</p>
    <select id="capTeamSel" style="width:100%;background:var(--dark);border:1px solid var(--border);border-radius:8px;padding:.55rem .75rem;color:var(--text);font-family:'Barlow';font-size:.9rem;outline:none;margin-bottom:.7rem">
      <option value="">Select your team…</option>
    </select>
    <input type="password" id="capPwd" placeholder="Team password…" onkeydown="if(event.key==='Enter')doCapLogin()">
    <div class="mbtns" style="margin-top:.8rem">
      <button class="btn bg" onclick="doCapLogin()">Login</button>
      <button class="btn" style="background:var(--border);color:var(--text)" onclick="closeCapLogin()">Cancel</button>
    </div>
  </div>
</div>

<!-- LOGIN MODAL -->
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

<!-- TEAM DETAIL MODAL -->
<div class="moverlay hidden" id="teamModal">
  <div class="modal" id="teamModalContent"></div>
</div>

<!-- PLAYER PROFILE MODAL -->
<div class="moverlay hidden" id="playerProfileModal">
  <div class="profile-modal" id="playerProfileContent"></div>
</div>

<!-- FIXTURE DETAIL MODAL (H2H) -->
<div class="moverlay hidden" id="fxDetailModal">
  <div class="h2h-modal" id="fxDetailContent"></div>
</div>

<!-- MATCH DETAIL MODAL -->
<div class="moverlay hidden" id="matchDetailModal">
  <div class="modal" id="matchDetailContent"></div>
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
    <button class="admin-btn" id="capNavBtn" style="background:linear-gradient(135deg,#2979FF,#1565C0)!important;color:#fff!important;" onclick="handleCapNav()">⚽ Captain</button>
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
  <h2 class="stitle" style="margin-top:.5rem">🏆 Featured Players</h2>
  <div id="featuredPlayers" class="featured-row"></div>
  <h2 class="stitle">🥇 Top Teams</h2>
  <div id="featuredTeams" class="featured-row"></div>
  <h2 class="stitle" style="margin-top:1rem">Tournament Best XI</h2>
  <div id="tournamentBestXI"></div>
  <h2 class="stitle" style="margin-top:1rem">Latest Fixtures</h2>
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
  <div style="display:flex;align-items:center;justify-content:space-between;flex-wrap:wrap;gap:.6rem;margin-bottom:1rem;">
    <h2 class="stitle" style="margin-bottom:0;border:none;">Points Table</h2>
    <button onclick="downloadStandingsJPG()" style="background:linear-gradient(135deg,#00C853,#009624);color:#000;border:none;border-radius:10px;padding:.5rem 1.2rem;font-family:'Barlow Condensed',sans-serif;font-size:.9rem;font-weight:800;cursor:pointer;letter-spacing:.5px;display:flex;align-items:center;gap:.4rem;box-shadow:0 4px 15px rgba(0,200,83,.3);">
      <svg width="14" height="14" viewBox="0 0 24 24" fill="currentColor"><path d="M12 16l-6-6h4V4h4v6h4l-6 6zm-8 2h16v2H4v-2z"/></svg>
      Download JPG
    </button>
  </div>
  <div class="twrap">
    <table><thead><tr>
      <th style="color:var(--green)">#</th>
      <th style="color:var(--green)">Team</th>
      <th style="color:var(--green)">P</th>
      <th style="color:var(--green)">W</th>
      <th style="color:var(--green)">D</th>
      <th style="color:var(--green)">L</th>
      <th style="color:var(--green)">WR%</th>
      <th style="color:var(--green)">GF</th>
      <th style="color:var(--green)">GA</th>
      <th style="color:var(--green)">GD</th>
      <th style="color:var(--green)">PTS</th>
      <th style="color:var(--green)">Form</th>
    </tr></thead><tbody id="ptBody"></tbody></table>
  </div>
</div>

<div class="section" id="section-ranking">
  <div style="display:flex;align-items:center;justify-content:space-between;flex-wrap:wrap;gap:.6rem;margin-bottom:1rem;">
    <h2 class="stitle" style="margin-bottom:0;border:none;">Player Rankings</h2>
    <div style="display:flex;gap:.5rem;flex-wrap:wrap;">
      <button onclick="downloadRankingJPG(1)" style="background:linear-gradient(135deg,#DC1E1E,#8B0000);color:#fff;border:none;border-radius:8px;padding:.45rem 1rem;font-family:'Barlow Condensed',sans-serif;font-size:.82rem;font-weight:800;cursor:pointer;letter-spacing:.5px;">
        Download #1-10
      </button>
      <button onclick="downloadRankingJPG(2)" style="background:linear-gradient(135deg,#444,#222);color:#fff;border:none;border-radius:8px;padding:.45rem 1rem;font-family:'Barlow Condensed',sans-serif;font-size:.82rem;font-weight:800;cursor:pointer;letter-spacing:.5px;">
        Download #11-20
      </button>
      <button onclick="downloadSigningJPG('best')" style="background:linear-gradient(135deg,#FFD700,#FFA000);color:#000;border:none;border-radius:8px;padding:.45rem 1rem;font-family:'Barlow Condensed',sans-serif;font-size:.82rem;font-weight:800;cursor:pointer;letter-spacing:.5px;">
        Best Signing Top 10
      </button>
      <button onclick="downloadSigningJPG('flop')" style="background:linear-gradient(135deg,#555,#222);color:rgba(255,255,255,.7);border:1px solid rgba(255,255,255,.15);border-radius:8px;padding:.45rem 1rem;font-family:'Barlow Condensed',sans-serif;font-size:.82rem;font-weight:800;cursor:pointer;letter-spacing:.5px;">
        Flop Signing Bottom 10
      </button>
    </div>
  </div>
  <div class="rank-info-box">
    ⚡ <strong>Individual Duel System</strong> — Stats based on each player's own duel result, separate from team score.<br>
    Formula: <strong style="color:var(--green)">Win×10</strong> + <strong style="color:var(--acc)">Draw×5</strong> + <strong style="color:var(--red)">Loss×(−10)</strong> + <strong style="color:#7CB9FF">GF×1</strong> + <strong style="color:#FF8A50">GC×(−1)</strong> + <strong style="color:var(--acc)">MOTM×5</strong> + <strong style="color:var(--green)">CS×2</strong>
  </div>
  <!-- Team filter -->
  <div style="margin-bottom:.8rem;display:flex;align-items:center;gap:.6rem;flex-wrap:wrap">
    <label style="font-size:.75rem;color:var(--muted);font-family:'Barlow Condensed';font-weight:700;text-transform:uppercase;letter-spacing:1px">Filter by Team:</label>
    <select id="rankTeamFilter" onchange="renderRank(currentRankType)" style="background:var(--card);border:1px solid var(--border);border-radius:8px;padding:.35rem .7rem;color:var(--text);font-family:'Barlow';font-size:.85rem;outline:none;cursor:pointer">
      <option value="">All Teams</option>
    </select>
  </div>
  <div class="rank-tabs">
    <button class="rtab active" onclick="showRank('total',this)">🏆 Points</button>
    <button class="rtab" onclick="showRank('goals',this)">⚽ Goals</button>
    <button class="rtab" onclick="showRank('motm',this)">👑 MOTM</button>
    <button class="rtab" onclick="showRank('cs',this)">🧤 Clean Sheets</button>
    <button class="rtab" onclick="showRank('wr',this)">📈 Win Ratio</button>
  </div>
  <div id="rankList" style="display:flex;flex-direction:column;gap:.6rem;"></div>
</div>

<!-- CAPTAIN SECTION -->
<div class="section" id="section-captain">
  <div id="captainHeader" style="background:linear-gradient(135deg,rgba(41,121,255,.08),rgba(41,121,255,.02));border:1px solid rgba(41,121,255,.25);border-radius:14px;padding:1rem 1.3rem;margin-bottom:1.2rem;display:flex;align-items:center;gap:.9rem;flex-wrap:wrap">
    <div id="capTeamLogo" style="width:52px;height:52px;border-radius:50%;background:var(--card2);border:2px solid rgba(41,121,255,.4);display:flex;align-items:center;justify-content:center;font-size:1.5rem">⚽</div>
    <div style="flex:1">
      <div id="capTeamName" style="font-family:'Bebas Neue';font-size:1.5rem;color:#7CB9FF;letter-spacing:2px">Team Name</div>
      <div style="font-size:.74rem;color:var(--muted)">Captain Dashboard — Player Info Manager</div>
    </div>
    <button class="lbtn" onclick="captainLogout()" style="border-color:rgba(41,121,255,.3);color:#7CB9FF">Logout</button>

  </div>
  <div id="captainContent"></div>
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
    <button class="atab" onclick="showATab('history',this)">History</button>
    <button class="atab" onclick="showATab('fxgen',this)">Fixture Gen</button>
    <button class="atab" onclick="showATab('standings',this)">Standings</button>
  </div>
  <div id="adminContent"><div style="color:var(--muted);padding:2rem;text-align:center">Loading…</div></div>
</div>

<script>
// ════════════════════════════════════════════
// STATE
// ════════════════════════════════════════════
var state = { teams:{}, players:{}, fixtures:{}, matches:{}, stats:{}, manual_standings:{}, news:{}, player_matches:{}, squad_submissions:{} };
var isAdmin = false;
var captainTeamId = null; // currently logged-in captain's team ID
var pendingMatch = null;
var currentRankType = 'total';
var currentFxFilter = 'all';
var fbReady = false;

window.fbTeams = {}; window.fbPlayers = {}; window.fbFixtures = {};
window.fbMatches = {}; window.fbStats = {}; window.fbManualStandings = {};
window.fbNews = {}; window.fbPlayerMatches = {}; window.fbSquadSubmissions = {};

function fb(){ return window._FB || null; }

function rebuildLocal(){
  state.teams    = window.fbTeams   || {};
  state.players  = window.fbPlayers || {};
  state.fixtures = window.fbFixtures|| {};
  state.matches  = window.fbMatches || {};
  state.stats    = window.fbStats   || {};
  state.manual_standings = window.fbManualStandings || {};
  state.news     = window.fbNews    || {};
  state.player_matches     = window.fbPlayerMatches    || {};
  state.squad_submissions  = window.fbSquadSubmissions || {};
  if(!fbReady){
    fbReady=true;
    document.getElementById('fbDot').classList.add('connected');
    document.getElementById('fbTxt').textContent='Live';
    document.getElementById('loader').classList.add('gone');
    renderHome();
  } else {
    refreshCurrentSection();
  }
}

function refreshCurrentSection(){
  var active=document.querySelector('.section.active');
  if(!active) return;
  var id=active.id.replace('section-','');
  if(id==='home') renderHome();
  else if(id==='teams') renderTeams();
  else if(id==='fixtures') renderFxList(currentFxFilter);
  else if(id==='points') renderPoints();
  else if(id==='ranking') renderRank(currentRankType);
  else if(id==='admin'){
    var atab=document.querySelector('.atab.active');
    renderATab(atab?atab.textContent.toLowerCase().trim():'teams');
  }
}

// ════════════════════════════════════════════
// FIREBASE SAVE / DELETE
// ════════════════════════════════════════════
function fsSet(col,id,data){
  var F=fb(); if(!F) return Promise.resolve();
  return F.setDoc(F.doc(F.db,col,String(id)),data);
}
function fsDel(col,id){
  var F=fb(); if(!F) return Promise.resolve();
  return F.deleteDoc(F.doc(F.db,col,String(id)));
}

// ════════════════════════════════════════════
// CONDITION SYSTEM
// ════════════════════════════════════════════
function getCondition(wr){
  if(wr>=80) return {label:'A+',cls:'cond-ap',boost:1.8,icon:'🔥'};
  if(wr>=70) return {label:'A', cls:'cond-a', boost:1.5,icon:'⚡'};
  if(wr>=60) return {label:'B+',cls:'cond-bp',boost:1.2,icon:'💪'};
  if(wr>=50) return {label:'B-',cls:'cond-bm',boost:1.1,icon:'👍'};
  if(wr>=40) return {label:'C', cls:'cond-c', boost:1.0,icon:'➖'};
  if(wr>=30) return {label:'D', cls:'cond-d', boost:0.8,icon:'📉'};
  return       {label:'E', cls:'cond-e', boost:0.6,icon:'💀'};
}
function condBadge(wr){
  var c=getCondition(wr);
  return '<span class="cond '+c.cls+'">'+c.icon+' '+c.label+'</span>';
}

// ── SVG ICONS (inline, no emoji) ─────────────────────────
var SVG_LOCAL = '<svg class="cat-icon" viewBox="0 0 20 20" fill="currentColor"><path d="M10 2L2 9h2v9h5v-5h2v5h5V9h2L10 2z"/></svg>';
var SVG_YOUTH = '<svg class="cat-icon" viewBox="0 0 20 20" fill="currentColor"><circle cx="10" cy="6" r="3"/><path d="M4 18c0-3.3 2.7-6 6-6s6 2.7 6 6H4z"/><path d="M10 1l.9 2.7H14l-2.5 1.8.9 2.7L10 6.5l-2.4 1.7.9-2.7L6 3.7h3.1z" opacity=".55"/></svg>';
var SVG_INVITED = '<svg class="cat-icon" viewBox="0 0 24 24" fill="currentColor"><path d="M21 16v-2l-8-5V3.5A1.5 1.5 0 0 0 11.5 2h-1A1.5 1.5 0 0 0 9 3.5V9L1 14v2l8-2.5V19l-2 1.5V22l3.5-1h3L17 22v-1.5L15 19v-5.5l6 2.5z"/></svg>';
var SVG_COIN = '<svg class="coin-icon" viewBox="0 0 20 20" fill="currentColor"><circle cx="10" cy="10" r="9" opacity=".25"/><circle cx="10" cy="10" r="7"/><text x="10" y="14" text-anchor="middle" font-size="8" font-weight="900" fill="#080D0A" font-family="sans-serif">C</text></svg>';
var SVG_COIN_LG = '<svg class="coin-icon-lg" viewBox="0 0 20 20" fill="currentColor"><circle cx="10" cy="10" r="9" opacity=".25"/><circle cx="10" cy="10" r="7"/><text x="10" y="14" text-anchor="middle" font-size="8" font-weight="900" fill="#080D0A" font-family="sans-serif">C</text></svg>';

function catBadge(cat){
  if(cat==='invited') return '<span class="cat-badge cat-invited">'+SVG_INVITED+' Invited</span>';
  if(cat==='youth')   return '<span class="cat-badge cat-youth">'+SVG_YOUTH+' Youth</span>';
  return '<span class="cat-badge cat-local">'+SVG_LOCAL+' Local</span>';
}
function coinBadge(amount, large){
  if(!amount&&amount!==0) return '';
  if(large) return '<span class="coin-badge-lg">'+SVG_COIN_LG+' '+amount+'</span>';
  return '<span class="coin-badge">'+SVG_COIN+' '+amount+'</span>';
}

// ════════════════════════════════════════════
// POINTS — New formula:
// win*10 + draw*5 + loss*(-10) + gf*1 + gc*(-1) + motm*5 + cs*2
// ════════════════════════════════════════════
function computeStatsFromHistory(pid){
  var history=getPlayerAllMatchHistory(pid);
  var s={wins:0,losses:0,draws:0,goals:0,cs:0,motm:0,mp:0,gf:0,ga:0};
  history.forEach(function(h){
    s.mp++;
    if(h.result==='W') s.wins++;
    else if(h.result==='L') s.losses++;
    else s.draws++;
    s.gf+=(h.myScore||0); s.ga+=(h.oppScore||0);
    s.goals+=(h.myScore||0);
    if(h.result==='W'&&(h.oppScore||0)===0) s.cs++;
    if(h.motm) s.motm++;
  });
  return s;
}
function realCalcPts(s){
  if(!s) return 0;
  var w=s.wins||0, l=s.losses||0, d=s.draws||0;
  var gf=s.gf||s.goals||0, gc=s.ga||0;
  var total=w+l+d;
  var wr=total>0?Math.round((w/total)*100):0;
  var raw=(w*10)+(d*5)+(l*(-10))+(gf)+(gc*(-1))+((s.motm||0)*5)+((s.cs||0)*2);
  if(total>=3){ var cond=getCondition(wr); return Math.round(raw*cond.boost); }
  return raw;
}
function winRatio(s){
  if(!s) return 0;
  var t=(s.wins||0)+(s.losses||0)+(s.draws||0);
  return t>0?Math.round(((s.wins||0)/t)*100):0;
}
function playerGD(s){
  if(!s) return 0;
  return (s.gf||s.goals||0)-(s.ga||0);
}

// ════════════════════════════════════════════
// HELPERS
// ════════════════════════════════════════════
function esc(s){ return String(s||'').replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;'); }
function uid(){ return Date.now().toString(36)+Math.random().toString(36).slice(2); }

function teamLogoEl(t,size){
  size=size||28;
  var src=t&&(t.logoUrl||t.logo);
  if(src&&src.startsWith('http'))
    return '<div class="mini-logo" style="width:'+size+'px;height:'+size+'px;"><img src="'+esc(src)+'" onerror="this.parentNode.innerHTML=\'⚽\'"></div>';
  return '<div class="mini-logo" style="width:'+size+'px;height:'+size+'px;font-size:'+(size*.48)+'px;">'+(src||'⚽')+'</div>';
}
function playerPhotoEl(p,size){
  size=size||34;
  var src=p&&(p.photoUrl||p.photo);
  if(src&&src.startsWith('http'))
    return '<div class="p-photo" style="width:'+size+'px;height:'+size+'px;"><img src="'+esc(src)+'" onerror="this.parentNode.innerHTML=\'👤\'"></div>';
  return '<div class="p-photo" style="width:'+size+'px;height:'+size+'px;font-size:'+(size*.42)+'px;">👤</div>';
}
function teamBigLogo(t){
  var src=t&&(t.logoUrl||t.logo);
  if(src&&src.startsWith('http'))
    return '<div class="t-logo-wrap" style="width:56px;height:56px;border-color:'+(t.color||'var(--border)')+'"><img src="'+esc(src)+'" onerror="this.innerHTML=\'⚽\'"></div>';
  return '<div class="t-logo-wrap" style="width:56px;height:56px;font-size:1.6rem;border-color:'+(t.color||'var(--border)')+'">'+esc(src||'⚽')+'</div>';
}

function getTeams(){ return Object.values(state.teams); }
function getPlayers(){ return Object.values(state.players); }
function getFixtures(){ return Object.values(state.fixtures).sort(function(a,b){return (b.date||'').localeCompare(a.date||'');});}
function getMatches(){ return Object.values(state.matches); }
function getStat(pid){ return state.stats[pid]||{wins:0,losses:0,draws:0,goals:0,cs:0,motm:0,mp:0,gf:0,ga:0}; }
function getTeamById(id){ return state.teams[id]||null; }
function getPlayersByTeam(tid){ return getPlayers().filter(function(p){return p.teamId===tid;}); }

function getPlayerAllMatchHistory(pid){
  var history=[];
  Object.values(state.player_matches).forEach(function(pm){
    if(pm.playerId===pid) history.push(pm);
  });
  return history.sort(function(a,b){ return (b.timestamp||0)-(a.timestamp||0); });
}

// ════════════════════════════════════════════
// TEAM STRENGTH & WIN PROBABILITY
// ════════════════════════════════════════════
function calcTeamStrength(tid){
  var players=getPlayersByTeam(tid);
  if(!players.length) return 50;
  var total=0,count=0;
  players.forEach(function(p){
    var s=getStat(p.id);
    var mp=(s.mp||0)||(s.wins||0)+(s.draws||0)+(s.losses||0);
    if(mp>0){ total+=winRatio(s); count++; }
  });
  if(!count) return 50;
  return Math.round(total/count);
}
function calcWinProb(homeId, awayId){
  var hs=calcTeamStrength(homeId)||50;
  var as_=calcTeamStrength(awayId)||50;
  var total=hs+as_;
  if(total===0) return {home:40,draw:20,away:40};
  var homeP=Math.round((hs/total)*80);
  var awayP=Math.round((as_/total)*80);
  var drawP=100-homeP-awayP;
  if(drawP<5){drawP=5;homeP=Math.round((100-drawP)*(hs/total));awayP=100-drawP-homeP;}
  return {home:homeP,draw:drawP,away:awayP};
}

// ════════════════════════════════════════════
// NAV
// ════════════════════════════════════════════
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
  if(sec==='captain'){ renderCaptainDashboard(); }
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
function handleCapNav(){
  if(captainTeamId){
    go('captain',document.getElementById('capNavBtn'));
  } else {
    // navigate to captain section which shows login prompt
    document.querySelectorAll('.section').forEach(function(s){s.classList.remove('active');});
    document.querySelectorAll('nav button').forEach(function(b){b.classList.remove('active');});
    document.getElementById('section-captain').classList.add('active');
    document.getElementById('capNavBtn').classList.add('active');
    renderCaptainDashboard();
  }
}
function openCapLogin(){
  // Populate team dropdown
  var sel=document.getElementById('capTeamSel');
  sel.innerHTML='<option value="">Select your team…</option>'+
    getTeams().map(function(t){ return '<option value="'+t.id+'">'+esc(t.name)+'</option>'; }).join('');
  document.getElementById('capLoginErr').style.display='none';
  document.getElementById('capPwd').value='';
  document.getElementById('captainLoginModal').classList.remove('hidden');
  setTimeout(function(){document.getElementById('capPwd').focus();},80);
}
function closeCapLogin(){ document.getElementById('captainLoginModal').classList.add('hidden'); }
function doCapLogin(){
  var tid=document.getElementById('capTeamSel').value;
  var pwd=document.getElementById('capPwd').value;
  if(!tid){ document.getElementById('capLoginErr').textContent='Select your team first.'; document.getElementById('capLoginErr').style.display='block'; return; }
  var t=getTeamById(tid);
  if(!t){ document.getElementById('capLoginErr').style.display='block'; return; }
  if(!t.capPassword){ document.getElementById('capLoginErr').textContent='No password set for this team yet. Ask admin.'; document.getElementById('capLoginErr').style.display='block'; return; }
  if(t.capPassword!==pwd){ document.getElementById('capLoginErr').textContent='Wrong password. Try again.'; document.getElementById('capLoginErr').style.display='block'; return; }
  captainTeamId=tid;
  document.getElementById('captainLoginModal').classList.add('hidden');
  var capBtn=document.getElementById('capNavBtn');
  capBtn.textContent='⚽ '+esc(t.name);
  document.querySelectorAll('nav button').forEach(function(b){b.classList.remove('active');});
  go('captain',capBtn);
}
function captainLogout(){
  captainTeamId=null;
  var capBtn=document.getElementById('capNavBtn');
  capBtn.textContent='⚽ Captain';
  capBtn.classList.remove('active');
  go('home',document.querySelector('nav button'));
}
function toggle(id){
  var el=document.getElementById(id);
  if(el) el.style.display=(el.style.display==='none'||el.style.display==='')?'block':'none';
}
function closeModal(id){ document.getElementById(id).classList.add('hidden'); }

// ════════════════════════════════════════════
// LOGO / PHOTO UPLOAD HELPERS
// ════════════════════════════════════════════
function buildLogoInput(idPrefix,label){
  label=label||'Logo';
  return '<div class="fg" style="grid-column:span 2"><label>'+label+' (URL or Upload)</label>'+
    '<div class="logo-input-group">'+
    '<div class="logo-tabs-sm">'+
    '<span class="logo-tab-sm active" onclick="switchLogoTab(\'url\',\''+idPrefix+'\',this)">🔗 URL</span>'+
    '<span class="logo-tab-sm" onclick="switchLogoTab(\'upload\',\''+idPrefix+'\',this)">📤 Upload</span>'+
    '</div>'+
    '<div id="'+idPrefix+'_url_wrap"><input id="'+idPrefix+'_url" placeholder="https://… or emoji like 🦅" oninput="previewLogo(\''+idPrefix+'\')"></div>'+
    '<div id="'+idPrefix+'_upload_wrap" style="display:none"><input id="'+idPrefix+'_file" type="file" accept="image/*" style="padding:.3rem" onchange="previewLogoFile(\''+idPrefix+'\')"></div>'+
    '<div style="display:flex;align-items:center;gap:.7rem;margin-top:.4rem">'+
    '<div class="logo-preview-circle" id="'+idPrefix+'_prev">⚽</div>'+
    '<div style="font-size:.7rem;color:var(--muted)">Preview</div></div>'+
    '</div></div>';
}
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
function switchLogoTab(type,prefix,btn){
  var urlWrap=document.getElementById(prefix+'_url_wrap');
  var upWrap=document.getElementById(prefix+'_upload_wrap');
  if(urlWrap) urlWrap.style.display=type==='url'?'block':'none';
  if(upWrap) upWrap.style.display=type==='upload'?'block':'none';
  if(btn&&btn.parentNode) btn.parentNode.querySelectorAll('.logo-tab-sm').forEach(function(b){b.classList.remove('active');});
  if(btn) btn.classList.add('active');
}
function previewLogo(prefix){
  var val=(document.getElementById(prefix+'_url')||{}).value||'';
  val=val.trim();
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
  var urlInp=document.getElementById(prefix+'_url');
  var fileInp=document.getElementById(prefix+'_file');
  var F=fb();
  if(fileInp&&fileInp.files&&fileInp.files[0]&&F){
    try{ var file=fileInp.files[0]; var storRef=F.ref(F.storage,'logos/'+uid()+'_'+file.name); await F.uploadBytes(storRef,file); return await F.getDownloadURL(storRef); }
    catch(e){ console.warn('Upload failed',e); }
  }
  if(urlInp) return urlInp.value.trim();
  return '';
}
async function resolvePhotoUrl(prefix){
  var urlInp=document.getElementById(prefix+'_url');
  var fileInp=document.getElementById(prefix+'_file');
  var F=fb();
  if(fileInp&&fileInp.files&&fileInp.files[0]&&F){
    try{ var file=fileInp.files[0]; var storRef=F.ref(F.storage,'photos/'+uid()+'_'+file.name); await F.uploadBytes(storRef,file); return await F.getDownloadURL(storRef); }
    catch(e){ console.warn('Upload failed',e); }
  }
  if(urlInp) return urlInp.value.trim();
  return '';
}

// ════════════════════════════════════════════
// NEWS ENGINE
// ════════════════════════════════════════════
function generateAutoNews(){
  var items=[];
  getFixtures().filter(function(f){return f.status==='played'&&f.homeScore!=null;}).slice(0,5).forEach(function(f){
    var ht=getTeamById(f.home), at=getTeamById(f.away);
    if(!ht||!at) return;
    var winner=f.homeScore>f.awayScore?ht.name:f.homeScore<f.awayScore?at.name:null;
    items.push({
      type:'result', tag:'Result', tagCls:'nc-result',
      title:(winner?winner+' win vs '+(winner===ht.name?at.name:ht.name)+'!':'Draw: '+ht.name+' vs '+at.name),
      body:ht.name+' '+f.homeScore+' – '+f.awayScore+' '+at.name+(f.round?' | '+f.round:''),
      ts:f.date||'', hot:Math.abs(f.homeScore-f.awayScore)>=4, fxId:f.id
    });
  });
  getPlayers().forEach(function(p){
    var s=getStat(p.id);
    if((s.goals||0)>=7){
      var t=getTeamById(p.teamId);
      items.push({
        type:'hot', tag:'Hot', tagCls:'nc-hot',
        title:'🔥 '+esc(p.name)+' on fire with '+(s.goals||0)+' goals!',
        body:(t?t.name+' star ':'')+p.name+' has scored '+(s.goals||0)+' goals this season.',
        ts:'Season', hot:true
      });
    }
  });
  var ranked=getPlayers().slice().sort(function(a,b){return realCalcPts(getStat(b.id))-realCalcPts(getStat(a.id));});
  if(ranked.length){
    var leader=ranked[0];
    var lt=getTeamById(leader.teamId);
    items.push({
      type:'special', tag:'Rankings', tagCls:'nc-special',
      title:'👑 '+esc(leader.name)+' leads player rankings',
      body:(lt?lt.name+' ':'')+'player tops the chart with '+realCalcPts(getStat(leader.id))+' pts. '+(ranked[1]?'2nd: '+ranked[1].name:''),
      ts:'Live', hot:false
    });
  }
  var standings=calcStandings();
  if(standings.length>0){
    items.push({
      type:'table', tag:'Table', tagCls:'nc-table',
      title:'📊 '+esc(standings[0].name)+' leads the table',
      body:'Top: '+standings.slice(0,3).map(function(r,i){return (i+1)+'. '+r.name+' ('+r.pts+'pts)';}).join(' | '),
      ts:'Live', hot:false
    });
  }
  Object.values(state.news).forEach(function(n){
    if(n.active!==false) items.push(Object.assign({manual:true},n));
  });
  return items;
}

function renderNewsTicker(items){
  if(!items.length){ document.getElementById('newsTicker').style.display='none'; return; }
  var inner=items.map(function(n){ return '<span class="news-item-tick"><span class="ni-icon">'+(n.hot?'🔥':n.type==='result'?'⚽':n.type==='table'?'📊':'📰')+'</span>'+esc(n.title)+'</span>'; }).join('');
  document.getElementById('newsTicker').style.display='flex';
  document.getElementById('newsTicker').innerHTML=
    '<span class="news-ticker-label">📡 LIVE NEWS</span>'+
    '<div class="news-ticker-track"><div class="news-ticker-inner">'+inner+inner+'</div></div>';
}

function renderNewsGrid(items){
  if(!items.length){
    document.getElementById('newsGrid').innerHTML='<p style="color:var(--muted)">No news yet.</p>';
    return;
  }
  var hot=items.filter(function(n){return n.hot;});
  var rest=items.filter(function(n){return !n.hot;});
  var sorted=hot.concat(rest).slice(0,6);
  document.getElementById('newsGrid').innerHTML=sorted.map(function(n){
    var cardCls=n.hot?'news-card hot':n.type==='result'?'news-card result':n.type==='table'?'news-card table':'news-card';
    var onclick=n.fxId?'showFxDetail(\''+n.fxId+'\')':'';
    return '<div class="'+cardCls+'"'+(onclick?' onclick="'+onclick+'"':'')+' style="'+(onclick?'cursor:pointer':'')+'">'+
      '<span class="nc-tag '+esc(n.tagCls||'nc-result')+'">'+esc(n.tag||'News')+'</span>'+
      '<div class="nc-title">'+esc(n.title)+'</div>'+
      '<div class="nc-body">'+esc(n.body||'')+'</div>'+
      '<div class="nc-time">'+esc(n.ts||'')+'</div>'+
      '</div>';
  }).join('');
}

function renderFeatured(){
  var players=getPlayers().slice().sort(function(a,b){ return realCalcPts(computeStatsFromHistory(b.id))-realCalcPts(computeStatsFromHistory(a.id)); }).slice(0,3);
  var medals=[
    {bg:'linear-gradient(135deg,#FFD700,#FFA000)',color:'#000',label:'1st'},
    {bg:'linear-gradient(135deg,#C0C0C0,#9E9E9E)',color:'#000',label:'2nd'},
    {bg:'linear-gradient(135deg,#CD7F32,#8D4E1A)',color:'#fff',label:'3rd'}
  ];
  document.getElementById('featuredPlayers').innerHTML=players.map(function(p,i){
    var s=computeStatsFromHistory(p.id); var t=getTeamById(p.teamId); var wr=winRatio(s); var m=medals[i]||medals[2];
    return '<div class="feat-card'+(i===0?' gold':'')+'" onclick="showPlayerProfile(\''+p.id+'\')" style="cursor:pointer">'+
      '<div class="feat-rank-badge" style="background:'+m.bg+';color:'+m.color+'">'+m.label+'</div>'+
      playerPhotoEl(p,46)+
      '<div style="flex:1;min-width:0">'+
        '<div style="font-weight:700;font-size:.95rem;white-space:nowrap;overflow:hidden;text-overflow:ellipsis">'+esc(p.name)+'</div>'+
        '<div style="font-size:.72rem;color:var(--muted)">'+(t?esc(t.name):'')+'</div>'+
        condBadge(wr)+
      '</div>'+
      '<div style="text-align:right"><div style="font-family:\'Bebas Neue\';font-size:1.8rem;color:var(--green)">'+realCalcPts(s)+'</div><div style="font-size:.62rem;color:var(--muted)">PTS</div></div>'+
      '</div>';
  }).join('')||'<p style="color:var(--muted)">No players yet.</p>';

  var teams=calcStandings().slice(0,2);
  document.getElementById('featuredTeams').innerHTML=teams.map(function(r,i){
    var t=getTeamById(r.id); if(!t) return '';
    var m=medals[i]||medals[1];
    return '<div class="feat-card'+(i===0?' gold':'')+'" onclick="showTeamDetail(\''+r.id+'\')" style="cursor:pointer">'+
      '<div class="feat-rank-badge" style="background:'+m.bg+';color:'+m.color+'">'+(i+1)+'</div>'+
      teamBigLogo(t)+
      '<div style="flex:1;min-width:0">'+
        '<div style="font-weight:700;font-size:.95rem">'+esc(t.name)+'</div>'+
        '<div style="font-size:.72rem;color:var(--muted)">W'+r.w+' D'+r.d+' L'+r.l+'</div>'+
      '</div>'+
      '<div style="text-align:right"><div style="font-family:\'Bebas Neue\';font-size:1.8rem;color:var(--green)">'+r.pts+'</div><div style="font-size:.62rem;color:var(--muted)">PTS</div></div>'+
      '</div>';
  }).join('')||'<p style="color:var(--muted)">No teams yet.</p>';
}

// ════════════════════════════════════════════
// HOME
// ════════════════════════════════════════════
function renderHome(){
  var teams=getTeams(), players=getPlayers();
  var played=getFixtures().filter(function(f){return f.status==='played';}).length;
  var up=getFixtures().filter(function(f){return f.status==='upcoming';}).length;
  document.getElementById('heroStats').innerHTML=
    '<div class="stat-box"><div class="num">'+teams.length+'</div><div class="lbl">Teams</div></div>'+
    '<div class="stat-box"><div class="num">'+players.length+'</div><div class="lbl">Players</div></div>'+
    '<div class="stat-box"><div class="num">'+played+'</div><div class="lbl">Played</div></div>'+
    '<div class="stat-box"><div class="num">'+up+'</div><div class="lbl">Upcoming</div></div>';
  renderFeatured();
  document.getElementById('homeFixtures').innerHTML=getFixtures().slice(0,4).map(function(f){return fxHTML(f,true);}).join('');
  renderTournamentBestXI();
}

// ════════════════════════════════════════════
// BEST XI — 4-3-3 formation
// Positions: GK(1) DEF(4) MID(3) FWD(3)
// ════════════════════════════════════════════
function buildBestXI(playerPool){
  // Sort by history-based points
  var sorted=playerPool.slice().sort(function(a,b){
    return realCalcPts(computeStatsFromHistory(b.id))-realCalcPts(computeStatsFromHistory(a.id));
  });

  var xi=[];
  var youthCount=0, invitedCount=0, localCount=0;
  var MIN_YOUTH=2, MAX_INVITED=7;

  // Separate by category sorted by points
  var locals   = sorted.filter(function(p){return (p.cat||'local')==='local';});
  var youths   = sorted.filter(function(p){return p.cat==='youth';});
  var inviteds = sorted.filter(function(p){return p.cat==='invited';});

  // Step 1: Fill min 2 youth first (take top 2 youth by pts)
  youths.slice(0, MIN_YOUTH).forEach(function(p){
    xi.push(p); youthCount++;
  });

  // Step 2: Fill remaining 9 slots from best available
  // respecting max 7 invited rule
  var remaining = sorted.filter(function(p){
    return !xi.find(function(x){return x.id===p.id;});
  });

  for(var i=0; i<remaining.length && xi.length<11; i++){
    var p=remaining[i];
    var cat=p.cat||'local';
    if(cat==='invited' && invitedCount>=MAX_INVITED) continue;
    xi.push(p);
    if(cat==='youth')   youthCount++;
    if(cat==='invited') invitedCount++;
  }

  // Fallback: if still <11, fill ignoring limits
  if(xi.length<11){
    var xiIds=xi.map(function(p){return p.id;});
    sorted.forEach(function(p){
      if(xi.length<11 && xiIds.indexOf(p.id)===-1) xi.push(p);
    });
  }

  var positions=['GK','LB','CB','CB','RB','CM','CM','CAM','LW','ST','RW'];
  return xi.slice(0,11).map(function(p,i){
    return Object.assign({},p,{pos:positions[i]||'MF'});
  });
}


function renderBestXI(players, containerId, title, coachName){
  var el=document.getElementById(containerId); if(!el) return;
  if(!players.length){ el.innerHTML='<p style="color:var(--muted);font-size:.8rem;padding:.5rem">No player data yet.</p>'; return; }

  var fwd=players.slice(8,11);
  var mid=players.slice(5,8);
  var def=players.slice(1,5);
  var gk=players.slice(0,1);

  function playerPin(p){
    var s=getStat(p.id);
    var pts=realCalcPts(s);
    var src=p.photoUrl||p.photo;
    var ph;
    if(src && src.startsWith('http')){
      ph='<img src="'+esc(src)+'" style="width:100%;height:100%;object-fit:cover;border-radius:50%;">';
    } else {
      ph=playerPhotoEl(p,46).replace('p-photo','').replace('style="width:46px;height:46px;','style="width:100%;height:100%;');
    }
    var pid=p.id;
    var pname=esc(p.name.split(' ')[0]);
    var ppos=esc(p.pos||'');
    return '<div class="xi-player" onclick="showPlayerProfile(\''
      +pid+'\')"><div class="xi-avatar">'
      +ph+'<div class="xi-pos-badge">'+ppos+'</div>'
      +'</div><div class="xi-name">'+pname
      +'</div><div class="xi-pts">'+pts+'pts</div></div>';
  }

  var coachHtml='';
  if(coachName){
    coachHtml='<div class="coach-row"><div class="coach-card">'
      +'<svg width="20" height="20" viewBox="0 0 20 20" fill="var(--acc)"><circle cx="10" cy="7" r="4"/>'
      +'<path d="M2 18c0-4.4 3.6-8 8-8s8 3.6 8 8H2z"/></svg>'
      +'<div><div style="font-size:.6rem;color:var(--muted);text-transform:uppercase;letter-spacing:1px;font-weight:700">Coach</div>'
      +'<div style="font-family:Barlow Condensed,sans-serif;font-size:.9rem;font-weight:700;color:var(--acc)">'+esc(coachName)+'</div>'
      +'</div></div></div>';
  }

  el.innerHTML='<div style="font-family:Bebas Neue,sans-serif;font-size:1.1rem;letter-spacing:2px;color:var(--green);margin-bottom:.6rem">'
    +(title||'Best XI')
    +'<span style="font-size:.72rem;color:var(--muted);margin-left:.6rem">4-3-3</span></div>'
    +'<div class="pitch">'
    +'<div class="pitch-line" style="top:25%"></div>'
    +'<div class="pitch-line" style="top:50%"></div>'
    +'<div class="pitch-line" style="top:75%"></div>'
    +'<div class="formation-row">'+fwd.map(playerPin).join('')+'</div>'
    +'<div class="formation-row">'+mid.map(playerPin).join('')+'</div>'
    +'<div class="formation-row">'+def.map(playerPin).join('')+'</div>'
    +'<div class="formation-row">'+gk.map(playerPin).join('')+'</div>'
    +'</div>'+coachHtml;
}

function renderTournamentBestXI(){
  var el=document.getElementById('tournamentBestXI'); if(!el) return;
  var allPlayers=getPlayers();
  if(!allPlayers.length){ el.innerHTML=''; return; }
  var xi=buildBestXI(allPlayers);
  // Coach = manager of top team in standings
  var standings=calcStandings();
  var topTeam=standings.length?getTeamById(standings[0].id):null;
  var coach=topTeam?(topTeam.president||topTeam.manager||topTeam.name+' FC'):'';
  renderBestXI(xi,'tournamentBestXI','Tournament Best XI',coach);
}

// ════════════════════════════════════════════
// TEAMS
// ════════════════════════════════════════════
function getBestPlayer(tid){
  var ps=getPlayersByTeam(tid);
  if(!ps.length) return null;
  return ps.slice().sort(function(a,b){return realCalcPts(computeStatsFromHistory(b.id))-realCalcPts(computeStatsFromHistory(a.id));})[0];
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
        '<div><div style="font-size:.6rem;color:var(--acc);font-weight:800;letter-spacing:1.2px;text-transform:uppercase;margin-bottom:.2rem">Best Player</div>'+
        '<div style="font-weight:700;font-size:.88rem">'+esc(best.name)+'</div>'+
        '<div style="display:flex;gap:.3rem;align-items:center;flex-wrap:wrap;margin-top:.25rem">'+catBadge(best.cat)+condBadge(wr)+'<span style="font-size:.7rem;color:var(--green)">'+realCalcPts(bs)+'pts</span></div></div></div>';
    }
    return '<div class="card" onclick="showTeamDetail(\''+t.id+'\')">'+
      '<div style="display:flex;align-items:center;gap:.8rem;margin-bottom:.8rem">'+
      teamBigLogo(t)+
      '<div><div style="font-family:\'Barlow Condensed\';font-size:1.15rem;font-weight:700">'+esc(t.name)+'</div>'+
      '<div style="font-size:.74rem;color:var(--muted)">👔 '+esc(t.president||'—')+'</div></div></div>'+
      '<div style="display:flex;gap:.3rem;flex-wrap:wrap;align-items:center;margin-bottom:.7rem">'+
      '<span class="cat-badge cat-local">'+SVG_LOCAL+' Local <strong style=\"font-size:.72rem\">'+local.length+'</strong></span>'+
      '<span class="cat-badge cat-youth">'+SVG_YOUTH+' Youth <strong style=\"font-size:.72rem\">'+youth.length+'</strong></span>'+
      '<span class="cat-badge cat-invited">'+SVG_INVITED+' Invited <strong style=\"font-size:.72rem\">'+inv.length+'</strong></span></div>'+
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
  var catLabels={local:'🟢 Local',youth:'🟡 Youth',invited:'🔴 Invited'};

  // Squad HTML — with bid prices
  var sq='';
  var totalSquadValue=0;
  Object.keys(catGrps).forEach(function(c){
    var arr=catGrps[c]; if(!arr.length) return;
    sq+='<div style="display:flex;align-items:center;gap:.4rem;margin:.65rem 0 .3rem">'+catBadge(c)+'<span style="font-size:.7rem;color:var(--muted)">('+arr.length+')</span></div>';
    arr.forEach(function(p){
      var s=getStat(p.id); var wr=winRatio(s);
      var bid=p.bid||0;
      if(bid) totalSquadValue+=bid;
      sq+='<div style="display:flex;align-items:center;gap:.45rem;padding:.32rem .1rem;border-bottom:1px solid var(--border);cursor:pointer" onclick="showPlayerProfile(\''+p.id+'\')">'+
        playerPhotoEl(p,26)+
        '<span style="flex:1;font-size:.83rem;font-weight:600;overflow:hidden;text-overflow:ellipsis;white-space:nowrap">'+esc(p.name)+'</span>'+
        (bid?coinBadge(bid,false):'')+
        '<span style="font-size:.67rem;color:var(--green)">'+realCalcPts(s)+'pts</span></div>';
    });
  });

  // Fixtures HTML — all fixtures involving this team
  var teamFixtures=getFixtures().filter(function(f){return f.home===tid||f.away===tid;});
  var upcoming=teamFixtures.filter(function(f){return f.status==='upcoming';});
  var played=teamFixtures.filter(function(f){return f.status==='played';});

  var upcomingHtml=upcoming.length?upcoming.map(function(f){
    var isHome=f.home===tid;
    var opp=getTeamById(isHome?f.away:f.home);
    return '<div style="display:flex;align-items:center;gap:.6rem;padding:.45rem .6rem;border-bottom:1px solid var(--border);font-size:.82rem">'+
      '<span style="background:rgba(255,214,0,.1);color:var(--acc);border:1px solid rgba(255,214,0,.25);padding:.1rem .4rem;border-radius:4px;font-size:.64rem;font-weight:700;font-family:\'Barlow Condensed\'">'+esc(f.round||'Match')+'</span>'+
      (opp?teamLogoEl(opp,22):'')+
      '<span style="flex:1;font-weight:600">'+(isHome?'vs ':'@ ')+esc(opp?opp.name:'?')+'</span>'+
      '<span style="font-size:.7rem;color:var(--muted)">'+esc(f.date||'TBD')+'</span>'+
      '<span class="sbadge s-up">Upcoming</span>'+
      '</div>';
  }).join(''):'<p style="color:var(--muted);font-size:.78rem;padding:.5rem">No upcoming fixtures.</p>';

  var resultsHtml=played.length?played.map(function(f){
    var isHome=f.home===tid;
    var opp=getTeamById(isHome?f.away:f.home);
    var myScore=isHome?f.homeScore:f.awayScore;
    var oppScore=isHome?f.awayScore:f.homeScore;
    var res=myScore>oppScore?'W':myScore<oppScore?'L':'D';
    var rc=res==='W'?'var(--green)':res==='L'?'var(--red)':'var(--acc)';
    var rcBg=res==='W'?'rgba(0,200,83,.08)':res==='L'?'rgba(255,61,61,.08)':'rgba(255,214,0,.06)';
    return '<div style="display:flex;align-items:center;gap:.6rem;padding:.45rem .6rem;border-bottom:1px solid var(--border);font-size:.82rem;background:'+rcBg+'">'+
      '<div style="width:22px;height:22px;border-radius:50%;background:'+rc+';display:flex;align-items:center;justify-content:center;flex-shrink:0"><span style="font-size:10px;color:#fff;font-weight:900">'+res+'</span></div>'+
      '<span style="background:rgba(100,100,100,.15);color:var(--muted);padding:.1rem .4rem;border-radius:4px;font-size:.64rem;font-weight:700;font-family:\'Barlow Condensed\'">'+esc(f.round||'Match')+'</span>'+
      (opp?teamLogoEl(opp,20):'')+
      '<span style="flex:1;font-weight:600">'+(isHome?'vs ':'@ ')+esc(opp?opp.name:'?')+'</span>'+
      '<span style="font-family:\'Bebas Neue\';font-size:1.1rem;color:'+rc+'">'+myScore+' — '+oppScore+'</span>'+
      '<span style="font-size:.68rem;color:var(--muted)">'+esc(f.date||'')+'</span>'+
      '</div>';
  }).join(''):'<p style="color:var(--muted);font-size:.78rem;padding:.5rem">No results yet.</p>';

  var bestH='';
  if(best){ var bs2=getStat(best.id); var wr2=winRatio(bs2);
    bestH='<div class="best-card" style="margin-bottom:.8rem">'+playerPhotoEl(best,40)+
      '<div><div style="font-size:.6rem;color:var(--acc);font-weight:800;letter-spacing:1.2px;text-transform:uppercase;margin-bottom:.2rem">Best Player</div>'+
      '<div style="font-weight:700;font-size:.9rem">'+esc(best.name)+'</div>'+
      '<div style="display:flex;gap:.3rem;align-items:center;flex-wrap:wrap;margin-top:.2rem">'+catBadge(best.cat)+(best.bid?coinBadge(best.bid,false):'')+condBadge(wr2)+'<span style="font-size:.68rem;color:var(--green)">'+realCalcPts(bs2)+'pts</span></div></div></div>'; }

  document.getElementById('teamModalContent').innerHTML=
    '<div style="display:flex;align-items:center;gap:.8rem;margin-bottom:.9rem">'+
    teamBigLogo(t)+
    '<div style="flex:1"><div style="font-family:\'Bebas Neue\';font-size:1.7rem;color:var(--green);line-height:1">'+esc(t.name)+'</div>'+
    '<div style="font-size:.75rem;color:var(--muted)">👔 '+esc(t.president||'—')+'</div></div>'+
    '<button onclick="closeModal(\'teamModal\')" style="background:none;border:none;color:var(--muted);font-size:1.4rem;cursor:pointer;padding:.2rem .5rem">✕</button></div>'+

    '<div style="display:flex;gap:.5rem;flex-wrap:wrap;margin-bottom:.9rem">'+
    '<div class="stat-box" style="padding:.5rem .8rem"><div class="num">'+(tp.pts||0)+'</div><div class="lbl">Pts</div></div>'+
    '<div class="stat-box" style="padding:.5rem .8rem"><div class="num">'+(tp.w||0)+'</div><div class="lbl">W</div></div>'+
    '<div class="stat-box" style="padding:.5rem .8rem"><div class="num">'+(tp.d||0)+'</div><div class="lbl">D</div></div>'+
    '<div class="stat-box" style="padding:.5rem .8rem"><div class="num">'+(tp.l||0)+'</div><div class="lbl">L</div></div>'+
    '<div class="stat-box" style="padding:.5rem .8rem"><div class="num">'+(tp.wr||0)+'%</div><div class="lbl">WR</div></div>'+
    '<div class="stat-box" style="padding:.5rem .8rem"><div class="num">'+(played.length)+'</div><div class="lbl">Played</div></div>'+
    '</div>'+

    bestH+

    // Mini tabs for squad / upcoming / results
    '<div style="display:flex;gap:.35rem;margin-bottom:.7rem;border-bottom:1px solid var(--border);padding-bottom:.5rem;flex-wrap:wrap" id="tmTabs">'+
    '<button class="tab active" style="font-size:.72rem" onclick="tmTab(\'squad\',this)">Squad ('+ps.length+')</button>'+
    '<button class="tab" style="font-size:.72rem" onclick="tmTab(\'upcoming\',this)">Upcoming ('+upcoming.length+')</button>'+
    '<button class="tab" style="font-size:.72rem" onclick="tmTab(\'results\',this)">Results ('+played.length+')</button>'+
    '<button class="tab" style="font-size:.72rem" onclick="tmTab(\'bestxi\',this);renderTeamBestXI(\''+tid+'\')">Best XI</button>'+
    '</div>'+

    '<div id="tmPanel_squad" style="display:block">'+
    (totalSquadValue>0?'<div class="squad-total-value"><svg width="18" height="18" viewBox="0 0 20 20" fill="var(--acc)"><circle cx="10" cy="10" r="9" opacity=".25"/><circle cx="10" cy="10" r="7"/><text x="10" y="14" text-anchor="middle" font-size="8" font-weight="900" fill="#080D0A" font-family="sans-serif">C</text></svg><div><div style="font-family:\'Bebas Neue\';font-size:1.1rem;color:var(--acc);letter-spacing:1px">'+totalSquadValue+' COINS</div><div style="font-size:.62rem;color:var(--muted);text-transform:uppercase;letter-spacing:.5px">Total Squad Value</div></div></div>':'')+
    sq+'</div>'+
    '<div id="tmPanel_upcoming" style="display:none"><div style="border:1px solid var(--border);border-radius:10px;overflow:hidden">'+upcomingHtml+'</div></div>'+
    '<div id="tmPanel_results" style="display:none"><div style="border:1px solid var(--border);border-radius:10px;overflow:hidden">'+resultsHtml+'</div></div>'+
    '<div id="tmPanel_bestxi" style="display:none"><div id="tmBestXIContainer"></div></div>';

  document.getElementById('teamModal').classList.remove('hidden');
}
function tmTab(panel, btn){
  document.querySelectorAll('#tmTabs .tab').forEach(function(b){b.classList.remove('active');});
  btn.classList.add('active');
  ['squad','upcoming','results','bestxi'].forEach(function(n){
    var el=document.getElementById('tmPanel_'+n);
    if(el) el.style.display=(n===panel)?'block':'none';
  });
}
function renderTeamBestXI(tid){
  var players=getPlayersByTeam(tid);
  var xi=buildBestXI(players);
  var t=getTeamById(tid);
  var coach=t?(t.president||t.manager||''):'';
  var container=document.getElementById('tmBestXIContainer');
  if(!container) return;
  if(!xi.length){ container.innerHTML='<p style="color:var(--muted);font-size:.8rem;padding:.5rem">Not enough players for Best XI.</p>'; return; }
  container.innerHTML='<div id="tmBestXIRender"></div>';
  renderBestXI(xi,'tmBestXIRender',t?t.name+' Best XI':'Best XI',coach);
}

// ════════════════════════════════════════════
// PLAYER PROFILE MODAL
// ════════════════════════════════════════════
function showPlayerProfile(pid){
  var p=getPlayers().find(function(pl){return pl.id===pid;});
  if(!p) return;
  var s=computeStatsFromHistory(pid);
  var t=getTeamById(p.teamId);
  var wr=winRatio(s);
  var mp=s.mp;
  var cond=getCondition(wr);
  var pts=realCalcPts(s);
  var history=getPlayerAllMatchHistory(pid);

  var formHtml='<div style="display:flex;gap:.25rem;flex-wrap:wrap">';
  if(history.length){
    history.slice(0,10).forEach(function(h){
      var rc=h.result==='W'?'linear-gradient(135deg,#00C853,#009624)':h.result==='L'?'linear-gradient(135deg,#FF3D3D,#B71C1C)':'linear-gradient(135deg,#FFD600,#F57F17)';
      formHtml+='<div style="width:22px;height:22px;border-radius:50%;background:'+rc+';display:flex;align-items:center;justify-content:center;flex-shrink:0">'+
        '<span style="font-family:\'Barlow Condensed\';font-weight:900;font-size:11px;color:#fff">'+h.result+'</span></div>';
    });
  } else { formHtml+='<span style="font-size:.7rem;color:var(--muted)">No matches yet</span>'; }
  formHtml+='</div>';

  var condDisplay=mp>=3?
    '<div style="background:rgba(0,200,83,.08);border:1px solid rgba(0,200,83,.2);border-radius:10px;padding:.6rem .9rem;margin:.8rem 0;display:flex;align-items:center;gap:.7rem">'+
    condCircle(wr,40)+
    '<div><div style="font-size:.75rem;font-weight:700;color:var(--text)">Condition: '+cond.label+' '+cond.icon+'</div>'+
    '<div style="font-size:.68rem;color:var(--muted)">Win ratio '+wr+'% → Boost ×'+cond.boost+' applied</div></div></div>':
    '<div style="background:rgba(255,214,0,.05);border:1px solid rgba(255,214,0,.15);border-radius:10px;padding:.5rem .8rem;margin:.8rem 0;font-size:.74rem;color:var(--muted)">⏳ Play '+Math.max(0,3-mp)+' more match(es) to unlock condition boost</div>';

  var histHtml='';
  if(history.length){
    history.forEach(function(h){
      var rCol=h.result==='W'?'var(--green)':h.result==='L'?'var(--red)':'var(--acc)';
      var rc=h.result==='W'?'linear-gradient(135deg,#00C853,#009624)':h.result==='L'?'linear-gradient(135deg,#FF3D3D,#B71C1C)':'linear-gradient(135deg,#FFD600,#F57F17)';
      histHtml+='<div class="match-hist-item">'+
        '<div class="mh-result" style="background:'+rc+'"><span style="font-size:11px;color:#fff;font-weight:900">'+h.result+'</span></div>'+
        '<div style="flex:1;min-width:0">'+
          '<div style="font-size:.84rem;font-weight:600">vs '+esc(h.opponentTeam||'?')+'</div>'+
          '<div style="font-size:.72rem;color:var(--muted)">'+esc(h.opponentName||'?')+' · '+esc(h.round||'')+'</div>'+
        '</div>'+
        '<div style="text-align:right">'+
          '<div style="font-family:\'Bebas Neue\';font-size:1.2rem;color:'+rCol+'">'+h.myScore+' — '+h.oppScore+'</div>'+
          (h.motm?'<div style="font-size:.62rem;color:var(--acc);font-weight:900">MOTM</div>':'')+
        '</div>'+
        '</div>';
    });
  } else { histHtml='<p style="color:var(--muted);font-size:.8rem;padding:.5rem">No match history recorded yet.</p>'; }

  document.getElementById('playerProfileContent').innerHTML=
    '<div class="profile-header">'+
    playerPhotoEl(p,70)+
    '<div style="flex:1">'+
      '<div style="font-family:\'Bebas Neue\';font-size:2rem;color:var(--green);line-height:1">'+esc(p.name)+'</div>'+
      '<div style="font-size:.8rem;color:var(--muted);margin-bottom:.3rem">'+(t?esc(t.name):'')+'</div>'+
      '<div style="display:flex;align-items:center;gap:.35rem;flex-wrap:wrap">'+catBadge(p.cat)+(p.bid?coinBadge(p.bid,false):'')+condBadge(wr)+'</div>'+
    '</div>'+
    '<button onclick="closeModal(\'playerProfileModal\')" style="background:none;border:none;color:var(--muted);font-size:1.5rem;cursor:pointer;align-self:flex-start">✕</button>'+
    '</div>'+
    '<div class="profile-body">'+
    '<div style="background:rgba(0,200,83,.04);border:1px solid rgba(0,200,83,.12);border-radius:8px;padding:.5rem .8rem;margin-bottom:.8rem;font-size:.73rem;color:var(--muted)">'+
    '⚡ Stats reflect <strong style="color:var(--green)">individual duel results</strong> — separate from team score</div>'+
    '<div class="profile-stat-grid">'+
    '<div class="pstat"><div class="pv">'+pts+'</div><div class="pl">Points</div></div>'+
    '<div class="pstat"><div class="pv">'+mp+'</div><div class="pl">Matches</div></div>'+
    '<div class="pstat"><div class="pv">'+(s.wins||0)+'</div><div class="pl">Wins</div></div>'+
    '<div class="pstat"><div class="pv">'+(s.draws||0)+'</div><div class="pl">Draws</div></div>'+
    '<div class="pstat"><div class="pv">'+(s.losses||0)+'</div><div class="pl">Losses</div></div>'+
    '<div class="pstat"><div class="pv">'+wr+'%</div><div class="pl">Win Rate</div></div>'+
    '<div class="pstat"><div class="pv">'+(s.goals||0)+'</div><div class="pl">Goals</div></div>'+
    '<div class="pstat"><div class="pv">'+(s.motm||0)+'</div><div class="pl">MOTM</div></div>'+
    '<div class="pstat"><div class="pv">'+(s.cs||0)+'</div><div class="pl">Clean Sheets</div></div>'+
    '</div>'+
    condDisplay+
    '<div style="font-size:.82rem;color:var(--muted);margin-bottom:.5rem;font-weight:700;text-transform:uppercase;letter-spacing:.5px">Form (Last 10)</div>'+
    formHtml+
    '<div style="font-family:\'Bebas Neue\';font-size:1.3rem;letter-spacing:2px;color:var(--green);margin:1rem 0 .5rem;border-top:1px solid var(--border);padding-top:.8rem">Match History ('+history.length+')</div>'+
    histHtml+
    '</div>';
  // Add download JPG button at top of profile body
  var profileBody = document.getElementById('playerProfileContent');
  if(profileBody){
    var dlBtn = document.createElement('button');
    dlBtn.textContent = 'Download Player Card';
    dlBtn.setAttribute('data-pid', pid);
    dlBtn.style.cssText = 'position:absolute;top:14px;right:46px;background:linear-gradient(135deg,#00E664,#009624);color:#000;border:none;border-radius:8px;padding:.35rem .85rem;font-family:Barlow Condensed,sans-serif;font-size:.78rem;font-weight:800;cursor:pointer;letter-spacing:.5px;z-index:10;';
    dlBtn.onclick = function(){ downloadPlayerCardJPG(this.getAttribute('data-pid')); };
    profileBody.style.position = 'relative';
    // Remove existing download btn if any
    var existing = profileBody.querySelector('.player-dl-btn');
    if(existing) existing.remove();
    dlBtn.className = 'player-dl-btn';
    profileBody.appendChild(dlBtn);
  }
  document.getElementById('playerProfileModal').classList.remove('hidden');
}

// ════════════════════════════════════════════
// FIXTURES
// ════════════════════════════════════════════
function teamTlEl(t){
  if(!t) return '<div class="tl">⚽</div>';
  var src=t.logoUrl||t.logo;
  if(src&&src.startsWith('http')) return '<div class="tl"><img src="'+esc(src)+'" onerror="this.parentNode.innerHTML=\'⚽\'"></div>';
  return '<div class="tl">'+(src||'⚽')+'</div>';
}
function fxHTML(f, noClick){
  var ht=getTeamById(f.home), at=getTeamById(f.away);
  if(!ht||!at) return '';
  var sc='<div class="vs-txt">VS</div>';
  if(f.homeScore!=null){
    sc='<div class="score-txt" onclick="event.stopPropagation();showMatchDetail(\''+f.id+'\')" title="Click for match detail">'+f.homeScore+' — '+f.awayScore+'</div>';
  }
  var scls=f.status==='played'?'s-pl':f.status==='live'?'s-lv':'s-up';

  var probBar='';
  if(f.status==='upcoming'){
    var prob=calcWinProb(f.home,f.away);
    probBar='<div style="margin-top:.4rem">'+
      '<div style="display:flex;justify-content:space-between;font-size:.62rem;color:var(--muted);margin-bottom:.2rem">'+
      '<span>'+esc(ht.name)+' '+prob.home+'%</span><span>Draw '+prob.draw+'%</span><span>'+prob.away+'% '+esc(at.name)+'</span></div>'+
      '<div class="prob-bar">'+
      '<div class="prob-home" style="width:'+prob.home+'%"></div>'+
      '<div class="prob-draw" style="width:'+prob.draw+'%"></div>'+
      '<div class="prob-away" style="width:'+prob.away+'%"></div>'+
      '</div></div>';
  }
  var clickAttr=noClick?'':' onclick="showFxDetail(\''+f.id+'\')"';
  return '<div class="fx-card"'+clickAttr+'>'+
    '<div class="vs-block">'+
    '<div class="t-side">'+teamTlEl(ht)+'<div class="tn">'+esc(ht.name)+'</div></div>'+
    sc+
    '<div class="t-side">'+teamTlEl(at)+'<div class="tn">'+esc(at.name)+'</div></div></div>'+
    '<div class="fx-info">'+
    '<div style="font-size:.82rem;color:var(--acc);font-weight:600">📅 '+esc(f.date||'TBD')+'</div>'+
    (f.round?'<div style="font-size:.7rem;color:var(--green);font-weight:700">'+esc(f.round)+'</div>':'')+
    '<div style="font-size:.72rem;color:var(--muted)">📍 '+esc(f.venue||'TBD')+'</div>'+
    '<div style="margin-top:.35rem"><span class="sbadge '+scls+'">'+(f.status==='live'?'🔴 LIVE':f.status.toUpperCase())+'</span></div>'+
    probBar+
    '</div></div>';
}
function renderFxList(filter){
  currentFxFilter=filter;
  var list=getFixtures().filter(function(f){return filter==='all'||f.status===filter;});
  document.getElementById('fxList').innerHTML=list.length?list.map(function(f){return fxHTML(f,false);}).join(''):'<p style="color:var(--muted)">No fixtures.</p>';
}
function filterFx(c,btn){
  document.querySelectorAll('#section-fixtures .tab').forEach(function(b){b.classList.remove('active');});
  btn.classList.add('active'); renderFxList(c);
}

function showFxDetail(fxId){
  var f=state.fixtures[fxId]; if(!f) return;
  var ht=getTeamById(f.home), at=getTeamById(f.away);
  if(!ht||!at) return;

  var h2h=getFixtures().filter(function(fx){
    return fx.status==='played'&&fx.homeScore!=null&&((fx.home===f.home&&fx.away===f.away)||(fx.home===f.away&&fx.away===f.home));
  });
  var hWins=0, aWins=0, draws=0;
  h2h.forEach(function(fx){
    var homeIsOurHome=(fx.home===f.home);
    var hs=fx.homeScore, as_=fx.awayScore;
    if(hs===as_){draws++;}
    else if((hs>as_&&homeIsOurHome)||(as_>hs&&!homeIsOurHome)){hWins++;}
    else{aWins++;}
  });

  var rows=calcStandings();
  var hRow=rows.find(function(r){return r.id===f.home;})||{};
  var aRow=rows.find(function(r){return r.id===f.away;})||{};
  rows.forEach(function(r,i){if(r.id===f.home) hRow.pos=i+1; if(r.id===f.away) aRow.pos=i+1;});

  var prob=calcWinProb(f.home,f.away);

  var h2hList=h2h.length?h2h.map(function(fx){
    var homeIsOurHome=(fx.home===f.home);
    var hs=fx.homeScore, as_=fx.awayScore;
    var whoWon=hs>as_?(homeIsOurHome?ht.name:at.name):hs<as_?(homeIsOurHome?at.name:ht.name):'Draw';
    var wCol=whoWon===ht.name?'var(--green)':whoWon==='Draw'?'var(--acc)':'var(--red)';
    return '<div style="display:flex;align-items:center;gap:.6rem;padding:.4rem .6rem;border-bottom:1px solid var(--border);font-size:.82rem">'+
      '<span style="flex:1">'+esc(ht.name)+' '+(homeIsOurHome?hs:as_)+' – '+(homeIsOurHome?as_:hs)+' '+esc(at.name)+'</span>'+
      '<span style="color:'+wCol+';font-weight:700">'+esc(whoWon)+'</span>'+
      '<span style="font-size:.68rem;color:var(--muted)">'+esc(fx.date||'')+'</span>'+
      '<button onclick="showMatchDetail(\''+fx.id+'\')" style="background:rgba(0,200,83,.08);border:1px solid rgba(0,200,83,.2);color:var(--green);border-radius:5px;padding:.15rem .4rem;font-size:.65rem;cursor:pointer;font-family:\'Barlow Condensed\';font-weight:700">Details</button>'+
      '</div>';
  }).join(''):'<p style="color:var(--muted);font-size:.8rem;padding:.5rem">No previous meetings.</p>';

  function sRow(label,hv,av){
    var hc=parseFloat(hv)>parseFloat(av)?'color:var(--green);font-weight:700':'';
    var ac=parseFloat(av)>parseFloat(hv)?'color:var(--green);font-weight:700':'';
    return '<tr><td style="color:var(--muted);font-size:.78rem;padding:.35rem .7rem">'+label+'</td>'+
      '<td style="text-align:right;padding:.35rem .7rem;'+hc+'">'+hv+'</td>'+
      '<td style="text-align:right;padding:.35rem .7rem;'+ac+'">'+av+'</td></tr>';
  }

  document.getElementById('fxDetailContent').innerHTML=
    '<div style="display:flex;align-items:center;gap:.7rem;margin-bottom:1.2rem">'+
    '<div style="font-family:\'Bebas Neue\';font-size:1.7rem;color:#7CB9FF;flex:1">⚔️ Fixture Detail</div>'+
    '<button onclick="closeModal(\'fxDetailModal\')" style="background:none;border:none;color:var(--muted);font-size:1.5rem;cursor:pointer">✕</button></div>'+
    '<div style="display:flex;align-items:center;justify-content:space-between;background:var(--card2);border-radius:12px;padding:1rem;margin-bottom:1rem;border:1px solid var(--border)">'+
    '<div style="text-align:center;flex:1">'+teamBigLogo(ht)+'<div style="font-weight:700;margin-top:.3rem">'+esc(ht.name)+'</div>'+
    '<div style="font-size:.7rem;color:var(--muted)">Strength: '+calcTeamStrength(f.home)+'%</div></div>'+
    '<div style="text-align:center;padding:0 .8rem">'+
    '<div style="font-family:\'Bebas Neue\';font-size:1.2rem;color:var(--muted)">'+(f.status==='played'?f.homeScore+' — '+f.awayScore:'VS')+'</div>'+
    '<div style="font-size:.7rem;color:var(--acc)">'+esc(f.date||'TBD')+'</div>'+
    (f.round?'<div style="font-size:.68rem;color:var(--green)">'+esc(f.round)+'</div>':'')+
    '</div>'+
    '<div style="text-align:center;flex:1">'+teamBigLogo(at)+'<div style="font-weight:700;margin-top:.3rem">'+esc(at.name)+'</div>'+
    '<div style="font-size:.7rem;color:var(--muted)">Strength: '+calcTeamStrength(f.away)+'%</div></div>'+
    '</div>'+
    '<div style="background:var(--card2);border-radius:10px;padding:.8rem;margin-bottom:1rem;border:1px solid var(--border)">'+
    '<div style="font-family:\'Barlow Condensed\';font-size:.8rem;font-weight:700;color:var(--muted);text-transform:uppercase;letter-spacing:1px;margin-bottom:.5rem">📊 Win Probability</div>'+
    '<div style="display:flex;justify-content:space-between;font-size:.72rem;margin-bottom:.3rem">'+
    '<span style="color:var(--green)">'+esc(ht.name)+' '+prob.home+'%</span><span style="color:var(--acc)">Draw '+prob.draw+'%</span><span style="color:var(--red)">'+prob.away+'% '+esc(at.name)+'</span></div>'+
    '<div class="prob-bar" style="height:12px">'+
    '<div class="prob-home" style="width:'+prob.home+'%"></div>'+
    '<div class="prob-draw" style="width:'+prob.draw+'%"></div>'+
    '<div class="prob-away" style="width:'+prob.away+'%"></div>'+
    '</div></div>'+
    '<div style="margin-bottom:1rem">'+
    '<div style="font-family:\'Barlow Condensed\';font-size:.85rem;font-weight:700;color:var(--muted);text-transform:uppercase;letter-spacing:1px;margin-bottom:.5rem">📋 Table Standing</div>'+
    '<div class="twrap"><table>'+
    '<thead><tr><th></th><th style="text-align:right">'+esc(ht.name)+'</th><th style="text-align:right">'+esc(at.name)+'</th></tr></thead><tbody>'+
    sRow('Position',hRow.pos||'—',aRow.pos||'—')+
    sRow('Points',hRow.pts||0,aRow.pts||0)+
    sRow('Played',hRow.p||0,aRow.p||0)+
    sRow('Wins',hRow.w||0,aRow.w||0)+
    sRow('Draws',hRow.d||0,aRow.d||0)+
    sRow('Losses',hRow.l||0,aRow.l||0)+
    sRow('Win %',(hRow.wr||0)+'%',(aRow.wr||0)+'%')+
    sRow('GF',hRow.gf||0,aRow.gf||0)+
    sRow('GA',hRow.ga||0,aRow.ga||0)+
    '</tbody></table></div></div>'+
    '<div>'+
    '<div style="font-family:\'Barlow Condensed\';font-size:.85rem;font-weight:700;color:var(--muted);text-transform:uppercase;letter-spacing:1px;margin-bottom:.5rem">⚔️ Head-to-Head ('+h2h.length+' matches)</div>'+
    (h2h.length?'<div style="display:flex;gap:.6rem;margin-bottom:.6rem;flex-wrap:wrap">'+
      '<span style="background:rgba(0,200,83,.1);color:var(--green);border:1px solid rgba(0,200,83,.3);padding:.2rem .6rem;border-radius:8px;font-size:.78rem">'+esc(ht.name)+': '+hWins+' wins</span>'+
      '<span style="background:rgba(255,214,0,.08);color:var(--acc);border:1px solid rgba(255,214,0,.2);padding:.2rem .6rem;border-radius:8px;font-size:.78rem">Draws: '+draws+'</span>'+
      '<span style="background:rgba(255,61,61,.08);color:var(--red);border:1px solid rgba(255,61,61,.2);padding:.2rem .6rem;border-radius:8px;font-size:.78rem">'+esc(at.name)+': '+aWins+' wins</span>'+
      '</div>':'')+
    '<div style="border:1px solid var(--border);border-radius:10px;overflow:hidden">'+h2hList+'</div></div>';

  document.getElementById('fxDetailModal').classList.remove('hidden');
}

function showMatchDetail(fxId){
  var match=getMatches().find(function(m){return m.fixtureId===fxId;});
  if(match){ viewMatchInModal(match.id); return; }
  var f=state.fixtures[fxId]; if(!f) return;
  var ht=getTeamById(f.home), at=getTeamById(f.away);
  document.getElementById('matchDetailContent').innerHTML=
    '<div style="display:flex;align-items:center;gap:.7rem;margin-bottom:1rem">'+
    '<div style="font-family:\'Bebas Neue\';font-size:1.6rem;color:var(--green);flex:1">Match Result</div>'+
    '<button onclick="closeModal(\'matchDetailModal\')" style="background:none;border:none;color:var(--muted);font-size:1.5rem;cursor:pointer">✕</button></div>'+
    '<div style="text-align:center;padding:1.5rem;background:var(--card2);border-radius:12px;border:1px solid var(--border)">'+
    '<div style="font-family:\'Bebas Neue\';font-size:2.5rem;color:var(--green)">'+(ht?esc(ht.name):'?')+' '+f.homeScore+' — '+f.awayScore+' '+(at?esc(at.name):'?')+'</div>'+
    '<div style="color:var(--muted);font-size:.8rem;margin-top:.3rem">No detailed scorecard yet.</div></div>';
  document.getElementById('matchDetailModal').classList.remove('hidden');
}

// ════════════════════════════════════════════
// POINTS TABLE
// ════════════════════════════════════════════
function calcStandings(){
  var map={};
  getTeams().forEach(function(t){ map[t.id]={id:t.id,name:t.name,logo:t.logoUrl||t.logo,p:0,w:0,d:0,l:0,gf:0,ga:0,pts:0,form:[]}; });
  getFixtures().filter(function(f){return f.status==='played'&&f.homeScore!=null;}).forEach(function(f){
    var h=map[f.home],a=map[f.away]; if(!h||!a) return;
    h.p++;a.p++;
    h.gf+=+f.homeScore; h.ga+=+f.awayScore;
    a.gf+=+f.awayScore; a.ga+=+f.homeScore;
    if(f.homeScore>f.awayScore){h.w++;h.pts+=3;h.form.push('fw');a.l++;a.form.push('ld');}
    else if(f.homeScore<f.awayScore){a.w++;a.pts+=3;a.form.push('fw');h.l++;h.form.push('ld');}
    else{h.d++;h.pts+=1;h.form.push('dr');a.d++;a.pts+=1;a.form.push('dr');}
  });
  var ms=state.manual_standings||{};
  Object.keys(ms).forEach(function(tid){
    if(!map[tid]) return;
    var ov=ms[tid];
    if(ov.pointsOverride!=null){ map[tid].pts=ov.pointsOverride; }
    else {
      if(ov.w!=null) map[tid].w=ov.w; if(ov.d!=null) map[tid].d=ov.d; if(ov.l!=null) map[tid].l=ov.l;
      if(ov.gf!=null) map[tid].gf=ov.gf; if(ov.ga!=null) map[tid].ga=ov.ga;
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
    var ms=state.manual_standings&&state.manual_standings[r.id];
    return !(ms&&ms.hidden);
  });
  document.getElementById('ptBody').innerHTML=rows.map(function(r,i){
    var col=i===0?'var(--acc)':i<3?'var(--green)':'var(--muted)';
    var logoH=r.logo&&r.logo.startsWith('http')?
      '<div class="mini-logo" style="width:26px;height:26px;"><img src="'+esc(r.logo)+'" onerror="this.parentNode.innerHTML=\'⚽\'"></div>':
      '<div class="mini-logo" style="width:26px;height:26px;font-size:.9rem;">'+(r.logo||'⚽')+'</div>';
    var wrColor=r.wr>=60?'var(--green)':r.wr>=40?'var(--acc)':'var(--red)';
    return '<tr>'+
      '<td><span class="pos-num" style="color:'+col+'">'+(i+1)+'</span></td>'+
      '<td><div class="team-cell">'+logoH+'<span style="color:var(--text)">'+esc(r.name)+'</span></div></td>'+
      '<td style="color:var(--text)">'+r.p+'</td>'+
      '<td style="color:var(--text)">'+r.w+'</td>'+
      '<td style="color:var(--text)">'+r.d+'</td>'+
      '<td style="color:var(--text)">'+r.l+'</td>'+
      '<td><span style="color:'+wrColor+';font-weight:700">'+r.wr+'%</span></td>'+
      '<td style="color:var(--text)">'+r.gf+'</td>'+
      '<td style="color:var(--text)">'+r.ga+'</td>'+
      '<td style="color:'+(r.gf-r.ga>=0?'var(--green)':'var(--red)')+'">'+((r.gf-r.ga)>=0?'+':'')+(r.gf-r.ga)+'</td>'+
      '<td><span class="pts-val">'+r.pts+'</span></td>'+
      '<td><div class="form-dots">'+r.form.slice(-5).map(function(c){return '<div class="fd '+c+'"></div>';}).join('')+'</div></td>'+
      '</tr>';
  }).join('');
}

// ════════════════════════════════════════════
// CONDITION CIRCLE
// ════════════════════════════════════════════
function condCircle(wr,size){
  size=size||36;
  var c=getCondition(wr);
  var bgMap={'cond-ap':'linear-gradient(135deg,#FFD700,#FFA500)','cond-a':'linear-gradient(135deg,#00C853,#009624)',
    'cond-bp':'linear-gradient(135deg,#2979FF,#1565C0)','cond-bm':'linear-gradient(135deg,#4FC3F7,#0288D1)',
    'cond-c':'linear-gradient(135deg,#78909C,#546E7A)','cond-d':'linear-gradient(135deg,#FF6D00,#E65100)',
    'cond-e':'linear-gradient(135deg,#FF3D3D,#B71C1C)'};
  var bg=bgMap[c.cls]||'linear-gradient(135deg,#78909C,#546E7A)';
  return '<div style="width:'+size+'px;height:'+size+'px;border-radius:50%;background:'+bg+';display:flex;align-items:center;justify-content:center;flex-shrink:0;box-shadow:0 2px 8px rgba(0,0,0,.4)">'+
    '<span style="font-family:\'Barlow Condensed\';font-weight:900;font-size:'+(size*.32)+'px;color:#fff;letter-spacing:-.5px">'+c.label+'</span></div>';
}

// ════════════════════════════════════════════
// RANKING — clean version, no history
// Tab-specific stat display, team filter, GC/GD
// ════════════════════════════════════════════
function renderRank(type){
  currentRankType=type;
  var sel=document.getElementById('rankTeamFilter');
  if(sel){
    var cv=sel.value;
    sel.innerHTML='<option value="">All Teams</option>'+
      getTeams().map(function(t){return '<option value="'+t.id+'"'+(t.id===cv?' selected':'')+'>'+esc(t.name)+'</option>';}).join('');
    if(cv) sel.value=cv;
  }
  var filterTid=sel?sel.value:'';

  var ps=getPlayers().filter(function(p){ return !filterTid||p.teamId===filterTid; }).slice().sort(function(a,b){
    var sa=computeStatsFromHistory(a.id), sb=computeStatsFromHistory(b.id);
    if(type==='total') return realCalcPts(sb)-realCalcPts(sa);
    if(type==='goals') return (sb.gf||0)-(sa.gf||0);
    if(type==='motm')  return (sb.motm||0)-(sa.motm||0);
    if(type==='cs')    return (sb.cs||0)-(sa.cs||0);
    if(type==='wr')    return winRatio(sb)-winRatio(sa);
    return 0;
  });

  var medals={0:'linear-gradient(135deg,#FFD700,#FFA000)',1:'linear-gradient(135deg,#C0C0C0,#9E9E9E)',2:'linear-gradient(135deg,#CD7F32,#8D4E1A)'};

  document.getElementById('rankList').innerHTML=ps.map(function(p,i){
    var t=getTeamById(p.teamId);
    var s=computeStatsFromHistory(p.id);
    var wr=winRatio(s);
    var wins=s.wins, draws=s.draws, losses=s.losses, mp=s.mp;
    var gf=s.gf, gc=s.ga, gd=gf-gc;
    var pts=realCalcPts(s);
    var cond=getCondition(wr);

    // Condition circle
    var bgMap={'cond-ap':'linear-gradient(135deg,#FFD700,#FFA500)','cond-a':'linear-gradient(135deg,#00C853,#009624)',
      'cond-bp':'linear-gradient(135deg,#2979FF,#1565C0)','cond-bm':'linear-gradient(135deg,#4FC3F7,#0288D1)',
      'cond-c':'linear-gradient(135deg,#78909C,#546E7A)','cond-d':'linear-gradient(135deg,#FF6D00,#E65100)',
      'cond-e':'linear-gradient(135deg,#FF3D3D,#B71C1C)'};
    var condBg=bgMap[cond.cls]||bgMap['cond-c'];
    var condHtml=mp>=3
      ?'<span style="background:'+condBg+';color:#fff;padding:.1rem .4rem;border-radius:5px;font-family:Barlow Condensed,sans-serif;font-size:.7rem;font-weight:900;white-space:nowrap;">'+cond.icon+' '+cond.label+'</span>'
      :'<span style="background:var(--card2);border:1px dashed var(--border);color:var(--muted);padding:.1rem .4rem;border-radius:5px;font-size:.65rem;white-space:nowrap;">NEW</span>';

    var boostTag=mp>=3
      ?'<span style="font-size:.58rem;color:var(--green);margin-left:.2rem;">x'+cond.boost+'</span>'
      :'';

    // Form dots
    var history=getPlayerAllMatchHistory(p.id).slice(0,8);
    var formDots='<div style="display:flex;gap:.2rem;align-items:center;">'
      +(history.length
        ? history.map(function(h){ var rc=h.result==='W'?'#00C853':h.result==='L'?'#FF3D3D':'#FFD600'; return '<div style="width:9px;height:9px;border-radius:50%;background:'+rc+';flex-shrink:0;"></div>'; }).join('')
        : '<span style="font-size:.6rem;color:var(--muted);">No matches</span>'
      )+'</div>';

    // Stat row
    var statRowHtml='', cols='repeat(8,1fr)';
    var wCell='<div style="text-align:center;"><div style="font-family:Bebas Neue,sans-serif;font-size:1rem;color:var(--green);line-height:1.1;">'+wins+'</div><div style="font-size:.55rem;color:var(--green);font-weight:700;">W</div></div>';
    var dCell='<div style="text-align:center;"><div style="font-family:Bebas Neue,sans-serif;font-size:1rem;color:var(--acc);line-height:1.1;">'+draws+'</div><div style="font-size:.55rem;color:var(--acc);font-weight:700;">D</div></div>';
    var lCell='<div style="text-align:center;"><div style="font-family:Bebas Neue,sans-serif;font-size:1rem;color:var(--red);line-height:1.1;">'+losses+'</div><div style="font-size:.55rem;color:var(--red);font-weight:700;">L</div></div>';
    if(type==='total'){
      cols='repeat(8,1fr)';
      statRowHtml=statCell('MP',mp)+wCell+dCell+lCell
        +'<div style="text-align:center;"><div style="font-family:Bebas Neue,sans-serif;font-size:1rem;color:#7CB9FF;line-height:1.1;">'+gf+'</div><div style="font-size:.55rem;color:#7CB9FF;font-weight:700;">GF</div></div>'
        +'<div style="text-align:center;"><div style="font-family:Bebas Neue,sans-serif;font-size:1rem;color:#FF8A50;line-height:1.1;">'+gc+'</div><div style="font-size:.55rem;color:#FF8A50;font-weight:700;">GC</div></div>'
        +'<div style="text-align:center;"><div style="font-family:Bebas Neue,sans-serif;font-size:1rem;color:'+(gd>=0?'var(--green)':'var(--red)')+';line-height:1.1;">'+(gd>=0?'+':'')+gd+'</div><div style="font-size:.55rem;color:var(--muted);font-weight:700;">GD</div></div>'
        +statCell('WR%',wr+'%');
    } else if(type==='goals'){
      cols='repeat(4,1fr)';
      statRowHtml=statCell('MP',mp)+statCell('GF',gf)+statCell('GC',gc)+statCell('GD',(gd>=0?'+':'')+gd);
    } else if(type==='motm'){
      cols='repeat(2,1fr)';
      statRowHtml=statCell('MP',mp)+statCell('MOTM',s.motm||0);
    } else if(type==='cs'){
      cols='repeat(2,1fr)';
      statRowHtml=statCell('MP',mp)+statCell('CS',s.cs||0);
    } else if(type==='wr'){
      cols='repeat(5,1fr)';
      statRowHtml=statCell('MP',mp)+statCell('WR%',wr+'%')+wCell+dCell+lCell;
    }

    var mainVal=type==='total'?pts:type==='goals'?gf:type==='motm'?(s.motm||0):type==='cs'?(s.cs||0):wr+'%';
    var mainLbl=type==='total'?'PTS':type==='goals'?'GF':type==='motm'?'MOTM':type==='cs'?'CS':'WR%';
    var mainColor=type==='total'?'var(--green)':type==='goals'?'#7CB9FF':type==='motm'?'var(--acc)':type==='cs'?'#7CB9FF':'var(--green)';

    return '<div style="background:var(--card);border:1px solid var(--border);border-radius:14px;padding:.85rem 1rem;cursor:pointer;" onclick="showPlayerProfile(\'' + p.id + '\')">'
      +'<div style="display:flex;align-items:center;gap:.6rem;">'
        +'<div style="width:26px;height:26px;border-radius:50%;background:'+(medals[i]||'var(--card2)')+';display:flex;align-items:center;justify-content:center;flex-shrink:0;">'
          +'<span style="font-family:Bebas Neue,sans-serif;font-size:.9rem;color:'+(i<3?'#000':'var(--muted)')+'">'+(i+1)+'</span></div>'
        +playerPhotoEl(p,38)
        +'<div style="flex:1;min-width:0;">'
          // ── Full name, no truncation ──
          +'<div style="font-weight:700;font-size:.9rem;word-break:break-word;line-height:1.3;">'+esc(p.name)+'</div>'
          +'<div style="display:flex;align-items:center;gap:.3rem;flex-wrap:wrap;margin-top:.15rem;">'
            +(t?'<span style="font-size:.67rem;color:var(--muted);">'+esc(t.name)+'</span><span style="color:var(--border);">·</span>':'')+catBadge(p.cat)
            +(p.bid?coinBadge(p.bid,false):'')
            // ── Condition shown next to name area ──
            +condHtml
          +'</div>'
        +'</div>'
        +'<div style="text-align:right;flex-shrink:0;">'
          +'<div style="font-family:Bebas Neue,sans-serif;font-size:1.6rem;color:'+mainColor+';line-height:1;">'+mainVal+'</div>'
          +'<div style="font-size:.58rem;color:var(--muted);text-transform:uppercase;">'+mainLbl+boostTag+'</div>'
        +'</div>'
      +'</div>'
      +'<div style="display:grid;grid-template-columns:'+cols+';gap:.25rem;margin-top:.6rem;background:var(--card2);border-radius:8px;padding:.45rem .35rem;">'
        +statRowHtml
      +'</div>'
      +'<div style="display:flex;align-items:center;gap:.4rem;margin-top:.45rem;">'
        +'<span style="font-size:.6rem;color:var(--muted);text-transform:uppercase;letter-spacing:.5px;flex-shrink:0;">Form</span>'
        +formDots
      +'</div>'
      +'</div>';
  }).join('')||'<p style="color:var(--muted);padding:1rem;">No players found.</p>';
}

function statCell(lbl,val){
  return '<div style="text-align:center">'+
    '<div style="font-family:\'Bebas Neue\';font-size:1.05rem;color:var(--text);line-height:1.1">'+val+'</div>'+
    '<div style="font-size:.55rem;color:var(--muted);text-transform:uppercase;letter-spacing:.3px;font-weight:600">'+lbl+'</div></div>';
}
function showRank(t,btn){
  document.querySelectorAll('.rtab').forEach(function(b){b.classList.remove('active');});
  btn.classList.add('active');
  renderRank(t);
}

// ════════════════════════════════════════════
// ADMIN TABS
// ════════════════════════════════════════════
function showATab(tab,btn){
  document.querySelectorAll('.atab').forEach(function(b){b.classList.remove('active');});
  if(btn) btn.classList.add('active');
  renderATab(tab);
}
function renderATab(tab){
  var el=document.getElementById('adminContent'); if(!el) return;
  if(tab==='teams') el.innerHTML=aTeamsHTML();
  else if(tab==='players') el.innerHTML=aPlayersHTML();
  else if(tab==='fixtures') el.innerHTML=aFixturesHTML();
  else if(tab==='matches') el.innerHTML=aMatchesHTML();
  else if(tab==='history')   el.innerHTML=aHistoryHTML();
  else if(tab==='fxgen')     el.innerHTML=aFxGenHTML();
  else if(tab==='standings') el.innerHTML=aStandingsHTML();
  else if(tab==='news') el.innerHTML=aNewsHTML();
  else el.innerHTML=aTeamsHTML();
}

// ════════════════════════════════════════════
// ADMIN — TEAMS
// ════════════════════════════════════════════
function aTeamsHTML(){
  var teams=getTeams();
  return '<div class="apanel"><h3>➕ Register New Team</h3>'+
    '<div class="fgrid">'+
    '<div class="fg"><label>Team Name *</label><input id="nt_nm" placeholder="e.g. Fire Wolves"></div>'+
    '<div class="fg"><label>President / Manager</label><input id="nt_pr" placeholder="Full name"></div>'+
    '<div class="fg"><label>Team Color</label><input id="nt_cl" type="color" value="#00C853"></div>'+
    '<div class="fg"><label style="color:var(--blue)">Captain Password</label><input id="nt_cappwd" type="text" placeholder="e.g. team123" autocomplete="off"></div>'+
    buildLogoInput('nt_lg','Team Logo')+
    '</div><div style="margin-top:.9rem"><button class="btn bg" onclick="addTeamAsync()">Register Team</button></div></div>'+
    '<div class="apanel"><h3>📋 All Teams ('+teams.length+')</h3><div class="alist">'+teams.map(function(t){
      var ps=getPlayersByTeam(t.id);
      return '<div><div class="aitem">'+teamLogoEl(t,32)+
        '<div class="ai"><div class="an">'+esc(t.name)+'</div><div class="am">👔 '+esc(t.president||'—')+' | '+ps.length+' players</div></div>'+
        '<button class="btn" style="background:rgba(0,200,83,.1);color:var(--green);border:1px solid rgba(0,200,83,.3);font-size:.72rem;padding:.27rem .6rem" onclick="openEditTeam(\''+t.id+'\')">Edit</button>'+
        '<button class="btn" style="background:rgba(41,121,255,.1);color:#6AB0FF;border:1px solid rgba(41,121,255,.3);font-size:.72rem;padding:.27rem .6rem" onclick="toggle(\'pf_'+t.id+'\')">+ Player</button>'+
        '<button class="bd" onclick="deleteTeamAsync(\''+t.id+'\')">Delete</button></div>'+
        '<div id="pf_'+t.id+'" class="expand-sec">'+
        '<div style="font-size:.78rem;color:var(--green);font-weight:700;margin-bottom:.8rem;text-transform:uppercase;letter-spacing:1px">Add Players to '+esc(t.name)+'</div>'+
        '<div style="background:rgba(0,200,83,.04);border:1px solid rgba(0,200,83,.15);border-radius:8px;padding:.8rem;margin-bottom:.8rem">'+
        '<div style="font-size:.72rem;color:var(--acc);font-weight:700;margin-bottom:.4rem;text-transform:uppercase;letter-spacing:1px">📋 Bulk Add (paste multiple names)</div>'+
        '<textarea id="bulk_'+t.id+'" style="width:100%;background:var(--dark);border:1px solid var(--border);border-radius:8px;padding:.55rem .75rem;color:var(--text);font-family:monospace;font-size:.8rem;resize:vertical;min-height:100px;outline:none;" placeholder="Ahmed Ali, local&#10;Tariq Nasser, invited&#10;Karim Said, youth"></textarea>'+
        '<button class="btn bg" onclick="bulkAddPlayersAsync(\''+t.id+'\')" style="font-size:.8rem;padding:.4rem .9rem;margin-top:.5rem">➕ Add All Players</button>'+
        '</div>'+
        '<div class="fgrid">'+
        '<div class="fg"><label>Name *</label><input id="pp_n_'+t.id+'" placeholder="Full name"></div>'+
        '<div class="fg"><label>Category</label><select id="pp_c_'+t.id+'"><option value="local">Local</option><option value="youth">Youth</option><option value="invited">Invited</option></select></div>'+
        '<div class="fg"><label>Bid (coins)</label><input id="pp_bid_'+t.id+'" type="number" placeholder="0" min="0"></div>'+
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
  var capPwd=(document.getElementById('nt_cappwd')||{}).value||'';
  await fsSet('teams',id,{id:id,name:name,president:document.getElementById('nt_pr').value.trim(),color:document.getElementById('nt_cl').value,logoUrl:logoUrl,capPassword:capPwd.trim()});
}
async function deleteTeamAsync(tid){
  if(!confirm('Delete team and ALL its players?')) return;
  var F=fb(); if(!F){alert('Firebase not ready');return;}
  try{
    await F.deleteDoc(F.doc(F.db,'teams',tid));
    var snap=await F.getDocs(F.collection(F.db,'players'));
    var batch=F.writeBatch(F.db); var bc=0;
    snap.forEach(function(d){ var pl=d.data(); if(pl.teamId===tid){batch.delete(F.doc(F.db,'players',d.id));batch.delete(F.doc(F.db,'stats',d.id));bc++;}});
    if(bc>0) await batch.commit();
    var fsnap=await F.getDocs(F.collection(F.db,'fixtures'));
    var fb2=F.writeBatch(F.db); var fc=0;
    fsnap.forEach(function(d){ var fx=d.data(); if(fx.home===tid||fx.away===tid){fb2.delete(F.doc(F.db,'fixtures',d.id));fc++;}});
    if(fc>0) await fb2.commit();
    alert('✅ Team deleted.');
  }catch(e){alert('Error: '+e.message);}
}
async function addPlayerToTeamAsync(tid){
  var name=document.getElementById('pp_n_'+tid).value.trim();
  if(!name){alert('Name required!');return;}
  var photoUrl=await resolvePhotoUrl('pp_ph_'+tid);
  var pid=uid();
  var bid=parseInt((document.getElementById('pp_bid_'+tid)||{}).value)||0;
  await fsSet('players',pid,{id:pid,name:name,teamId:tid,cat:document.getElementById('pp_c_'+tid).value,photoUrl:photoUrl,bid:bid});
  await fsSet('stats',pid,{wins:0,losses:0,draws:0,goals:0,cs:0,motm:0,mp:0,gf:0,ga:0});
  toggle('pf_'+tid);
}
async function bulkAddPlayersAsync(tid){
  var textarea=document.getElementById('bulk_'+tid); if(!textarea){alert('Not found');return;}
  var text=textarea.value.trim(); if(!text){alert('Paste at least one name!');return;}
  var lines=text.split('\n').map(function(l){return l.trim();}).filter(Boolean);
  var catMap={'local':'local','youth':'youth','invited':'invited','l':'local','y':'youth','i':'invited'};
  var added=0,skipped=0; var F=fb();
  for(var line of lines){
    var parts=line.split(','); var name=(parts[0]||'').trim(); if(!name){skipped++;continue;}
    var cat=catMap[(parts[1]||'local').trim().toLowerCase()]||'local';
    var pid=uid();
    try{ if(F){ await F.setDoc(F.doc(F.db,'players',pid),{id:pid,name:name,teamId:tid,cat:cat,photoUrl:''}); await F.setDoc(F.doc(F.db,'stats',pid),{wins:0,losses:0,draws:0,goals:0,cs:0,motm:0,mp:0,gf:0,ga:0}); } added++; }
    catch(e){skipped++;}
  }
  textarea.value='';
  alert('✅ Added '+added+(skipped?' | ⚠️ Skipped '+skipped:'')+'.');
}

// ════════════════════════════════════════════
// ADMIN — PLAYERS
// ════════════════════════════════════════════
function aPlayersHTML(){
  var ps=getPlayers();
  return '<div class="apanel"><h3>➕ Register Player</h3>'+
    '<div class="fgrid">'+
    '<div class="fg"><label>Name *</label><input id="gp_n" placeholder="Full name"></div>'+
    '<div class="fg"><label>Team *</label><select id="gp_t">'+getTeams().map(function(t){return '<option value="'+t.id+'">'+esc(t.name)+'</option>';}).join('')+'</select></div>'+
    '<div class="fg"><label>Category</label><select id="gp_c"><option value="local">Local</option><option value="youth">Youth</option><option value="invited">Invited</option></select></div>'+
    '<div class="fg"><label>Bid Price (coins)</label><input id="gp_bid" type="number" placeholder="0" min="0"></div>'+
    buildPhotoInput('gp_ph')+
    '</div><div style="margin-top:.9rem"><button class="btn bg" onclick="addPlayerAsync()">Register Player</button></div></div>'+
    '<div class="apanel"><h3>📋 All Players ('+ps.length+')</h3><div class="alist">'+ps.map(function(p){
      var t=getTeamById(p.teamId); var s=computeStatsFromHistory(p.id); var wr=winRatio(s);
      var wins=s.wins||0, draws=s.draws||0, losses=s.losses||0, mp=s.mp||0, pts=realCalcPts(s);
      return '<div class="aitem">'+playerPhotoEl(p,30)+
        '<div class="ai"><div class="an">'+esc(p.name)+' '+condBadge(wr)+'</div>'+
        '<div class="am">'+catBadge(p.cat)+' '+(t?esc(t.name):'')+
        ' | <span style="color:var(--green)">W:'+wins+'</span>'+
        ' <span style="color:var(--acc)">D:'+draws+'</span>'+
        ' <span style="color:var(--red)">L:'+losses+'</span>'+
        ' '+(p.bid?coinBadge(p.bid,false):'')+' '+realCalcPts(s)+'pts</div></div>'+
        '<button class="btn" style="background:rgba(41,121,255,.1);color:#6AB0FF;border:1px solid rgba(41,121,255,.3);font-size:.72rem;padding:.27rem .6rem" onclick="openEditPlayer(\''+p.id+'\')">✏️ Edit</button>'+
        '<button class="bd" onclick="removePlayerAsync(\''+p.id+'\')">Remove</button></div>';
    }).join('')+'</div></div>';
}
async function addPlayerAsync(){
  var name=document.getElementById('gp_n').value.trim(); var tid=document.getElementById('gp_t').value;
  if(!name||!tid){alert('Name and team required!');return;}
  var photoUrl=await resolvePhotoUrl('gp_ph'); var pid=uid();
  var bid=parseInt(document.getElementById('gp_bid').value)||0;
  await fsSet('players',pid,{id:pid,name:name,teamId:tid,cat:document.getElementById('gp_c').value,photoUrl:photoUrl,bid:bid});
  await fsSet('stats',pid,{wins:0,losses:0,draws:0,goals:0,cs:0,motm:0,mp:0,gf:0,ga:0});
}
async function removePlayerAsync(pid){
  if(!confirm('Remove player?')) return;
  await fsDel('players',pid); await fsDel('stats',pid);
}
function findPlayerInState(pid){
  if(!pid) return null;
  return getPlayers().find(function(pl){return pl.id===pid;})||null;
}

function openEditPlayer(pid){
  var p=findPlayerInState(pid); if(!p){alert('Player not found');return;}
  var teams=getTeams();
  var teamOpts=teams.map(function(t){
    return '<option value="'+t.id+'"'+(t.id===p.teamId?' selected':'')+'>'+esc(t.name)+'</option>';
  }).join('');
  var catOpts=['local','youth','invited'].map(function(c){
    return '<option value="'+c+'"'+(p.cat===c?' selected':'')+'>'+c.charAt(0).toUpperCase()+c.slice(1)+'</option>';
  }).join('');

  var modal=document.getElementById('editPlayerModal');
  if(!modal){
    var div=document.createElement('div');
    div.id='editPlayerModal'; div.className='moverlay';
    div.innerHTML='<div class="modal" style="width:min(92vw,600px);max-height:90vh;overflow-y:auto;"><div id="editPlayerContent"></div></div>';
    document.body.appendChild(div); modal=div;
  }

  document.getElementById('editPlayerContent').innerHTML=
    '<div style="display:flex;align-items:center;gap:.8rem;margin-bottom:1rem;">'
    +'<div style="flex:1;font-family:Bebas Neue,sans-serif;font-size:1.6rem;color:var(--green);">Edit Player</div>'
    +'<button id="epCloseX" style="background:none;border:none;color:var(--muted);font-size:1.5rem;cursor:pointer;">&#10005;</button>'
    +'</div>'
    +'<div style="display:flex;gap:.35rem;margin-bottom:.8rem;border-bottom:1px solid var(--border);padding-bottom:.5rem;" id="epTabs">'
    +'<button class="tab active" data-p="info" onclick="epTab(this.dataset.p,this)" style="font-size:.76rem;">Info</button>'
    +'<button class="tab" data-p="history" onclick="epTab(this.dataset.p,this)" style="font-size:.76rem;">Match History</button>'
    +'</div>'
    +'<div id="epPanel_info">'
    +'<div class="fgrid" style="margin-bottom:.9rem;">'
    +'<div class="fg"><label>Name *</label><input id="ep_name" value="'+esc(p.name)+'"></div>'
    +'<div class="fg"><label>Team</label><select id="ep_team">'+teamOpts+'</select></div>'
    +'<div class="fg"><label>Category</label><select id="ep_cat">'+catOpts+'</select></div>'
    +'<div class="fg"><label>Bid (coins)</label><input id="ep_bid" type="number" value="'+(p.bid||0)+'" min="0"></div>'
    +'</div>'
    +buildPhotoInput('ep_ph')
    +'<div style="display:flex;gap:.7rem;flex-wrap:wrap;margin-top:.9rem;">'
    +'<button id="epSave" class="btn bg">Save</button>'
    +'<button id="epCancel" class="btn" style="background:var(--border);color:var(--text);">Cancel</button>'
    +'</div></div>'
    +'<div id="epPanel_history" style="display:none;"><div id="epHistoryList"></div></div>';

  document.getElementById('epCloseX').onclick  = function(){ closeModal('editPlayerModal'); };
  document.getElementById('epSave').onclick    = function(){ saveEditPlayer(pid); };
  document.getElementById('epCancel').onclick  = function(){ closeModal('editPlayerModal'); };
  modal.classList.remove('hidden');
  renderEditPlayerHistory(pid);
}

function epTab(panel,btn){
  document.querySelectorAll('#epTabs .tab').forEach(function(b){b.classList.remove('active');});
  btn.classList.add('active');
  ['info','history'].forEach(function(n){
    var el=document.getElementById('epPanel_'+n);
    if(el) el.style.display=(n===panel)?'block':'none';
  });
}

function renderEditPlayerHistory(pid){
  var el=document.getElementById('epHistoryList'); if(!el) return;
  var history=getPlayerAllMatchHistory(pid);
  if(!history.length){ el.innerHTML='<p style="color:var(--muted);font-size:.8rem;padding:.5rem;">No history yet.</p>'; return; }
  el.innerHTML='<div style="font-size:.72rem;color:var(--muted);margin-bottom:.5rem;">'+history.length+' entries</div>'
    +'<div style="display:flex;flex-direction:column;gap:.3rem;">'
    +history.map(function(h){
      var rc=h.result==='W'?'var(--green)':h.result==='L'?'var(--red)':'var(--acc)';
      var rcBg=h.result==='W'?'rgba(0,200,83,.08)':h.result==='L'?'rgba(255,61,61,.08)':'rgba(255,214,0,.06)';
      var dt=h.timestamp?new Date(h.timestamp).toLocaleDateString('en-GB',{day:'2-digit',month:'short'}):'';
      return '<div data-hmid="'+h.id+'" data-hpid="'+pid+'" style="background:'+rcBg+';border:1px solid var(--border);border-radius:9px;padding:.4rem .7rem;display:flex;align-items:center;gap:.45rem;flex-wrap:wrap;">'
        +'<div style="width:22px;height:22px;border-radius:50%;background:'+rc+';display:flex;align-items:center;justify-content:center;flex-shrink:0;">'
          +'<span style="font-family:Barlow Condensed,sans-serif;font-weight:900;font-size:.78rem;color:#fff;">'+h.result+'</span></div>'
        +'<div style="flex:1;min-width:80px;">'
          +'<div style="font-size:.82rem;font-weight:600;">vs '+esc(h.opponentTeam||'?')+'</div>'
          +'<div style="font-size:.68rem;color:var(--muted);">'+esc(h.opponentName||'?')+' · '+esc(h.round||'')+' · '+dt+'</div>'
        +'</div>'
        +'<div style="font-family:Bebas Neue,sans-serif;font-size:1.05rem;color:'+rc+';">'+h.myScore+' — '+h.oppScore+'</div>'
        +(h.motm?'<div style="font-size:.58rem;color:var(--acc);font-weight:900;">MOTM</div>':'')
        +'<button class="ep-hist-edit" data-id="'+h.id+'" data-pid="'+pid+'" style="background:rgba(255,214,0,.08);color:var(--acc);border:1px solid rgba(255,214,0,.2);border-radius:5px;padding:.18rem .45rem;font-family:Barlow Condensed,sans-serif;font-size:.7rem;font-weight:700;cursor:pointer;">Edit</button>'
        +'<button class="ep-hist-del" data-id="'+h.id+'" data-pid="'+pid+'" style="background:rgba(255,61,61,.08);color:var(--red);border:1px solid rgba(255,61,61,.2);border-radius:5px;padding:.18rem .45rem;font-family:Barlow Condensed,sans-serif;font-size:.7rem;font-weight:700;cursor:pointer;">Del</button>'
        +'</div>';
    }).join('')+'</div>';

  // Attach events after render
  el.querySelectorAll('.ep-hist-edit').forEach(function(btn){
    btn.onclick=function(){ openEditHistEntry(this.dataset.id, this.dataset.pid); };
  });
  el.querySelectorAll('.ep-hist-del').forEach(function(btn){
    btn.onclick=function(){ deleteHistEntryAndRefresh(this.dataset.id, this.dataset.pid); };
  });
}

async function deleteHistEntryAndRefresh(pmId, pid){
  if(!confirm('Delete this history entry?')) return;
  var pm=state.player_matches[pmId];
  if(pm){
    var oppEntries=Object.values(state.player_matches).filter(function(x){
      return x.id!==pmId && x.fixtureId===pm.fixtureId && x.opponentName===pm.playerName
        && x.myScore==pm.oppScore && x.oppScore==pm.myScore;
    });
    for(var oe of oppEntries) await fsDel('player_matches', oe.id);
  }
  await fsDel('player_matches', pmId);
  renderEditPlayerHistory(pid);
}

function openEditHistEntry(pmId, pid){
  var pm=state.player_matches[pmId]; if(!pm){alert('Not found');return;}
  var modal=document.getElementById('editHistModal');
  if(!modal){
    var div=document.createElement('div');
    div.id='editHistModal'; div.className='moverlay';
    div.innerHTML='<div class="modal" style="width:min(92vw,460px)"><div id="editHistContent"></div></div>';
    document.body.appendChild(div); modal=div;
  }
  var resOpts=['W','D','L'].map(function(v){
    return '<option value="'+v+'"'+(pm.result===v?' selected':'')+'>'+{W:'Win',D:'Draw',L:'Loss'}[v]+'</option>';
  }).join('');
  document.getElementById('editHistContent').innerHTML=
    '<div style="display:flex;align-items:center;gap:.7rem;margin-bottom:1rem;">'
    +'<div style="font-family:Bebas Neue,sans-serif;font-size:1.4rem;color:var(--green);flex:1;">Edit History</div>'
    +'<button id="ehCloseX" style="background:none;border:none;color:var(--muted);font-size:1.4rem;cursor:pointer;">&#10005;</button></div>'
    +'<div style="background:rgba(255,214,0,.05);border:1px solid rgba(255,214,0,.15);border-radius:8px;padding:.45rem .7rem;margin-bottom:.7rem;font-size:.71rem;color:var(--muted);">'
    +'Opponent entry will be synced automatically.</div>'
    +'<div class="fgrid">'
    +'<div class="fg"><label>Result</label><select id="eh_res">'+resOpts+'</select></div>'
    +'<div class="fg"><label>My Score</label><input id="eh_ms" type="number" value="'+(pm.myScore||0)+'" min="0"></div>'
    +'<div class="fg"><label>Opp Score</label><input id="eh_os" type="number" value="'+(pm.oppScore||0)+'" min="0"></div>'
    +'<div class="fg"><label>Opp Team</label><input id="eh_ot" value="'+esc(pm.opponentTeam||'')+'"></div>'
    +'<div class="fg"><label>Opp Player</label><input id="eh_on" value="'+esc(pm.opponentName||'')+'"></div>'
    +'<div class="fg"><label>Round</label><input id="eh_rnd" value="'+esc(pm.round||'')+'"></div>'
    +'</div>'
    +'<div style="display:flex;align-items:center;gap:.5rem;margin:.6rem 0;">'
    +'<input type="checkbox" id="eh_motm"'+(pm.motm?' checked':'')+' style="width:15px;height:15px;">'
    +'<label for="eh_motm" style="font-size:.78rem;">MOTM</label></div>'
    +'<div style="display:flex;gap:.6rem;flex-wrap:wrap;">'
    +'<button id="ehSaveBtn" class="btn bg">Save</button>'
    +'<button id="ehCancelBtn" class="btn" style="background:var(--border);color:var(--text);">Cancel</button>'
    +'</div>';

  document.getElementById('ehCloseX').onclick    = function(){ closeModal('editHistModal'); };
  document.getElementById('ehCancelBtn').onclick = function(){ closeModal('editHistModal'); };
  document.getElementById('ehSaveBtn').onclick   = function(){ saveHistEntryWithSync(pmId,pid); };
  modal.classList.remove('hidden');
}

async function saveHistEntryWithSync(pmId,pid){
  var pm=state.player_matches[pmId]; if(!pm) return;
  var newR=document.getElementById('eh_res').value;
  var newMS=parseInt(document.getElementById('eh_ms').value)||0;
  var newOS=parseInt(document.getElementById('eh_os').value)||0;
  var newOT=document.getElementById('eh_ot').value.trim();
  var newON=document.getElementById('eh_on').value.trim();
  var newRnd=document.getElementById('eh_rnd').value.trim();
  var newMotm=document.getElementById('eh_motm').checked;
  await fsSet('player_matches',pmId,Object.assign({},pm,{result:newR,myScore:newMS,oppScore:newOS,opponentTeam:newOT,opponentName:newON,round:newRnd,motm:newMotm}));
  var oppR=newR==='W'?'L':newR==='L'?'W':'D';
  var oppEntries=Object.values(state.player_matches).filter(function(x){
    return x.id!==pmId&&x.fixtureId===pm.fixtureId&&x.opponentName===pm.playerName;
  });
  for(var oe of oppEntries){
    await fsSet('player_matches',oe.id,Object.assign({},oe,{result:oppR,myScore:newOS,oppScore:newMS,opponentTeam:pm.playerTeam||'',opponentName:pm.playerName||'',round:newRnd,motm:false}));
  }
  closeModal('editHistModal');
  renderEditPlayerHistory(pid);
  alert('Updated'+(oppEntries.length?' + opponent synced':'')+'!');
}


async function saveEditPlayer(pid){
  var name=document.getElementById('ep_name').value.trim();
  if(!name){alert('Name required!');return;}
  var newPhoto=await resolvePhotoUrl('ep_ph');
  var existing=findPlayerInState(pid);
  var photoUrl=newPhoto||(existing?existing.photoUrl||'':'');
  var bid=parseInt(document.getElementById('ep_bid').value)||0;
  await fsSet('players',pid,{
    id:pid, name:name,
    teamId:document.getElementById('ep_team').value,
    cat:document.getElementById('ep_cat').value,
    photoUrl:photoUrl, bid:bid
  });
  closeModal('editPlayerModal');
  alert('Player updated!');
}


// ════════════════════════════════════════════
// ADMIN — FIXTURES
// ════════════════════════════════════════════
function aFixturesHTML(){
  var teams=getTeams(); var fxs=getFixtures();
  var teamOpts=teams.map(function(t){return '<option value="'+t.id+'">'+esc(t.name)+'</option>';}).join('');
  return '<div class="apanel"><h3>➕ Create Fixture</h3>'+
    '<div class="fgrid">'+
    '<div class="fg"><label>Home Team</label><select id="nf_h">'+teamOpts+'</select></div>'+
    '<div class="fg"><label>Away Team</label><select id="nf_a">'+teamOpts+'</select></div>'+
    '<div class="fg"><label>Date</label><input id="nf_d" type="date"></div>'+
    '<div class="fg"><label>Venue</label><input id="nf_v" placeholder="Main Stadium"></div>'+
    '<div class="fg"><label>Round</label><input id="nf_r" placeholder="Round 1"></div>'+
    '<div class="fg"><label>Status</label><select id="nf_st"><option value="upcoming">Upcoming</option><option value="live">Live</option><option value="played">Played</option></select></div>'+
    '<div class="fg"><label>Home Score</label><input id="nf_hs" type="number" placeholder="0" min="0"></div>'+
    '<div class="fg"><label>Away Score</label><input id="nf_as" type="number" placeholder="0" min="0"></div>'+
    '</div><div style="margin-top:.9rem"><button class="btn bg" onclick="addFixtureAsync()">Create Fixture</button></div></div>'+
    '<div class="apanel"><h3>📋 All Fixtures ('+fxs.length+')</h3><div class="alist">'+fxs.map(function(f){
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

// ════════════════════════════════════════════
// ADMIN — MATCHES
// ════════════════════════════════════════════
function aMatchesHTML(){
  var fxs=getFixtures();
  var fxOpts=fxs.map(function(f){
    var ht=getTeamById(f.home), at=getTeamById(f.away);
    return '<option value="'+f.id+'">'+(ht?esc(ht.name):'?')+' vs '+(at?esc(at.name):'?')+' ('+esc(f.date||'')+')</option>';
  }).join('');
  var matchList=getMatches().map(function(m){
    var f=state.fixtures[m.fixtureId];
    var ht=f?getTeamById(f.home):null, at=f?getTeamById(f.away):null;
    return '<div class="aitem">'+
      '<div class="ai"><div class="an">'+(ht?esc(ht.name):'?')+' '+m.homeScore+' — '+m.awayScore+' '+(at?esc(at.name):'?')+'</div>'+
      '<div class="am">'+(m.round?esc(m.round)+' | ':'')+((m.homeResults?m.homeResults.length:0)+(m.awayResults?m.awayResults.length:0))+' players | MOTM:'+(m.motm?esc(m.motm):'—')+'</div></div>'+
      '<button class="btn" style="background:rgba(0,200,83,.1);color:var(--green);border:1px solid rgba(0,200,83,.3);font-size:.7rem;padding:.27rem .6rem" onclick="viewMatchInModal(\''+m.id+'\')">View</button>'+
      '<button class="bd" onclick="deleteMatchAsync(\''+m.id+'\')">Del</button></div>';
  }).join('');
  return '<div class="apanel"><h3>📋 Submit Match Result</h3>'+
    '<div style="background:rgba(0,200,83,.04);border:1px solid rgba(0,200,83,.15);border-radius:10px;padding:.75rem 1rem;margin-bottom:1rem;font-size:.75rem;color:var(--muted);line-height:1.7">'+
    '⚡ <strong style="color:var(--green)">Individual Duel System</strong> — Each player\'s W/D/L is from their own duel score, NOT the team result.<br>'+
    '🛠️ = team separator | 🆚 = score divider | 👑 = MOTM | ✈️ = away player marker</div>'+
    '<div class="fgrid" style="margin-bottom:.8rem"><div class="fg" style="grid-column:1/-1"><label>Select Fixture *</label><select id="mr_fx">'+fxOpts+'</select></div></div>'+
    '<div class="fg" style="margin-bottom:.8rem"><label>Paste Match Result Text</label>'+
    '<textarea id="mr_txt" style="background:var(--dark);border:1px solid var(--border);border-radius:8px;padding:.6rem .8rem;color:var(--text);font-family:monospace;font-size:.79rem;resize:vertical;min-height:180px;outline:none;width:100%;margin-top:.3rem" placeholder="Paste here…"></textarea></div>'+
    '<div style="display:flex;gap:.7rem;flex-wrap:wrap"><button class="btn bg" onclick="parseAndPreview()">🔍 Parse &amp; Preview</button></div></div>'+
    '<div class="apanel hidden" id="previewPanel"><h3>👁 Preview</h3><div id="previewContent"></div>'+
    '<div style="margin-top:1rem;display:flex;gap:.7rem;flex-wrap:wrap">'+
    '<button class="btn bg" onclick="submitMatchAsync()">✅ Submit</button>'+
    '<button class="btn" style="background:var(--border);color:var(--text)" onclick="document.getElementById(\'previewPanel\').classList.add(\'hidden\')">Cancel</button></div></div>'+
    '<div class="apanel hidden" id="viewPanel"><h3>📄 Match Detail</h3><div id="viewContent"></div>'+
    '<button class="btn" style="background:var(--border);color:var(--text);margin-top:.8rem" onclick="document.getElementById(\'viewPanel\').classList.add(\'hidden\')">Close</button></div>'+
    (matchList?'<div class="apanel"><h3>📂 Submitted Matches ('+getMatches().length+')</h3><div class="alist">'+matchList+'</div></div>':'');
}

// ════════════════════════════════════════════
// ADMIN — HISTORY (player_matches edit/delete)
// ════════════════════════════════════════════
function aHistoryHTML(){
  var players=getPlayers();
  var playerOpts='<option value="">— Select Player —</option>'+
    players.map(function(p){
      var t=getTeamById(p.teamId);
      return '<option value="'+p.id+'">'+esc(p.name)+(t?' ('+esc(t.name)+')':'')+'</option>';
    }).join('');

  return '<div class="apanel"><h3>📜 Player Match History — Edit / Delete</h3>'+
    '<div style="font-size:.73rem;color:var(--muted);margin-bottom:.9rem;background:rgba(255,61,61,.04);border:1px solid rgba(255,61,61,.15);border-radius:8px;padding:.5rem .8rem">'+
    '⚠️ Deleting a history entry here does <strong style="color:var(--red)">NOT</strong> revert player stats. Use "Matches → Del" to revert stats. This only cleans the history log.</div>'+
    '<div class="fg" style="margin-bottom:.9rem"><label>Select Player</label>'+
    '<select id="histPlayerSel" onchange="loadPlayerHistory()" style="background:var(--dark);border:1px solid var(--border);border-radius:8px;padding:.45rem .7rem;color:var(--text);font-family:\'Barlow\';font-size:.85rem;outline:none;width:100%;max-width:360px">'+
    playerOpts+'</select></div>'+
    '<div id="histEntries"></div></div>';
}

function loadPlayerHistory(){
  var pid=document.getElementById('histPlayerSel').value;
  var el=document.getElementById('histEntries'); if(!el) return;
  if(!pid){el.innerHTML='';return;}
  var history=getPlayerAllMatchHistory(pid);
  if(!history.length){el.innerHTML='<p style="color:var(--muted);font-size:.8rem">No history entries.</p>';return;}
  el.innerHTML='<div style="font-size:.72rem;color:var(--muted);margin-bottom:.5rem">'+history.length+' entries — most recent first</div>'+
    '<div class="alist">'+history.map(function(h){
      var rc=h.result==='W'?'var(--green)':h.result==='L'?'var(--red)':'var(--acc)';
      var rcBg=h.result==='W'?'rgba(0,200,83,.12)':h.result==='L'?'rgba(255,61,61,.12)':'rgba(255,214,0,.1)';
      var dt=h.timestamp?new Date(h.timestamp).toLocaleDateString('en-GB',{day:'2-digit',month:'short'}):'';
      return '<div class="aitem">'+
        '<div style="width:28px;height:28px;border-radius:50%;background:'+rcBg+';border:1px solid '+rc+';display:flex;align-items:center;justify-content:center;flex-shrink:0">'+
          '<span style="font-family:\'Barlow Condensed\';font-weight:900;font-size:.82rem;color:'+rc+'">'+h.result+'</span></div>'+
        '<div class="ai">'+
          '<div class="an">vs '+esc(h.opponentTeam||'?')+' · <span style="color:var(--muted);font-weight:500">'+esc(h.opponentName||'?')+'</span></div>'+
          '<div class="am">'+esc(h.round||'')+' | Score: '+h.myScore+' — '+h.oppScore+(h.motm?' | 👑 MOTM':'')+' | '+dt+'</div>'+
        '</div>'+
        '<button class="btn" style="background:rgba(255,214,0,.08);color:var(--acc);border:1px solid rgba(255,214,0,.2);font-size:.68rem;padding:.24rem .55rem" onclick="openEditHistEntry(\''+h.id+'\',\''+pid+'\')">✏️</button>'+
        '<button class="bd" onclick="deleteHistEntry(\''+h.id+'\',\''+pid+'\')">Del</button>'+
        '</div>';
    }).join('')+'</div>';
}

async function deleteHistEntry(pmId, pid){
  if(!confirm('Delete this history entry? Stats will NOT be reverted.')) return;
  await fsDel('player_matches', pmId);
  loadPlayerHistory();
  alert('✅ Entry deleted.');
}

function openEditHistEntry(pmId, pid){
  var pm=state.player_matches[pmId]; if(!pm){alert('Not found');return;}
  var modal=document.getElementById('editHistModal');
  if(!modal){
    var div=document.createElement('div'); div.id='editHistModal'; div.className='moverlay';
    div.innerHTML='<div class="modal" style="width:min(92vw,460px)"><div id="editHistContent"></div></div>';
    document.body.appendChild(div); modal=div;
  }
  document.getElementById('editHistContent').innerHTML=
    '<div style="display:flex;align-items:center;gap:.7rem;margin-bottom:1rem">'+
    '<div style="font-family:\'Bebas Neue\';font-size:1.5rem;color:var(--green);flex:1">✏️ Edit History</div>'+
    '<button onclick="closeModal(\'editHistModal\')" style="background:none;border:none;color:var(--muted);font-size:1.4rem;cursor:pointer">✕</button></div>'+
    '<div class="fgrid">'+
    '<div class="fg"><label>Result</label><select id="eh_res">'+
      '<option value="W"'+(pm.result==='W'?' selected':'')+'>Win (W)</option>'+
      '<option value="D"'+(pm.result==='D'?' selected':'')+'>Draw (D)</option>'+
      '<option value="L"'+(pm.result==='L'?' selected':'')+'>Loss (L)</option>'+
    '</select></div>'+
    '<div class="fg"><label>My Score</label><input id="eh_ms" type="number" value="'+(pm.myScore||0)+'" min="0"></div>'+
    '<div class="fg"><label>Opp Score</label><input id="eh_os" type="number" value="'+(pm.oppScore||0)+'" min="0"></div>'+
    '<div class="fg"><label>Opp Team</label><input id="eh_ot" value="'+esc(pm.opponentTeam||'')+'"></div>'+
    '<div class="fg"><label>Opp Name</label><input id="eh_on" value="'+esc(pm.opponentName||'')+'"></div>'+
    '<div class="fg"><label>Round</label><input id="eh_rnd" value="'+esc(pm.round||'')+'"></div>'+
    '</div>'+
    '<div style="margin-top:.7rem;display:flex;gap:.5rem">'+
    '<button class="btn bg" onclick="saveHistEntry(\''+pmId+'\',\''+pid+'\')">💾 Save</button>'+
    '<button class="btn" style="background:var(--border);color:var(--text)" onclick="closeModal(\'editHistModal\')">Cancel</button></div>';
  modal.classList.remove('hidden');
}

async function saveHistEntry(pmId, pid){
  var pm=state.player_matches[pmId]; if(!pm) return;
  var updated=Object.assign({},pm,{
    result:   document.getElementById('eh_res').value,
    myScore:  parseInt(document.getElementById('eh_ms').value)||0,
    oppScore: parseInt(document.getElementById('eh_os').value)||0,
    opponentTeam: document.getElementById('eh_ot').value.trim(),
    opponentName: document.getElementById('eh_on').value.trim(),
    round:    document.getElementById('eh_rnd').value.trim()
  });
  await fsSet('player_matches', pmId, updated);
  closeModal('editHistModal');
  loadPlayerHistory();
  alert('✅ Entry updated.');
}

// ════════════════════════════════════════════
// PARSE HELPERS
// ════════════════════════════════════════════
function cleanName(s){
  return String(s||'').replace(/[👑🔑✈️👤🔥🏆⭐⛔🛠️📌]/gu,'').replace(/@/g,'').replace(/\s+/g,' ').trim();
}
function fuzzyFind(name, players){
  if(!name||!players||!players.length) return null;
  var n=name.toLowerCase().replace(/[^a-z0-9 ]/g,'').trim();
  if(n.length<2) return null;
  var ex=players.find(function(p){return p.name.toLowerCase()===n;}); if(ex) return ex;
  var co=players.find(function(p){ var pn=p.name.toLowerCase(); return pn.indexOf(n)>=0||n.indexOf(pn)>=0; }); if(co) return co;
  var tokens=n.split(' ').filter(function(t){return t.length>=3;});
  for(var i=0;i<tokens.length;i++){
    var tm=players.find(function(p){return p.name.toLowerCase().indexOf(tokens[i])>=0;}); if(tm) return tm;
  }
  return null;
}
function fuzzyTeam(name){
  if(!name) return null;
  var n=name.toLowerCase().replace(/[^a-z0-9 ]/g,'').trim(); if(n.length<2) return null;
  return getTeams().find(function(t){
    var tn=t.name.toLowerCase().replace(/[^a-z0-9 ]/g,'');
    if(tn===n||tn.indexOf(n)>=0||n.indexOf(tn)>=0) return true;
    var abbr=t.name.toLowerCase().split(' ').map(function(w){return w[0]||'';}).join(''); return abbr===n;
  })||null;
}

// ════════════════════════════════════════════
// PARSE MATCH TEXT
// ════════════════════════════════════════════
function parseMatchText(text, fx){
  var ht=getTeamById(fx.home)||{id:fx.home,name:'Home'};
  var at=getTeamById(fx.away)||{id:fx.away,name:'Away'};
  var lines=text.split('\n').map(function(l){return l.trim();}).filter(Boolean);
  var homeResults=[], awayResults=[], unmatched=[];
  var motm=null, motmId=null, round='';
  var detHome=ht, detAway=at;
  var finalHomeScore=null, finalAwayScore=null;

  var rm=text.match(/ROUND\s+(\d+)/i); if(rm) round='Round '+rm[1];

  var teamLine=lines.find(function(l){return l.includes('🛠️');});
  if(teamLine){
    var tparts=teamLine.split('🛠️');
    var tA=fuzzyTeam(cleanName(tparts[0]||'')); var tB=fuzzyTeam(cleanName(tparts[1]||''));
    if(tA) detHome=tA; if(tB) detAway=tB;
  }

  var homePlayers=getPlayersByTeam(detHome.id);
  var awayPlayers=getPlayersByTeam(detAway.id);

  // POINTS block
  lines.forEach(function(l){
    var clean=l.replace(/[^\w\s:]/g,'').trim();
    var m2=clean.match(/^([A-Za-z][A-Za-z0-9\s]{0,30})\s*:\s*(\d+)\s*$/);
    if(m2){
      var nm=m2[1].trim(); var pts=parseInt(m2[2]);
      if(nm.length<2||pts<0) return;
      var matched=fuzzyTeam(nm);
      if(matched){
        if(matched.id===detHome.id) finalHomeScore=pts;
        else if(matched.id===detAway.id) finalAwayScore=pts;
      }
    }
  });

  // Player duel lines (contains 🆚)
  lines.forEach(function(line){
    if(!line.includes('🆚')) return;
    var parts=line.split('🆚'); if(parts.length<2) return;
    var lp=parts[0]; var rp=parts.slice(1).join('');
    var lsm=lp.match(/(\d+)\s*$/); var rsm=rp.match(/^\s*(\d+)/);
    var ls=lsm?parseInt(lsm[1]):0; var rs=rsm?parseInt(rsm[1]):0;
    var lName=cleanName(lp.replace(/\d+\s*$/,'')); var rName=cleanName(rp.replace(/^\s*\d+/,''));
    var lIsAway=lp.includes('✈️'); var rIsAway=rp.includes('✈️');
    var homeN,awayN,hS,aS;
    if(rIsAway&&!lIsAway){homeN=lName;awayN=rName;hS=ls;aS=rs;}
    else if(lIsAway&&!rIsAway){homeN=rName;awayN=lName;hS=rs;aS=ls;}
    else{homeN=lName;awayN=rName;hS=ls;aS=rs;}
    var hMOTM=(lIsAway?rp:lp).includes('👑'); var aMOTM=(lIsAway?lp:rp).includes('👑');

    // individual result based purely on this duel
    var homeIndivResult = hS > aS ? 'W' : hS < aS ? 'L' : 'D';
    var awayIndivResult = aS > hS ? 'W' : aS < hS ? 'L' : 'D';

    if(homeN&&homeN.length>1){
      var hMatch=fuzzyFind(homeN,homePlayers);
      if(hMatch){
        homeResults.push({playerId:hMatch.id,playerName:hMatch.name,rawName:homeN,myScore:hS,oppScore:aS,individualResult:homeIndivResult,isMOTM:hMOTM,matched:true});
        if(hMOTM&&!motmId){motm=hMatch.name;motmId=hMatch.id;}
      } else {
        homeResults.push({playerId:null,playerName:homeN,rawName:homeN,myScore:hS,oppScore:aS,individualResult:homeIndivResult,isMOTM:false,matched:false});
        unmatched.push({rawName:homeN,side:'home',ts:hS,os:aS,resultIdx:homeResults.length-1,inHome:true});
      }
    }
    if(awayN&&awayN.length>1){
      var aMatch=fuzzyFind(awayN,awayPlayers);
      if(aMatch){
        awayResults.push({playerId:aMatch.id,playerName:aMatch.name,rawName:awayN,myScore:aS,oppScore:hS,individualResult:awayIndivResult,isMOTM:aMOTM,matched:true});
        if(aMOTM&&!motmId){motm=aMatch.name;motmId=aMatch.id;}
      } else {
        awayResults.push({playerId:null,playerName:awayN,rawName:awayN,myScore:aS,oppScore:hS,individualResult:awayIndivResult,isMOTM:false,matched:false});
        unmatched.push({rawName:awayN,side:'away',ts:aS,os:hS,resultIdx:awayResults.length-1,inHome:false});
      }
    }
  });

  var totalH=finalHomeScore!==null?finalHomeScore:homeResults.reduce(function(s,r){return s+(r.myScore||0);},0);
  var totalA=finalAwayScore!==null?finalAwayScore:awayResults.reduce(function(s,r){return s+(r.myScore||0);},0);

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
  if(!text){alert('Paste match text first!');return;}
  if(!fxId){alert('Select a fixture!');return;}
  var fx=state.fixtures[fxId]; if(!fx){alert('Fixture not found!');return;}
  try{
    var r=parseMatchText(text,fx);
    pendingMatch=Object.assign({},r,{fixtureId:fxId});
    var ht=getTeamById(r.detHomeId), at=getTeamById(r.detAwayId);
    var htName=ht?ht.name:r.detHomeName, atName=at?at.name:r.detAwayName;
    var html='';

    var hRes=r.homeScore>r.awayScore?'WIN':r.homeScore===r.awayScore?'DRAW':'LOSS';
    var aRes=r.awayScore>r.homeScore?'WIN':r.homeScore===r.awayScore?'DRAW':'LOSS';
    var hCol=hRes==='WIN'?'var(--green)':hRes==='DRAW'?'var(--acc)':'var(--red)';
    var aCol=aRes==='WIN'?'var(--green)':aRes==='DRAW'?'var(--acc)':'var(--red)';
    html+='<div style="background:var(--card2);border-radius:14px;padding:1.2rem;margin-bottom:1rem;text-align:center;border:1px solid var(--border)">'+
      '<div style="display:flex;align-items:center;justify-content:center;gap:1.5rem;flex-wrap:wrap">'+
      '<div style="text-align:center;min-width:100px">'+teamLogoEl(ht||{},44)+'<div style="font-weight:700;font-size:.88rem;margin-top:.3rem">'+esc(htName)+'</div>'+
      '<div style="font-family:\'Bebas Neue\';font-size:2.2rem;color:'+hCol+'">'+r.homeScore+'</div>'+
      '<div style="font-size:.65rem;color:'+hCol+';font-weight:700">'+hRes+' (team total)</div></div>'+
      '<div style="font-family:\'Bebas Neue\';font-size:1.2rem;color:var(--muted)">SCORE</div>'+
      '<div style="text-align:center;min-width:100px">'+teamLogoEl(at||{},44)+'<div style="font-weight:700;font-size:.88rem;margin-top:.3rem">'+esc(atName)+'</div>'+
      '<div style="font-family:\'Bebas Neue\';font-size:2.2rem;color:'+aCol+'">'+r.awayScore+'</div>'+
      '<div style="font-size:.65rem;color:'+aCol+';font-weight:700">'+aRes+' (team total)</div></div>'+
      '</div>'+
      (r.motm?'<div style="font-size:.8rem;color:var(--acc);margin-top:.5rem">👑 MOTM: <strong>'+esc(r.motm)+'</strong></div>':'')+
      '<div style="font-size:.7rem;color:var(--muted);margin-top:.6rem;background:rgba(0,200,83,.06);border-radius:8px;padding:.4rem .7rem;display:inline-block">'+
      '⚡ Each player\'s W/D/L = their OWN duel result — not team result</div>'+
      '</div>';

    var matchedCount=r.homeResults.filter(function(x){return x.matched;}).length+r.awayResults.filter(function(x){return x.matched;}).length;
    var totalCount=r.homeResults.length+r.awayResults.length;
    html+='<div style="display:flex;gap:.5rem;flex-wrap:wrap;margin-bottom:.8rem">'+
      '<span style="font-size:.72rem;background:rgba(0,200,83,.1);color:var(--green);border:1px solid rgba(0,200,83,.3);padding:.2rem .6rem;border-radius:10px">✅ '+matchedCount+'/'+totalCount+' matched</span>'+
      (r.unmatched.length?'<span style="font-size:.72rem;background:rgba(255,61,61,.1);color:var(--red);border:1px solid rgba(255,61,61,.3);padding:.2rem .6rem;border-radius:10px">⚠️ '+r.unmatched.length+' unmatched</span>':'')+
      '</div>';
    html+='<div style="margin:.4rem 0 .2rem;font-family:\'Barlow Condensed\';font-size:.8rem;font-weight:700;color:var(--muted);text-transform:uppercase;padding:0 .7rem;display:grid;grid-template-columns:1fr auto 1fr">'+
      '<div style="text-align:right">'+esc(htName)+'</div><div></div><div>'+esc(atName)+'</div></div>';
    html+=buildScorecardTable(r.homeResults,r.awayResults,r);

    if(r.unmatched.length){
      html+='<div style="background:rgba(255,214,0,.04);border:1px solid rgba(255,214,0,.2);border-radius:12px;padding:1rem;margin-top:.8rem">'+
        '<div style="color:var(--acc);font-weight:700;font-size:.82rem;margin-bottom:.6rem">⚠️ Fix Unmatched Players</div>';
      r.unmatched.forEach(function(u,i){
        var sideTeam=u.side==='home'?getTeamById(r.detHomeId):getTeamById(r.detAwayId);
        var sidePlayers=sideTeam?getPlayersByTeam(sideTeam.id):[];
        var opts='<option value="">❌ Skip</option>'+sidePlayers.map(function(p){return '<option value="'+p.id+'">'+esc(p.name)+'</option>';}).join('');
        html+='<div style="display:flex;align-items:center;gap:.6rem;flex-wrap:wrap;padding:.4rem 0;border-bottom:1px solid var(--border);font-size:.8rem">'+
          '<div style="min-width:130px"><span style="color:var(--red)">❓</span> <strong>'+esc(u.rawName)+'</strong></div>'+
          '<span style="color:var(--muted);font-size:.7rem">'+(u.side==='home'?esc(htName):esc(atName))+' | '+u.ts+' — '+u.os+'</span>'+
          '<select id="um_'+i+'" style="flex:1;min-width:140px;background:var(--dark);border:1px solid var(--border);border-radius:6px;padding:.3rem .5rem;color:var(--text);font-size:.78rem">'+opts+'</select>'+
          '</div>';
      });
      html+='</div>';
    }
    document.getElementById('previewContent').innerHTML=html;
    document.getElementById('previewPanel').classList.remove('hidden');
    setTimeout(function(){document.getElementById('previewPanel').scrollIntoView({behavior:'smooth',block:'start'});},50);
  }catch(e){ console.error('Parse error:',e); alert('Parse error: '+e.message); }
}

function buildScorecardTable(homeResults,awayResults,fullR){
  var maxRows=Math.max(homeResults.length,awayResults.length);
  if(!maxRows) return '';
  var html='<div style="border:1px solid var(--border);border-radius:12px;overflow:hidden;margin:.8rem 0">';
  for(var i=0;i<maxRows;i++){
    var hr=homeResults[i]||null, ar=awayResults[i]||null;
    var hp=hr?findPlayerInState(hr.playerId):null, ap=ar?findPlayerInState(ar.playerId):null;
    var hMOTM=hr&&fullR&&fullR.motmId&&hr.playerId===fullR.motmId;
    var aMOTM=ar&&fullR&&fullR.motmId&&ar.playerId===fullR.motmId;
    var hScore=hr?hr.myScore:'-', aScore=ar?ar.myScore:'-';
    var hWinsDuel=hr&&ar&&(+hr.myScore>+ar.myScore);
    var aWinsDuel=hr&&ar&&(+ar.myScore>+hr.myScore);
    var hScoreCol=hWinsDuel?'var(--green)':aWinsDuel?'var(--red)':'var(--acc)';
    var aScoreCol=aWinsDuel?'var(--green)':hWinsDuel?'var(--red)':'var(--acc)';
    var hResHtml='',aResHtml='';
    if(hr){
      var rc=hr.individualResult==='W'?'#00C853':hr.individualResult==='D'?'#FFD600':'#FF3D3D';
      hResHtml='<div style="width:16px;height:16px;border-radius:50%;background:'+rc+';display:flex;align-items:center;justify-content:center;flex-shrink:0"><span style="font-size:8px;color:#000;font-weight:900">'+hr.individualResult+'</span></div>';
    }
    if(ar){
      var rc2=ar.individualResult==='W'?'#00C853':ar.individualResult==='D'?'#FFD600':'#FF3D3D';
      aResHtml='<div style="width:16px;height:16px;border-radius:50%;background:'+rc2+';display:flex;align-items:center;justify-content:center;flex-shrink:0"><span style="font-size:8px;color:#000;font-weight:900">'+ar.individualResult+'</span></div>';
    }
    var rowBg=i%2===0?'background:rgba(255,255,255,.015)':'';
    html+='<div style="display:grid;grid-template-columns:1fr auto 1fr;align-items:center;padding:.45rem .7rem;border-bottom:1px solid var(--border);'+rowBg+'">';
    html+='<div style="display:flex;align-items:center;gap:.3rem;justify-content:flex-end">';
    if(hr){ if(hMOTM) html+='<span style="font-size:.55rem;color:var(--acc);font-weight:900">MOTM</span>'; html+=hResHtml; html+='<span style="font-size:.84rem;font-weight:'+(hWinsDuel?'700':'500')+';color:'+(hWinsDuel?'var(--text)':'var(--muted)')+'">'+esc(hr.playerName||hr.rawName||'?')+'</span>'; if(!hp) html+='<span style="font-size:.55rem;color:var(--red)">?</span>'; html+=playerPhotoEl(hp,22); }
    else html+='<span style="color:var(--muted);font-size:.78rem">—</span>';
    html+='</div>';
    html+='<div style="text-align:center;padding:0 .6rem;white-space:nowrap">'+
      '<span style="font-family:\'Bebas Neue\';font-size:1.3rem;color:'+hScoreCol+'">'+hScore+'</span>'+
      '<span style="font-family:\'Bebas Neue\';font-size:.9rem;color:var(--muted);margin:0 .15rem">vs</span>'+
      '<span style="font-family:\'Bebas Neue\';font-size:1.3rem;color:'+aScoreCol+'">'+aScore+'</span>'+
      '</div>';
    html+='<div style="display:flex;align-items:center;gap:.3rem">';
    if(ar){ html+=playerPhotoEl(ap,22); html+='<span style="font-size:.84rem;font-weight:'+(aWinsDuel?'700':'500')+';color:'+(aWinsDuel?'var(--text)':'var(--muted)')+'">'+esc(ar.playerName||ar.rawName||'?')+'</span>'; if(!ap) html+='<span style="font-size:.55rem;color:var(--red)">?</span>'; html+=aResHtml; if(aMOTM) html+='<span style="font-size:.55rem;color:var(--acc);font-weight:900">MOTM</span>'; }
    else html+='<span style="color:var(--muted);font-size:.78rem">—</span>';
    html+='</div></div>';
  }
  return html+'</div>';
}

// ════════════════════════════════════════════
// ✅ FIXED submitMatchAsync
// KEY FIX: Build a playerStats MAP first, update it in memory,
// then write ALL players at once. This prevents stale reads
// where the second applyStats call reads un-updated Firebase
// values and double-counts.
// ════════════════════════════════════════════
async function submitMatchAsync(){
  if(!pendingMatch){alert('Nothing to submit!');return;}
  var m=pendingMatch;
  var fx=state.fixtures[m.fixtureId];

  // Resolve unmatched selections first
  if(m.unmatched){
    m.unmatched.forEach(function(u,i){
      var sel=document.getElementById('um_'+i); if(!sel||!sel.value) return;
      var found=findPlayerInState(sel.value); if(!found) return;
      var indivRes=(+u.ts>+u.os)?'W':(+u.ts===+u.os)?'D':'L';
      var entry={playerId:found.id,playerName:found.name,myScore:u.ts,oppScore:u.os,individualResult:indivRes,isMOTM:false,matched:true};
      if(u.side==='home') m.homeResults.push(entry);
      else m.awayResults.push(entry);
    });
  }

  // Update fixture score
  if(fx){
    await fsSet('fixtures',m.fixtureId,Object.assign({},fx,{
      homeScore:m.homeScore,awayScore:m.awayScore,
      status:'played',round:m.round||fx.round
    }));
  }

  var htName=m.detHomeName, atName=m.detAwayName;

  // ══════════════════════════════════════════════════════════
  // BUILD IN-MEMORY STATS MAP — load current stats for every
  // player involved ONCE, then update in memory, write once.
  // This is the core fix — no stale reads between home/away.
  // ══════════════════════════════════════════════════════════
  var statsMap = {}; // pid -> stats object

  function getOrInitStat(pid){
    if(!statsMap[pid]){
      // Clone from current state (already in memory from Firebase listener)
      var existing = state.stats[pid] || {};
      statsMap[pid] = {
        wins:   existing.wins   || 0,
        losses: existing.losses || 0,
        draws:  existing.draws  || 0,
        goals:  existing.goals  || 0,
        cs:     existing.cs     || 0,
        motm:   existing.motm   || 0,
        mp:     existing.mp     || 0,
        gf:     existing.gf     || 0,
        ga:     existing.ga     || 0
      };
    }
    return statsMap[pid];
  }

  // Process all results — home and away — in ONE pass
  // Each entry has its own individualResult already set correctly.
  function processResult(r, oppEntry, playerTeamName, oppTeamName){
    if(!r.playerId) return null; // unmatched, skip

    var s = getOrInitStat(r.playerId);

    // +1 match played
    s.mp += 1;

    // W / D / L — purely from individual duel result
    var res = r.individualResult || 'D';
    if      (res === 'W') s.wins   += 1;
    else if (res === 'L') s.losses += 1;
    else                  s.draws  += 1;

    // Goals scored = this player's duel score
    s.goals += (r.myScore || 0);
    s.gf    += (r.myScore || 0);

    // Goals against = opponent's score in THIS duel
    var oppScore = oppEntry ? (+oppEntry.myScore || 0) : 0;
    s.ga += oppScore;

    // Clean sheet: won duel AND opponent scored 0
    if(res === 'W' && oppScore === 0) s.cs += 1;

    // MOTM
    if(r.isMOTM || (m.motmId && r.playerId === m.motmId)) s.motm += 1;

    // Return the history entry to save separately
    return {
      playerTeamName: playerTeamName,
      oppTeamName:    oppTeamName,
      oppEntry:       oppEntry,
      res:            res
    };
  }

  // Collect all player_matches entries to write
  var playerMatchEntries = [];

  // Process home players
  m.homeResults.forEach(function(r, idx){
    var oppEntry = m.awayResults[idx] || null; // matched by duel position
    var meta = processResult(r, oppEntry, htName, atName);
    if(!meta) return;
    var oppPlayer = oppEntry ? findPlayerInState(oppEntry.playerId) : null;
    playerMatchEntries.push({
      playerId:     r.playerId,
      playerName:   r.playerName || '',
      playerTeam:   htName,
      opponentTeam: atName,
      opponentName: oppPlayer ? oppPlayer.name : (oppEntry ? oppEntry.playerName || oppEntry.rawName || '?' : '?'),
      result:       meta.res,
      myScore:      r.myScore  || 0,
      oppScore:     oppEntry ? (+oppEntry.myScore || 0) : 0,
      motm:         !!(r.isMOTM || (m.motmId && r.playerId === m.motmId)),
      round:        m.round || '',
      fixtureId:    m.fixtureId,
      timestamp:    Date.now()
    });
  });

  // Process away players
  m.awayResults.forEach(function(r, idx){
    var oppEntry = m.homeResults[idx] || null;
    var meta = processResult(r, oppEntry, atName, htName);
    if(!meta) return;
    var oppPlayer = oppEntry ? findPlayerInState(oppEntry.playerId) : null;
    playerMatchEntries.push({
      playerId:     r.playerId,
      playerName:   r.playerName || '',
      playerTeam:   atName,
      opponentTeam: htName,
      opponentName: oppPlayer ? oppPlayer.name : (oppEntry ? oppEntry.playerName || oppEntry.rawName || '?' : '?'),
      result:       meta.res,
      myScore:      r.myScore  || 0,
      oppScore:     oppEntry ? (+oppEntry.myScore || 0) : 0,
      motm:         !!(r.isMOTM || (m.motmId && r.playerId === m.motmId)),
      round:        m.round || '',
      fixtureId:    m.fixtureId,
      timestamp:    Date.now()
    });
  });

  // ── Write all stats to Firebase (one write per player) ──
  for(var pid in statsMap){
    await fsSet('stats', pid, statsMap[pid]);
  }

  // ── Write all player match history entries ──
  for(var i=0; i<playerMatchEntries.length; i++){
    var pmId = uid();
    var entry = Object.assign({id: pmId}, playerMatchEntries[i]);
    await fsSet('player_matches', pmId, entry);
  }

  // ── Save match record ──
  var mid=uid();
  await fsSet('matches',mid,{
    id:mid,fixtureId:m.fixtureId,
    homeScore:m.homeScore,awayScore:m.awayScore,
    homeResults:m.homeResults,awayResults:m.awayResults,
    motm:m.motm||null,motmId:m.motmId||null,
    round:m.round||null,
    detHomeId:m.detHomeId,detAwayId:m.detAwayId,
    detHomeName:m.detHomeName,detAwayName:m.detAwayName
  });

  pendingMatch=null;
  document.getElementById('previewPanel').classList.add('hidden');
  document.getElementById('mr_txt').value='';
  alert('✅ Match submitted! Each player: exactly 1 MP, 1 W/D/L from their own duel only.');
}

// ════════════════════════════════════════════
// ✅ FIXED deleteMatchAsync — mirrors the statsMap approach
// ════════════════════════════════════════════
async function deleteMatchAsync(mid){
  if(!confirm('Delete match? This will REVERT all stats.')) return;
  var m=state.matches[mid];
  if(!m){ await fsDel('matches',mid); return; }

  // Build revert map in memory
  var revertMap = {};

  function getOrInitRevert(pid){
    if(!revertMap[pid]){
      var existing = state.stats[pid] || {};
      revertMap[pid] = {
        wins:   existing.wins   || 0,
        losses: existing.losses || 0,
        draws:  existing.draws  || 0,
        goals:  existing.goals  || 0,
        cs:     existing.cs     || 0,
        motm:   existing.motm   || 0,
        mp:     existing.mp     || 0,
        gf:     existing.gf     || 0,
        ga:     existing.ga     || 0
      };
    }
    return revertMap[pid];
  }

  function revertEntry(r, oppEntry){
    if(!r.playerId) return;
    var s = getOrInitRevert(r.playerId);

    s.mp = Math.max(0, s.mp - 1);

    var res = r.individualResult || 'D';
    if      (res === 'W') s.wins   = Math.max(0, s.wins   - 1);
    else if (res === 'L') s.losses = Math.max(0, s.losses - 1);
    else                  s.draws  = Math.max(0, s.draws  - 1);

    var myGoals = r.myScore || 0;
    s.goals = Math.max(0, s.goals - myGoals);
    s.gf    = Math.max(0, s.gf    - myGoals);

    var oppScore = oppEntry ? (+oppEntry.myScore || 0) : 0;
    s.ga = Math.max(0, s.ga - oppScore);

    if(res === 'W' && oppScore === 0) s.cs = Math.max(0, s.cs - 1);

    if(r.isMOTM || (m.motmId && r.playerId === m.motmId)) s.motm = Math.max(0, s.motm - 1);
  }

  (m.homeResults||[]).forEach(function(r, idx){ revertEntry(r, (m.awayResults||[])[idx]||null); });
  (m.awayResults||[]).forEach(function(r, idx){ revertEntry(r, (m.homeResults||[])[idx]||null); });

  // Write all reverted stats
  for(var pid in revertMap){
    await fsSet('stats', pid, revertMap[pid]);
  }

  // Delete player_matches for this fixture
  var F=fb();
  if(F){
    try{
      var pmSnap=await F.getDocs(F.collection(F.db,'player_matches'));
      var pmBatch=F.writeBatch(F.db); var pmc=0;
      pmSnap.forEach(function(d){
        var pm=d.data();
        if(pm.fixtureId===m.fixtureId){ pmBatch.delete(F.doc(F.db,'player_matches',d.id)); pmc++; }
      });
      if(pmc>0) await pmBatch.commit();
    }catch(e){console.warn('player_matches cleanup:',e);}
  }

  await fsDel('matches',mid);
  alert('✅ Match deleted and all stats reverted.');
}

async function deleteFixtureAsync(fid){
  if(!confirm('Delete fixture?')) return;
  var related=getMatches().filter(function(m){return m.fixtureId===fid;});
  for(var m of related){ await deleteMatchAsync(m.id); }
  await fsDel('fixtures',fid);
}

// ════════════════════════════════════════════
// VIEW MATCH IN MODAL
// ════════════════════════════════════════════
function viewMatchInModal(mid){
  var m=state.matches[mid]; if(!m) return;
  var ht=getTeamById(m.detHomeId), at=getTeamById(m.detAwayId);
  var htName=ht?ht.name:(m.detHomeName||'Home'), atName=at?at.name:(m.detAwayName||'Away');
  var hRes=m.homeScore>m.awayScore?'WIN':m.homeScore===m.awayScore?'DRAW':'LOSS';
  var aRes=m.awayScore>m.homeScore?'WIN':m.homeScore===m.awayScore?'DRAW':'LOSS';
  var hCol=hRes==='WIN'?'var(--green)':hRes==='DRAW'?'var(--acc)':'var(--red)';
  var aCol=aRes==='WIN'?'var(--green)':aRes==='DRAW'?'var(--acc)':'var(--red)';
  document.getElementById('matchDetailContent').innerHTML=
    '<div style="display:flex;align-items:center;gap:.7rem;margin-bottom:1rem">'+
    '<div style="font-family:\'Bebas Neue\';font-size:1.6rem;color:var(--green);flex:1">Match Detail</div>'+
    '<button onclick="closeModal(\'matchDetailModal\')" style="background:none;border:none;color:var(--muted);font-size:1.5rem;cursor:pointer">✕</button></div>'+
    '<div style="background:var(--card2);border-radius:12px;padding:1rem;margin-bottom:.8rem;border:1px solid var(--border)">'+
    '<div style="display:flex;align-items:center;justify-content:center;gap:1.5rem;flex-wrap:wrap;text-align:center">'+
    '<div>'+teamLogoEl(ht||{},40)+'<div style="font-weight:700;font-size:.85rem;margin-top:.25rem">'+esc(htName)+'</div>'+
    '<div style="font-family:\'Bebas Neue\';font-size:2rem;color:'+hCol+'">'+m.homeScore+'</div>'+
    '<div style="font-size:.62rem;color:'+hCol+';font-weight:700">'+hRes+'</div></div>'+
    '<div style="font-family:\'Bebas Neue\';font-size:1.2rem;color:var(--muted)">TOTAL SCORE</div>'+
    '<div>'+teamLogoEl(at||{},40)+'<div style="font-weight:700;font-size:.85rem;margin-top:.25rem">'+esc(atName)+'</div>'+
    '<div style="font-family:\'Bebas Neue\';font-size:2rem;color:'+aCol+'">'+m.awayScore+'</div>'+
    '<div style="font-size:.62rem;color:'+aCol+';font-weight:700">'+aRes+'</div></div>'+
    '</div>'+
    (m.round?'<div style="text-align:center;font-size:.74rem;color:var(--acc);margin-top:.4rem;font-weight:700">'+esc(m.round)+'</div>':'')+
    (m.motm?'<div style="text-align:center;font-size:.76rem;color:var(--acc);margin-top:.2rem">MOTM: <strong>'+esc(m.motm)+'</strong></div>':'')+
    '</div>'+
    '<div style="font-size:.72rem;color:var(--muted);margin-bottom:.4rem;font-style:italic;background:rgba(0,200,83,.04);border:1px solid rgba(0,200,83,.12);border-radius:8px;padding:.4rem .7rem">'+
    '⚡ Each player\'s W/D/L = their own duel result — separate from team score</div>'+
    '<div style="display:grid;grid-template-columns:1fr auto 1fr;padding:0 .7rem;margin-bottom:.2rem;font-family:\'Barlow Condensed\';font-size:.78rem;font-weight:700;color:var(--muted);text-transform:uppercase">'+
    '<div style="text-align:right">'+esc(htName)+'</div><div></div><div>'+esc(atName)+'</div></div>'+
    buildScorecardTable(m.homeResults||[],m.awayResults||[],m);
  if(isAdmin){
    var mc=document.getElementById('matchDetailContent');
    if(mc){
      var editDiv=document.createElement('div');
      editDiv.style.cssText='margin-top:.8rem;display:flex;gap:.6rem;flex-wrap:wrap;';
      editDiv.innerHTML='<button data-mid="'+mid+'" onclick="openEditMatchModal(this.dataset.mid)" style="background:rgba(255,214,0,.1);color:var(--acc);border:1px solid rgba(255,214,0,.25);border-radius:8px;padding:.45rem 1rem;font-family:Barlow Condensed,sans-serif;font-size:.82rem;font-weight:700;cursor:pointer;">Edit Match</button>';
      mc.appendChild(editDiv);
    }
  }
  document.getElementById('matchDetailModal').classList.remove('hidden');
  var vp=document.getElementById('viewPanel'); if(vp) vp.classList.add('hidden');
}
function viewMatch(mid){ viewMatchInModal(mid); }

function openEditMatchModal(mid){
  var m=state.matches[mid]; if(!m) return;
  var modal=document.getElementById('editMatchModal');
  if(!modal){
    var div=document.createElement('div'); div.id='editMatchModal'; div.className='moverlay';
    div.innerHTML='<div class="modal" style="width:min(92vw,400px)"><div id="editMatchContent"></div></div>';
    document.body.appendChild(div); modal=div;
  }
  document.getElementById('editMatchContent').innerHTML=
    '<div style="display:flex;align-items:center;gap:.7rem;margin-bottom:1rem;">'
    +'<div style="font-family:Bebas Neue,sans-serif;font-size:1.4rem;color:var(--green);flex:1;">Edit Match Score</div>'
    +'<button id="emCloseX" style="background:none;border:none;color:var(--muted);font-size:1.4rem;cursor:pointer;">&#10005;</button></div>'
    +'<div class="fgrid">'
    +'<div class="fg"><label>Home Score</label><input id="ematch_hs" type="number" value="'+m.homeScore+'" min="0"></div>'
    +'<div class="fg"><label>Away Score</label><input id="ematch_as" type="number" value="'+m.awayScore+'" min="0"></div>'
    +'<div class="fg" style="grid-column:1/-1;"><label>Round</label><input id="ematch_rnd" value="'+esc(m.round||'')+'"></div>'
    +'</div>'
    +'<div style="margin-top:.8rem;display:flex;gap:.6rem;flex-wrap:wrap;">'
    +'<button id="emSaveBtn" class="btn bg">Save</button>'
    +'<button id="emCancelBtn" class="btn" style="background:var(--border);color:var(--text);">Cancel</button>'
    +'</div>';
  document.getElementById('emCloseX').onclick    = function(){ closeModal('editMatchModal'); };
  document.getElementById('emCancelBtn').onclick = function(){ closeModal('editMatchModal'); };
  document.getElementById('emSaveBtn').onclick   = function(){ saveMatchEdit(mid); };
  modal.classList.remove('hidden');
}

async function saveMatchEdit(mid){
  var m=state.matches[mid]; if(!m) return;
  var hs=parseInt(document.getElementById('ematch_hs').value)||0;
  var as_=parseInt(document.getElementById('ematch_as').value)||0;
  var rnd=document.getElementById('ematch_rnd').value.trim();
  await fsSet('matches',mid,Object.assign({},m,{homeScore:hs,awayScore:as_,round:rnd}));
  if(m.fixtureId){
    var fx=state.fixtures[m.fixtureId];
    if(fx) await fsSet('fixtures',m.fixtureId,Object.assign({},fx,{homeScore:hs,awayScore:as_,round:rnd}));
  }
  closeModal('editMatchModal');
  closeModal('matchDetailModal');
  alert('Match updated!');
}

// ════════════════════════════════════════════
// ADMIN — STANDINGS
// ════════════════════════════════════════════
function aStandingsHTML(){
  var rows=calcStandings();
  var teams=getTeams();
  var teamOpts=teams.map(function(t){return '<option value="'+t.id+'">'+esc(t.name)+'</option>';}).join('');
  var topPlayers=getPlayers().slice().sort(function(a,b){return realCalcPts(computeStatsFromHistory(b.id))-realCalcPts(computeStatsFromHistory(a.id));}).slice(0,15);
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
    rows.map(function(r,i){
      var col=i===0?'var(--acc)':i<3?'var(--green)':'var(--muted)';
      var logo=r.logo; var logoH=logo&&logo.startsWith('http')?'<div class="mini-logo" style="width:26px;height:26px;"><img src="'+esc(logo)+'" onerror="this.parentNode.innerHTML=\'⚽\'"></div>':'<div class="mini-logo" style="width:26px;height:26px;font-size:.9rem;">'+(logo||'⚽')+'</div>';
      var hidden=state.manual_standings&&state.manual_standings[r.id]&&state.manual_standings[r.id].hidden;
      return '<div class="aitem"'+(hidden?' style="opacity:.45"':'')+'>'+
        '<div style="font-family:\'Bebas Neue\';font-size:1.3rem;width:24px;color:'+col+'">'+(hidden?'—':(i+1))+'</div>'+logoH+
        '<div class="ai"><div class="an">'+esc(r.name)+(hidden?' <span style="font-size:.62rem;color:var(--red)">[hidden]</span>':'')+'</div>'+
        '<div class="am">W'+r.w+' D'+r.d+' L'+r.l+' Pts:'+r.pts+' WR:'+r.wr+'%</div></div>'+
        '<div style="font-family:\'Bebas Neue\';font-size:1.7rem;color:var(--green)">'+r.pts+'</div>'+
        (hidden?'<button class="btn bg" onclick="restoreTeamInTable(\''+r.id+'\')" style="font-size:.68rem;padding:.24rem .5rem">Restore</button>':
          '<button class="bd" onclick="removeTeamFromTable(\''+r.id+'\')" style="font-size:.68rem;padding:.24rem .5rem">Hide</button>')+
        '</div>';
    }).join('')+'</div>'+
    '<div class="apanel"><h3>✏️ Manual Override</h3>'+
    '<div style="display:flex;flex-direction:column;gap:.5rem">'+manualRows+'</div></div>'+
    '<div class="apanel"><h3>➕ Manual Table Entry</h3>'+
    '<div class="fgrid">'+
    '<div class="fg"><label>Team</label><select id="mn_team">'+teamOpts+'</select></div>'+
    '<div class="fg"><label>W</label><input id="mn_w" type="number" value="0" min="0"></div>'+
    '<div class="fg"><label>D</label><input id="mn_d" type="number" value="0" min="0"></div>'+
    '<div class="fg"><label>L</label><input id="mn_l" type="number" value="0" min="0"></div>'+
    '<div class="fg"><label>GF</label><input id="mn_gf" type="number" value="0" min="0"></div>'+
    '<div class="fg"><label>GA</label><input id="mn_ga" type="number" value="0" min="0"></div>'+
    '</div><div style="margin-top:.8rem"><button class="btn bg" onclick="saveManualEntry()">Save</button></div></div>'+
    '<div class="apanel"><h3>🏆 Player Rankings (Individual Duel System)</h3>'+
    '<div style="font-size:.72rem;color:var(--muted);margin-bottom:.8rem;background:rgba(0,200,83,.04);border:1px solid rgba(0,200,83,.12);padding:.5rem .8rem;border-radius:8px">'+
    'Formula: <strong style="color:var(--green)">Win×10</strong> + <strong style="color:var(--acc)">Draw×5</strong> + <strong style="color:var(--red)">Loss×(−10)</strong> + <strong style="color:#7CB9FF">GF×1</strong> + <strong style="color:#FF8A50">GC×(−1)</strong> + <strong style="color:var(--acc)">MOTM×5</strong> + <strong style="color:var(--green)">CS×2</strong><br>'+
    'W/D/L = each player\'s own duel outcome — completely separate from team result</div>'+
    topPlayers.map(function(p,i){
      var s=computeStatsFromHistory(p.id); var t=getTeamById(p.teamId); var wr=winRatio(s);
      return '<div class="aitem">'+
        '<div style="font-family:\'Bebas Neue\';font-size:1.2rem;width:22px;color:'+(i===0?'var(--acc)':'var(--muted)')+'">'+(i+1)+'</div>'+
        playerPhotoEl(p,30)+
        '<div class="ai"><div class="an">'+esc(p.name)+' '+condBadge(wr)+'</div>'+
        '<div class="am">'+(t?esc(t.name):'')+
        ' <span style="color:var(--green)">W'+(s.wins||0)+'</span>'+
        ' <span style="color:var(--acc)">D'+(s.draws||0)+'</span>'+
        ' <span style="color:var(--red)">L'+(s.losses||0)+'</span>'+
        ' ⚽'+(s.goals||0)+' 👑'+(s.motm||0)+' WR:'+wr+'%</div></div>'+
        '<div style="font-family:\'Bebas Neue\';font-size:1.5rem;color:var(--green)">'+realCalcPts(s)+'</div></div>';
    }).join('')+'</div>';
}
async function saveManualStanding(tid){
  var w=parseInt(document.getElementById('mo_w_'+tid).value)||0;
  var d=parseInt(document.getElementById('mo_d_'+tid).value)||0;
  var l=parseInt(document.getElementById('mo_l_'+tid).value)||0;
  var gf=parseInt(document.getElementById('mo_gf_'+tid).value)||0;
  var ga=parseInt(document.getElementById('mo_ga_'+tid).value)||0;
  await fsSet('manual_standings',tid,{teamId:tid,w:w,d:d,l:l,gf:gf,ga:ga,updatedAt:Date.now()});
  alert('✅ Standing saved.');
}
async function saveManualEntry(){
  var tid=document.getElementById('mn_team').value; if(!tid){alert('Select a team!');return;}
  var w=parseInt(document.getElementById('mn_w').value)||0,d=parseInt(document.getElementById('mn_d').value)||0,l=parseInt(document.getElementById('mn_l').value)||0;
  var gf=parseInt(document.getElementById('mn_gf').value)||0,ga=parseInt(document.getElementById('mn_ga').value)||0;
  await fsSet('manual_standings',tid,{teamId:tid,w:w,d:d,l:l,gf:gf,ga:ga,updatedAt:Date.now()}); alert('✅ Entry saved!');
}
async function removeTeamFromTable(tid){
  var ex=Object.assign({},state.manual_standings&&state.manual_standings[tid]||{});
  ex.teamId=tid; ex.hidden=true; ex.updatedAt=Date.now(); await fsSet('manual_standings',tid,ex);
}
async function restoreTeamInTable(tid){
  var ex=Object.assign({},state.manual_standings&&state.manual_standings[tid]||{});
  ex.teamId=tid; ex.hidden=false; ex.updatedAt=Date.now(); await fsSet('manual_standings',tid,ex);
}

// ════════════════════════════════════════════
// ADMIN — NEWS
// ════════════════════════════════════════════
function aNewsHTML(){
  var newsItems=Object.values(state.news);
  return '<div class="apanel"><h3>➕ Add Manual News</h3>'+
    '<div class="fgrid">'+
    '<div class="fg"><label>Tag</label><select id="nn_tag">'+
      '<option value="Hot">🔥 Hot</option>'+
      '<option value="Result">⚽ Result</option>'+
      '<option value="Table">📊 Table</option>'+
      '<option value="Special">⭐ Special</option>'+
    '</select></div>'+
    '<div class="fg" style="grid-column:span 2"><label>Title *</label><input id="nn_title" placeholder="News headline…"></div>'+
    '<div class="fg" style="grid-column:span 3"><label>Body</label><input id="nn_body" placeholder="News details…"></div>'+
    '</div><div style="margin-top:.8rem"><button class="btn bg" onclick="addNewsAsync()">Add News</button></div></div>'+
    '<div class="apanel"><h3>📰 Manual News ('+newsItems.length+')</h3>'+
    '<div class="alist">'+newsItems.map(function(n){
      return '<div class="aitem"><div class="ai"><div class="an">'+esc(n.title)+'</div><div class="am">'+esc(n.tag||'')+'</div></div>'+
        '<button class="bd" onclick="deleteNewsAsync(\''+n.id+'\')">Delete</button></div>';
    }).join('')+'</div></div>'+
    '<div class="apanel"><h3>ℹ️ Auto-Generated News</h3>'+
    '<p style="font-size:.76rem;color:var(--muted)">The system auto-generates news from: match results, players with 7+ goals, ranking leaders, and table position.</p></div>';
}
async function addNewsAsync(){
  var title=document.getElementById('nn_title').value.trim(); if(!title){alert('Title required!');return;}
  var tag=document.getElementById('nn_tag').value;
  var tagClsMap={'Hot':'nc-hot','Result':'nc-result','Table':'nc-table','Special':'nc-special'};
  var id=uid();
  await fsSet('news',id,{id:id,title:title,body:document.getElementById('nn_body').value.trim(),tag:tag,tagCls:tagClsMap[tag]||'nc-result',hot:tag==='Hot',ts:'Manual',active:true,type:tag.toLowerCase()});
}
async function deleteNewsAsync(id){ await fsDel('news',id); }

// ════════════════════════════════════════════
// EDIT TEAM MODAL
// ════════════════════════════════════════════
function openEditTeam(tid){
  var t=getTeamById(tid); if(!t){alert('Team not found');return;}
  var modal=document.getElementById('editTeamModal');
  if(!modal){
    var div=document.createElement('div'); div.id='editTeamModal'; div.className='moverlay';
    div.innerHTML='<div class="modal" style="width:min(92vw,520px)"><div id="editTeamContent"></div></div>';
    document.body.appendChild(div); modal=div;
  }
  document.getElementById('editTeamContent').innerHTML=
    '<div style="display:flex;align-items:center;gap:.8rem;margin-bottom:1.2rem">'+
    '<div style="flex:1"><div style="font-family:\'Bebas Neue\';font-size:1.6rem;color:var(--green)">Edit Team</div></div>'+
    '<button onclick="closeModal(\'editTeamModal\')" style="background:none;border:none;color:var(--muted);font-size:1.5rem;cursor:pointer">✕</button></div>'+
    '<div class="fgrid" style="margin-bottom:1rem">'+
    '<div class="fg"><label>Team Name *</label><input id="et_name" value="'+esc(t.name)+'"></div>'+
    '<div class="fg"><label>President / Manager</label><input id="et_pres" value="'+esc(t.president||'')+'"></div>'+
    '<div class="fg"><label>Team Color</label><input id="et_color" type="color" value="'+(t.color||'#00C853')+'"></div>'+
    '<div class="fg"><label style="color:var(--blue)">Captain Password</label><input id="et_cappwd" type="text" value="'+esc(t.capPassword||'')+'" placeholder="Set team login password…" autocomplete="off"></div>'+
    '</div>'+
    buildLogoInput('et_lg','Team Logo')+
    '<div style="display:flex;gap:.7rem;flex-wrap:wrap;margin-top:1rem">'+
    '<button class="btn bg" onclick="saveEditTeam(\''+tid+'\')">💾 Save</button>'+
    '<button class="btn" style="background:var(--border);color:var(--text)" onclick="closeModal(\'editTeamModal\')">Cancel</button></div>';
  modal.classList.remove('hidden');
}
async function saveEditTeam(tid){
  var name=document.getElementById('et_name').value.trim(); if(!name){alert('Name required!');return;}
  var newLogoUrl=await resolveLogoUrl('et_lg');
  var t=getTeamById(tid)||{};
  var logoUrl=newLogoUrl||(t.logoUrl||t.logo||'⚽');
  var capPwd=document.getElementById('et_cappwd').value.trim();
  await fsSet('teams',tid,Object.assign({},t,{id:tid,name:name,president:document.getElementById('et_pres').value.trim(),color:document.getElementById('et_color').value,logoUrl:logoUrl,capPassword:capPwd||t.capPassword||''}));
  closeModal('editTeamModal'); alert('✅ Team updated!');
}

// ════════════════════════════════════════════
// CAPTAIN DASHBOARD
// ════════════════════════════════════════════
function renderCaptainDashboard(){
  if(!captainTeamId){
    document.getElementById('captainContent').innerHTML=
      '<div style="text-align:center;padding:3rem 1rem">'
      +'<div style="font-family:Bebas Neue,sans-serif;font-size:2rem;color:#7CB9FF;letter-spacing:3px;margin-bottom:.5rem">Captain Login</div>'
      +'<div style="font-size:.85rem;color:var(--muted);margin-bottom:1.5rem">Login with your team password to manage squad & player info</div>'
      +'<button onclick="openCapLogin()" style="background:linear-gradient(135deg,#2979FF,#1565C0);color:#fff;border:none;border-radius:10px;padding:.75rem 2rem;font-family:Barlow Condensed,sans-serif;font-size:1rem;font-weight:700;cursor:pointer;letter-spacing:1px">Login as Captain</button>'
      +'</div>';
    var nameEl=document.getElementById('capTeamName');
    if(nameEl) nameEl.textContent='Captain Panel';
    return;
  }
  var t=getTeamById(captainTeamId); if(!t) return;
  var ps=getPlayersByTeam(captainTeamId);
  var logoEl=document.getElementById('capTeamLogo');
  var nameEl=document.getElementById('capTeamName');
  if(logoEl){
    var src=t.logoUrl||t.logo;
    logoEl.innerHTML=(src&&src.startsWith('http'))
      ?'<img src="'+esc(src)+'" style="width:100%;height:100%;object-fit:cover;border-radius:50%">'
      :esc(src||'⚽');
  }
  if(nameEl) nameEl.textContent=t.name;

  // ── Tabs: Squad Submit | Player Info ────────────────────────────────
  document.getElementById('captainContent').innerHTML=
    '<div style="display:flex;gap:.4rem;margin-bottom:1rem;border-bottom:1px solid var(--border);padding-bottom:.6rem;flex-wrap:wrap" id="capTabs">'
    +'<button class="tab active" style="font-size:.78rem" onclick="capTab(this.dataset.p,this)" data-p="squad">Squad Submission</button>'
    +'<button class="tab" style="font-size:.78rem" onclick="capTab(this.dataset.p,this)" data-p="playerinfo">Player Info</button>'
    +'</div>'
    +'<div id="capPanel_squad"></div>'
    +'<div id="capPanel_playerinfo" style="display:none"></div>';

  renderCapSquadForm();
  renderCapPlayerInfo();
}

function capTab(panel, btn){
  document.querySelectorAll('#capTabs .tab').forEach(function(b){b.classList.remove('active');});
  btn.classList.add('active');
  ['squad','playerinfo'].forEach(function(n){
    var el=document.getElementById('capPanel_'+n);
    if(el) el.style.display=(n===panel)?'block':'none';
  });
}

// ══════════════════════════════════════════════════════════════
// SQUAD SUBMISSION FORM
// ══════════════════════════════════════════════════════════════
function renderCapSquadForm(){
  var el=document.getElementById('capPanel_squad'); if(!el) return;
  var ps=getPlayersByTeam(captainTeamId);
  if(!ps.length){ el.innerHTML='<p style="color:var(--muted)">No players registered yet.</p>'; return; }

  // Get latest submission if exists
  var latestSub=null;
  var subs=Object.values(state.squad_submissions).filter(function(s){return s.teamId===captainTeamId;});
  if(subs.length) latestSub=subs.sort(function(a,b){return (b.timestamp||0)-(a.timestamp||0);})[0];

  function makePlayerOpts(selectedId){
    var opts='<option value="">— Select Player —</option>';
    ps.forEach(function(p){ opts+='<option value="'+p.id+'"'+(p.id===selectedId?' selected':'')+'>'+esc(p.name)+(p.cat==="invited"?" (INV)":p.cat==="youth"?" (YTH)":"")+'</option>'; });
    return opts;
  }

  var slots=[
    {key:'fd1',  label:'First Day 1',   vpn:true},
    {key:'fd2',  label:'First Day 2',   vpn:true},
    {key:'fd3',  label:'First Day 3',   vpn:false},
    {key:'fd4',  label:'First Day 4',   vpn:false},
    {key:'st1',  label:'Star 1',        vpn:true},
    {key:'st2',  label:'Star 2',        vpn:false},
    {key:'ns1',  label:'Non-Star 1',    vpn:true},
    {key:'ns2',  label:'Non-Star 2',    vpn:true},
    {key:'ns3',  label:'Non-Star 3',    vpn:false},
    {key:'ns4',  label:'Non-Star 4',    vpn:false},
    {key:'ns5',  label:'Non-Star 5',    vpn:false},
    {key:'ref',  label:'Referee',       vpn:false, referee:true},
  ];

  var formRows=slots.map(function(slot){
    var val=latestSub?(latestSub[slot.key]||''):'';
    var labelColor=slot.vpn?'color:var(--acc)':'color:var(--muted)';
    var vpnTag=slot.vpn?'<span style="background:rgba(255,214,0,.15);color:var(--acc);border:1px solid rgba(255,214,0,.35);padding:.06rem .35rem;border-radius:4px;font-size:.62rem;font-weight:800;margin-left:.3rem">VPN</span>':'';
    var refTag=slot.referee?'<span style="background:rgba(41,121,255,.12);color:#7CB9FF;border:1px solid rgba(41,121,255,.3);padding:.06rem .35rem;border-radius:4px;font-size:.62rem;font-weight:800;margin-left:.3rem">REF</span>':'';
    return '<div style="display:flex;align-items:center;gap:.6rem;padding:.4rem 0;border-bottom:1px solid var(--border)">'
      +'<div style="min-width:110px;font-family:Barlow Condensed,sans-serif;font-size:.78rem;font-weight:700;'+labelColor+'">'+slot.label+vpnTag+refTag+'</div>'
      +'<select id="sq_'+slot.key+'" style="flex:1;background:var(--dark);border:1px solid var(--border);border-radius:7px;padding:.35rem .6rem;color:var(--text);font-family:Barlow,sans-serif;font-size:.82rem;outline:none">'
      +makePlayerOpts(val)+'</select>'
      +'</div>';
  }).join('');

  var lastTime=latestSub?('<span style="color:var(--green)">Last submitted: '+new Date(latestSub.timestamp).toLocaleString()+'</span>'):'<span style="color:var(--muted)">No submission yet</span>';

  el.innerHTML=
    '<div style="background:rgba(255,214,0,.04);border:1px solid rgba(255,214,0,.15);border-radius:12px;padding:.8rem 1rem;margin-bottom:1rem;font-size:.74rem;color:var(--muted)">'
    +'Select 11 players + 1 referee. <strong style="color:var(--acc)">VPN</strong> = VPN players (marked with key icon in output).</div>'
    +formRows
    +'<div style="margin-top:1rem;display:flex;align-items:center;gap:.8rem;flex-wrap:wrap">'
    +'<button onclick="submitSquad()" style="background:var(--green);color:#000;border:none;border-radius:8px;padding:.55rem 1.4rem;font-family:Barlow Condensed,sans-serif;font-size:.9rem;font-weight:700;cursor:pointer">Submit Squad</button>'
    +'<div style="font-size:.72rem">'+lastTime+'</div>'
    +'</div>';
}

async function submitSquad(){
  if(!captainTeamId) return;
  var slots=['fd1','fd2','fd3','fd4','st1','st2','ns1','ns2','ns3','ns4','ns5','ref'];
  var data={teamId:captainTeamId, timestamp:Date.now()};
  var missing=[];
  slots.forEach(function(k){
    var el=document.getElementById('sq_'+k);
    data[k]=el?el.value:'';
    if(!data[k]) missing.push(k);
  });
  if(missing.length>0){
    if(!confirm('Some slots are empty ('+missing.join(', ')+'). Submit anyway?')) return;
  }
  var subId=captainTeamId+'_'+Date.now();
  await fsSet('squad_submissions', subId, data);
  alert('Squad submitted!');
  renderCapSquadForm();
}

// ══════════════════════════════════════════════════════════════
// PLAYER INFO FORM (existing functionality)
// ══════════════════════════════════════════════════════════════
function renderCapPlayerInfo(){
  var el=document.getElementById('capPanel_playerinfo'); if(!el) return;
  var t=getTeamById(captainTeamId); if(!t) return;
  var ps=getPlayersByTeam(captainTeamId);
  var rows=calcStandings();
  var tp=rows.find(function(r){return r.id===captainTeamId;})||{};

  var catGrps={local:[],youth:[],invited:[]};
  ps.forEach(function(p){ (catGrps[p.cat]||catGrps.local).push(p); });

  var playerRows='';
  Object.keys(catGrps).forEach(function(cat){
    var arr=catGrps[cat]; if(!arr.length) return;
    playerRows+='<tr><td colspan="4" style="background:var(--card2);padding:.4rem .8rem;font-family:Barlow Condensed,sans-serif;font-size:.72rem;font-weight:700;letter-spacing:1px;text-transform:uppercase;border-top:1px solid var(--border);">'
      +catBadge(cat)+'<span style="margin-left:.4rem;color:var(--muted)">('+arr.length+')</span></td></tr>';
    arr.forEach(function(p){
      var uid_val=p.uid||'';
      var dev_val=p.deviceName||'';
      playerRows+=
        '<tr id="prow_'+p.id+'">'
        +'<td style="padding:.55rem .7rem;border-top:1px solid var(--border);">'
          +'<div style="display:flex;align-items:center;gap:.5rem;">'
          +playerPhotoEl(p,26)
          +'<div><div style="font-weight:700;font-size:.85rem;">'+esc(p.name)+'</div>'
          +(p.bid?'<div>'+coinBadge(p.bid,false)+'</div>':'')
          +'</div></div>'
        +'</td>'
        +'<td style="padding:.4rem .5rem;border-top:1px solid var(--border);">'
          +'<input id="uid_'+p.id+'" value="'+esc(uid_val)+'" placeholder="User ID" style="background:var(--dark);border:1px solid var(--border);border-radius:6px;padding:.3rem .5rem;color:var(--text);font-size:.8rem;width:100%;outline:none;min-width:80px;">'
        +'</td>'
        +'<td style="padding:.4rem .5rem;border-top:1px solid var(--border);">'
          +'<input id="dev_'+p.id+'" value="'+esc(dev_val)+'" placeholder="Device name" style="background:var(--dark);border:1px solid var(--border);border-radius:6px;padding:.3rem .5rem;color:var(--text);font-size:.8rem;width:100%;outline:none;min-width:90px;">'
        +'</td>'
        +'<td style="padding:.4rem .5rem;border-top:1px solid var(--border);text-align:center;">'
          +'<button onclick="savePlayerInfo(\''
          +p.id
          +'\''+')" style="background:var(--green);color:#000;border:none;border-radius:6px;padding:.28rem .65rem;font-family:Barlow Condensed,sans-serif;font-size:.75rem;font-weight:700;cursor:pointer;">Save</button>'
        +'</td>'
        +'</tr>';
    });
  });

  el.innerHTML=
    '<div style="display:grid;grid-template-columns:repeat(auto-fill,minmax(110px,1fr));gap:.5rem;margin-bottom:1rem;">'
    +'<div class="stat-box" style="padding:.5rem .7rem;"><div class="num">'+(tp.pts||0)+'</div><div class="lbl">Pts</div></div>'
    +'<div class="stat-box" style="padding:.5rem .7rem;"><div class="num">'+(tp.w||0)+'</div><div class="lbl">W</div></div>'
    +'<div class="stat-box" style="padding:.5rem .7rem;"><div class="num">'+(tp.d||0)+'</div><div class="lbl">D</div></div>'
    +'<div class="stat-box" style="padding:.5rem .7rem;"><div class="num">'+(tp.l||0)+'</div><div class="lbl">L</div></div>'
    +'<div class="stat-box" style="padding:.5rem .7rem;"><div class="num">'+ps.length+'</div><div class="lbl">Players</div></div>'
    +'</div>'
    +'<div class="twrap"><table><thead><tr>'
    +'<th>Player</th><th>User ID</th><th>Device Name</th><th style="text-align:center;">Action</th>'
    +'</tr></thead><tbody>'+playerRows+'</tbody></table></div>'
    +'<div style="margin-top:.8rem;display:flex;gap:.6rem;flex-wrap:wrap;">'
    +'<button onclick="saveAllPlayerInfo()" style="background:var(--green);color:#000;border:none;border-radius:8px;padding:.5rem 1.2rem;font-family:Barlow Condensed,sans-serif;font-size:.85rem;font-weight:700;cursor:pointer;">Save All</button>'

    +'</div>';
}


async function savePlayerInfo(pid){
  var uid_inp=document.getElementById('uid_'+pid);
  var dev_inp=document.getElementById('dev_'+pid);
  if(!uid_inp||!dev_inp) return;
  var p=getPlayers().find(function(pl){return pl.id===pid;}); if(!p) return;
  await fsSet('players',pid,Object.assign({},p,{uid:uid_inp.value.trim(),deviceName:dev_inp.value.trim()}));
  // flash green
  var row=document.getElementById('prow_'+pid);
  if(row){ row.style.background='rgba(0,200,83,.08)'; setTimeout(function(){row.style.background='';},1000); }
}

async function saveAllPlayerInfo(){
  var ps=getPlayersByTeam(captainTeamId);
  var saved=0;
  for(var p of ps){
    var uid_inp=document.getElementById('uid_'+p.id);
    var dev_inp=document.getElementById('dev_'+p.id);
    if(!uid_inp||!dev_inp) continue;
    await fsSet('players',p.id,Object.assign({},p,{uid:uid_inp.value.trim(),deviceName:dev_inp.value.trim()}));
    saved++;
  }
  alert('Saved '+saved+' players.');
}

async function savePlayerInfo(pid){
  var uid_inp=document.getElementById('uid_'+pid);
  var dev_inp=document.getElementById('dev_'+pid);
  if(!uid_inp||!dev_inp) return;
  var p=getPlayers().find(function(pl){return pl.id===pid;}); if(!p) return;
  await fsSet('players',pid,Object.assign({},p,{uid:uid_inp.value.trim(),deviceName:dev_inp.value.trim()}));
  // flash green
  var row=document.getElementById('prow_'+pid);
  if(row){ row.style.background='rgba(0,200,83,.08)'; setTimeout(function(){row.style.background='';},1000); }
}

async function saveAllPlayerInfo(){
  var ps=getPlayersByTeam(captainTeamId);
  var saved=0;
  for(var p of ps){
    var uid_inp=document.getElementById('uid_'+p.id);
    var dev_inp=document.getElementById('dev_'+p.id);
    if(!uid_inp||!dev_inp) continue;
    await fsSet('players',p.id,Object.assign({},p,{uid:uid_inp.value.trim(),deviceName:dev_inp.value.trim()}));
    saved++;
  }
  alert('Saved '+saved+' players.');
}

// ════════════════════════════════════════════
// ADMIN — FIXTURE GENERATOR
// ════════════════════════════════════════════
function getLatestSquad(teamId){
  var subs=Object.values(state.squad_submissions||{}).filter(function(s){return s.teamId===teamId;});
  if(!subs.length) return null;
  return subs.sort(function(a,b){return (b.timestamp||0)-(a.timestamp||0);})[0];
}

function getPlayerName(pid){
  if(!pid) return '?';
  var p=getPlayers().find(function(pl){return pl.id===pid;});
  return p?p.name:'?';
}

function calcDeadlines(matchDateStr){
  if(!matchDateStr) return {fd:'TBD',star:'TBD',ns:'TBD'};
  var parts=matchDateStr.split('-');
  var d=new Date(parseInt(parts[0]),parseInt(parts[1])-1,parseInt(parts[2]));
  var months=['January','February','March','April','May','June','July','August','September','October','November','December'];
  function addDays(dt,n){ var r=new Date(dt); r.setDate(r.getDate()+n); return r; }
  function fmt(dt,time){ return months[dt.getMonth()]+' '+dt.getDate()+' | '+time; }
  return {
    fd:   fmt(addDays(d,1),'12:50 AM'),
    star: fmt(addDays(d,1),'11:00 PM'),
    ns:   fmt(addDays(d,2),'12:50 AM')
  };
}

function toBold(str){
  var boldMap={};
  var nrm='ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789';
  for(var i=0;i<26;i++){
    boldMap[nrm[i]]=String.fromCodePoint(0x1D400+i);
    boldMap[nrm[26+i]]=String.fromCodePoint(0x1D41A+i);
  }
  for(var d=0;d<10;d++) boldMap[nrm[52+d]]=String.fromCodePoint(0x1D7CE+d);
  var out='';
  for(var ci=0;ci<str.length;ci++){ out+=boldMap[str[ci]]||str[ci]; }
  return out;
}

function renderSquadSubmissionsAdmin(){
  var subs=Object.values(state.squad_submissions||{});
  if(!subs.length) return '<p style="color:var(--muted);font-size:.8rem;">No submissions yet.</p>';
  var byTeam={};
  subs.forEach(function(s){ if(!byTeam[s.teamId]||s.timestamp>byTeam[s.teamId].timestamp) byTeam[s.teamId]=s; });
  return '<div class="alist">'+Object.values(byTeam).map(function(s){
    var t=getTeamById(s.teamId);
    var dt=new Date(s.timestamp).toLocaleString();
    var slots=['fd1','fd2','fd3','fd4','st1','st2','ns1','ns2','ns3','ns4','ns5'];
    var names=slots.map(function(k){ return getPlayerName(s[k]); });
    var refName=getPlayerName(s.ref);
    return '<div class="aitem" style="flex-direction:column;align-items:flex-start;gap:.4rem;">'
      +'<div style="display:flex;align-items:center;gap:.6rem;width:100%;">'
      +(t?teamLogoEl(t,26):'')
      +'<strong style="font-size:.88rem;">'+(t?esc(t.name):'?')+'</strong>'
      +'<span style="font-size:.68rem;color:var(--muted);margin-left:auto;">'+esc(dt)+'</span>'
      +'</div>'
      +'<div style="font-size:.72rem;color:var(--muted);line-height:1.8;">'
      +'<span style="color:var(--acc);font-weight:700;">FD:</span> '+names.slice(0,4).join(', ')+'<br>'
      +'<span style="color:var(--acc);font-weight:700;">Star:</span> '+names.slice(4,6).join(', ')+'<br>'
      +'<span style="color:var(--acc);font-weight:700;">NS:</span> '+names.slice(6,11).join(', ')+'<br>'
      +'<span style="color:var(--acc);font-weight:700;">Ref:</span> '+esc(refName)
      +'</div>'
      +'</div>';
  }).join('')+'</div>';
}

function aFxGenHTML(){
  var fxs=getFixtures().filter(function(f){ return f.status!=='played'; });
  var fxOpts=fxs.map(function(f){
    var ht=getTeamById(f.home), at=getTeamById(f.away);
    return '<option value="'+f.id+'">'+(ht?esc(ht.name):'?')+' vs '+(at?esc(at.name):'?')+' - '+esc(f.date||'No date')+'</option>';
  }).join('');
  if(!fxOpts) fxOpts='<option value="">No upcoming fixtures</option>';

  return '<div class="apanel"><h3>Fixture Generator</h3>'
    +'<div style="background:rgba(41,121,255,.06);border:1px solid rgba(41,121,255,.2);border-radius:10px;padding:.75rem 1rem;margin-bottom:1rem;font-size:.74rem;color:var(--muted);">'
    +'Fetches latest squad submissions from both teams and generates fixture text automatically with correct pairing, VPN logic, and deadlines.</div>'
    +'<div style="margin-bottom:.9rem;">'
    +'<label style="font-size:.7rem;color:var(--muted);text-transform:uppercase;letter-spacing:.5px;font-weight:600;display:block;margin-bottom:.3rem;">Select Fixture</label>'
    +'<select id="fxgen_sel" style="width:100%;background:var(--dark);border:1px solid var(--border);border-radius:8px;padding:.5rem .7rem;color:var(--text);font-family:Barlow,sans-serif;font-size:.85rem;outline:none;">'+fxOpts+'</select>'
    +'</div>'
    +'<div style="display:flex;gap:.6rem;flex-wrap:wrap;margin-bottom:1rem;">'
    +'<button onclick="generateFixture()" style="background:var(--green);color:#000;border:none;border-radius:8px;padding:.55rem 1.4rem;font-family:Barlow Condensed,sans-serif;font-size:.9rem;font-weight:700;cursor:pointer;letter-spacing:.5px;">Generate Fixture</button>'
    +'</div>'
    +'<div id="fxgen_output" style="display:none;">'
    +'<div style="display:flex;align-items:center;gap:.6rem;margin-bottom:.6rem;">'
    +'<div style="font-family:Barlow Condensed,sans-serif;font-size:.85rem;font-weight:700;color:var(--green);text-transform:uppercase;letter-spacing:1px;">Generated Output</div>'
    +'<button onclick="copyFixtureText()" id="copyFxBtn" style="background:rgba(0,200,83,.1);color:var(--green);border:1px solid rgba(0,200,83,.3);border-radius:6px;padding:.28rem .8rem;font-family:Barlow Condensed,sans-serif;font-size:.78rem;font-weight:700;cursor:pointer;">Copy Text</button>'
    +'</div>'
    +'<pre id="fxgen_text" style="background:var(--dark);border:1px solid var(--border);border-radius:10px;padding:1rem;font-family:monospace;font-size:.78rem;color:var(--text);white-space:pre-wrap;word-break:break-word;max-height:500px;overflow-y:auto;line-height:1.7;"></pre>'
    +'</div>'
    +'</div>'
    +'<div class="apanel"><h3>Squad Submissions</h3>'
    +renderSquadSubmissionsAdmin()
    +'</div>';
}

function generateFixture(){
  var fxId=document.getElementById('fxgen_sel').value;
  if(!fxId){ alert('Select a fixture first!'); return; }
  var fx=state.fixtures[fxId];
  if(!fx){ alert('Fixture not found!'); return; }
  var ht=getTeamById(fx.home), at=getTeamById(fx.away);
  if(!ht||!at){ alert('Teams not found!'); return; }

  var homeSub=getLatestSquad(fx.home);
  var awaySub=getLatestSquad(fx.away);
  if(!homeSub){ alert(ht.name+' has not submitted a squad yet!'); return; }
  if(!awaySub){ alert(at.name+' has not submitted a squad yet!'); return; }

  // Extract players — VPN = key icon positions
  var home={
    fd_vpn: [getPlayerName(homeSub.fd1), getPlayerName(homeSub.fd2)],
    fd_nor: [getPlayerName(homeSub.fd3), getPlayerName(homeSub.fd4)],
    st_vpn: [getPlayerName(homeSub.st1)],
    st_nor: [getPlayerName(homeSub.st2)],
    ns_vpn: [getPlayerName(homeSub.ns1), getPlayerName(homeSub.ns2)],
    ns_nor: [getPlayerName(homeSub.ns3), getPlayerName(homeSub.ns4), getPlayerName(homeSub.ns5)],
    ref:     getPlayerName(homeSub.ref)
  };
  var away={
    fd_vpn: [getPlayerName(awaySub.fd1), getPlayerName(awaySub.fd2)],
    fd_nor: [getPlayerName(awaySub.fd3), getPlayerName(awaySub.fd4)],
    st_vpn: [getPlayerName(awaySub.st1)],
    st_nor: [getPlayerName(awaySub.st2)],
    ns_vpn: [getPlayerName(awaySub.ns1), getPlayerName(awaySub.ns2)],
    ns_nor: [getPlayerName(awaySub.ns3), getPlayerName(awaySub.ns4), getPlayerName(awaySub.ns5)],
    ref:     getPlayerName(awaySub.ref)
  };

  var dl=calcDeadlines(fx.date||'');
  var round=fx.round||'Round ?';
  var B=toBold;
  var KEY='\uD83D\uDD11'; // key emoji
  var VS ='\uD83C\uDD9A'; // VS emoji

  // First Day: VPN vs non-VPN cross pairing
  var fdLines=[
    home.fd_vpn[0]+' '+KEY+' '+VS+' '+away.fd_nor[0],
    home.fd_nor[0]+' '+VS+' '+away.fd_vpn[0]+' '+KEY,
    home.fd_vpn[1]+' '+KEY+' '+VS+' '+away.fd_nor[1],
    home.fd_nor[1]+' '+VS+' '+away.fd_vpn[1]+' '+KEY,
  ];

  // Star: VPN vs non-VPN
  var starLines=[
    home.st_vpn[0]+' '+KEY+' '+VS+' '+away.st_nor[0],
    home.st_nor[0]+' '+VS+' '+away.st_vpn[0]+' '+KEY,
  ];

  // Non-Star: random team gets +1 VPN (3 vs 2)
  var homeGetsExtra=(homeSub.timestamp%2===0);
  var hVPN=home.ns_vpn.slice();
  var aVPN=away.ns_vpn.slice();
  var hNOR=home.ns_nor.slice();
  var aNOR=away.ns_nor.slice();
  if(homeGetsExtra){ hVPN.push(hNOR.pop()); }
  else { aVPN.push(aNOR.pop()); }

  var nsLines=[];
  var hVPN2=hVPN.slice(), aNOR2=aNOR.slice(), aVPN2=aVPN.slice(), hNOR2=hNOR.slice();
  // home VPN vs away NOR
  while(hVPN2.length&&aNOR2.length){ nsLines.push(hVPN2.shift()+' '+KEY+' '+VS+' '+aNOR2.shift()); }
  // away VPN vs home NOR
  while(aVPN2.length&&hNOR2.length){ nsLines.push(hNOR2.shift()+' '+VS+' '+aVPN2.shift()+' '+KEY); }
  // NOR vs NOR
  while(hNOR2.length&&aNOR2.length){ nsLines.push(hNOR2.shift()+' '+VS+' '+aNOR2.shift()); }
  while(nsLines.length<5) nsLines.push('? '+VS+' ?');
  nsLines=nsLines.slice(0,5);

  var extraNote=homeGetsExtra
    ? '('+ht.name+' gets 3 '+KEY+' this round)'
    : '('+at.name+' gets 3 '+KEY+' this round)';

  // Add blank line between each pair
  function addSpacing(lines){
    var out=[];
    lines.forEach(function(l,i){ out.push(l); if(i<lines.length-1) out.push(''); });
    return out;
  }

  // Generate PDF URL for info cards
  var output=[
    '\uD83D\uDD25 '+B('Thriller Loading'),
    '',
    '\uD83C\uDFC6'+B('JPL 2026 LEAGUE STAGE'),
    '',
    B(round.toUpperCase()),
    '',
    ht.name+' \u2692\uFE0F '+at.name,
    '',
    '\uD83D\uDEA8 '+B('First Day')+': '+dl.fd,
    '',
  ].concat(addSpacing(fdLines)).concat([
    '',
    '\u2B50 '+B('Star')+': '+dl.star,
    '',
  ]).concat(addSpacing(starLines)).concat([
    '',
    '\u26D4 '+B('Non-Star')+': '+dl.ns,
    extraNote,
    '',
  ]).concat(addSpacing(nsLines)).concat([
    '',
    B('POINTS')+':',
    ht.name+':',
    at.name+':',
    '',
    'ROOM SETTING : 8 MINUTES NORMAL',
    '',
    B('Referee')+': '+home.ref+' / '+away.ref,
    '',
    '\uD83D\uDCCC \u09B0\u09C1\u09B2\u09B8: https://cobegbd.com/rules/'
  ]);

  var text=output.join('\n');
  window._lastFixtureText=text;
  window._lastFxData={homeId:fx.home, awayId:fx.away, homeName:ht.name, awayName:at.name};
  document.getElementById('fxgen_output').style.display='block';
  document.getElementById('fxgen_text').textContent=text;

  // Show JPG download buttons
  var jpgBtnWrap=document.getElementById('fxgen_jpg_btns');
  if(!jpgBtnWrap){
    jpgBtnWrap=document.createElement('div');
    jpgBtnWrap.id='fxgen_jpg_btns';
    jpgBtnWrap.style.cssText='margin-top:.7rem;display:flex;gap:.6rem;flex-wrap:wrap;';
    document.getElementById('fxgen_output').appendChild(jpgBtnWrap);
  }
  jpgBtnWrap.innerHTML=
    '<button onclick="downloadFixtureJPG()" style="background:linear-gradient(135deg,rgba(0,200,83,.15),rgba(0,200,83,.08));color:var(--green);border:1px solid rgba(0,200,83,.35);border-radius:8px;padding:.5rem 1.2rem;font-family:Barlow Condensed,sans-serif;font-size:.88rem;font-weight:700;cursor:pointer;letter-spacing:.5px;">Download Fixture Card</button>'
    +'<button onclick="downloadTeamInfoCardJPG(window._lastFxData.homeId,window._lastFxData.homeName,null)" style="background:linear-gradient(135deg,rgba(0,200,83,.12),rgba(0,200,83,.06));color:var(--green);border:1px solid rgba(0,200,83,.3);border-radius:8px;padding:.5rem 1.2rem;font-family:Barlow Condensed,sans-serif;font-size:.88rem;font-weight:700;cursor:pointer;letter-spacing:.5px;">Download Home Team Info</button>'
    +'<button onclick="downloadTeamInfoCardJPG(window._lastFxData.awayId,window._lastFxData.awayName,null)" style="background:linear-gradient(135deg,rgba(41,121,255,.15),rgba(41,121,255,.08));color:#7CB9FF;border:1px solid rgba(41,121,255,.35);border-radius:8px;padding:.5rem 1.2rem;font-family:Barlow Condensed,sans-serif;font-size:.88rem;font-weight:700;cursor:pointer;letter-spacing:.5px;">Download Away Team Info</button>';

  document.getElementById('fxgen_text').scrollIntoView({behavior:'smooth',block:'start'});
}

// ════════════════════════════════════════════════════════════
// JPG DOWNLOAD — Fixture Card
// ════════════════════════════════════════════════════════════
function downloadFixtureJPG(){
  var text = window._lastFixtureText || '';
  if(!text){ alert('Generate a fixture first!'); return; }
  var fx  = window._lastFxData || {};

  // Build a styled card div in memory
  var card = document.createElement('div');
  card.style.cssText = [
    'position:fixed','top:-9999px','left:-9999px',
    'width:520px','min-height:300px',
    'background:linear-gradient(160deg,#080D0A 0%,#0d2010 100%)',
    'border:2px solid #00C853',
    'border-radius:18px',
    'padding:28px 30px',
    'font-family:Arial,sans-serif',
    'color:#E8F5E9',
    'box-shadow:0 0 40px rgba(0,200,83,.25)',
    'white-space:pre-wrap',
    'word-break:break-word',
    'line-height:1.75',
    'font-size:14px',
    'letter-spacing:.2px'
  ].join(';');

  // Header
  var header = document.createElement('div');
  header.style.cssText = 'display:flex;align-items:center;gap:10px;margin-bottom:16px;padding-bottom:12px;border-bottom:1px solid rgba(0,200,83,.3);';
  header.innerHTML = '<div style="width:36px;height:36px;background:linear-gradient(135deg,#00C853,#009624);border-radius:50%;display:flex;align-items:center;justify-content:center;font-size:18px;flex-shrink:0;">&#9917;</div>'
    + '<div><div style="font-size:17px;font-weight:900;color:#00C853;letter-spacing:2px;">JPL 2026</div>'
    + '<div style="font-size:11px;color:#5A8465;letter-spacing:1px;">FIXTURE CARD</div></div>';

  // Text body
  var body = document.createElement('pre');
  body.style.cssText = 'margin:0;white-space:pre-wrap;word-break:break-word;font-family:Arial,sans-serif;font-size:13px;color:#E8F5E9;line-height:1.75;';
  body.textContent = text;

  card.appendChild(header);
  card.appendChild(body);
  document.body.appendChild(card);

  if(typeof html2canvas === 'undefined'){
    document.body.removeChild(card);
    alert('html2canvas not loaded yet. Please wait and try again.');
    return;
  }

  html2canvas(card, {
    backgroundColor: '#080D0A',
    scale: 2,
    useCORS: true,
    logging: false
  }).then(function(canvas){
    document.body.removeChild(card);
    var link = document.createElement('a');
    link.download = 'JPL_Fixture_' + (fx.homeName||'Home').replace(/\s/g,'_') + '_vs_' + (fx.awayName||'Away').replace(/\s/g,'_') + '.jpg';
    link.href = canvas.toDataURL('image/jpeg', 0.95);
    link.click();
  }).catch(function(err){
    document.body.removeChild(card);
    console.error('JPG error:', err);
    alert('JPG generation failed: ' + err.message);
  });
}

// ════════════════════════════════════════════════════════════
// JPG DOWNLOAD — Player Info Cards (2 teams, one JPG each)
// ════════════════════════════════════════════════════════════
function downloadInfoCardJPG(homeId, awayId, homeName, awayName){
  if(typeof html2canvas === 'undefined'){
    alert('html2canvas not loaded yet. Please wait and try again.'); return;
  }
  // Download home team card first, then away
  downloadTeamInfoCardJPG(homeId, homeName, function(){
    setTimeout(function(){ downloadTeamInfoCardJPG(awayId, awayName, null); }, 800);
  });
}

function downloadTeamInfoCardJPG(teamId, teamName, callback){
  var t = getTeamById(teamId);
  var players = getPlayersByTeam(teamId);

  var card = document.createElement('div');
  card.style.cssText = [
    'position:fixed','top:-9999px','left:-9999px',
    'width:600px',
    'background:linear-gradient(160deg,#080D0A 0%,#0d2010 100%)',
    'border:2px solid #00C853',
    'border-radius:18px',
    'padding:24px 26px 20px',
    'font-family:Arial,sans-serif',
    'color:#E8F5E9'
  ].join(';');

  // Header
  var logoSrc = t ? (t.logoUrl||t.logo||'') : '';
  var logoImg = (logoSrc && logoSrc.startsWith('http'))
    ? '<img src="'+logoSrc+'" style="width:48px;height:48px;border-radius:50%;border:2px solid #00C853;object-fit:cover;" crossorigin="anonymous">'
    : '<div style="width:48px;height:48px;border-radius:50%;background:linear-gradient(135deg,#00C853,#009624);display:flex;align-items:center;justify-content:center;font-size:22px;">&#9917;</div>';

  var header = '<div style="display:flex;align-items:center;gap:12px;margin-bottom:16px;padding-bottom:12px;border-bottom:2px solid rgba(0,200,83,.3);">'
    + logoImg
    + '<div>'
      + '<div style="font-size:20px;font-weight:900;color:#00C853;letter-spacing:1px;">'+esc(teamName)+'</div>'
      + '<div style="font-size:11px;color:#5A8465;letter-spacing:1px;margin-top:2px;">PLAYER INFO CARD · JPL 2026</div>'
    + '</div>'
    + '</div>';

  // Table
  var catColors = {local:'#00C853', youth:'#2979FF', invited:'#FF6B35'};
  var rows = players.map(function(p, i){
    var cat = p.cat||'local';
    var cc = catColors[cat]||'#00C853';
    var catLabel = cat.toUpperCase();
    var bg = i%2===0 ? 'rgba(0,200,83,.04)' : 'transparent';
    return '<tr style="background:'+bg+';border-bottom:1px solid rgba(255,255,255,.07);">'
      + '<td style="padding:7px 8px;font-weight:700;color:#5A8465;font-size:12px;">'+(i+1)+'</td>'
      + '<td style="padding:7px 8px;font-weight:700;font-size:13px;color:#E8F5E9;">'+esc(p.name)+'</td>'
      + '<td style="padding:7px 8px;"><span style="background:'+cc+'22;color:'+cc+';padding:2px 7px;border-radius:4px;font-size:10px;font-weight:900;border:1px solid '+cc+'55;">'+catLabel+'</span></td>'
      + '<td style="padding:7px 8px;font-size:12px;color:#FFD600;font-weight:700;">'+(p.bid||'—')+'</td>'
      + '<td style="padding:7px 8px;font-size:11px;color:#90CAF9;max-width:120px;word-break:break-all;">'+(p.uid||'<span style="color:#3a5a3a;">—</span>')+'</td>'
      + '<td style="padding:7px 8px;font-size:11px;color:#A5D6A7;max-width:110px;word-break:break-all;">'+(p.deviceName||'<span style="color:#3a5a3a;">—</span>')+'</td>'
      + '</tr>';
  }).join('');

  var table = '<table style="width:100%;border-collapse:collapse;font-size:12px;">'
    + '<thead><tr style="background:rgba(0,200,83,.12);border-bottom:1px solid rgba(0,200,83,.3);">'
    + '<th style="padding:7px 8px;text-align:left;color:#00C853;font-size:10px;text-transform:uppercase;letter-spacing:.8px;">#</th>'
    + '<th style="padding:7px 8px;text-align:left;color:#00C853;font-size:10px;text-transform:uppercase;letter-spacing:.8px;">Player</th>'
    + '<th style="padding:7px 8px;text-align:left;color:#00C853;font-size:10px;text-transform:uppercase;letter-spacing:.8px;">Cat</th>'
    + '<th style="padding:7px 8px;text-align:left;color:#00C853;font-size:10px;text-transform:uppercase;letter-spacing:.8px;">Bid</th>'
    + '<th style="padding:7px 8px;text-align:left;color:#00C853;font-size:10px;text-transform:uppercase;letter-spacing:.8px;">User ID</th>'
    + '<th style="padding:7px 8px;text-align:left;color:#00C853;font-size:10px;text-transform:uppercase;letter-spacing:.8px;">Device</th>'
    + '</tr></thead>'
    + '<tbody>'+rows+'</tbody>'
    + '</table>';

  var footer = '<div style="margin-top:12px;font-size:10px;color:#3a5a3a;text-align:right;">Juvenile League 2026 · '+new Date().toLocaleDateString()+'</div>';

  card.innerHTML = header + table + footer;
  document.body.appendChild(card);

  html2canvas(card, {
    backgroundColor: '#080D0A',
    scale: 2,
    useCORS: true,
    logging: false
  }).then(function(canvas){
    document.body.removeChild(card);
    var link = document.createElement('a');
    link.download = 'JPL_InfoCard_' + teamName.replace(/\s/g,'_') + '.jpg';
    link.href = canvas.toDataURL('image/jpeg', 0.95);
    link.click();
    if(callback) callback();
  }).catch(function(err){
    document.body.removeChild(card);
    console.error('JPG error:', err);
    if(callback) callback();
  });
}

function copyFixtureText(){
  var text=window._lastFixtureText||'';
  if(!text) return;
  var btn=document.getElementById('copyFxBtn');
  function showCopied(){ if(btn){ btn.textContent='Copied!'; btn.style.background='rgba(0,200,83,.25)'; setTimeout(function(){ btn.textContent='Copy Text'; btn.style.background='rgba(0,200,83,.1)'; },2000); } }
  if(navigator.clipboard){
    navigator.clipboard.writeText(text).then(showCopied).catch(function(){
      var ta=document.createElement('textarea');
      ta.value=text; ta.style.cssText='position:fixed;opacity:0;top:0;left:0;';
      document.body.appendChild(ta); ta.select(); document.execCommand('copy');
      document.body.removeChild(ta); showCopied();
    });
  } else {
    var ta=document.createElement('textarea');
    ta.value=text; ta.style.cssText='position:fixed;opacity:0;top:0;left:0;';
    document.body.appendChild(ta); ta.select(); document.execCommand('copy');
    document.body.removeChild(ta); showCopied();
  }
}

// ════════════════════════════════════════════
// STANDINGS CARD — Download JPG (Canva Pro style)
// ════════════════════════════════════════════
function downloadStandingsJPG(){
  if(typeof html2canvas === 'undefined'){
    alert('html2canvas not loaded yet. Please wait and try again.');
    return;
  }
  var rows = calcStandings().filter(function(r){
    var ms = state.manual_standings && state.manual_standings[r.id];
    return !(ms && ms.hidden);
  });
  if(!rows.length){ alert('No standings data yet.'); return; }

  // ── Build the card DOM element ──────────────────────────────────────
  var card = document.createElement('div');
  card.style.cssText = [
    'position:fixed','top:-9999px','left:-9999px',
    'width:700px',
    'background:linear-gradient(145deg,#050c07 0%,#0a1a0c 40%,#060e08 100%)',
    'border-radius:20px',
    'padding:0',
    'font-family:Arial,Helvetica,sans-serif',
    'overflow:hidden',
    'box-shadow:0 0 0 1px rgba(0,200,83,.2)'
  ].join(';');

  // ── Header ───────────────────────────────────────────────────────────
  var headerDiv = document.createElement('div');
  headerDiv.style.cssText = [
    'background:linear-gradient(135deg,rgba(0,200,83,.15) 0%,rgba(0,200,83,.04) 60%,transparent 100%)',
    'border-bottom:1px solid rgba(0,200,83,.25)',
    'padding:26px 30px 22px',
    'display:flex','align-items:center','justify-content:space-between'
  ].join(';');

  headerDiv.innerHTML =
    '<div style="display:flex;align-items:center;gap:14px;">'
      + '<div style="width:52px;height:52px;background:linear-gradient(135deg,#00C853,#009624);border-radius:50%;display:flex;align-items:center;justify-content:center;font-size:26px;box-shadow:0 0 20px rgba(0,200,83,.4);">&#9917;</div>'
      + '<div>'
        + '<div style="font-size:22px;font-weight:900;color:#FFFFFF;letter-spacing:1.5px;line-height:1.1;">JUVENILE PREMIER LEAGUE</div>'
        + '<div style="font-size:12px;color:#00C853;letter-spacing:3px;text-transform:uppercase;margin-top:3px;font-weight:700;">Season 1 &nbsp;&#9646;&nbsp; Official Standings</div>'
      + '</div>'
    + '</div>'
    + '<div style="text-align:right;">'
      + '<div style="font-size:11px;color:rgba(255,255,255,.35);letter-spacing:1px;">' + new Date().toLocaleDateString('en-GB',{day:'2-digit',month:'short',year:'numeric'}).toUpperCase() + '</div>'
      + '<div style="font-size:10px;color:rgba(0,200,83,.5);letter-spacing:1px;margin-top:3px;">JPL OFFICIAL</div>'
    + '</div>';

  card.appendChild(headerDiv);

  // ── Column header ─────────────────────────────────────────────────
  var colHeader = document.createElement('div');
  colHeader.style.cssText = 'padding:10px 30px;display:grid;grid-template-columns:30px 36px 1fr 40px 40px 40px 40px 50px 55px 80px;gap:0;align-items:center;background:rgba(0,200,83,.06);border-bottom:1px solid rgba(0,200,83,.12);';

  var colLabels = [
    {text:'#',      align:'center', color:'rgba(0,200,83,.6)'},
    {text:'',       align:'center', color:'transparent'},
    {text:'TEAM',   align:'left',   color:'rgba(0,200,83,.6)'},
    {text:'MP',     align:'center', color:'rgba(255,255,255,.4)'},
    {text:'W',      align:'center', color:'rgba(0,200,83,.6)'},
    {text:'D',      align:'center', color:'rgba(255,214,0,.6)'},
    {text:'L',      align:'center', color:'rgba(255,61,61,.6)'},
    {text:'GD',     align:'center', color:'rgba(255,255,255,.4)'},
    {text:'FORM',   align:'center', color:'rgba(255,255,255,.4)'},
    {text:'PTS',    align:'center', color:'rgba(0,200,83,.8)'},
  ];
  colHeader.innerHTML = colLabels.map(function(c){
    return '<div style="font-size:9px;font-weight:800;color:'+c.color+';text-align:'+c.align+';letter-spacing:1.2px;">'+c.text+'</div>';
  }).join('');
  card.appendChild(colHeader);

  // ── Rows ──────────────────────────────────────────────────────────
  var tableDiv = document.createElement('div');
  tableDiv.style.cssText = 'padding:4px 0 8px;';

  rows.forEach(function(r, i){
    var t = getTeamById(r.id);
    var pos = i+1;

    // Position color
    var posColor = pos===1 ? '#FFD700' : pos<=3 ? '#00C853' : pos<=6 ? '#2979FF' : 'rgba(255,255,255,.4)';
    var posGlow  = pos===1 ? '0 0 8px rgba(255,215,0,.4)' : pos<=3 ? '0 0 6px rgba(0,200,83,.3)' : 'none';

    // Row background
    var rowBg = pos===1
      ? 'linear-gradient(90deg,rgba(255,215,0,.06) 0%,transparent 70%)'
      : i%2===0 ? 'rgba(255,255,255,.015)' : 'transparent';

    // Logo
    var logoHtml;
    var logoSrc = t ? (t.logoUrl||t.logo||'') : '';
    if(logoSrc && logoSrc.startsWith('http')){
      logoHtml = '<img src="'+logoSrc+'" style="width:28px;height:28px;border-radius:50%;object-fit:cover;border:1px solid rgba(0,200,83,.3);" crossorigin="anonymous">';
    } else {
      var emoji = logoSrc||'&#9917;';
      logoHtml = '<div style="width:28px;height:28px;border-radius:50%;background:rgba(0,200,83,.12);border:1px solid rgba(0,200,83,.25);display:flex;align-items:center;justify-content:center;font-size:14px;">'+emoji+'</div>';
    }

    // Form dots
    var formDots = (r.form||[]).slice(-3).map(function(f){
      var fc = f==='fw'?'#00C853':f==='ld'?'#FF3D3D':'#FFD600';
      var fl = f==='fw'?'W':f==='ld'?'L':'D';
      return '<div style="width:16px;height:16px;border-radius:50%;background:'+fc+';display:flex;align-items:center;justify-content:center;flex-shrink:0;">'
        + '<span style="font-size:8px;font-weight:900;color:#000;">'+fl+'</span></div>';
    }).join('');

    // GD color
    var gd = (r.gf||0)-(r.ga||0);
    var gdColor = gd>0 ? '#00C853' : gd<0 ? '#FF3D3D' : 'rgba(255,255,255,.5)';
    var gdText = (gd>0?'+':'')+gd;

    // Team name color/weight for top 3
    var teamColor = pos<=3 ? '#FFFFFF' : 'rgba(255,255,255,.8)';

    var row = document.createElement('div');
    row.style.cssText = 'padding:9px 30px;display:grid;grid-template-columns:30px 36px 1fr 40px 40px 40px 40px 50px 55px 80px;gap:0;align-items:center;background:'+rowBg+';border-bottom:1px solid rgba(255,255,255,.04);transition:all .2s;';

    row.innerHTML =
      // Pos
      '<div style="font-family:Arial,sans-serif;font-size:15px;font-weight:900;color:'+posColor+';text-align:center;text-shadow:'+posGlow+';">'+pos+'</div>'
      // Logo
      + '<div style="display:flex;align-items:center;justify-content:center;">'+logoHtml+'</div>'
      // Team name
      + '<div style="font-size:13px;font-weight:700;color:'+teamColor+';letter-spacing:.3px;padding-left:4px;">'+esc(r.name)+'</div>'
      // MP
      + '<div style="font-size:13px;color:rgba(255,255,255,.5);text-align:center;font-weight:600;">'+r.p+'</div>'
      // W
      + '<div style="font-size:13px;color:#00C853;text-align:center;font-weight:700;">'+r.w+'</div>'
      // D
      + '<div style="font-size:13px;color:#FFD600;text-align:center;font-weight:700;">'+r.d+'</div>'
      // L
      + '<div style="font-size:13px;color:#FF3D3D;text-align:center;font-weight:700;">'+r.l+'</div>'
      // GD
      + '<div style="font-size:13px;color:'+gdColor+';text-align:center;font-weight:700;">'+gdText+'</div>'
      // Form
      + '<div style="display:flex;gap:3px;align-items:center;justify-content:center;">'+formDots+'</div>'
      // PTS
      + '<div style="text-align:center;">'
        + '<span style="font-family:Arial,sans-serif;font-size:'+(pos===1?'22':'18')+'px;font-weight:900;color:'+(pos===1?'#FFD700':'#00C853')+';text-shadow:'+(pos===1?'0 0 12px rgba(255,215,0,.5)':'none')+';">'+r.pts+'</span>'
      + '</div>';

    tableDiv.appendChild(row);
  });

  card.appendChild(tableDiv);

  // ── Footer ────────────────────────────────────────────────────────
  var footerDiv = document.createElement('div');
  footerDiv.style.cssText = 'padding:14px 30px;display:flex;align-items:center;justify-content:space-between;border-top:1px solid rgba(0,200,83,.12);background:rgba(0,200,83,.03);';
  footerDiv.innerHTML =
    '<div style="display:flex;align-items:center;gap:8px;">'
      + '<div style="width:3px;height:16px;background:linear-gradient(180deg,#00C853,#009624);border-radius:2px;"></div>'
      + '<div style="font-size:10px;color:rgba(0,200,83,.5);letter-spacing:1.5px;text-transform:uppercase;font-weight:700;">W = Win &nbsp; D = Draw &nbsp; L = Loss &nbsp; GD = Goal Diff</div>'
    + '</div>'
    + '<div style="font-size:10px;color:rgba(255,255,255,.2);letter-spacing:.5px;">JPL © 2026</div>';
  card.appendChild(footerDiv);

  document.body.appendChild(card);

  // Wait for images to load, then render
  var imgs = card.querySelectorAll('img');
  var imgCount = imgs.length;
  if(imgCount === 0){
    renderStandingsCanvas(card);
  } else {
    var loaded = 0;
    imgs.forEach(function(img){
      if(img.complete){
        loaded++;
        if(loaded === imgCount) renderStandingsCanvas(card);
      } else {
        img.onload = img.onerror = function(){
          loaded++;
          if(loaded === imgCount) renderStandingsCanvas(card);
        };
      }
    });
  }
}

function renderStandingsCanvas(card){
  html2canvas(card, {
    backgroundColor: '#050c07',
    scale: 2.5,
    useCORS: true,
    allowTaint: true,
    logging: false,
    onclone: function(doc, el){
      el.style.top  = '0';
      el.style.left = '0';
      el.style.position = 'relative';
    }
  }).then(function(canvas){
    document.body.removeChild(card);
    var link = document.createElement('a');
    link.download = 'JPL_Season1_Standings.jpg';
    link.href = canvas.toDataURL('image/jpeg', 0.97);
    link.click();
  }).catch(function(err){
    if(document.body.contains(card)) document.body.removeChild(card);
    console.error('Standings JPG error:', err);
    alert('JPG generation failed. Try again.');
  });
}

// ════════════════════════════════════════════
// PLAYER RANKING JPG — 10 players per card
// No emoji, use SVG icons
// ════════════════════════════════════════════
function downloadRankingJPG(page){
  if(typeof html2canvas==='undefined'){alert('html2canvas not loaded.');return;}

  var ps=getPlayers().slice().sort(function(a,b){
    return realCalcPts(computeStatsFromHistory(b.id))-realCalcPts(computeStatsFromHistory(a.id));
  });
  var startIdx=(page-1)*10;
  var slice=ps.slice(startIdx,startIdx+10);
  if(!slice.length){alert('No players in this range.');return;}
  var rangeLabel='#'+(startIdx+1)+' - #'+(startIdx+slice.length);

  /* ── CARD WRAPPER ── */
  var wrap=document.createElement('div');
  wrap.style.cssText=[
    'position:fixed','top:-9999px','left:-9999px',
    'width:660px',
    'background:#0d0d0d',
    'border-radius:20px',
    'overflow:hidden',
    'font-family:Arial,Helvetica,sans-serif',
    'border:1px solid rgba(220,30,30,.35)',
    'box-shadow:0 0 0 1px rgba(220,30,30,.15),0 0 40px rgba(180,0,0,.2)'
  ].join(';');

  /* glow top-right */
  var g1=document.createElement('div');
  g1.style.cssText='position:absolute;top:-80px;right:-80px;width:300px;height:300px;background:radial-gradient(circle,rgba(200,0,0,.18) 0%,transparent 70%);pointer-events:none;z-index:1;';
  wrap.appendChild(g1);
  /* glow bottom-left */
  var g2=document.createElement('div');
  g2.style.cssText='position:absolute;bottom:-60px;left:-60px;width:220px;height:220px;background:radial-gradient(circle,rgba(200,0,0,.1) 0%,transparent 70%);pointer-events:none;z-index:1;';
  wrap.appendChild(g2);

  var inner=document.createElement('div');
  inner.style.cssText='position:relative;z-index:2;';

  /* ── HEADER ── */
  var hdr=document.createElement('div');
  hdr.style.cssText=[
    'padding:22px 26px 18px',
    'display:flex','align-items:center','justify-content:space-between',
    'background:linear-gradient(90deg,rgba(180,0,0,.25) 0%,rgba(180,0,0,.06) 60%,transparent 100%)',
    'border-bottom:1px solid rgba(220,30,30,.2)'
  ].join(';');
  hdr.innerHTML=
    '<div style="display:flex;align-items:center;gap:14px;">'
      +'<div style="width:46px;height:46px;background:linear-gradient(135deg,#DC1E1E,#8B0000);border-radius:12px;display:flex;align-items:center;justify-content:center;box-shadow:0 0 18px rgba(220,30,30,.6),0 4px 12px rgba(0,0,0,.5);">'
        +'<svg width="22" height="22" viewBox="0 0 24 24" fill="#fff"><path d="M5 3h14a1 1 0 0 1 1 1v1H4V4a1 1 0 0 1 1-1zm-1 4h16v13a1 1 0 0 1-1 1H5a1 1 0 0 1-1-1V7zm3 2v2h2V9H7zm0 4v2h2v-2H7zm4-4v2h2V9h-2zm0 4v2h2v-2h-2zm4-4v2h2V9h-2zm0 4v2h2v-2h-2z"/></svg>'
      +'</div>'
      +'<div>'
        +'<div style="font-size:19px;font-weight:900;color:#FFFFFF;letter-spacing:2px;line-height:1;text-shadow:0 0 20px rgba(220,30,30,.4);">JPL PLAYER RANKINGS</div>'
        +'<div style="font-size:10px;color:#FF4444;letter-spacing:3px;text-transform:uppercase;margin-top:5px;font-weight:700;opacity:.85;">SEASON 1 &nbsp;&#9645;&nbsp; '+rangeLabel+'</div>'
      +'</div>'
    +'</div>'
    +'<div style="text-align:right;font-size:10px;color:rgba(255,255,255,.2);letter-spacing:1px;font-weight:600;">'+new Date().toLocaleDateString('en-GB',{day:'2-digit',month:'short',year:'numeric'}).toUpperCase()+'</div>';
  inner.appendChild(hdr);

  /* ── COL HEADERS ── */
  var colH=document.createElement('div');
  colH.style.cssText='padding:8px 26px;display:grid;grid-template-columns:28px 32px 1fr 30px 30px 30px 38px 38px 48px 60px;gap:2px;align-items:center;background:rgba(220,30,30,.07);border-bottom:1px solid rgba(220,30,30,.12);';
  var cDefs=[
    {t:'#',c:'rgba(255,100,100,.6)'},{t:'',c:'transparent'},{t:'PLAYER',c:'rgba(255,100,100,.6)',l:true},
    {t:'MP',c:'rgba(255,255,255,.3)'},{t:'W',c:'rgba(80,220,120,.6)'},{t:'D',c:'rgba(255,214,0,.55)'},
    {t:'L',c:'rgba(255,80,80,.7)'},{t:'GF',c:'rgba(180,210,255,.55)'},{t:'COND',c:'rgba(255,255,255,.3)'},
    {t:'PTS',c:'#FF4444'}
  ];
  colH.innerHTML=cDefs.map(function(c){
    return '<div style="font-size:8px;font-weight:800;color:'+c.c+';text-align:'+(c.l?'left':'center')+';letter-spacing:1.3px;">'+c.t+'</div>';
  }).join('');
  inner.appendChild(colH);

  /* ── ROWS ── */
  var condGrads={
    'cond-ap':'linear-gradient(135deg,#FFD700,#FFA000)','cond-a':'linear-gradient(135deg,#00C853,#009624)',
    'cond-bp':'linear-gradient(135deg,#2979FF,#1565C0)','cond-bm':'linear-gradient(135deg,#4FC3F7,#0277BD)',
    'cond-c':'linear-gradient(135deg,#78909C,#546E7A)','cond-d':'linear-gradient(135deg,#FF6D00,#E65100)',
    'cond-e':'linear-gradient(135deg,#FF3D3D,#B71C1C)'
  };

  var bodyDiv=document.createElement('div');
  bodyDiv.style.cssText='padding:3px 0 6px;';

  slice.forEach(function(p,i){
    var rank=startIdx+i+1;
    var s=computeStatsFromHistory(p.id);
    var wr=winRatio(s), cond=getCondition(wr), pts=realCalcPts(s);
    var t=getTeamById(p.teamId);

    /* Row styling */
    var rowBg,nameStyle,rankColor,rankGlow,leftBar,ptsBg,ptsColor,ptsGlow;
    if(rank===1){
      rowBg='linear-gradient(90deg,rgba(220,30,30,.18) 0%,rgba(220,30,30,.05) 60%,transparent 100%)';
      nameStyle='font-size:13px;font-weight:900;color:#FFFFFF;letter-spacing:.5px;text-shadow:0 0 12px rgba(255,255,255,.3);';
      rankColor='#FF4444';rankGlow='0 0 12px rgba(255,68,68,.7)';
      leftBar='#DC1E1E';ptsBg='linear-gradient(135deg,#DC1E1E,#8B0000)';ptsColor='#fff';ptsGlow='0 0 16px rgba(220,30,30,.6)';
    } else if(rank===2){
      rowBg='linear-gradient(90deg,rgba(180,180,180,.1) 0%,transparent 60%)';
      nameStyle='font-size:13px;font-weight:800;color:#F5F5F5;';
      rankColor='#D0D0D0';rankGlow='0 0 8px rgba(200,200,200,.3)';
      leftBar='#AAAAAA';ptsBg='linear-gradient(135deg,#666,#bbb)';ptsColor='#fff';ptsGlow='none';
    } else if(rank===3){
      rowBg='linear-gradient(90deg,rgba(205,127,50,.1) 0%,transparent 60%)';
      nameStyle='font-size:13px;font-weight:800;color:#F0E0C8;';
      rankColor='#CD9B3A';rankGlow='0 0 8px rgba(205,127,50,.4)';
      leftBar='#CD9B3A';ptsBg='linear-gradient(135deg,#7a4a10,#CD9B3A)';ptsColor='#fff';ptsGlow='none';
    } else {
      rowBg=i%2===0?'rgba(255,255,255,.022)':'transparent';
      /* Alternate name highlight: red tint for even rows */
      nameStyle=i%2===0
        ?'font-size:12.5px;font-weight:700;color:#FFD0D0;'
        :'font-size:12.5px;font-weight:700;color:rgba(255,255,255,.88);';
      rankColor='rgba(255,255,255,.35)';rankGlow='none';
      leftBar='transparent';ptsBg='transparent';ptsColor='#FF4444';ptsGlow='0 0 8px rgba(220,30,30,.4)';
    }

    /* Photo */
    var pSrc=p.photoUrl||p.photo||'';
    var photoHtml=pSrc&&pSrc.startsWith('http')
      ?'<img src="'+pSrc+'" style="width:28px;height:28px;border-radius:50%;object-fit:cover;border:1.5px solid rgba(220,30,30,.4);" crossorigin="anonymous">'
      :'<div style="width:28px;height:28px;border-radius:50%;background:rgba(220,30,30,.1);border:1.5px solid rgba(220,30,30,.3);display:flex;align-items:center;justify-content:center;"><svg width="14" height="14" viewBox="0 0 24 24" fill="rgba(255,100,100,.6)"><path d="M12 12c2.7 0 4.8-2.1 4.8-4.8S14.7 2.4 12 2.4 7.2 4.5 7.2 7.2 9.3 12 12 12zm0 2.4c-3.2 0-9.6 1.6-9.6 4.8v2.4h19.2v-2.4c0-3.2-6.4-4.8-9.6-4.8z"/></svg></div>';

    /* Team logo */
    var tSrc=t?(t.logoUrl||t.logo||''):'';
    var tLogo=tSrc&&tSrc.startsWith('http')
      ?'<img src="'+tSrc+'" style="width:12px;height:12px;border-radius:50%;object-fit:cover;vertical-align:middle;" crossorigin="anonymous">'
      :'<svg width="10" height="10" viewBox="0 0 24 24" fill="rgba(220,30,30,.45)"><circle cx="12" cy="12" r="10"/></svg>';
    var tName=t?(t.name.length>13?t.name.slice(0,12)+'.':t.name):'';

    /* Condition badge */
    var cBg=condGrads[cond.cls]||condGrads['cond-c'];
    var condBadge=s.mp>=3
      ?'<div style="background:'+cBg+';border-radius:5px;padding:2px 6px;display:inline-block;"><span style="font-size:9px;font-weight:900;color:#fff;letter-spacing:.5px;">'+cond.label+'</span></div>'
      :'<div style="background:rgba(255,255,255,.06);border:1px solid rgba(255,255,255,.1);border-radius:5px;padding:2px 6px;"><span style="font-size:9px;font-weight:700;color:rgba(255,255,255,.3);">NEW</span></div>';

    /* PTS cell */
    var ptsCellHtml=ptsBg!=='transparent'
      ?'<div style="background:'+ptsBg+';border-radius:8px;padding:4px 8px;display:inline-block;box-shadow:'+ptsGlow+';"><span style="font-size:'+(rank===1?'20':'17')+'px;font-weight:900;color:'+ptsColor+';line-height:1;">'+pts+'</span></div>'
      :'<span style="font-size:17px;font-weight:900;color:'+ptsColor+';text-shadow:'+ptsGlow+';">'+pts+'</span>';

    var row=document.createElement('div');
    row.style.cssText='padding:7px 26px;display:grid;grid-template-columns:28px 32px 1fr 30px 30px 30px 38px 38px 48px 60px;gap:2px;align-items:center;background:'+rowBg+';border-bottom:1px solid rgba(255,255,255,.04);position:relative;';
    row.innerHTML=
      (rank<=3?'<div style="position:absolute;left:0;top:0;bottom:0;width:3px;background:'+leftBar+';box-shadow:0 0 8px '+leftBar+';border-radius:0 2px 2px 0;"></div>':'')
      /* Rank # */
      +'<div style="font-size:14px;font-weight:900;color:'+rankColor+';text-align:center;text-shadow:'+rankGlow+';">'+rank+'</div>'
      /* Photo */
      +'<div style="display:flex;align-items:center;justify-content:center;">'+photoHtml+'</div>'
      /* Name + team */
      +'<div style="padding-left:4px;">'
        +'<div style="'+nameStyle+'word-break:break-word;line-height:1.25;">'+esc(p.name)+'</div>'
        +'<div style="display:flex;align-items:center;gap:3px;margin-top:2px;">'+tLogo+'<span style="font-size:9px;color:rgba(255,255,255,.3);">'+esc(tName)+'</span></div>'
      +'</div>'
      /* MP */
      +'<div style="font-size:11.5px;color:rgba(255,255,255,.38);text-align:center;font-weight:600;">'+s.mp+'</div>'
      /* W */
      +'<div style="font-size:12px;color:#50DC78;text-align:center;font-weight:800;">'+s.wins+'</div>'
      /* D */
      +'<div style="font-size:12px;color:#FFD600;text-align:center;font-weight:800;">'+s.draws+'</div>'
      /* L */
      +'<div style="font-size:12px;color:#FF4444;text-align:center;font-weight:800;">'+s.losses+'</div>'
      /* GF */
      +'<div style="font-size:12px;color:#B4D4FF;text-align:center;font-weight:800;">'+s.gf+'</div>'
      /* COND */
      +'<div style="text-align:center;">'+condBadge+'</div>'
      /* PTS */
      +'<div style="display:flex;align-items:center;justify-content:center;">'+ptsCellHtml+'</div>';

    bodyDiv.appendChild(row);
  });
  inner.appendChild(bodyDiv);

  /* ── FOOTER ── */
  var foot=document.createElement('div');
  foot.style.cssText='padding:12px 26px;display:flex;align-items:center;justify-content:space-between;border-top:1px solid rgba(220,30,30,.15);background:rgba(0,0,0,.2);';
  foot.innerHTML=
    '<div style="font-size:9px;color:rgba(220,30,30,.4);letter-spacing:1.2px;font-weight:700;text-transform:uppercase;">W(10)+D(5)+L(-10)+GF-GC+MOTM(5)+CS(2) x Condition Boost</div>'
    +'<div style="font-size:9px;color:rgba(255,255,255,.15);letter-spacing:2px;font-weight:700;">JPL 2026</div>';
  inner.appendChild(foot);
  wrap.appendChild(inner);
  document.body.appendChild(wrap);

  var imgs=wrap.querySelectorAll('img');
  var loaded=0,total=imgs.length;
  function doRender(){
    html2canvas(wrap,{
      backgroundColor:'#0d0d0d',scale:2.5,useCORS:true,allowTaint:true,logging:false,
      onclone:function(doc,el){el.style.top='0';el.style.left='0';el.style.position='relative';}
    }).then(function(canvas){
      document.body.removeChild(wrap);
      var a=document.createElement('a');
      a.download='JPL_Rankings_'+rangeLabel.replace(/[#\-\s]/g,'')+'.jpg';
      a.href=canvas.toDataURL('image/jpeg',0.97);
      a.click();
    }).catch(function(e){
      if(document.body.contains(wrap)) document.body.removeChild(wrap);
      alert('JPG error: '+e.message);
    });
  }
  if(total===0){doRender();}
  else{imgs.forEach(function(img){
    if(img.complete){loaded++;if(loaded===total)doRender();}
    else{img.onload=img.onerror=function(){loaded++;if(loaded===total)doRender();};}
  });}
}


function mp_3plus(s){ return (s.mp||0)>=3; }

// ════════════════════════════════════════════
// PLAYER STAT CARD — Download JPG (Social Media)
// ════════════════════════════════════════════
function downloadPlayerCardJPG(pid){
  if(typeof html2canvas==='undefined'){alert('html2canvas not loaded.');return;}
  var p=getPlayers().find(function(pl){return pl.id===pid;});
  if(!p){alert('Player not found');return;}
  var s=computeStatsFromHistory(pid);
  var t=getTeamById(p.teamId);
  var wr=winRatio(s), cond=getCondition(wr), pts=realCalcPts(s);
  var history=getPlayerAllMatchHistory(pid).slice(0,5);

  var condGrads={
    'cond-ap':'linear-gradient(135deg,#FFD700,#FFA000)','cond-a':'linear-gradient(135deg,#00E664,#009624)',
    'cond-bp':'linear-gradient(135deg,#2979FF,#1565C0)','cond-bm':'linear-gradient(135deg,#4FC3F7,#0277BD)',
    'cond-c':'linear-gradient(135deg,#78909C,#546E7A)','cond-d':'linear-gradient(135deg,#FF6D00,#E65100)',
    'cond-e':'linear-gradient(135deg,#FF3D3D,#B71C1C)'
  };
  var cBg=condGrads[cond.cls]||condGrads['cond-c'];
  var catColors={local:'#00E664',youth:'#2979FF',invited:'#FF6B35'};
  var catC=catColors[p.cat||'local']||'#00E664';

  /* Form dots */
  var formDots=history.map(function(h){
    var bc=h.result==='W'?'#00E664':h.result==='L'?'#FF4444':'#FFD600';
    var sc=h.result==='W'?'rgba(0,230,100,.2)':h.result==='L'?'rgba(255,68,68,.2)':'rgba(255,214,0,.2)';
    var label=h.result;
    return '<div style="width:28px;height:28px;border-radius:50%;background:'+bc+';box-shadow:0 0 8px '+bc+';display:flex;align-items:center;justify-content:center;flex-shrink:0;">'
      +'<span style="font-size:11px;font-weight:900;color:#000;">'+label+'</span></div>';
  }).join('');

  /* Stat blocks */
  var stats=[
    {label:'MATCHES',value:s.mp,color:'rgba(255,255,255,.7)'},
    {label:'WINS',value:s.wins,color:'#00E664'},
    {label:'DRAWS',value:s.draws,color:'#FFD600'},
    {label:'LOSSES',value:s.losses,color:'#FF4444'},
    {label:'GOALS',value:s.gf,color:'#7CB9FF'},
    {label:'CONCEDED',value:s.ga,color:'#FF8A50'},
    {label:'CLEAN SH',value:s.cs,color:'#00E664'},
    {label:'MOTM',value:s.motm,color:'#FFD600'},
  ];
  var statGrid=stats.map(function(st){
    return '<div style="background:rgba(0,0,0,.25);border:1px solid rgba(0,230,100,.1);border-radius:10px;padding:10px 8px;text-align:center;">'
      +'<div style="font-size:20px;font-weight:900;color:'+st.color+';line-height:1;text-shadow:0 0 10px '+st.color+'44;">'+st.value+'</div>'
      +'<div style="font-size:8px;color:rgba(255,255,255,.35);letter-spacing:1.2px;text-transform:uppercase;margin-top:4px;font-weight:700;">'+st.label+'</div>'
      +'</div>';
  }).join('');

  var wrap=document.createElement('div');
  wrap.style.cssText=[
    'position:fixed','top:-9999px','left:-9999px',
    'width:520px',
    'background:linear-gradient(145deg,#021B13 0%,#032D1C 50%,#021810 100%)',
    'border-radius:22px','overflow:hidden',
    'font-family:Arial,Helvetica,sans-serif',
    'box-shadow:0 0 0 2px rgba(0,230,100,.2),0 0 60px rgba(0,230,100,.1)'
  ].join(';');

  /* Glow effects */
  wrap.innerHTML=
    '<div style="position:absolute;top:-100px;left:-80px;width:350px;height:350px;background:radial-gradient(circle,rgba(0,230,100,.12) 0%,transparent 70%);pointer-events:none;z-index:1;"></div>'
    +'<div style="position:absolute;bottom:-60px;right:-60px;width:250px;height:250px;background:radial-gradient(circle,rgba('+
      (cond.cls==='cond-ap'?'255,215,0':cond.cls==='cond-e'?'255,60,60':'0,150,255')+
    ',.07) 0%,transparent 70%);pointer-events:none;z-index:1;"></div>';

  var inner=document.createElement('div');
  inner.style.cssText='position:relative;z-index:2;';

  /* ── Top bar: Team name strip ── */
  var tSrc=t?(t.logoUrl||t.logo||''):'';
  var tLogoHtml=tSrc&&tSrc.startsWith('http')
    ?'<img src="'+tSrc+'" style="width:20px;height:20px;border-radius:50%;object-fit:cover;vertical-align:middle;" crossorigin="anonymous">'
    :'<svg width="16" height="16" viewBox="0 0 24 24" fill="rgba(0,230,100,.5)"><circle cx="12" cy="12" r="10"/></svg>';

  var catLabel=(p.cat||'local').toUpperCase();

  var topBar=document.createElement('div');
  topBar.style.cssText='padding:10px 22px;display:flex;align-items:center;justify-content:space-between;background:rgba(0,230,100,.07);border-bottom:1px solid rgba(0,230,100,.12);';
  topBar.innerHTML=
    '<div style="display:flex;align-items:center;gap:8px;">'+tLogoHtml+'<span style="font-size:11px;font-weight:700;color:rgba(255,255,255,.55);letter-spacing:.5px;">'+(t?esc(t.name):'')+'</span></div>'
    +'<div style="display:flex;align-items:center;gap:6px;">'
      +'<span style="background:'+catC+'22;color:'+catC+';border:1px solid '+catC+'55;border-radius:5px;padding:2px 8px;font-size:9px;font-weight:800;letter-spacing:1px;">'+catLabel+'</span>'
      +'<span style="font-size:10px;color:rgba(255,255,255,.22);letter-spacing:1px;">'+new Date().toLocaleDateString('en-GB',{day:'2-digit',month:'short'}).toUpperCase()+'</span>'
    +'</div>';
  inner.appendChild(topBar);

  /* ── Hero section: Photo + Name + PTS ── */
  var hero=document.createElement('div');
  hero.style.cssText='padding:22px 22px 16px;display:flex;align-items:center;gap:18px;';
  var pSrc=p.photoUrl||p.photo||'';
  var heroPhoto=pSrc&&pSrc.startsWith('http')
    ?'<img src="'+pSrc+'" style="width:84px;height:84px;border-radius:50%;object-fit:cover;border:3px solid rgba(0,230,100,.4);box-shadow:0 0 20px rgba(0,230,100,.25),0 4px 16px rgba(0,0,0,.5);" crossorigin="anonymous">'
    :'<div style="width:84px;height:84px;border-radius:50%;background:rgba(0,230,100,.08);border:3px solid rgba(0,230,100,.3);display:flex;align-items:center;justify-content:center;flex-shrink:0;"><svg width="40" height="40" viewBox="0 0 24 24" fill="rgba(0,230,100,.4)"><path d="M12 12c2.7 0 4.8-2.1 4.8-4.8S14.7 2.4 12 2.4 7.2 4.5 7.2 7.2 9.3 12 12 12zm0 2.4c-3.2 0-9.6 1.6-9.6 4.8v2.4h19.2v-2.4c0-3.2-6.4-4.8-9.6-4.8z"/></svg></div>';

  hero.innerHTML=heroPhoto
    +'<div style="flex:1;">'
      +'<div style="font-size:24px;font-weight:900;color:#fff;letter-spacing:.5px;line-height:1.1;text-shadow:0 2px 12px rgba(0,0,0,.5);">'+esc(p.name)+'</div>'
      +'<div style="margin-top:6px;display:flex;align-items:center;gap:8px;">'
        +'<div style="background:'+cBg+';border-radius:8px;padding:4px 12px;display:inline-flex;align-items:center;gap:5px;box-shadow:0 2px 10px rgba(0,0,0,.4);">'
          +'<span style="font-size:12px;font-weight:900;color:#fff;letter-spacing:.5px;">'+cond.label+'</span>'
          +'<span style="font-size:10px;color:rgba(255,255,255,.75);">x'+cond.boost+'</span>'
        +'</div>'
        +'<span style="font-size:10px;color:rgba(255,255,255,.35);">'+wr+'% WR</span>'
      +'</div>'
    +'</div>'
    +'<div style="text-align:center;background:rgba(0,0,0,.3);border:1px solid rgba(0,230,100,.2);border-radius:14px;padding:12px 18px;box-shadow:0 0 20px rgba(0,230,100,.1);">'
      +'<div style="font-size:42px;font-weight:900;color:#00E664;line-height:1;text-shadow:0 0 20px rgba(0,230,100,.5);">'+pts+'</div>'
      +'<div style="font-size:9px;color:rgba(0,230,100,.6);letter-spacing:2px;font-weight:700;margin-top:3px;">POINTS</div>'
    +'</div>';
  inner.appendChild(hero);

  /* ── Stat grid ── */
  var sgDiv=document.createElement('div');
  sgDiv.style.cssText='padding:0 22px 18px;display:grid;grid-template-columns:repeat(4,1fr);gap:6px;';
  sgDiv.innerHTML=statGrid;
  inner.appendChild(sgDiv);

  /* ── Form strip ── */
  if(history.length){
    var formDiv=document.createElement('div');
    formDiv.style.cssText='padding:0 22px 16px;';
    formDiv.innerHTML=
      '<div style="background:rgba(0,0,0,.25);border:1px solid rgba(0,230,100,.1);border-radius:12px;padding:12px 16px;">'
        +'<div style="font-size:9px;color:rgba(0,230,100,.5);letter-spacing:2px;font-weight:800;margin-bottom:10px;text-transform:uppercase;">Recent Form</div>'
        +'<div style="display:flex;gap:8px;align-items:center;">'+formDots+'</div>'
      +'</div>';
    inner.appendChild(formDiv);
  }

  /* ── Footer ── */
  var foot=document.createElement('div');
  foot.style.cssText='padding:10px 22px;display:flex;align-items:center;justify-content:space-between;border-top:1px solid rgba(0,230,100,.1);background:rgba(0,0,0,.15);';
  foot.innerHTML=
    '<div style="font-size:10px;color:rgba(0,230,100,.35);letter-spacing:2px;font-weight:700;">JUVENILE PREMIER LEAGUE</div>'
    +'<div style="font-size:9px;color:rgba(0,230,100,.18);letter-spacing:1.5px;">JPL OFFICIAL 2026</div>';
  inner.appendChild(foot);
  wrap.appendChild(inner);
  document.body.appendChild(wrap);

  var imgs=wrap.querySelectorAll('img');
  var loaded=0,total=imgs.length;
  function doRender(){
    html2canvas(wrap,{
      backgroundColor:'#021B13',scale:2.5,useCORS:true,allowTaint:true,logging:false,
      onclone:function(doc,el){el.style.top='0';el.style.left='0';el.style.position='relative';}
    }).then(function(canvas){
      document.body.removeChild(wrap);
      var a=document.createElement('a');
      a.download='JPL_Player_'+p.name.replace(/\s/g,'_')+'.jpg';
      a.href=canvas.toDataURL('image/jpeg',0.97);
      a.click();
    }).catch(function(e){
      if(document.body.contains(wrap)) document.body.removeChild(wrap);
      alert('JPG error: '+e.message);
    });
  }
  if(total===0){doRender();}
  else{imgs.forEach(function(img){
    if(img.complete){loaded++;if(loaded===total)doRender();}
    else{img.onload=img.onerror=function(){loaded++;if(loaded===total)doRender();};}
  });}
}

// ════════════════════════════════════════════
// SIGNING CARD — Best / Flop (Value = pts÷bid×100)
// ════════════════════════════════════════════
function downloadSigningJPG(mode){
  if(typeof html2canvas==='undefined'){alert('html2canvas not loaded.');return;}

  // Only players WITH a bid price
  var allPs = getPlayers().filter(function(p){ return (p.bid||0)>0; });
  if(!allPs.length){ alert('No players with bid price set yet.'); return; }

  // Compute value score = (pts / bid) * 100
  allPs = allPs.map(function(p){
    var s = computeStatsFromHistory(p.id);
    var pts = realCalcPts(s);
    var val = pts>0 ? Math.round((pts / (p.bid||1)) * 100) : (pts<0 ? -1 : 0);
    return Object.assign({},p,{_pts:pts,_val:val,_s:s});
  });

  // Sort: best = highest value, flop = lowest value
  allPs.sort(function(a,b){ return mode==='best' ? b._val-a._val : a._val-b._val; });
  var slice = allPs.slice(0,10);

  var isBest = mode==='best';
  var accentColor   = isBest ? '#FFD700' : '#888888';
  var accentGlow    = isBest ? 'rgba(255,215,0,.5)' : 'rgba(150,150,150,.3)';
  var bgGrad        = isBest ? 'linear-gradient(145deg,#0d0a00 0%,#1a1200 50%,#0d0a00 100%)' : 'linear-gradient(145deg,#0d0d0d 0%,#111 50%,#0d0d0d 100%)';
  var headerGrad    = isBest ? 'linear-gradient(90deg,rgba(255,215,0,.2) 0%,rgba(255,215,0,.05) 60%,transparent 100%)' : 'linear-gradient(90deg,rgba(100,100,100,.18) 0%,rgba(80,80,80,.05) 60%,transparent 100%)';
  var iconBg        = isBest ? 'linear-gradient(135deg,#FFD700,#A67C00)' : 'linear-gradient(135deg,#555,#222)';
  var titleColor    = isBest ? '#FFD700' : '#AAAAAA';
  var subColor      = isBest ? '#FFC200' : '#777';
  var borderColor   = isBest ? 'rgba(255,215,0,.3)' : 'rgba(120,120,120,.25)';
  var glowColor     = isBest ? 'rgba(180,130,0,.2)' : 'rgba(80,80,80,.15)';
  var cardTitle     = isBest ? 'BEST SIGNINGS' : 'FLOP SIGNINGS';
  var cardSub       = isBest ? 'TOP 10 — VALUE / COIN' : 'BOTTOM 10 — LOWEST VALUE';

  var wrap = document.createElement('div');
  wrap.style.cssText = [
    'position:fixed','top:-9999px','left:-9999px',
    'width:640px',
    'background:'+bgGrad,
    'border-radius:20px','overflow:hidden',
    'font-family:Arial,Helvetica,sans-serif',
    'border:1px solid '+borderColor,
    'box-shadow:0 0 0 1px '+borderColor+',0 0 40px '+glowColor
  ].join(';');

  // Glow overlay
  var g1 = document.createElement('div');
  g1.style.cssText = 'position:absolute;top:-80px;right:-60px;width:280px;height:280px;background:radial-gradient(circle,'+accentGlow+' 0%,transparent 70%);pointer-events:none;z-index:1;';
  wrap.appendChild(g1);

  var inner = document.createElement('div');
  inner.style.cssText = 'position:relative;z-index:2;';

  // Header
  var hdr = document.createElement('div');
  hdr.style.cssText = 'padding:22px 26px 17px;display:flex;align-items:center;justify-content:space-between;background:'+headerGrad+';border-bottom:1px solid '+borderColor+';';
  hdr.innerHTML =
    '<div style="display:flex;align-items:center;gap:14px;">'
      +'<div style="width:46px;height:46px;background:'+iconBg+';border-radius:12px;display:flex;align-items:center;justify-content:center;box-shadow:0 0 16px '+accentGlow+',0 4px 12px rgba(0,0,0,.5);">'
        +(isBest
          ?'<svg width="22" height="22" viewBox="0 0 24 24" fill="#000"><path d="M12 2l3.09 6.26L22 9.27l-5 4.87 1.18 6.88L12 17.77l-6.18 3.25L7 14.14 2 9.27l6.91-1.01L12 2z"/></svg>'
          :'<svg width="22" height="22" viewBox="0 0 24 24" fill="#aaa"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm1 15h-2v-2h2v2zm0-4h-2V7h2v6z"/></svg>'
        )
      +'</div>'
      +'<div>'
        +'<div style="font-size:19px;font-weight:900;color:'+titleColor+';letter-spacing:2px;line-height:1;text-shadow:0 0 20px '+accentGlow+';">JPL '+cardTitle+'</div>'
        +'<div style="font-size:10px;color:'+subColor+';letter-spacing:3px;text-transform:uppercase;margin-top:5px;font-weight:700;opacity:.8;">SEASON 1 &nbsp;&#9645;&nbsp; '+cardSub+'</div>'
      +'</div>'
    +'</div>'
    +'<div style="font-size:10px;color:rgba(255,255,255,.2);letter-spacing:1px;font-weight:600;">'+new Date().toLocaleDateString('en-GB',{day:'2-digit',month:'short',year:'numeric'}).toUpperCase()+'</div>';
  inner.appendChild(hdr);

  // Formula note
  var formula = document.createElement('div');
  formula.style.cssText = 'padding:8px 26px;background:rgba(255,255,255,.03);border-bottom:1px solid rgba(255,255,255,.05);font-size:9px;color:rgba(255,255,255,.3);letter-spacing:1px;font-weight:600;';
  formula.textContent = 'VALUE SCORE = (POINTS / BID COINS) x 100';
  inner.appendChild(formula);

  // Col headers
  var colH = document.createElement('div');
  colH.style.cssText = 'padding:8px 26px;display:grid;grid-template-columns:28px 32px 1fr 50px 44px 60px;gap:4px;align-items:center;background:rgba(255,255,255,.03);border-bottom:1px solid rgba(255,255,255,.05);';
  var chs = [{t:'#',c:'rgba(255,255,255,.4)'},{t:'',c:'transparent'},{t:'PLAYER',c:'rgba(255,255,255,.4)',l:true},
    {t:'BID',c:accentColor},{t:'PTS',c:'rgba(255,255,255,.4)'},{t:'VALUE',c:accentColor}];
  colH.innerHTML = chs.map(function(c){
    return '<div style="font-size:8.5px;font-weight:800;color:'+c.c+';text-align:'+(c.l?'left':'center')+';letter-spacing:1.3px;">'+c.t+'</div>';
  }).join('');
  inner.appendChild(colH);

  // Rows
  var bodyDiv = document.createElement('div');
  bodyDiv.style.cssText = 'padding:3px 0 6px;';

  slice.forEach(function(p,i){
    var rank = i+1;
    var s = p._s, pts = p._pts, val = p._val;
    var t = getTeamById(p.teamId);

    var rowBg = rank===1
      ? 'linear-gradient(90deg,rgba(255,215,0,.1) 0%,rgba(255,215,0,.03) 60%,transparent 100%)'
      : i%2===0?'rgba(255,255,255,.022)':'transparent';
    var nameC = rank===1?accentColor:rank<=3?'#fff':'rgba(255,255,255,.82)';
    var leftBar = rank===1?accentColor:rank<=3?'rgba(255,255,255,.3)':'transparent';

    // Photo
    var pSrc = p.photoUrl||p.photo||'';
    var photoHtml = pSrc&&pSrc.startsWith('http')
      ?'<img src="'+pSrc+'" style="width:28px;height:28px;border-radius:50%;object-fit:cover;border:1.5px solid '+accentColor+'44;" crossorigin="anonymous">'
      :'<div style="width:28px;height:28px;border-radius:50%;background:rgba(255,255,255,.06);border:1.5px solid rgba(255,255,255,.15);display:flex;align-items:center;justify-content:center;"><svg width="14" height="14" viewBox="0 0 24 24" fill="rgba(255,255,255,.4)"><path d="M12 12c2.7 0 4.8-2.1 4.8-4.8S14.7 2.4 12 2.4 7.2 4.5 7.2 7.2 9.3 12 12 12zm0 2.4c-3.2 0-9.6 1.6-9.6 4.8v2.4h19.2v-2.4c0-3.2-6.4-4.8-9.6-4.8z"/></svg></div>';

    // Team
    var tSrc=t?(t.logoUrl||t.logo||''):'';
    var tLogo=tSrc&&tSrc.startsWith('http')
      ?'<img src="'+tSrc+'" style="width:12px;height:12px;border-radius:50%;object-fit:cover;vertical-align:middle;" crossorigin="anonymous">'
      :'<svg width="10" height="10" viewBox="0 0 24 24" fill="rgba(255,255,255,.3)"><circle cx="12" cy="12" r="10"/></svg>';
    var tName=t?(t.name.length>14?t.name.slice(0,13)+'.':t.name):'';

    // Value badge
    var valColor = val>=100?'#00E664':val>=50?'#FFD700':val>=0?'#FF8A50':'#FF4444';
    var valBg = rank===1?'background:'+iconBg+';box-shadow:0 0 10px '+accentGlow+';':'background:rgba(255,255,255,.07);';
    var valTextC = rank===1?'#000':''+valColor;

    var row = document.createElement('div');
    row.style.cssText = 'padding:8px 26px;display:grid;grid-template-columns:28px 32px 1fr 50px 44px 60px;gap:4px;align-items:center;background:'+rowBg+';border-bottom:1px solid rgba(255,255,255,.04);position:relative;';
    row.innerHTML =
      (rank<=3?'<div style="position:absolute;left:0;top:0;bottom:0;width:3px;background:'+leftBar+';box-shadow:0 0 6px '+leftBar+';border-radius:0 2px 2px 0;"></div>':'')
      +'<div style="font-size:14px;font-weight:900;color:'+accentColor+';text-align:center;text-shadow:0 0 10px '+accentGlow+';">'+rank+'</div>'
      +'<div style="display:flex;align-items:center;justify-content:center;">'+photoHtml+'</div>'
      +'<div style="padding-left:4px;">'
        +'<div style="font-size:12.5px;font-weight:800;color:'+nameC+';line-height:1.25;word-break:break-word;">'+esc(p.name)+'</div>'
        +'<div style="display:flex;align-items:center;gap:3px;margin-top:2px;">'+tLogo+'<span style="font-size:9px;color:rgba(255,255,255,.3);">'+esc(tName)+'</span></div>'
      +'</div>'
      +'<div style="text-align:center;"><div style="display:inline-flex;align-items:center;gap:3px;background:rgba(255,255,255,.06);border-radius:6px;padding:3px 7px;"><svg width="9" height="9" viewBox="0 0 24 24" fill="'+accentColor+'"><circle cx="12" cy="12" r="9" opacity=".25"/><circle cx="12" cy="12" r="7"/></svg><span style="font-size:12px;font-weight:900;color:'+accentColor+';">'+p.bid+'</span></div></div>'
      +'<div style="font-size:12px;color:'+(pts>0?'#00C853':pts<0?'#FF4444':'rgba(255,255,255,.5)')+';text-align:center;font-weight:800;">'+pts+'</div>'
      +'<div style="text-align:center;"><div style="'+valBg+'border-radius:8px;padding:4px 8px;display:inline-block;"><span style="font-size:14px;font-weight:900;color:'+valTextC+';line-height:1;">'+val+'</span></div></div>';
    bodyDiv.appendChild(row);
  });
  inner.appendChild(bodyDiv);

  // Footer
  var foot = document.createElement('div');
  foot.style.cssText = 'padding:11px 26px;display:flex;align-items:center;justify-content:space-between;border-top:1px solid rgba(255,255,255,.06);background:rgba(0,0,0,.2);';
  foot.innerHTML =
    '<div style="font-size:9px;color:rgba(255,255,255,.2);letter-spacing:1.2px;font-weight:700;">VALUE SCORE 100+ = EXCELLENT &nbsp; 50+ = GOOD &nbsp; 0-50 = BELOW AVG</div>'
    +'<div style="font-size:9px;color:rgba(255,255,255,.15);letter-spacing:2px;font-weight:700;">JPL 2026</div>';
  inner.appendChild(foot);
  wrap.appendChild(inner);
  document.body.appendChild(wrap);

  var imgs=wrap.querySelectorAll('img');
  var loaded=0,total=imgs.length;
  function doRender(){
    html2canvas(wrap,{
      backgroundColor: isBest?'#0d0a00':'#0d0d0d',
      scale:2.5,useCORS:true,allowTaint:true,logging:false,
      onclone:function(doc,el){el.style.top='0';el.style.left='0';el.style.position='relative';}
    }).then(function(canvas){
      document.body.removeChild(wrap);
      var a=document.createElement('a');
      a.download='JPL_'+(isBest?'BestSigning':'FlopSigning')+'.jpg';
      a.href=canvas.toDataURL('image/jpeg',0.97);
      a.click();
    }).catch(function(e){
      if(document.body.contains(wrap)) document.body.removeChild(wrap);
      alert('JPG error: '+e.message);
    });
  }
  if(total===0){doRender();}
  else{imgs.forEach(function(img){
    if(img.complete){loaded++;if(loaded===total)doRender();}
    else{img.onload=img.onerror=function(){loaded++;if(loaded===total)doRender();};}
  });}
}

// ════════════════════════════════════════════
// FALLBACK
// ════════════════════════════════════════════
setTimeout(function(){
  if(!fbReady){
    fbReady=true;
    document.getElementById('fbDot').style.background='var(--acc)';
    document.getElementById('fbTxt').textContent='Offline';
    document.getElementById('loader').classList.add('gone');
    renderHome();
  }
},5000);
</script>
</body>
</html>
