here

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
  listen('player_matches', d => { window.fbPlayerMatches = d; rebuildLocal(); });
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
.section{display:none;padding:1.5rem;max-width:1200px;margin:0 auto;animation:fi .3s;}
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
.twrap{overflow-x:auto;border-radius:12px;border:1px solid var(--border);}
table{width:100%;border-collapse:collapse;font-size:.88rem;}
thead{background:rgba(0,200,83,.07);}
th{padding:.7rem .9rem;text-align:left;font-family:'Barlow Condensed';font-size:.76rem;letter-spacing:1px;text-transform:uppercase;color:var(--green);font-weight:600;white-space:nowrap;}
td{padding:.6rem .9rem;border-top:1px solid var(--border);color:var(--text);}
tr:hover td{background:rgba(0,200,83,.03);}
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
  <div id="newsTicker" style="display:none"></div>
  <h2 class="stitle" style="margin-top:.5rem">🔥 Hot News</h2>
  <div id="newsGrid" class="news-grid"></div>
  <h2 class="stitle">🏆 Featured Players</h2>
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
  <h2 class="stitle">Points Table</h2>
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
  <h2 class="stitle">Player Rankings</h2>
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
    <button onclick="downloadRosterPDF()" style="background:linear-gradient(135deg,rgba(41,121,255,.15),rgba(41,121,255,.08));border:1px solid rgba(41,121,255,.35);color:#7CB9FF;padding:.4rem .9rem;border-radius:8px;cursor:pointer;font-family:'Barlow Condensed';font-size:.82rem;font-weight:700">PDF Download</button>
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
    <button class="atab" onclick="showATab('standings',this)">Standings</button>
    <button class="atab" onclick="showATab('news',this)">News</button>
  </div>
  <div id="adminContent"><div style="color:var(--muted);padding:2rem;text-align:center">Loading…</div></div>
</div>

<script>
// ════════════════════════════════════════════
// STATE
// ════════════════════════════════════════════
var state = { teams:{}, players:{}, fixtures:{}, matches:{}, stats:{}, manual_standings:{}, news:{}, player_matches:{} };
var isAdmin = false;
var captainTeamId = null; // currently logged-in captain's team ID
var pendingMatch = null;
var currentRankType = 'total';
var currentFxFilter = 'all';
var fbReady = false;

window.fbTeams = {}; window.fbPlayers = {}; window.fbFixtures = {};
window.fbMatches = {}; window.fbStats = {}; window.fbManualStandings = {};
window.fbNews = {}; window.fbPlayerMatches = {};

function fb(){ return window._FB || null; }

function rebuildLocal(){
  state.teams    = window.fbTeams   || {};
  state.players  = window.fbPlayers || {};
  state.fixtures = window.fbFixtures|| {};
  state.matches  = window.fbMatches || {};
  state.stats    = window.fbStats   || {};
  state.manual_standings = window.fbManualStandings || {};
  state.news     = window.fbNews    || {};
  state.player_matches = window.fbPlayerMatches || {};
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
function realCalcPts(s){
  if(!s) return 0;
  var w=s.wins||0, l=s.losses||0, d=s.draws||0;
  var gf=s.gf||s.goals||0;
  var gc=s.ga||0;
  return (w*10) + (d*5) + (l*(-10)) + (gf*1) + (gc*(-1)) + ((s.motm||0)*5) + ((s.cs||0)*2);
}
function winRatio(s){
  if(!s) return 0;
  var t=(s.wins||0)+(s.losses||0)+(s.draws||0);
  return t>0?Math.round(((s.wins||0)/t)*100):0;
}
// Goal difference for a player
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
  var players=getPlayers().slice().sort(function(a,b){ return realCalcPts(getStat(b.id))-realCalcPts(getStat(a.id)); }).slice(0,3);
  var medals=[
    {bg:'linear-gradient(135deg,#FFD700,#FFA000)',color:'#000',label:'1st'},
    {bg:'linear-gradient(135deg,#C0C0C0,#9E9E9E)',color:'#000',label:'2nd'},
    {bg:'linear-gradient(135deg,#CD7F32,#8D4E1A)',color:'#fff',label:'3rd'}
  ];
  document.getElementById('featuredPlayers').innerHTML=players.map(function(p,i){
    var s=getStat(p.id); var t=getTeamById(p.teamId); var wr=winRatio(s); var m=medals[i]||medals[2];
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
  var newsItems=generateAutoNews();
  renderNewsTicker(newsItems);
  renderNewsGrid(newsItems);
  renderFeatured();
  document.getElementById('homeFixtures').innerHTML=getFixtures().slice(0,4).map(function(f){return fxHTML(f,true);}).join('');
  renderTournamentBestXI();
}

// ════════════════════════════════════════════
// BEST XI — 4-3-3 formation
// Positions: GK(1) DEF(4) MID(3) FWD(3)
// ════════════════════════════════════════════
function buildBestXI(playerPool){
  // Sort all players by points
  var sorted=playerPool.slice().sort(function(a,b){return realCalcPts(getStat(b.id))-realCalcPts(getStat(a.id));});
  // Assign by position slots: top scorer = FWD, next as MID/DEF/GK heuristic
  // Simply take top 11 in order, assign role labels by slot
  var xi=sorted.slice(0,11);
  var positions=['GK','LB','CB','CB','RB','CM','CM','CAM','LW','ST','RW'];
  return xi.map(function(p,i){ return Object.assign({},p,{pos:positions[i]||'P'}); });
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
  return ps.slice().sort(function(a,b){return realCalcPts(getStat(b.id))-realCalcPts(getStat(a.id));})[0];
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
  var s=getStat(pid);
  var t=getTeamById(p.teamId);
  var wr=winRatio(s);
  var mp=(s.mp||0)||(s.wins||0)+(s.draws||0)+(s.losses||0);
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

  // Populate team filter dropdown
  var sel=document.getElementById('rankTeamFilter');
  if(sel){
    var currentVal=sel.value;
    sel.innerHTML='<option value="">All Teams</option>'+
      getTeams().map(function(t){return '<option value="'+t.id+'"'+(t.id===currentVal?' selected':'')+'>'+esc(t.name)+'</option>';}).join('');
    if(currentVal) sel.value=currentVal;
  }
  var filterTid=sel?sel.value:'';

  var ps=getPlayers().filter(function(p){
    return !filterTid || p.teamId===filterTid;
  }).slice().sort(function(a,b){
    var sa=getStat(a.id), sb=getStat(b.id);
    if(type==='total') return realCalcPts(sb)-realCalcPts(sa);
    if(type==='goals') return (sb.gf||sb.goals||0)-(sa.gf||sa.goals||0);
    if(type==='motm')  return (sb.motm||0)-(sa.motm||0);
    if(type==='cs')    return (sb.cs||0)-(sa.cs||0);
    if(type==='wr')    return winRatio(sb)-winRatio(sa);
    return 0;
  });

  var medals={0:'linear-gradient(135deg,#FFD700,#FFA000)',1:'linear-gradient(135deg,#C0C0C0,#9E9E9E)',2:'linear-gradient(135deg,#CD7F32,#8D4E1A)'};

  document.getElementById('rankList').innerHTML=ps.map(function(p,i){
    var t=getTeamById(p.teamId);
    var s=getStat(p.id);
    var wr=winRatio(s);
    var wins   = s.wins   || 0;
    var draws  = s.draws  || 0;
    var losses = s.losses || 0;
    var mp     = s.mp     || (wins+draws+losses);
    var gf     = s.gf     || s.goals || 0;
    var gc     = s.ga     || 0;
    var gd     = gf - gc;
    var pts    = realCalcPts(s);
    var rankNumBg=medals[i]||'var(--card2)';
    var rankNumColor=i<3?'#000':'var(--muted)';

    // Form bubbles from history (just dots, no text)
    var history=getPlayerAllMatchHistory(p.id).slice(0,8);
    var formHtml='<div style="display:flex;gap:.2rem;align-items:center">';
    if(!history.length){
      formHtml+='<span style="font-size:.62rem;color:var(--muted)">No matches</span>';
    } else {
      history.forEach(function(h){
        var rc=h.result==='W'?'#00C853':h.result==='L'?'#FF3D3D':'#FFD600';
        formHtml+='<div title="'+h.result+'" style="width:10px;height:10px;border-radius:50%;background:'+rc+';flex-shrink:0"></div>';
      });
    }
    formHtml+='</div>';

    // Build stat row depending on active tab
    var statRowHtml='';
    if(type==='total'){
      // MP | W | D | L | GF | GC | GD | WR%
      statRowHtml=
        statCell('MP',mp)+
        '<div style="text-align:center"><div style="font-family:\'Bebas Neue\';font-size:1.05rem;color:var(--green);line-height:1.1">'+wins+'</div><div style="font-size:.55rem;color:var(--green);font-weight:700">W</div></div>'+
        '<div style="text-align:center"><div style="font-family:\'Bebas Neue\';font-size:1.05rem;color:var(--acc);line-height:1.1">'+draws+'</div><div style="font-size:.55rem;color:var(--acc);font-weight:700">D</div></div>'+
        '<div style="text-align:center"><div style="font-family:\'Bebas Neue\';font-size:1.05rem;color:var(--red);line-height:1.1">'+losses+'</div><div style="font-size:.55rem;color:var(--red);font-weight:700">L</div></div>'+
        '<div style="text-align:center"><div style="font-family:\'Bebas Neue\';font-size:1.05rem;color:#7CB9FF;line-height:1.1">'+gf+'</div><div style="font-size:.55rem;color:#7CB9FF;font-weight:700">GF</div></div>'+
        '<div style="text-align:center"><div style="font-family:\'Bebas Neue\';font-size:1.05rem;color:#FF8A50;line-height:1.1">'+gc+'</div><div style="font-size:.55rem;color:#FF8A50;font-weight:700">GC</div></div>'+
        '<div style="text-align:center"><div style="font-family:\'Bebas Neue\';font-size:1.05rem;color:'+(gd>=0?'var(--green)':'var(--red)')+';line-height:1.1">'+(gd>=0?'+':'')+gd+'</div><div style="font-size:.55rem;color:var(--muted);font-weight:700">GD</div></div>'+
        statCell('WR%',wr+'%');
    } else if(type==='goals'){
      statRowHtml=statCell('MP',mp)+statCell('⚽ GF',gf)+statCell('🥅 GC',gc)+statCell('GD',(gd>=0?'+':'')+gd);
    } else if(type==='motm'){
      statRowHtml=statCell('MP',mp)+statCell('👑 MOTM',s.motm||0);
    } else if(type==='cs'){
      statRowHtml=statCell('MP',mp)+statCell('🧤 CS',s.cs||0);
    } else if(type==='wr'){
      statRowHtml=statCell('MP',mp)+statCell('WR%',wr+'%')+
        '<div style="text-align:center"><div style="font-family:\'Bebas Neue\';font-size:1.05rem;color:var(--green);line-height:1.1">'+wins+'</div><div style="font-size:.55rem;color:var(--green);font-weight:700">W</div></div>'+
        '<div style="text-align:center"><div style="font-family:\'Bebas Neue\';font-size:1.05rem;color:var(--acc);line-height:1.1">'+draws+'</div><div style="font-size:.55rem;color:var(--acc);font-weight:700">D</div></div>'+
        '<div style="text-align:center"><div style="font-family:\'Bebas Neue\';font-size:1.05rem;color:var(--red);line-height:1.1">'+losses+'</div><div style="font-size:.55rem;color:var(--red);font-weight:700">L</div></div>';
    }

    // Main value shown on right depends on tab
    var mainVal=type==='total'?pts:type==='goals'?gf:type==='motm'?(s.motm||0):type==='cs'?(s.cs||0):wr+'%';
    var mainLbl=type==='total'?'PTS':type==='goals'?'GF':type==='motm'?'MOTM':type==='cs'?'CS':'WR%';
    var mainColor=type==='total'?'var(--green)':type==='goals'?'#7CB9FF':type==='motm'?'var(--acc)':type==='cs'?'#7CB9FF':'var(--green)';

    var cols=type==='total'?'repeat(8,1fr)':type==='goals'?'repeat(4,1fr)':type==='motm'||type==='cs'?'repeat(2,1fr)':'repeat(5,1fr)';

    return '<div style="background:var(--card);border:1px solid var(--border);border-radius:14px;padding:.85rem 1rem;cursor:pointer;transition:all .2s" onclick="showPlayerProfile(\''+p.id+'\')">'+
      '<div style="display:flex;align-items:center;gap:.65rem">'+
      '<div style="width:26px;height:26px;border-radius:50%;background:'+rankNumBg+';display:flex;align-items:center;justify-content:center;flex-shrink:0">'+
        '<span style="font-family:\'Bebas Neue\';font-size:.9rem;color:'+rankNumColor+'">'+(i+1)+'</span></div>'+
      playerPhotoEl(p,38)+
      '<div style="flex:1;min-width:0">'+
        '<div style="font-weight:700;font-size:.92rem;white-space:nowrap;overflow:hidden;text-overflow:ellipsis">'+esc(p.name)+'</div>'+
        '<div style="display:flex;align-items:center;gap:.3rem;flex-wrap:wrap;margin-top:.1rem">'+
          (t?'<span style="font-size:.68rem;color:var(--muted)">'+esc(t.name)+'</span><span style="color:var(--border);font-size:.6rem">·</span>':'')+catBadge(p.cat)+
          (p.bid?coinBadge(p.bid,false):'')+
        '</div>'+
      '</div>'+
      '<div style="display:flex;align-items:center;gap:.5rem;flex-shrink:0">'+
        formHtml+
        '<div style="text-align:right">'+
          '<div style="font-family:\'Bebas Neue\';font-size:1.65rem;color:'+mainColor+';line-height:1">'+mainVal+'</div>'+
          '<div style="font-size:.58rem;color:var(--muted);text-transform:uppercase">'+mainLbl+'</div>'+
        '</div>'+
      '</div></div>'+
      '<div style="display:grid;grid-template-columns:'+cols+';gap:.25rem;margin-top:.6rem;background:var(--card2);border-radius:8px;padding:.45rem .35rem">'+
        statRowHtml+
      '</div>'+
      '</div>';
  }).join('')||'<p style="color:var(--muted);padding:1rem">No players found.</p>';
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
  else if(tab==='history') el.innerHTML=aHistoryHTML();
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
      var t=getTeamById(p.teamId); var s=getStat(p.id); var wr=winRatio(s);
      var wins=s.wins||0, draws=s.draws||0, losses=s.losses||0;
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
  var s=getStat(pid); var teams=getTeams();
  var teamOpts=teams.map(function(t){return '<option value="'+t.id+'"'+(t.id===p.teamId?' selected':'')+'>'+esc(t.name)+'</option>';}).join('');
  var modal=document.getElementById('editPlayerModal');
  if(!modal){
    var div=document.createElement('div'); div.id='editPlayerModal'; div.className='moverlay';
    div.innerHTML='<div class="modal" style="width:min(92vw,560px)"><div id="editPlayerContent"></div></div>';
    document.body.appendChild(div); modal=div;
  }
  document.getElementById('editPlayerContent').innerHTML=
    '<div style="display:flex;align-items:center;gap:.8rem;margin-bottom:1.2rem">'+
    '<div style="flex:1"><div style="font-family:\'Bebas Neue\';font-size:1.6rem;color:var(--green)">✏️ Edit Player</div></div>'+
    '<button onclick="closeModal(\'editPlayerModal\')" style="background:none;border:none;color:var(--muted);font-size:1.5rem;cursor:pointer">✕</button></div>'+
    '<div class="fgrid" style="margin-bottom:1rem">'+
    '<div class="fg"><label>Name *</label><input id="ep_name" value="'+esc(p.name)+'"></div>'+
    '<div class="fg"><label>Team</label><select id="ep_team">'+teamOpts+'</select></div>'+
    '<div class="fg"><label>Category</label><select id="ep_cat">'+
      '<option value="local"'+(p.cat==='local'?' selected':'')+'>Local</option>'+
      '<option value="youth"'+(p.cat==='youth'?' selected':'')+'>Youth</option>'+
      '<option value="invited"'+(p.cat==='invited'?' selected':'')+'>Invited</option>'+
    '</select></div>'+
    '<div class="fg"><label>Bid Price (coins)</label><input id="ep_bid" type="number" value="'+(p.bid||0)+'" min="0"></div>'+
    '</div>'+
    buildPhotoInput('ep_ph')+
    '<div style="background:rgba(255,214,0,.04);border:1px solid rgba(255,214,0,.12);border-radius:10px;padding:.9rem;margin:1rem 0">'+
    '<div style="font-size:.72rem;color:var(--acc);font-weight:700;text-transform:uppercase;letter-spacing:1px;margin-bottom:.6rem">📊 Stats Override (Individual Duel Results)</div>'+
    '<div class="fgrid">'+
    '<div class="fg"><label>⚽ Goals</label><input id="ep_goals" type="number" value="'+(s.goals||0)+'" min="0"></div>'+
    '<div class="fg"><label>👑 MOTM</label><input id="ep_motm" type="number" value="'+(s.motm||0)+'" min="0"></div>'+
    '<div class="fg"><label>🧤 Clean Sheets</label><input id="ep_cs" type="number" value="'+(s.cs||0)+'" min="0"></div>'+
    '<div class="fg"><label style="color:var(--green)">🏆 Duel Wins</label><input id="ep_wins" type="number" value="'+(s.wins||0)+'" min="0"></div>'+
    '<div class="fg"><label style="color:var(--acc)">🤝 Duel Draws</label><input id="ep_draws" type="number" value="'+(s.draws||0)+'" min="0"></div>'+
    '<div class="fg"><label style="color:var(--red)">❌ Duel Losses</label><input id="ep_losses" type="number" value="'+(s.losses||0)+'" min="0"></div>'+
    '<div class="fg"><label>🎮 Matches Played</label><input id="ep_mp" type="number" value="'+(s.mp||0)+'" min="0"></div>'+
    '</div></div>'+
    '<div style="display:flex;gap:.7rem;flex-wrap:wrap">'+
    '<button class="btn bg" onclick="saveEditPlayer(\''+pid+'\')">💾 Save</button>'+
    '<button class="btn" style="background:var(--border);color:var(--text)" onclick="closeModal(\'editPlayerModal\')">Cancel</button></div>';
  modal.classList.remove('hidden');
}
async function saveEditPlayer(pid){
  var name=document.getElementById('ep_name').value.trim(); if(!name){alert('Name required!');return;}
  var newPhoto=await resolvePhotoUrl('ep_ph');
  var existing=findPlayerInState(pid);
  var photoUrl=newPhoto||(existing?existing.photoUrl||'':'');
  var wins   = parseInt(document.getElementById('ep_wins').value)   || 0;
  var draws  = parseInt(document.getElementById('ep_draws').value)  || 0;
  var losses = parseInt(document.getElementById('ep_losses').value) || 0;
  var goals  = parseInt(document.getElementById('ep_goals').value)  || 0;
  var mp     = parseInt(document.getElementById('ep_mp').value)     || (wins+draws+losses);
  var oldStats=getStat(pid);
  var bid=parseInt(document.getElementById('ep_bid').value)||0;
  await fsSet('players',pid,{id:pid,name:name,teamId:document.getElementById('ep_team').value,cat:document.getElementById('ep_cat').value,photoUrl:photoUrl,bid:bid});
  await fsSet('stats',pid,{
    goals:  goals,
    gf:     goals, // gf mirrors goals for individual ranking
    ga:     oldStats.ga||0, // keep existing ga
    motm:   parseInt(document.getElementById('ep_motm').value)  || 0,
    cs:     parseInt(document.getElementById('ep_cs').value)    || 0,
    wins:   wins, draws: draws, losses: losses, mp: mp
  });
  closeModal('editPlayerModal'); alert('✅ Player updated!');
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
  document.getElementById('matchDetailModal').classList.remove('hidden');
  var vp=document.getElementById('viewPanel'); if(vp) vp.classList.add('hidden');
}
function viewMatch(mid){ viewMatchInModal(mid); }

// ════════════════════════════════════════════
// ADMIN — STANDINGS
// ════════════════════════════════════════════
function aStandingsHTML(){
  var rows=calcStandings();
  var teams=getTeams();
  var teamOpts=teams.map(function(t){return '<option value="'+t.id+'">'+esc(t.name)+'</option>';}).join('');
  var topPlayers=getPlayers().slice().sort(function(a,b){return realCalcPts(getStat(b.id))-realCalcPts(getStat(a.id));}).slice(0,15);
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
      var s=getStat(p.id); var t=getTeamById(p.teamId); var wr=winRatio(s);
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
    // Not logged in — show login prompt in section
    document.getElementById('captainContent').innerHTML=
      '<div style="text-align:center;padding:3rem 1rem">'
      +'<div style="font-family:Bebas Neue,sans-serif;font-size:2rem;color:#7CB9FF;letter-spacing:3px;margin-bottom:.5rem">Captain Login</div>'
      +'<div style="font-size:.85rem;color:var(--muted);margin-bottom:1.5rem">Login with your team password to manage player info</div>'
      +'<button onclick="openCapLogin()" style="background:linear-gradient(135deg,#2979FF,#1565C0);color:#fff;border:none;border-radius:10px;padding:.75rem 2rem;font-family:Barlow Condensed,sans-serif;font-size:1rem;font-weight:700;cursor:pointer;letter-spacing:1px">Login as Captain</button>'
      +'</div>';
    // also update header to show generic
    var nameEl=document.getElementById('capTeamName');
    if(nameEl) nameEl.textContent='Captain Panel';
    return;
  }
  var t=getTeamById(captainTeamId); if(!t) return;
  var ps=getPlayersByTeam(captainTeamId);

  // Update header
  var logoEl=document.getElementById('capTeamLogo');
  var nameEl=document.getElementById('capTeamName');
  if(logoEl){
    var src=t.logoUrl||t.logo;
    logoEl.innerHTML=(src&&src.startsWith('http'))
      ?'<img src="'+esc(src)+'" style="width:100%;height:100%;object-fit:cover;border-radius:50%">'
      :esc(src||'⚽');
  }
  if(nameEl) nameEl.textContent=t.name;

  var rows=calcStandings();
  var tp=rows.find(function(r){return r.id===captainTeamId;})||{};

  var catGrps={local:[],youth:[],invited:[]};
  ps.forEach(function(p){ (catGrps[p.cat]||catGrps.local).push(p); });

  var playerRows='';
  Object.keys(catGrps).forEach(function(cat){
    var arr=catGrps[cat]; if(!arr.length) return;
    playerRows+='<tr><td colspan="4" style="background:var(--card2);padding:.4rem .8rem;font-family:Barlow Condensed,sans-serif;font-size:.72rem;font-weight:700;letter-spacing:1px;text-transform:uppercase;border-top:1px solid var(--border)">'
      +catBadge(cat)+'<span style="margin-left:.4rem;color:var(--muted)">('+arr.length+')</span></td></tr>';
    arr.forEach(function(p){
      var uid_val=p.uid||'';
      var dev_val=p.deviceName||'';
      playerRows+=
        '<tr id="prow_'+p.id+'">'
        +'<td style="padding:.55rem .7rem;border-top:1px solid var(--border)">'
          +'<div style="display:flex;align-items:center;gap:.5rem">'
          +playerPhotoEl(p,26)
          +'<div>'
            +'<div style="font-weight:700;font-size:.85rem">'+esc(p.name)+'</div>'
            +(p.bid?'<div>'+coinBadge(p.bid,false)+'</div>':'')
          +'</div></div>'
        +'</td>'
        +'<td style="padding:.4rem .5rem;border-top:1px solid var(--border)">'
          +'<input id="uid_'+p.id+'" value="'+esc(uid_val)+'" placeholder="User ID" style="background:var(--dark);border:1px solid var(--border);border-radius:6px;padding:.3rem .5rem;color:var(--text);font-size:.8rem;width:100%;outline:none;min-width:80px">'
        +'</td>'
        +'<td style="padding:.4rem .5rem;border-top:1px solid var(--border)">'
          +'<input id="dev_'+p.id+'" value="'+esc(dev_val)+'" placeholder="Device name" style="background:var(--dark);border:1px solid var(--border);border-radius:6px;padding:.3rem .5rem;color:var(--text);font-size:.8rem;width:100%;outline:none;min-width:90px">'
        +'</td>'
        +'<td style="padding:.4rem .5rem;border-top:1px solid var(--border);text-align:center">'
          +'<button onclick="savePlayerInfo(\''
          +p.id
          +'\''+')" style="background:var(--green);color:#000;border:none;border-radius:6px;padding:.28rem .65rem;font-family:Barlow Condensed,sans-serif;font-size:.75rem;font-weight:700;cursor:pointer">Save</button>'
        +'</td>'
        +'</tr>';
    });
  });

  document.getElementById('captainContent').innerHTML=
    '<div style="display:grid;grid-template-columns:repeat(auto-fill,minmax(110px,1fr));gap:.5rem;margin-bottom:1.1rem">'
    +'<div class="stat-box" style="padding:.5rem .7rem"><div class="num">'+(tp.pts||0)+'</div><div class="lbl">Pts</div></div>'
    +'<div class="stat-box" style="padding:.5rem .7rem"><div class="num">'+(tp.w||0)+'</div><div class="lbl">Wins</div></div>'
    +'<div class="stat-box" style="padding:.5rem .7rem"><div class="num">'+(tp.d||0)+'</div><div class="lbl">Draws</div></div>'
    +'<div class="stat-box" style="padding:.5rem .7rem"><div class="num">'+(tp.l||0)+'</div><div class="lbl">Loss</div></div>'
    +'<div class="stat-box" style="padding:.5rem .7rem"><div class="num">'+ps.length+'</div><div class="lbl">Players</div></div>'
    +'</div>'
    +'<div style="background:rgba(41,121,255,.04);border:1px solid rgba(41,121,255,.18);border-radius:12px;padding:.8rem 1rem;margin-bottom:1rem;font-size:.75rem;color:var(--muted)">'
    +'Fill in <strong style="color:#7CB9FF">User ID</strong> and <strong style="color:#7CB9FF">Device Name</strong> for each player, then press <strong style="color:var(--green)">Save</strong>.</div>'
    +'<div class="twrap">'
    +'<table><thead><tr>'
    +'<th>Player</th><th>User ID</th><th>Device Name</th><th style="text-align:center">Action</th>'
    +'</tr></thead><tbody>'+playerRows+'</tbody></table></div>'
    +'<div style="margin-top:.8rem;display:flex;gap:.6rem;flex-wrap:wrap">'
    +'<button onclick="saveAllPlayerInfo()" style="background:var(--green);color:#000;border:none;border-radius:8px;padding:.5rem 1.2rem;font-family:Barlow Condensed,sans-serif;font-size:.85rem;font-weight:700;cursor:pointer">Save All</button>'
    +'<button onclick="downloadRosterPDF()" style="background:rgba(41,121,255,.12);color:#7CB9FF;border:1px solid rgba(41,121,255,.3);border-radius:8px;padding:.5rem 1.2rem;font-family:Barlow Condensed,sans-serif;font-size:.85rem;font-weight:700;cursor:pointer">PDF Download</button>'
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

function downloadRosterPDF(){
  if(!captainTeamId) return;
  var t=getTeamById(captainTeamId); if(!t) return;
  var ps=getPlayersByTeam(captainTeamId);
  var rows=calcStandings(); var tp=rows.find(function(r){return r.id===captainTeamId;})||{};

  var tableRows=ps.map(function(p,i){
    var s=getStat(p.id);
    var catLabel=p.cat==='invited'?'INVITED':p.cat==='youth'?'YOUTH':'LOCAL';
    var catColor=p.cat==='invited'?'#FF7A40':p.cat==='youth'?'#5B9BFF':'#00C853';
    return '<tr style="'+(i%2===0?'background:#f8fff9':'')+'">'+
      '<td style="padding:6px 10px;border:1px solid #ddd;font-weight:600">'+(i+1)+'</td>'+
      '<td style="padding:6px 10px;border:1px solid #ddd;font-weight:700">'+esc(p.name)+'</td>'+
      '<td style="padding:6px 10px;border:1px solid #ddd"><span style="background:'+catColor+'22;color:'+catColor+';padding:2px 7px;border-radius:4px;font-size:11px;font-weight:800;border:1px solid '+catColor+'44">'+catLabel+'</span></td>'+
      '<td style="padding:6px 10px;border:1px solid #ddd">'+(p.bid||'-')+'</td>'+
      '<td style="padding:6px 10px;border:1px solid #ddd">'+(p.uid||'<span style="color:#aaa">—</span>')+'</td>'+
      '<td style="padding:6px 10px;border:1px solid #ddd">'+(p.deviceName||'<span style="color:#aaa">—</span>')+'</td>'+
      '<td style="padding:6px 10px;border:1px solid #ddd;text-align:right;font-weight:700;color:#009624">'+realCalcPts(s)+'</td>'+
      '</tr>';
  }).join('');

  var html='<!DOCTYPE html><html><head><meta charset="UTF-8">'+
    '<title>'+esc(t.name)+' — Roster</title>'+
    '<style>body{font-family:Arial,sans-serif;margin:24px;color:#111}'+
    'h1{color:#009624;margin-bottom:4px}'+
    '.info{color:#555;font-size:13px;margin-bottom:18px}'+
    'table{width:100%;border-collapse:collapse;font-size:13px}'+
    'th{background:#009624;color:#fff;padding:7px 10px;text-align:left;border:1px solid #009624}'+
    '.footer{margin-top:20px;font-size:11px;color:#aaa;border-top:1px solid #eee;padding-top:8px}'+
    '@media print{button{display:none}}</style></head><body>'+
    '<h1>'+esc(t.name)+'</h1>'+
    '<div class="info">Manager / President: '+esc(t.president||'—')+'&nbsp;&nbsp;|&nbsp;&nbsp;'+
    'W: '+(tp.w||0)+'&nbsp; D: '+(tp.d||0)+'&nbsp; L: '+(tp.l||0)+'&nbsp; Pts: '+(tp.pts||0)+'</div>'+
    '<table><thead><tr>'+
      '<th>#</th><th>Player Name</th><th>Category</th><th>Bid</th><th>User ID</th><th>Device Name</th><th>Points</th>'+
    '</tr></thead><tbody>'+tableRows+'</tbody></table>'+
    '<div class="footer">Juvenile League Official Portal &nbsp;|&nbsp; Generated: '+new Date().toLocaleString()+'</div>'+
    '<div style="margin-top:12px;text-align:right"><button onclick="window.print()" style="background:#009624;color:#fff;border:none;padding:8px 20px;border-radius:6px;cursor:pointer;font-size:13px">Print / Save PDF</button></div>'+
    '</body></html>';

  var win=window.open('','_blank');
  win.document.write(html);
  win.document.close();
  setTimeout(function(){win.print();},400);
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
