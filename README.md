<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Tournament Hub</title>
  <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-database-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
  <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@600;800&family=Montserrat:wght@500;700&display=swap" rel="stylesheet">

  <style>
    :root {
      --primary: #3b82f6;
      --primary-dark: #2563eb;
      --accent: #60a5fa;
      --bg: #ffffff;
      --card: #eff6ff;
      --border: #dbeafe;
      --text: #1f2937;
      --text-light: #6b7280;
    }

    * { margin:0; padding:0; box-sizing:border-box; }
    body {
      font-family: 'Segoe UI', system-ui, sans-serif;
      background: var(--bg);
      color: var(--text);
      line-height: 1.6;
      overflow-x: hidden;
      opacity: 1;
      transition: opacity 0.3s ease;
    }

    .header {
      background: linear-gradient(135deg, #3b82f6, #60a5fa);
      box-shadow: 0 4px 12px rgba(59, 130, 246, 0.2);
      position: sticky;
      top: 0;
      z-index: 100;
      padding: 1rem 0;
    }

    nav {
      max-width: 1280px;
      margin: 0 auto;
      display: flex;
      justify-content: center;
      gap: 1rem;
      flex-wrap: wrap;
    }

    nav button {
      padding: 12px 28px;
      background: rgba(255,255,255,0.2);
      color: white;
      border: 2px solid rgba(255,255,255,0.3);
      border-radius: 9999px;
      cursor: pointer;
      transition: all 0.3s ease;
      font-weight: 600;
    }

    nav button:hover, nav button.active {
      background: white;
      color: var(--primary);
      border-color: white;
      transform: translateY(-2px);
    }

    .container {
      max-width: 1280px;
      margin: 2rem auto;
      padding: 0 1.5rem;
    }

    h1 {
      font-size: 2.8rem;
      background: linear-gradient(90deg, #3b82f6, #60a5fa);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      margin-bottom: 0.5rem;
      text-align: center;
    }

    h2, h3 { color: var(--primary); }

    .card {
      background: var(--card);
      border: 2px solid var(--border);
      border-radius: 16px;
      padding: 2rem;
      box-shadow: 0 10px 30px rgba(59,130,246,0.1);
      transition: transform 0.3s ease, box-shadow 0.3s ease;
      margin-bottom: 1.5rem;
    }

    .card:hover {
      transform: translateY(-4px);
      box-shadow: 0 20px 40px rgba(59,130,246,0.15);
    }

    .section { display: none; }
    .section.active { display: block; }

    .badge {
      display: inline-flex;
      align-items: center;
      justify-content: center;
      width: 24px;
      height: 24px;
      border-radius: 50%;
      font-size: 12px;
      font-weight: 700;
      margin-right: 6px;
      vertical-align: middle;
      flex-shrink: 0;
    }

    .badge-primary { background: var(--primary); color: white; }
    .badge-success { background: #10b981; color: white; }
    .badge-warning { background: #f59e0b; color: white; }
    .badge-danger { background: #ef4444; color: white; }
    .badge-info { background: #3b82f6; color: white; }

    .upload-area {
      border: 2px dashed var(--primary);
      border-radius: 12px;
      padding: 2rem;
      text-align: center;
      background: rgba(59,130,246,0.05);
      transition: all 0.3s;
    }

    .preview {
      max-width: 240px;
      border-radius: 12px;
      border: 3px solid var(--primary);
      margin: 1rem auto;
      display: none;
      box-shadow: 0 0 20px rgba(59,130,246,0.3);
    }

    table {
      width: 100%;
      border-collapse: collapse;
      background: white;
      border-radius: 12px;
      overflow: hidden;
    }
    th {
      background: var(--primary);
      padding: 16px;
      text-align: left;
      color: white;
      font-weight: 600;
    }
    td {
      padding: 14px 16px;
      border-bottom: 1px solid var(--border);
    }
    tr:hover { background: var(--card); }

    input, select, textarea {
      width: 100%;
      padding: 14px;
      background: white;
      border: 2px solid var(--border);
      border-radius: 8px;
      color: var(--text);
      margin: 8px 0;
      transition: border-color 0.3s;
      font-family: inherit;
    }
    input:focus, select:focus, textarea:focus {
      outline: none;
      border-color: var(--primary);
    }

    button {
      padding: 12px 24px;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      font-weight: 600;
      transition: all 0.3s;
    }

    .btn-primary { background: var(--primary); color: white; }
    .btn-primary:hover { background: var(--primary-dark); transform: scale(1.03); }
    .btn-success { background: #10b981; color: white; }
    .btn-success:hover { background: #059669; }
    .btn-danger { background: #ef4444; color: white; }
    .btn-danger:hover { background: #dc2626; }
    .btn-warning { background: #f59e0b; color: white; }
    .btn-warning:hover { background: #d97706; }

    .url-display {
      background: white;
      padding: 14px;
      border-radius: 8px;
      font-size: 0.95rem;
      word-break: break-all;
      margin: 12px 0;
      border-left: 4px solid var(--primary);
    }

    /* SCORECARD STYLES */
    .scorecard-dark {
      background: #2c2c2c;
      border-radius: 15px;
      padding: 30px;
      box-shadow: 0 0 20px rgba(59,130,246,0.4);
      border: 2px solid #3b82f6;
      font-family: 'Montserrat', sans-serif;
    }

    .scorecard-light {
      background: #ffffff;
      border-radius: 15px;
      padding: 30px;
      box-shadow: 0 0 20px rgba(59,130,246,0.2);
      border: 2px solid #60a5fa;
      font-family: 'Montserrat', sans-serif;
    }

    .sc-title-container {
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 15px;
      margin-bottom: 10px;
    }

    .sc-tournament-logo {
      width: 80px; height: 80px;
      border-radius: 50%;
      border: 3px solid #60a5fa;
      object-fit: cover;
      box-shadow: 0 0 15px rgba(59,130,246,0.5);
    }

    .sc-team-logo-img {
      width: 80px; height: 80px;
      border-radius: 50%;
      border: 3px solid #60a5fa;
      object-fit: cover;
      box-shadow: 0 0 15px rgba(59,130,246,0.5);
      flex-shrink: 0;
    }

    .scorecard-dark .sc-title {
      font-size: 36px;
      font-family: 'Orbitron', sans-serif;
      color: #ffffff;
      text-shadow: 0 0 10px rgba(59,130,246,0.7);
      border: 3px solid #60a5fa;
      padding: 10px 20px;
      border-radius: 10px;
      background: #1a1a1a;
    }

    .scorecard-light .sc-title {
      font-size: 36px;
      font-family: 'Orbitron', sans-serif;
      color: #1f2937;
      border: 3px solid #60a5fa;
      padding: 10px 20px;
      border-radius: 10px;
      background: #eff6ff;
    }

    .scorecard-dark .sc-date {
      font-size: 20px;
      font-family: 'Orbitron', sans-serif;
      color: #60a5fa;
      text-align: center;
      margin-bottom: 25px;
    }

    .scorecard-light .sc-date {
      font-size: 20px;
      font-family: 'Orbitron', sans-serif;
      color: #3b82f6;
      text-align: center;
      margin-bottom: 25px;
    }

    .sc-teams {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 20px;
      gap: 10px;
    }

    .scorecard-dark .sc-team-panel {
      flex: 1;
      text-align: center;
      font-size: 22px;
      font-weight: 800;
      padding: 15px;
      border-radius: 10px;
      border: 3px solid #60a5fa;
      font-family: 'Orbitron', sans-serif;
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 10px;
      color: #ffffff;
      background: #1a1a1a;
    }

    .scorecard-light .sc-team-panel {
      flex: 1;
      text-align: center;
      font-size: 22px;
      font-weight: 800;
      padding: 15px;
      border-radius: 10px;
      border: 3px solid #60a5fa;
      font-family: 'Orbitron', sans-serif;
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 10px;
      color: #1f2937;
      background: #eff6ff;
    }

    .sc-team-score {
      width: 80px; height: 50px;
      background: #3b82f6;
      color: #ffffff;
      border-radius: 8px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-family: 'Orbitron', sans-serif;
      font-size: 24px;
      font-weight: 700;
      border: 3px solid #60a5fa;
      flex-shrink: 0;
    }

    .sc-matches { display: flex; flex-direction: column; gap: 10px; }

    .sc-player-container {
      flex: 1;
      display: flex;
      align-items: center;
      justify-content: center;
      padding: 8px;
      border: 2px solid #60a5fa;
      border-radius: 5px;
      height: 42px;
      box-sizing: border-box;
      position: relative;
    }

    .scorecard-dark .sc-player-container { background: #1a1a1a; }
    .scorecard-light .sc-player-container { background: #eff6ff; }

    .scorecard-dark .sc-player-name {
      font-family: 'Orbitron', sans-serif;
      font-size: 18px;
      font-weight: 600;
      color: #ffffff;
      font-style: italic;
      text-align: center;
      width: 100%;
    }

    .scorecard-light .sc-player-name {
      font-family: 'Orbitron', sans-serif;
      font-size: 18px;
      font-weight: 600;
      color: #1f2937;
      font-style: italic;
      text-align: center;
      width: 100%;
    }

    .sc-motm-player { border: 3px solid #10b981 !important; }
    .sc-motm-player::before {
      content: '👑';
      position: absolute;
      top: -18px;
      left: 50%;
      transform: translateX(-50%);
      font-size: 14px;
    }

    .scorecard-dark .sc-motm-player .sc-player-name {
      color: #60a5fa;
      font-style: normal;
      font-weight: 800;
    }

    .scorecard-light .sc-motm-player .sc-player-name {
      color: #2563eb;
      font-style: normal;
      font-weight: 800;
    }

    .sc-score-box {
      width: 90px; height: 40px;
      background: #3b82f6;
      color: #ffffff;
      border-radius: 8px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-family: 'Orbitron', sans-serif;
      font-size: 18px;
      margin: 0 10px;
      border: 2px solid #60a5fa;
      flex-shrink: 0;
    }

    .sc-results {
      margin-top: 20px;
      text-align: center;
      font-family: 'Orbitron', sans-serif;
    }

    .scorecard-dark .sc-winner {
      color: #60a5fa;
      font-size: 22px;
      font-weight: 800;
    }

    .scorecard-light .sc-winner {
      color: #2563eb;
      font-size: 22px;
      font-weight: 800;
    }

    /* POINT TABLE */
    .pt-container {
      background: white;
      border-radius: 16px;
      overflow: hidden;
      border: 2px solid var(--border);
      box-shadow: 0 10px 30px rgba(59,130,246,0.1);
      margin-bottom: 2rem;
    }

    .pt-header {
      background: linear-gradient(135deg, #3b82f6, #60a5fa);
      padding: 1.5rem;
      color: white;
      text-align: center;
      font-size: 1.3rem;
      font-weight: 800;
      letter-spacing: 1px;
    }

    .pt-table {
      width: 100%;
      border-collapse: collapse;
    }

    .pt-thead th {
      background: #f3f4f6;
      padding: 1rem;
      text-align: left;
      font-weight: 700;
      color: var(--text);
      border-bottom: 2px solid var(--border);
      font-size: 0.9rem;
    }

    .pt-tbody td {
      padding: 0.9rem 1rem;
      border-bottom: 1px solid var(--border);
      color: var(--text);
    }

    .pt-tbody tr:hover {
      background: var(--card);
    }

    .pt-medal {
      display: inline-flex;
      align-items: center;
      justify-content: center;
      width: 32px;
      height: 32px;
      border-radius: 50%;
      font-weight: 800;
      color: white;
      font-size: 1.2rem;
    }

    .pt-medal.gold {
      background: linear-gradient(135deg, #fbbf24, #f59e0b);
      box-shadow: 0 0 15px rgba(251, 191, 36, 0.4);
    }

    .pt-medal.silver {
      background: linear-gradient(135deg, #e5e7eb, #d1d5db);
      box-shadow: 0 0 15px rgba(209, 213, 219, 0.4);
      color: #374151;
    }

    .pt-medal.bronze {
      background: linear-gradient(135deg, #f97316, #ea580c);
      box-shadow: 0 0 15px rgba(249, 115, 22, 0.4);
    }

    .pt-team {
      display: flex;
      align-items: center;
      gap: 10px;
      font-weight: 700;
    }

    .pt-team-logo {
      width: 40px;
      height: 40px;
      border-radius: 50%;
      object-fit: cover;
      border: 2px solid var(--border);
    }

    .pt-points {
      font-size: 1.2rem;
      font-weight: 800;
      color: var(--primary);
      text-align: center;
    }

    .group-badge {
      display: inline-block;
      background: linear-gradient(135deg, #3b82f6, #60a5fa);
      color: white;
      padding: 8px 16px;
      border-radius: 20px;
      font-size: 0.85rem;
      font-weight: 700;
      margin-bottom: 1rem;
    }

    .admin-tabs {
      display: flex;
      gap: 0;
      margin-bottom: 2rem;
      background: white;
      border: 2px solid var(--border);
      border-radius: 12px;
      overflow: hidden;
      flex-wrap: wrap;
    }

    .admin-tab-btn {
      flex: 1;
      min-width: 140px;
      padding: 14px 10px;
      background: white;
      color: var(--text-light);
      border: none;
      border-radius: 0;
      cursor: pointer;
      font-weight: 700;
      font-size: 0.9rem;
      transition: all 0.25s;
      border-right: 1px solid var(--border);
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 6px;
    }

    .admin-tab-btn:last-child { border-right: none; }

    .admin-tab-btn:hover {
      background: var(--card);
      color: var(--primary);
    }

    .admin-tab-btn.active {
      background: var(--primary);
      color: white;
    }

    .form-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
      gap: 1rem;
    }

    .form-field label {
      display: block;
      font-weight: 700;
      color: var(--primary);
      margin-bottom: 4px;
      font-size: 0.9rem;
    }

    .toast {
      position: fixed;
      bottom: 24px;
      right: 24px;
      padding: 12px 20px;
      border-radius: 10px;
      font-weight: 700;
      font-size: 0.9rem;
      z-index: 9999;
      display: none;
      animation: slideIn 0.3s ease;
    }
    .toast.success { background: #3b82f6; color: white; }
    .toast.error { background: #ef4444; color: white; }
    @keyframes slideIn {
      from { transform: translateY(20px); opacity: 0; }
      to { transform: translateY(0); opacity: 1; }
    }
  </style>
</head>
<body>
  <div class="header">
    <nav>
      <button onclick="showSection('viewer')" class="active" id="nav-viewer"><span class="badge badge-primary">🏆</span> Viewer</button>
      <button onclick="showSection('scorecard')" id="nav-scorecard"><span class="badge badge-info">⚽</span> Scorecard</button>
      <button onclick="showSection('admin')" id="nav-admin"><span class="badge badge-warning">⚙️</span> Admin</button>
      <button onclick="showSection('captain')" id="nav-captain"><span class="badge badge-success">🎖️</span> Captain</button>
    </nav>
  </div>

  <div class="container">
    <!-- VIEWER SECTION -->
    <div id="viewer" class="section active">
      <h1>Tournament Hub</h1>
      <p style="text-align:center; font-size:1.3rem; margin-bottom:2rem; color:#3b82f6;">Manage Your Tournaments</p>
      
      <div class="card">
        <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:1.5rem;flex-wrap:wrap;gap:1rem;">
          <h2 style="margin:0;"><span class="badge badge-primary">🏆</span> Point Table</h2>
          <button class="btn-success" onclick="downloadPointTableJPG()"><span class="badge badge-success">📥</span> Download JPG</button>
        </div>
        <div id="points-table-view"></div>
      </div>

      <div class="card">
        <h2 style="text-align:center; margin-bottom:1.5rem;"><span class="badge badge-info">👥</span> Teams</h2>
        <div id="viewer-teams"></div>
      </div>

      <div class="card">
        <h2 style="text-align:center; margin-bottom:1.5rem;"><span class="badge badge-warning">📅</span> Recent Matches</h2>
        <div id="viewer-fixtures"></div>
      </div>
    </div>

    <!-- SCORECARD SECTION -->
    <div id="scorecard" class="section">
      <div class="card" style="max-width:1100px; margin:0 auto;">
        <h2><span class="badge badge-info">⚽</span> Scorecard Generator</h2>
        
        <div style="margin-bottom:1.5rem;">
          <span style="font-weight:700;color:var(--primary);display:block;margin:1rem 0;">🎮 Match Data</span>
          <textarea id="scPasteText" placeholder="Paste the full generated fixture here — e.g.&#10;Team1 ⚔️ Team2&#10;Player1 🔑 2🆚4 Player2&#10;...&#10;(POINTS section is used for the final score)" style="width:100%;height:180px;padding:14px;border:2px solid var(--border);border-radius:8px;"></textarea>

          <div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(250px,1fr));gap:1rem;margin:1rem 0;">
            <div><label style="font-weight:700;color:var(--primary);">Tournament Name</label><input type="text" id="scNameInput" placeholder="La Viola Cup 2026"></div>
            <div><label style="font-weight:700;color:var(--primary);">Stage/Date</label><input type="text" id="scStageInput" placeholder="Group Stage"></div>
            <div><label style="font-weight:700;color:var(--primary);">Logo URL</label><input type="text" id="scLogoInput" placeholder="https://..."></div>
          </div>

          <button class="btn-primary" onclick="generateScorecard()" style="width:100%;padding:14px;">⚡ Generate Scorecards</button>
        </div>

        <div style="display:flex;gap:2rem;flex-direction:column;">
          <div>
            <h3>🌑 Dark Version</h3>
            <div class="scorecard-dark" id="sc-dark">
              <div class="sc-title-container">
                <img id="sc-dark-logo" class="sc-tournament-logo" crossorigin="anonymous" style="display:none;">
                <div class="sc-title" id="sc-dark-name">La Viola Cup</div>
              </div>
              <div class="sc-date" id="sc-dark-stage">Group Stage</div>
              <div class="sc-teams">
                <div class="sc-team-panel" id="sc-dark-t1">
                  <img id="sc-dark-t1-logo" class="sc-team-logo-img" crossorigin="anonymous" style="display:none;">
                  <span id="sc-dark-t1-name">Team 1</span>
                </div>
                <div class="sc-team-score" id="sc-dark-s1">0</div>
                <div class="sc-team-score" id="sc-dark-s2">0</div>
                <div class="sc-team-panel" id="sc-dark-t2">
                  <img id="sc-dark-t2-logo" class="sc-team-logo-img" crossorigin="anonymous" style="display:none;">
                  <span id="sc-dark-t2-name">Team 2</span>
                </div>
              </div>
              <div class="sc-matches" id="sc-dark-matches"></div>
              <div class="sc-results">
                <div class="sc-winner" id="sc-dark-winner">Winner: -</div>
              </div>
            </div>
            <button class="btn-success" onclick="downloadScorecard('sc-dark')" style="width:100%;margin-top:1rem;">⬇ Download PNG</button>
          </div>

          <div>
            <h3>☀️ Light Version</h3>
            <div class="scorecard-light" id="sc-light">
              <div class="sc-title-container">
                <img id="sc-light-logo" class="sc-tournament-logo" crossorigin="anonymous" style="display:none;">
                <div class="sc-title" id="sc-light-name">La Viola Cup</div>
              </div>
              <div class="sc-date" id="sc-light-stage">Group Stage</div>
              <div class="sc-teams">
                <div class="sc-team-panel" id="sc-light-t1">
                  <img id="sc-light-t1-logo" class="sc-team-logo-img" crossorigin="anonymous" style="display:none;">
                  <span id="sc-light-t1-name">Team 1</span>
                </div>
                <div class="sc-team-score" id="sc-light-s1">0</div>
                <div class="sc-team-score" id="sc-light-s2">0</div>
                <div class="sc-team-panel" id="sc-light-t2">
                  <img id="sc-light-t2-logo" class="sc-team-logo-img" crossorigin="anonymous" style="display:none;">
                  <span id="sc-light-t2-name">Team 2</span>
                </div>
              </div>
              <div class="sc-matches" id="sc-light-matches"></div>
              <div class="sc-results">
                <div class="sc-winner" id="sc-light-winner">Winner: -</div>
              </div>
            </div>
            <button class="btn-success" onclick="downloadScorecard('sc-light')" style="width:100%;margin-top:1rem;">⬇ Download PNG</button>
          </div>
        </div>
      </div>
    </div>

    <!-- ADMIN SECTION -->
    <div id="admin" class="section">
      <div class="card" style="max-width:1000px; margin:0 auto;">
        <h2 style="text-align:center;"><span class="badge badge-warning">⚙️</span> Admin Panel</h2>

        <div id="admin-login" style="max-width:380px;margin:0 auto;background:var(--card);border-radius:14px;padding:2rem;border:2px solid var(--border);">
          <div style="text-align:center;font-size:3rem;margin-bottom:1rem;">🔐</div>
          <input type="password" id="adminPass" placeholder="Enter Admin Password">
          <button class="btn-primary" style="width:100%;padding:14px;" onclick="adminLogin()">Login</button>
        </div>

        <div id="admin-content" style="display:none;">
          <div class="admin-tabs">
            <button class="admin-tab-btn active" onclick="showAdminTab(1)">👥 Teams</button>
            <button class="admin-tab-btn" onclick="showAdminTab(2)">📅 Fixtures</button>
            <button class="admin-tab-btn" onclick="showAdminTab(3)">🏆 Tournaments</button>
            <button class="admin-tab-btn" onclick="showAdminTab(4)">✏️ Player Changes</button>
            <button class="admin-tab-btn" onclick="showAdminTab(5)">🎨 Config</button>
            <button class="admin-tab-btn" onclick="showAdminTab(6)">🎯 Fixture Generator</button>
          </div>

          <!-- TAB 1: TEAMS -->
          <div id="admin-tab1">
            <div style="background:var(--card);border-radius:14px;padding:1.5rem;border:2px solid var(--border);margin-bottom:1.5rem;">
              <h3>➕ Register Team</h3>
              <div class="form-grid">
                <div><label>Team Name</label><input type="text" id="teamName" placeholder="Team Name"></div>
                <div><label>Captain</label><input type="text" id="captainName" placeholder="Captain Name"></div>
                <div><label>Password</label><input type="text" id="teamPassword" placeholder="Password"></div>
              </div>
              <div class="upload-area" style="margin-top:1rem;">
                <p style="font-weight:600;">🖼️ Team Logo URL</p>
                <input type="text" id="teamLogoUrl" placeholder="Paste logo image URL (https://...)">
                <button class="btn-primary" onclick="previewTeamLogo()" style="width:100%;margin-top:0.5rem;">👁️ Preview</button>
                <img id="teamLogoPreview" class="preview">
              </div>
              <button class="btn-success" style="width:100%;margin-top:1rem;padding:14px;" onclick="registerTeam()">✅ Register</button>
            </div>
            <h3>Registered Teams</h3>
            <div id="admin-teams-list"></div>
          </div>

          <!-- TAB 2: FIXTURES -->
          <div id="admin-tab2" style="display:none;">
            <div style="background:var(--card);border-radius:14px;padding:1.5rem;border:2px solid var(--border);margin-bottom:1.5rem;">
              <h3>➕ Create Fixture</h3>
              <div class="form-grid">
                <div><label>Home Team</label><select id="fixtureTeam1"><option>Select</option></select></div>
                <div><label>Away Team</label><select id="fixtureTeam2"><option>Select</option></select></div>
                <div><label>Date</label><input type="date" id="fixtureDate"></div>
                <div><label>Format</label><select id="fixtureFormat"><option value="">Select</option><option>10v10</option><option>12v12</option><option>8v8</option><option>6v6</option></select></div>
              </div>
              <button class="btn-success" style="width:100%;margin-top:1rem;padding:14px;" onclick="createFixture()">📅 Create</button>
            </div>
            <h3>Fixtures</h3>
            <div id="admin-fixtures-list"></div>
          </div>

          <!-- TAB 3: TOURNAMENTS -->
          <div id="admin-tab3" style="display:none;">
            <div style="background:var(--card);border-radius:14px;padding:1.5rem;border:2px solid var(--border);margin-bottom:1.5rem;">
              <h3>🏆 Create Tournament</h3>
              <div class="form-grid">
                <div><label>Tournament Name</label><input type="text" id="tourneyName" placeholder="e.g. La Viola Cup 2026"></div>
                <div><label>Total Slots</label><select id="tourneySlots"><option>32</option><option>48</option><option>64</option><option>128</option></select></div>
                <div><label>Teams per Group</label><select id="teamsPerGroup"><option>4</option><option>6</option><option>8</option><option>12</option></select></div>
              </div>
              <button class="btn-success" style="width:100%;margin-top:1rem;padding:14px;" onclick="createTournament()">🎯 Create Tournament</button>
            </div>

            <div id="tournament-list" style="margin-top:2rem;">
              <h3>Active Tournaments</h3>
              <div id="tourney-display"></div>
            </div>
          </div>

          <!-- TAB 4: PLAYER NAME CHANGE REVIEWS -->
          <div id="admin-tab4" style="display:none;">
            <h2 style="margin-bottom:1.5rem;">✏️ Player Name Change Requests</h2>
            <div id="admin-changes-list"></div>
          </div>

          <!-- TAB 5: CONFIG -->
          <div id="admin-tab5" style="display:none;">
            <div style="background:var(--card);border-radius:14px;padding:1.5rem;border:2px solid var(--border);">
              <h3>🎨 Tournament Config</h3>
              <div class="form-grid">
                <div><label>Tournament Name</label><input type="text" id="scConfigName" placeholder="e.g. La Viola Cup 2026"></div>
                <div><label>Stage</label><input type="text" id="scConfigStage" placeholder="e.g. Group Stage"></div>
              </div>
              <div class="upload-area" style="margin-top:1rem;">
                <p style="font-weight:600;">🖼️ Tournament Logo URL</p>
                <input type="text" id="scConfigLogo" placeholder="Paste tournament logo image URL (https://...)">
                <button class="btn-primary" onclick="previewTourneyLogo()" style="width:100%;margin-top:0.5rem;">👁️ Preview</button>
                <img id="scConfigLogoPreview" class="preview">
              </div>
              <button class="btn-primary" style="width:100%;margin-top:1rem;padding:14px;" onclick="saveConfig()">💾 Save</button>
            </div>

            <div style="background:#fef2f2;border:2px solid #ef4444;border-radius:14px;padding:1.5rem;margin-top:1.5rem;">
              <h3 style="color:#ef4444;">⚠️ Danger Zone</h3>
              <p style="color:#6b7280;font-size:0.9rem;margin:0.5rem 0 1rem;">Permanently deletes ALL data in this app's storage — teams, squads, fixtures, tournaments, submissions, and configs. This cannot be undone.</p>
              <button class="btn-danger" style="width:100%;padding:14px;" onclick="deleteAllStorage()">🗑️ Delete Storage</button>
            </div>
          </div>

          <!-- TAB 6: FIXTURE GENERATOR (password-protected via existing admin login) -->
          <div id="admin-tab6" style="display:none;">
            <div style="background:var(--card);border-radius:14px;padding:1.5rem;border:2px solid var(--border);margin-bottom:1.5rem;">
              <h3>🎯 Fixture Generator</h3>
              <div class="form-grid">
                <div><label>Team 1</label><select id="fgTeam1"><option value="">Select</option></select></div>
                <div><label>Team 2</label><select id="fgTeam2"><option value="">Select</option></select></div>
                <div><label>Match Start Date</label><input type="date" id="fgStartDate"></div>
                <div><label>Referee 1</label><input type="text" id="fgReferee1" placeholder="Referee name"></div>
                <div><label>Referee 2</label><input type="text" id="fgReferee2" placeholder="Referee name"></div>
              </div>
              <button class="btn-success" style="width:100%;margin-top:1rem;padding:14px;" onclick="generateFixture()">⚡ Generate Fixture</button>
            </div>

            <div id="fixture-output-wrap" style="display:none;">
              <h3>📋 Generated Fixture (editable)</h3>
              <textarea id="fixtureOutput" style="width:100%;min-height:420px;font-family:monospace;white-space:pre-wrap;padding:14px;border:2px solid var(--border);border-radius:8px;"></textarea>
              <button class="btn-primary" style="width:100%;margin-top:1rem;padding:14px;" onclick="copyFixture()">📋 Copy Fixture</button>

              <h3 style="margin-top:2rem;">🖼️ Team Info Cards (JPG)</h3>
              <p style="color:#6b7280;font-size:0.9rem;margin-bottom:1rem;">Download a premium roster card for each team (player name, user ID, device).</p>
              <div style="display:grid;grid-template-columns:1fr 1fr;gap:1rem;">
                <button class="btn-success" id="fgDownloadTeam1Btn" onclick="downloadTeamInfoCard(1)" style="padding:14px;">📥 Download Team 1 JPG</button>
                <button class="btn-success" id="fgDownloadTeam2Btn" onclick="downloadTeamInfoCard(2)" style="padding:14px;">📥 Download Team 2 JPG</button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- CAPTAIN SECTION -->
    <div id="captain" class="section">
      <div class="card" style="max-width:960px; margin:0 auto;">
        <h2 style="text-align:center;"><span class="badge badge-success">🎖️</span> Captain Panel</h2>
        
        <div id="captain-login-area">
          <div style="max-width:400px;margin:0 auto;background:var(--card);border-radius:14px;padding:2rem;border:2px solid var(--border);">
            <div style="text-align:center;font-size:3rem;margin-bottom:1rem;">🔐</div>
            <label style="font-weight:700;color:var(--primary);display:block;">Team</label>
            <select id="captainTeamSelect" style="margin-bottom:1rem;"><option>Select</option></select>
            <label style="font-weight:700;color:var(--primary);display:block;">Password</label>
            <input type="password" id="teamPasswordLogin" placeholder="Password" style="margin-bottom:1rem;">
            <button class="btn-primary" style="width:100%;padding:14px;" onclick="captainLogin()">Login</button>
          </div>
        </div>

        <div id="captain-content" style="display:none;">
          <h3 id="currentTeamName" style="text-align:center;"></h3>
          
          <div class="admin-tabs" style="margin-bottom:1.5rem;">
            <button class="admin-tab-btn active" onclick="showCaptainTab(1)">👥 Players</button>
            <button class="admin-tab-btn" onclick="showCaptainTab(2)">📋 Squad Submission</button>
            <button class="admin-tab-btn" onclick="showCaptainTab(3)">📝 Change Log</button>
            <button class="admin-tab-btn" onclick="showCaptainTab(4)">⚙️ Settings</button>
          </div>

          <!-- CAPTAIN TAB 1: PLAYER REGISTRATION -->
          <div id="captain-tab1">
            <div style="background:var(--card);border-radius:14px;padding:1.5rem;border:2px solid var(--border);margin-bottom:1.5rem;">
              <h3>👥 Register Player</h3>
              <div class="form-grid">
                <div>
                  <label>Player Slot (1-30)</label>
                  <select id="playerSlot">
                    <option value="">Select Slot</option>
                  </select>
                </div>
                <div>
                  <label>Player Name *</label>
                  <input type="text" id="playerName" placeholder="Full player name">
                </div>
                <div>
                  <label>User ID</label>
                  <input type="text" id="playerUid" placeholder="In-game UID">
                </div>
                <div>
                  <label>Device Name</label>
                  <input type="text" id="playerDevice" placeholder="e.g., iPhone 14, Samsung S24">
                </div>
              </div>
              <button class="btn-success" style="width:100%;margin-top:1rem;padding:14px;" onclick="addCaptainPlayer()">✅ Add Player</button>
            </div>

            <h3>📋 Registered Players</h3>
            <div id="captain-players-list"></div>
          </div>

          <!-- CAPTAIN TAB 2: SQUAD SUBMISSION -->
          <div id="captain-tab2" style="display:none;">
            <div style="background:var(--card);border-radius:14px;padding:1.5rem;border:2px solid var(--border);">
              <h3>📋 Squad Submission (14-Man)</h3>
              <p style="color:#6b7280;font-size:0.9rem;margin-bottom:1rem;">Assign each registered player to a time slot. 🔑 = VPN.</p>
              <div id="squad-slot-counter" style="margin-bottom:1rem;"></div>
              <div id="squad-submission-form"></div>
              <div style="margin-top:1.5rem;">
                <label style="font-weight:700;color:var(--primary);display:block;margin-bottom:4px;">Captain Name *</label>
                <input type="text" id="submissionCaptainName" placeholder="Captain full name" required>
              </div>
              <button class="btn-success" style="width:100%;margin-top:1rem;padding:14px;" onclick="submitSquadSubmission()">✅ Submit Squad</button>
            </div>

            <h3 style="margin-top:2rem;">📜 Submission History</h3>
            <div id="squad-submission-history"></div>
          </div>

          <!-- CAPTAIN TAB 3: CHANGE LOG -->
          <div id="captain-tab3" style="display:none;">
            <h3>📝 Player Info Change Log</h3>
            <div id="captain-changelog-list"></div>
          </div>

          <!-- CAPTAIN TAB 4: SETTINGS -->
          <div id="captain-tab4" style="display:none;">
            <div style="background:var(--card);border-radius:14px;padding:1.5rem;border:2px solid var(--border);">
              <h3>🔑 Team Password</h3>
              <p style="color:#6b7280;margin-bottom:1rem;">Current password is hidden for security</p>
              <div class="form-grid">
                <div><label>New Password</label><input type="password" id="newTeamPass" placeholder="Enter new password"></div>
                <div><label>Confirm Password</label><input type="password" id="confirmTeamPass" placeholder="Confirm new password"></div>
              </div>
              <button class="btn-warning" style="width:100%;margin-top:1rem;padding:14px;" onclick="changeTeamPassword()">🔑 Change Password</button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <div class="toast" id="toast"></div>

  <script>
    let db, storage;
    const ADMIN_PASSWORD = "*Fardous#";
    let currentTeamId = null;
    let teamLogoUrl = '';
    let allPlayers = [];
    let standings = {};
    let groups = {};
    let scConfig = { name: 'La Viola Cup', stage: 'Group Stage', logo: '' };
    let lastFixtureData = null;

    // Initialize Firebase
    const firebaseConfig = {
      apiKey: "AIzaSyA6NZ7rL8o9pQ1w2e3r4t5y6u7i8o9p0q",
      authDomain: "sylhet-abf80.firebaseapp.com",
      databaseURL: "https://sylhet-abf80-default-rtdb.firebaseio.com",
      projectId: "sylhet-abf80",
      storageBucket: "sylhet-abf80.appspot.com",
      messagingSenderId: "123456789",
      appId: "1:123456789:web:abcdef123456"
    };

    if (!firebase.apps.length) {
      firebase.initializeApp(firebaseConfig);
      db = firebase.database();
      storage = firebase.storage();
    } else {
      db = firebase.database();
      storage = firebase.storage();
    }

    function showToast(msg, type = 'success') {
      const t = document.getElementById('toast');
      if (t) {
        t.textContent = msg;
        t.className = 'toast ' + type;
        t.style.display = 'block';
        setTimeout(() => t.style.display = 'none', 3000);
      }
    }

    function showSection(section) {
      document.querySelectorAll('.section').forEach(s => s.classList.remove('active'));
      document.getElementById(section).classList.add('active');
      document.querySelectorAll('nav button').forEach(b => b.classList.remove('active'));
      document.getElementById('nav-' + section).classList.add('active');
      
      if (section === 'viewer') { loadViewerData(); loadViewerFixtures(); calculateStandings(); renderPointTables(); }
      if (section === 'admin') loadAdminTeams();
    }

    function showAdminTab(tab) {
      for (let i = 1; i <= 6; i++) {
        const el = document.getElementById('admin-tab' + i);
        if (el) el.style.display = tab === i ? 'block' : 'none';
      }
      if (tab === 2) { loadTeamsForFixtures(); loadAdminFixtures(); }
      if (tab === 3) { loadTourneyList(); }
      if (tab === 4) { loadAdminPlayerChanges(); }
      if (tab === 5) {
        document.getElementById('scConfigName').value = scConfig.name || '';
        document.getElementById('scConfigStage').value = scConfig.stage || '';
        document.getElementById('scConfigLogo').value = scConfig.logo || '';
      }
      if (tab === 6) { loadFixtureGeneratorTeams(); }
    }

    // ========== ADMIN FUNCTIONS ==========
    function adminLogin() {
      const pass = document.getElementById('adminPass').value;
      if (pass === ADMIN_PASSWORD) {
        document.getElementById('admin-login').style.display = 'none';
        document.getElementById('admin-content').style.display = 'block';
        showAdminTab(1);
      } else {
        showToast('Wrong password', 'error');
      }
    }

    function registerTeam() {
      const name = document.getElementById('teamName').value.trim();
      const captain = document.getElementById('captainName').value.trim();
      const password = document.getElementById('teamPassword').value.trim();
      const logoUrl = document.getElementById('teamLogoUrl').value.trim();

      if (!name || !captain || !password) return showToast('Fill all required fields', 'error');
      if (!logoUrl) return showToast('Paste team logo URL', 'error');

      const teamId = 'team_' + Date.now();
      db.ref('teams/' + teamId).set({ name, captain, password, logo: logoUrl, createdAt: Date.now() })
        .then(() => {
          showToast('✅ Team registered!');
          ['teamName','captainName','teamPassword','teamLogoUrl'].forEach(id => document.getElementById(id).value = '');
          document.getElementById('teamLogoPreview').style.display = 'none';
          loadAdminTeams();
        })
        .catch(err => showToast('Registration failed: ' + err.message, 'error'));
    }

    function loadAdminTeams() {
      db.ref('teams').once('value', snapshot => {
        const teams = snapshot.val() || {};
        const entries = Object.entries(teams);
        if (entries.length === 0) {
          document.getElementById('admin-teams-list').innerHTML = '<div style="text-align:center;padding:2rem;color:#6b7280;">No teams yet</div>';
          return;
        }
        let html = '<table><thead><tr><th>Team</th><th>Captain</th><th>Password</th><th>Action</th></tr></thead><tbody>';
        entries.forEach(([id, team]) => {
          html += `<tr><td>${team.name}</td><td>${team.captain}</td><td>${team.password}</td><td><button class="btn-danger" onclick="deleteTeam('${id}')">Delete</button></td></tr>`;
        });
        html += '</tbody></table>';
        document.getElementById('admin-teams-list').innerHTML = html;
      });
    }

    function deleteTeam(teamId) {
      if (confirm('Delete team?')) {
        db.ref('teams/' + teamId).remove().then(() => { showToast('Deleted'); loadAdminTeams(); });
      }
    }

    // ========== ADMIN PLAYER CHANGES MANAGEMENT ==========
    function loadAdminPlayerChanges() {
      db.ref('changeLogs/playerName').once('value', snapshot => {
        const logs = snapshot.val() || {};
        const sortedLogs = Object.entries(logs)
          .sort((a, b) => b[1].timestamp - a[1].timestamp);

        if (sortedLogs.length === 0) {
          document.getElementById('admin-changes-list').innerHTML = '<div style="text-align:center;padding:2rem;color:#6b7280;">No player name changes to review</div>';
          return;
        }

        let html = '<table style="width:100%;border-collapse:collapse;background:white;border-radius:12px;overflow:hidden;box-shadow:0 2px 8px rgba(0,0,0,0.1);">';
        html += '<thead><tr style="background:linear-gradient(135deg, #3b82f6, #60a5fa);color:white;"><th style="padding:1rem;text-align:left;">Team</th><th style="padding:1rem;text-align:left;">Slot</th><th style="padding:1rem;text-align:left;">Old Name</th><th style="padding:1rem;text-align:left;">→ New Name</th><th style="padding:1rem;text-align:center;">Status</th><th style="padding:1rem;text-align:center;">Action</th></tr></thead><tbody>';

        sortedLogs.forEach(([logId, log]) => {
          db.ref('teams/' + log.teamId).once('value', teamSnap => {
            const teamName = teamSnap.val()?.name || 'Unknown';
            
            let statusBadge = '';
            if (log.status === 'pending') statusBadge = '🟡 <span style="font-weight:700;">Pending</span>';
            else if (log.status === 'approved') statusBadge = '✅ <span style="font-weight:700;color:#10b981;">Approved</span>';
            else statusBadge = '❌ <span style="font-weight:700;color:#ef4444;">Rejected</span>';

            let actionBtns = '';
            if (log.status === 'pending') {
              actionBtns = `
                <button class="btn-success" onclick="approvePlayerChange('${logId}', '${log.teamId}', ${log.slot}, '${log.newValue}')" style="padding:6px 8px;font-size:0.8rem;margin-right:4px;">✅ Approve</button>
                <button class="btn-danger" onclick="rejectPlayerChange('${logId}')" style="padding:6px 8px;font-size:0.8rem;">❌ Reject</button>
              `;
            } else {
              actionBtns = '<span style="color:#6b7280;font-size:0.9rem;">-</span>';
            }

            // Build row (this happens asynchronously, so we're updating a cached version)
            const row = `<tr style="border-bottom:1px solid var(--border);">
              <td style="padding:1rem;font-weight:700;">${teamName}</td>
              <td style="padding:1rem;"><span style="background:var(--primary);color:white;padding:4px 8px;border-radius:4px;">${log.slot}</span></td>
              <td style="padding:1rem;">${log.oldValue}</td>
              <td style="padding:1rem;font-weight:700;color:var(--primary);">${log.newValue}</td>
              <td style="padding:1rem;text-align:center;">${statusBadge}</td>
              <td style="padding:1rem;text-align:center;font-size:0.85rem;">${actionBtns}</td>
            </tr>`;
            
            // Insert row into table before closing tbody
            const tbody = document.querySelector('#admin-changes-list table tbody');
            if (tbody) {
              tbody.insertAdjacentHTML('beforeend', row);
            }
          });
        });

        html += '</tbody></table>';
        document.getElementById('admin-changes-list').innerHTML = html;
      });
    }

    function approvePlayerChange(logId, teamId, slot, newName) {
      if (!confirm('Approve this name change?')) return;

      // Update change log
      db.ref('changeLogs/playerName/' + logId).update({ status: 'approved' });

      // Update player name in squad
      db.ref('squads/' + teamId + '/' + slot).update({
        name: newName,
        nameChangeRequest: null
      }).then(() => {
        showToast('✅ Name change approved!');
        loadAdminPlayerChanges();
      });
    }

    function rejectPlayerChange(logId) {
      if (!confirm('Reject this name change?')) return;

      db.ref('changeLogs/playerName/' + logId).update({ status: 'rejected' }).then(() => {
        showToast('❌ Name change rejected');
        loadAdminPlayerChanges();
      });
    }

    function previewTeamLogo() {
      const url = document.getElementById('teamLogoUrl').value.trim();
      if (!url) return showToast('Enter URL first', 'error');
      teamLogoUrl = url;
      const preview = document.getElementById('teamLogoPreview');
      preview.src = url;
      preview.style.display = 'block';
      preview.onload = () => showToast('✅ Preview loaded!');
      preview.onerror = () => showToast('Invalid image URL', 'error');
    }

    function loadTeamsForFixtures() {
      db.ref('teams').once('value', snapshot => {
        const teams = snapshot.val() || {};
        let options = '<option>Select</option>';
        Object.entries(teams).forEach(([id, team]) => {
          options += `<option value="${id}">${team.name}</option>`;
        });
        document.getElementById('fixtureTeam1').innerHTML = options;
        document.getElementById('fixtureTeam2').innerHTML = options;
      });
    }

    function createFixture() {
      const t1 = document.getElementById('fixtureTeam1').value;
      const t2 = document.getElementById('fixtureTeam2').value;
      const date = document.getElementById('fixtureDate').value;
      const format = document.getElementById('fixtureFormat').value;
      if (!t1 || !t2 || !date || !format) return showToast('Fill all fields', 'error');
      if (t1 === t2) return showToast('Different teams!', 'error');

      db.ref('fixtures/fix_' + Date.now()).set({ team1: t1, team2: t2, date, format, createdAt: Date.now() })
        .then(() => {
          showToast('✅ Fixture created!');
          loadAdminFixtures();
        });
    }

    function loadAdminFixtures() {
      Promise.all([db.ref('fixtures').once('value'), db.ref('teams').once('value')]).then(([fs, ts]) => {
        const fixtures = fs.val() || {}, teams = ts.val() || {};
        let html = '';
        Object.entries(fixtures).forEach(([id, f]) => {
          const t1 = teams[f.team1]?.name || '?';
          const t2 = teams[f.team2]?.name || '?';
          html += `<div style="background:white;border:2px solid var(--border);padding:1rem;margin:0.5rem 0;border-radius:8px;">
            <div style="font-weight:700;color:var(--primary);">${t1} vs ${t2}</div>
            <div style="font-size:0.9rem;color:#6b7280;">📅 ${f.date} • ${f.format}</div>
            <button class="btn-danger" onclick="deleteFixture('${id}')" style="margin-top:0.5rem;">Delete</button>
          </div>`;
        });
        if (!html) html = '<div style="text-align:center;padding:2rem;color:#6b7280;">No fixtures</div>';
        document.getElementById('admin-fixtures-list').innerHTML = html;
      });
    }

    function deleteFixture(id) {
      if (confirm('Delete?')) {
        db.ref('fixtures/' + id).remove().then(() => { showToast('Deleted'); loadAdminFixtures(); });
      }
    }

    // ========== TOURNAMENT SYSTEM ==========
    let currentTourneyId = null;
    let tourneyData = {};

    function createTournament() {
      const name = document.getElementById('tourneyName').value.trim();
      const slots = parseInt(document.getElementById('tourneySlots').value);
      const teamsPerGroup = parseInt(document.getElementById('teamsPerGroup').value);

      if (!name) return showToast('Enter tournament name', 'error');

      const tourneId = 'tourney_' + Date.now();
      const numGroups = slots / teamsPerGroup;

      db.ref('tournaments/' + tourneId).set({
        name: name,
        slots: slots,
        teamsPerGroup: teamsPerGroup,
        numGroups: numGroups,
        createdAt: Date.now(),
        teams: {},
        seeding: {}
      }).then(() => {
        showToast('✅ Tournament created!');
        document.getElementById('tourneyName').value = '';
        loadTourneyList();
      });
    }

    function loadTourneyList() {
      db.ref('tournaments').once('value', snapshot => {
        const tournaments = snapshot.val() || {};
        let html = '';

        Object.entries(tournaments).forEach(([id, tourney]) => {
          const teamsCount = Object.keys(tourney.teams || {}).length;
          const groupLetters = [];
          for (let i = 0; i < tourney.numGroups; i++) {
            groupLetters.push(String.fromCharCode(65 + i)); // A, B, C, D...
          }

          html += `<div style="background:white;border:2px solid var(--border);border-radius:12px;padding:1.5rem;margin-bottom:1rem;">
            <div style="display:flex;justify-content:space-between;align-items:start;margin-bottom:1rem;">
              <div>
                <h3 style="color:var(--primary);margin:0;">${tourney.name}</h3>
                <div style="color:#6b7280;font-size:0.9rem;margin-top:0.5rem;">
                  ${tourney.slots} Slots • ${tourney.teamsPerGroup} per Group • ${teamsCount}/${tourney.slots} Teams
                </div>
                <div style="margin-top:0.5rem;font-size:0.85rem;">
                  Groups: ${groupLetters.join(', ')}
                </div>
              </div>
              <button class="btn-primary" onclick="editTournament('${id}')">🎯 Manage</button>
            </div>
          </div>`;
        });

        if (!html) html = '<div style="text-align:center;padding:2rem;color:#6b7280;">No tournaments yet</div>';
        document.getElementById('tourney-display').innerHTML = html;
      });
    }

    function editTournament(tourneId) {
      currentTourneyId = tourneId;
      db.ref('tournaments/' + tourneId).once('value', snapshot => {
        const tourney = snapshot.val();
        if (!tourney) return;

        tourneyData = tourney;

        // Show modal for team selection and seeding
        let html = `<div style="position:fixed;top:0;left:0;right:0;bottom:0;background:rgba(0,0,0,0.7);z-index:9999;display:flex;align-items:center;justify-content:center;">
          <div style="background:white;border-radius:16px;padding:2rem;max-width:800px;width:90%;max-height:90vh;overflow-y:auto;">
            <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:1.5rem;">
              <h2 style="margin:0;color:var(--primary);">🎯 ${tourney.name}</h2>
              <button onclick="closeModal()" style="background:none;border:none;font-size:2rem;cursor:pointer;">×</button>
            </div>

            <div style="margin-bottom:2rem;">
              <h3 style="color:var(--primary);margin-bottom:1rem;">1️⃣ Add Teams to Tournament</h3>
              <select id="teamSelect" multiple style="width:100%;height:200px;padding:0.5rem;border:2px solid var(--border);border-radius:8px;">`;

        db.ref('teams').once('value', teamsSnap => {
          const teams = teamsSnap.val() || {};
          Object.entries(teams).forEach(([id, team]) => {
            const isAdded = tourney.teams && tourney.teams[id];
            html += `<option value="${id}" ${isAdded ? 'selected' : ''}>${team.name}</option>`;
          });

          html += `</select>
              <p style="font-size:0.85rem;color:#6b7280;margin-top:0.5rem;">Select ${tourney.slots} teams (Ctrl+Click for multiple)</p>
              <button class="btn-success" onclick="addTeamsToTourney()" style="width:100%;margin-top:1rem;padding:12px;">✅ Add Selected Teams</button>
            </div>

            <div style="margin-bottom:2rem;">
              <h3 style="color:var(--primary);margin-bottom:1rem;">2️⃣ Manual Seeding (Assign to Groups)</h3>
              <div id="seedingForm"></div>
              <button class="btn-primary" onclick="saveTourneySeeding()" style="width:100%;margin-top:1rem;padding:12px;">💾 Save Seeding</button>
            </div>

            <button onclick="closeModal()" class="btn-danger" style="width:100%;padding:12px;">Close</button>
          </div>
        </div>`;

          document.body.insertAdjacentHTML('beforeend', html);
          renderSeedingForm(tourney);
        });
      });
    }

    function renderSeedingForm(tourney) {
      let html = '';
      const groupLetters = [];
      for (let i = 0; i < tourney.numGroups; i++) {
        groupLetters.push(String.fromCharCode(65 + i));
      }

      groupLetters.forEach(group => {
        html += `<div style="background:var(--card);border:2px solid var(--border);border-radius:8px;padding:1rem;margin-bottom:1rem;">
          <h4 style="color:var(--primary);margin-bottom:0.5rem;">📌 Group ${group}</h4>
          <select id="group-${group}-select" multiple style="width:100%;height:120px;padding:0.5rem;border:2px solid var(--border);border-radius:4px;">`;

        const teamIds = Object.keys(tourney.teams || {});
        db.ref('teams').once('value', teamsSnap => {
          const teams = teamsSnap.val() || {};
          teamIds.forEach(id => {
            if (teams[id]) {
              const isInGroup = tourney.seeding && tourney.seeding[id] === group;
              html += `<option value="${id}" ${isInGroup ? 'selected' : ''}>${teams[id].name}</option>`;
            }
          });
        });

        html += `</select></div>`;
      });

      document.getElementById('seedingForm').innerHTML = html;
    }

    function addTeamsToTourney() {
      const select = document.getElementById('teamSelect');
      const selected = Array.from(select.selectedOptions).map(opt => opt.value);

      if (selected.length === 0) return showToast('Select teams', 'error');

      const teamsObj = {};
      selected.forEach(teamId => {
        teamsObj[teamId] = true;
      });

      db.ref('tournaments/' + currentTourneyId + '/teams').set(teamsObj).then(() => {
        showToast('✅ Teams added!');
        renderSeedingForm({ ...tourneyData, teams: teamsObj });
      });
    }

    function saveTourneySeeding() {
      const seeding = {};
      const groupLetters = [];
      for (let i = 0; i < tourneyData.numGroups; i++) {
        groupLetters.push(String.fromCharCode(65 + i));
      }

      groupLetters.forEach(group => {
        const select = document.getElementById('group-' + group + '-select');
        if (select) {
          const selected = Array.from(select.selectedOptions).map(opt => opt.value);
          selected.forEach(teamId => {
            seeding[teamId] = group;
          });
        }
      });

      db.ref('tournaments/' + currentTourneyId + '/seeding').set(seeding).then(() => {
        showToast('✅ Seeding saved!');
        closeModal();
        loadTourneyList();
      });
    }

    function closeModal() {
      const modal = document.querySelector('[style*="position:fixed"][style*="z-index:9999"]');
      if (modal) modal.remove();
    }

    // ========== FIXTURE GENERATOR (password-protected via existing admin login) ==========
    function loadFixtureGeneratorTeams() {
      db.ref('teams').once('value', snapshot => {
        const teams = snapshot.val() || {};
        let options = '<option value="">Select</option>';
        Object.entries(teams).forEach(([id, team]) => {
          options += `<option value="${id}">${team.name}</option>`;
        });
        document.getElementById('fgTeam1').innerHTML = options;
        document.getElementById('fgTeam2').innerHTML = options;
      });
    }

    // Converts ASCII letters/digits to Mathematical Sans-Serif Bold unicode for fixture headings
    function toBoldUnicode(str) {
      const upperBase = 0x1D5D4, lowerBase = 0x1D5EE, digitBase = 0x1D7EC;
      let out = '';
      for (const ch of str) {
        const code = ch.codePointAt(0);
        if (code >= 65 && code <= 90) out += String.fromCodePoint(upperBase + (code - 65));
        else if (code >= 97 && code <= 122) out += String.fromCodePoint(lowerBase + (code - 97));
        else if (code >= 48 && code <= 57) out += String.fromCodePoint(digitBase + (code - 48));
        else out += ch;
      }
      return out;
    }

    function getLatestSquadSubmission(teamId) {
      return db.ref('squadSubmissions/' + teamId).once('value').then(snap => {
        const subs = snap.val() || {};
        const entries = Object.entries(subs).sort((a, b) => b[1].submittedAt - a[1].submittedAt);
        return entries.length ? entries[0][1] : null;
      });
    }

    function formatFixtureDate(baseDateStr, offsetDays) {
      const d = new Date(baseDateStr + 'T00:00:00');
      d.setDate(d.getDate() + offsetDays);
      return d.toLocaleDateString('en-US', { month: 'long', day: 'numeric' });
    }

    function groupAssignmentsBySlot(sub) {
      const grouped = {};
      TIME_SLOTS.forEach(s => grouped[s.key] = []);
      Object.values((sub && sub.assignments) || {}).forEach(a => {
        if (a.timeSlot && grouped[a.timeSlot]) grouped[a.timeSlot].push({ name: a.name, vpn: a.vpn });
      });
      return grouped;
    }

    // Matchup rule: within a time slot, a KEY (VPN) player always faces a NON-KEY player.
    // e.g. FIRST DAY KEY vs FIRST DAY NON-KEY. Only if counts don't allow a clean split
    // (mismatched vpn/non-vpn counts between the two teams) do leftovers fall back to
    // key-vs-key / non-key-vs-non-key so no player is dropped from the fixture.
    function pairKeyVsNonKey(listA, listB) {
      const vpnA = listA.filter(p => p.vpn).slice();
      const nonA = listA.filter(p => !p.vpn).slice();
      const vpnB = listB.filter(p => p.vpn).slice();
      const nonB = listB.filter(p => !p.vpn).slice();
      const pairs = [];

      while (vpnA.length && nonB.length) pairs.push([vpnA.shift(), nonB.shift()]);
      while (nonA.length && vpnB.length) pairs.push([nonA.shift(), vpnB.shift()]);
      // Fallback for leftovers when the key/non-key split doesn't match evenly
      while (vpnA.length && vpnB.length) pairs.push([vpnA.shift(), vpnB.shift()]);
      while (nonA.length && nonB.length) pairs.push([nonA.shift(), nonB.shift()]);
      while (vpnA.length) pairs.push([vpnA.shift(), null]);
      while (nonA.length) pairs.push([nonA.shift(), null]);
      while (vpnB.length) pairs.push([null, vpnB.shift()]);
      while (nonB.length) pairs.push([null, nonB.shift()]);

      return pairs;
    }

    async function generateFixture() {
      const team1Id = document.getElementById('fgTeam1').value;
      const team2Id = document.getElementById('fgTeam2').value;
      const startDate = document.getElementById('fgStartDate').value;
      const ref1 = document.getElementById('fgReferee1').value.trim() || 'TBD';
      const ref2 = document.getElementById('fgReferee2').value.trim() || 'TBD';

      if (!team1Id || !team2Id) return showToast('Select both teams', 'error');
      if (team1Id === team2Id) return showToast('Choose two different teams', 'error');
      if (!startDate) return showToast('Select match start date', 'error');

      const [team1Snap, team2Snap, sub1, sub2] = await Promise.all([
        db.ref('teams/' + team1Id).once('value'),
        db.ref('teams/' + team2Id).once('value'),
        getLatestSquadSubmission(team1Id),
        getLatestSquadSubmission(team2Id)
      ]);

      if (!sub1 || !sub2) return showToast('Both teams must submit a squad first', 'error');

      const team1Name = (team1Snap.val() || {}).name || 'Team 1';
      const team2Name = (team2Snap.val() || {}).name || 'Team 2';
      const g1 = groupAssignmentsBySlot(sub1);
      const g2 = groupAssignmentsBySlot(sub2);

      let totalMatches = 0;
      const lines = [];
      lines.push(toBoldUnicode('WARRIORS OF PBOC PRESENTS') + ' 💥', '');
      lines.push(toBoldUnicode('PBOC SUPERCOPA SEASON TWO') + ' ✨', '');
      lines.push(toBoldUnicode('GROUP STAGE') + ' 🔥', '');
      lines.push(`${team1Name} ⚔️ ${team2Name}`, '');

      TIME_SLOTS.forEach(s => {
        const date = formatFixtureDate(startDate, s.dateOffset);
        const headerLabel = toBoldUnicode(s.announceLabel || s.label) + (s.headerColon ? ':' : '');
        lines.push(`${headerLabel} ${date} | RESPONSE ${s.response}`, '');

        const p1 = g1[s.key] || [], p2 = g2[s.key] || [];
        const pairs = pairKeyVsNonKey(p1, p2);
        if (pairs.length === 0) {
          lines.push('No players assigned');
        } else {
          pairs.forEach(([a, b]) => {
            const aName = a ? a.name + (a.vpn ? ' 🔑' : '') : '-';
            const bName = b ? b.name + (b.vpn ? ' 🔑' : '') : '-';
            lines.push(`${aName} 🆚 ${bName}`);
            totalMatches++;
          });
        }
        lines.push('');
      });

      lines.push(toBoldUnicode('REFEREE') + ':');
      lines.push(`${ref1} & ${ref2}`, '');
      lines.push(toBoldUnicode('POINTS') + ':', '');
      lines.push(`${team1Name} : 0`);
      lines.push(`${team2Name} : 0`, '');
      lines.push(toBoldUnicode('MATCH REMAINING') + ` : ${totalMatches}`, '');
      lines.push(toBoldUnicode('RULES') + ':');
      lines.push('https://tinyurl.com/Pboc-supercopa');

      document.getElementById('fixtureOutput').value = lines.join('\n');
      document.getElementById('fixture-output-wrap').style.display = 'block';

      // Keep context so the JPG roster cards can be built without re-selecting teams
      lastFixtureData = { team1Id, team2Id, team1Name, team2Name };

      showToast('✅ Fixture generated!');
    }

    // ========== TEAM INFO CARD (JPG) ==========
    function downloadTeamInfoCard(teamNum) {
      if (!lastFixtureData) return showToast('Generate a fixture first', 'error');
      const teamId = teamNum === 1 ? lastFixtureData.team1Id : lastFixtureData.team2Id;
      const teamName = teamNum === 1 ? lastFixtureData.team1Name : lastFixtureData.team2Name;

      Promise.all([
        db.ref('teams/' + teamId).once('value'),
        db.ref('squads/' + teamId).once('value')
      ]).then(([teamSnap, squadSnap]) => {
        const team = teamSnap.val() || {};
        const squad = squadSnap.val() || {};
        const players = Object.values(squad).sort((a, b) => a.slot - b.slot);

        if (players.length === 0) {
          showToast('No registered players for this team', 'error');
          return;
        }

        let rowsHtml = '';
        players.forEach((p, idx) => {
          const stripe = idx % 2 === 0 ? 'rgba(255,255,255,0.03)' : 'transparent';
          rowsHtml += `<tr style="background:${stripe};">
            <td style="padding:10px 14px;color:#60a5fa;font-family:'Orbitron',sans-serif;font-weight:700;text-align:center;width:50px;">${p.slot}</td>
            <td style="padding:10px 14px;color:#ffffff;font-weight:700;font-size:15px;">${p.name}</td>
            <td style="padding:10px 14px;color:#cbd5e1;font-family:monospace;font-size:13px;">${p.uid || '-'}</td>
            <td style="padding:10px 14px;color:#cbd5e1;font-size:13px;">${p.device || '-'}</td>
          </tr>`;
        });

        const cardHtml = `
          <div id="team-info-card-render" style="width:820px;background:linear-gradient(160deg,#111827,#1f2937);padding:32px;font-family:'Montserrat',sans-serif;border:3px solid #60a5fa;border-radius:18px;box-shadow:0 0 40px rgba(59,130,246,0.5);">
            <div style="display:flex;align-items:center;gap:18px;margin-bottom:20px;padding-bottom:20px;border-bottom:2px solid rgba(96,165,250,0.4);">
              ${team.logo ? `<img src="${team.logo}" crossorigin="anonymous" style="width:70px;height:70px;border-radius:50%;object-fit:cover;border:3px solid #60a5fa;box-shadow:0 0 15px rgba(96,165,250,0.6);">` : ''}
              <div>
                <div style="font-family:'Orbitron',sans-serif;font-size:26px;font-weight:800;color:#ffffff;text-shadow:0 0 10px rgba(59,130,246,0.7);">${teamName}</div>
                <div style="color:#60a5fa;font-size:13px;font-weight:700;letter-spacing:1px;margin-top:4px;">SQUAD ROSTER • ${players.length} PLAYERS</div>
              </div>
            </div>
            <table style="width:100%;border-collapse:collapse;">
              <thead>
                <tr style="border-bottom:2px solid rgba(96,165,250,0.5);">
                  <th style="padding:8px 14px;text-align:center;color:#60a5fa;font-family:'Orbitron',sans-serif;font-size:12px;">SLOT</th>
                  <th style="padding:8px 14px;text-align:left;color:#60a5fa;font-family:'Orbitron',sans-serif;font-size:12px;">PLAYER NAME</th>
                  <th style="padding:8px 14px;text-align:left;color:#60a5fa;font-family:'Orbitron',sans-serif;font-size:12px;">USER ID</th>
                  <th style="padding:8px 14px;text-align:left;color:#60a5fa;font-family:'Orbitron',sans-serif;font-size:12px;">DEVICE</th>
                </tr>
              </thead>
              <tbody>${rowsHtml}</tbody>
            </table>
            <div style="margin-top:20px;text-align:center;color:#6b7280;font-size:11px;letter-spacing:1px;">WARRIORS OF PBOC • PBOC SUPERCOPA</div>
          </div>`;

        const wrap = document.createElement('div');
        wrap.style.position = 'fixed';
        wrap.style.left = '-9999px';
        wrap.style.top = '0';
        wrap.innerHTML = cardHtml;
        document.body.appendChild(wrap);

        const target = document.getElementById('team-info-card-render');
        html2canvas(target, { scale: 2, backgroundColor: '#111827', useCORS: true }).then(canvas => {
          const link = document.createElement('a');
          link.download = `${teamName.replace(/\s+/g, '_')}-roster-${Date.now()}.jpg`;
          link.href = canvas.toDataURL('image/jpeg', 0.95);
          link.click();
          document.body.removeChild(wrap);
          showToast('✅ Downloaded!');
        }).catch(() => {
          document.body.removeChild(wrap);
          showToast('Download failed', 'error');
        });
      });
    }

    function copyFixture() {
      const text = document.getElementById('fixtureOutput').value;
      navigator.clipboard.writeText(text).then(() => {
        showToast('✅ Fixture copied!');
      }).catch(() => showToast('Copy failed — select and copy manually', 'error'));
    }

    // ========== VIEWER FUNCTIONS ==========
    function loadViewerData() {
      db.ref('teams').once('value', snapshot => {
        const teams = snapshot.val() || {};
        let html = '';
        Object.entries(teams).forEach(([id, team]) => {
          html += `<div style="background:white;border:2px solid var(--border);padding:1rem;margin:0.5rem 0;border-radius:8px;display:flex;align-items:center;gap:1rem;">
            <img src="${team.logo}" style="width:50px;height:50px;border-radius:50%;object-fit:cover;">
            <div><div style="font-weight:700;">${team.name}</div><div style="font-size:0.9rem;color:#6b7280;">Captain: ${team.captain}</div></div>
          </div>`;
        });
        document.getElementById('viewer-teams').innerHTML = html || '<div>No teams</div>';
      });
    }

    function loadViewerFixtures() {
      Promise.all([db.ref('fixtures').once('value'), db.ref('teams').once('value')]).then(([fs, ts]) => {
        const fixtures = fs.val() || {}, teams = ts.val() || {};
        let html = '';
        Object.entries(fixtures).forEach(([, f]) => {
          const t1 = teams[f.team1]?.name || '?';
          const t2 = teams[f.team2]?.name || '?';
          html += `<div style="background:white;border:2px solid var(--border);padding:1rem;margin:0.5rem 0;border-radius:8px;">
            <div style="font-weight:700;color:var(--primary);">${t1} vs ${t2}</div>
            <div style="font-size:0.9rem;color:#6b7280;">📅 ${f.date} • ${f.format}</div>
          </div>`;
        });
        document.getElementById('viewer-fixtures').innerHTML = html || '<div>No matches</div>';
      });
    }

    function calculateStandings() {
      db.ref('results').once('value', snapshot => {
        const results = snapshot.val() || {};
        standings = {};
        Object.values(results).forEach(result => {
          if (!standings[result.team1]) standings[result.team1] = { w: 0, d: 0, l: 0, pts: 0 };
          if (!standings[result.team2]) standings[result.team2] = { w: 0, d: 0, l: 0, pts: 0 };
          
          if (result.score1 > result.score2) {
            standings[result.team1].w++;
            standings[result.team1].pts += 3;
            standings[result.team2].l++;
          } else if (result.score1 < result.score2) {
            standings[result.team2].w++;
            standings[result.team2].pts += 3;
            standings[result.team1].l++;
          } else {
            standings[result.team1].d++;
            standings[result.team1].pts++;
            standings[result.team2].d++;
            standings[result.team2].pts++;
          }
        });
      });
    }

    function renderPointTables() {
      db.ref('tournaments').once('value', tourneysSnap => {
        const tournaments = tourneysSnap.val() || {};
        
        if (Object.keys(tournaments).length === 0) {
          document.getElementById('points-table-view').innerHTML = '<div style="text-align:center;padding:2rem;color:#6b7280;">Create a tournament to see point tables</div>';
          return;
        }

        // Get the first/latest tournament
        const latestTourney = Object.entries(tournaments).sort((a, b) => b[1].createdAt - a[1].createdAt)[0];
        if (!latestTourney) return;

        const [tourneId, tourney] = latestTourney;
        db.ref('teams').once('value', teamsSnap => {
          const teams = teamsSnap.val() || {};
          let html = `<div style="background:var(--card);border-radius:8px;padding:1rem;margin-bottom:1.5rem;">
            <h3 style="color:var(--primary);">${tourney.name}</h3>
            <div style="color:#6b7280;font-size:0.9rem;">${Object.keys(tourney.teams || {}).length}/${tourney.slots} Teams • ${tourney.numGroups} Groups</div>
          </div>`;
          
          const groupLetters = [];
          for (let i = 0; i < tourney.numGroups; i++) {
            groupLetters.push(String.fromCharCode(65 + i));
          }

          groupLetters.forEach(group => {
            const teamsInGroup = Object.entries(teams)
              .filter(([id]) => tourney.seeding && tourney.seeding[id] === group)
              .sort((a, b) => (standings[b[0]]?.pts || 0) - (standings[a[0]]?.pts || 0));
            
            if (teamsInGroup.length === 0) return;
            
            html += `
              <div class="pt-container">
                <div class="pt-header">🏆 Group ${group} Standings</div>
                <table class="pt-table">
                  <thead class="pt-thead"><tr>
                    <th>Pos</th><th>Team</th><th>W</th><th>D</th><th>L</th><th>Pts</th>
                  </tr></thead>
                  <tbody class="pt-tbody">`;
            
            teamsInGroup.forEach(([id, team], idx) => {
              const s = standings[id] || { w: 0, d: 0, l: 0, pts: 0 };
              const medal = idx === 0 ? '🥇' : idx === 1 ? '🥈' : idx === 2 ? '🥉' : '  ';
              html += `<tr>
                <td style="text-align:center;">${medal}</td>
                <td><div class="pt-team"><img src="${team.logo}" style="width:40px;height:40px;border-radius:50%;object-fit:cover;border:2px solid var(--border);">${team.name}</div></td>
                <td>${s.w}</td><td>${s.d}</td><td>${s.l}</td><td class="pt-points">${s.pts}</td>
              </tr>`;
            });
            
            html += `</tbody></table></div>`;
          });
          
          document.getElementById('points-table-view').innerHTML = html;
        });
      });
    }

    function downloadPointTableJPG() {
      showToast('JPG download feature coming soon');
    }

    // ========== SCORECARD FUNCTIONS ==========
    // Strips 🔑 / @ / tabs / extra whitespace to leave a clean player name
    function cleanScorecardName(s) {
      return s.replace(/🔑/g, '').replace(/@/g, '').replace(/[\t]+/g, ' ').replace(/\s+/g, ' ').trim();
    }

    // Parses one "PlayerA 🔑 2🆚4 PlayerB 🔑" style line (tabs/spacing vary a lot in real pastes)
    const VS_EMOJI = '🆚';
    function parseScorecardMatchLine(rawLine) {
      const idx = rawLine.indexOf(VS_EMOJI);
      if (idx === -1) return null;

      let left = rawLine.slice(0, idx);
      let right = rawLine.slice(idx + VS_EMOJI.length); // 🆚 is a surrogate pair (2 code units)
      let scoreA = null, scoreB = null;

      const leftScoreMatch = left.match(/(\d+)\s*$/);
      if (leftScoreMatch) {
        scoreA = leftScoreMatch[1];
        left = left.slice(0, leftScoreMatch.index);
      }
      const rightScoreMatch = right.match(/^\s*(\d+)/);
      if (rightScoreMatch) {
        scoreB = rightScoreMatch[1];
        right = right.slice(rightScoreMatch.index + rightScoreMatch[0].length);
      }

      const vpnA = /🔑/.test(left);
      const vpnB = /🔑/.test(right);
      const nameA = cleanScorecardName(left);
      const nameB = cleanScorecardName(right);

      if (!nameA || !nameB) return null;
      return { nameA, nameB, scoreA, scoreB, vpnA, vpnB };
    }

    function renderScorecardMatches(elementId, matches) {
      let html = '';
      matches.forEach(m => {
        const hasScore = m.scoreA !== null && m.scoreB !== null;
        html += `<div style="display:flex;align-items:center;gap:10px;">
          <div class="sc-player-container" style="flex:1;">
            <div class="sc-player-name">${m.nameA}</div>
          </div>
          <div class="sc-score-box" style="${hasScore ? '' : 'opacity:0.55;font-size:14px;'}">${hasScore ? `${m.scoreA} - ${m.scoreB}` : 'VS'}</div>
          <div class="sc-player-container" style="flex:1;">
            <div class="sc-player-name">${m.nameB}</div>
          </div>
        </div>`;
      });
      document.getElementById(elementId).innerHTML = html;
    }

    function setLogoImg(imgId, url) {
      const img = document.getElementById(imgId);
      if (!img) return;
      if (url) {
        img.onerror = () => { img.style.display = 'none'; };
        img.src = url;
        img.style.display = 'block';
      } else {
        img.style.display = 'none';
        img.removeAttribute('src');
      }
    }

    async function generateScorecard() {
      const raw = document.getElementById('scPasteText').value;
      if (!raw || !raw.trim()) return showToast('Enter match data', 'error');

      const lines = raw.split('\n').map(l => l.trim()).filter(l => l.length > 0);

      // Team header line: "Team1 ⚔️ Team2" (also accepts the older ⚒️ symbol). Never the
      // line containing 🆚, since that's a player matchup, not the team separator.
      const teamSepRegex = /[\u2694\u2692]\uFE0F?/;
      let team1 = '', team2 = '';
      for (const line of lines) {
        if (line.includes('🆚')) continue;
        if (teamSepRegex.test(line)) {
          const parts = line.split(teamSepRegex).map(p => cleanScorecardName(p)).filter(Boolean);
          if (parts.length >= 2) { team1 = parts[0]; team2 = parts[1]; break; }
        }
      }
      if (!team1 || !team2) return showToast('Could not find a "Team1 ⚔️ Team2" line', 'error');

      // Player matchup lines: anything containing 🆚
      const matches = lines
        .filter(l => l.includes('🆚'))
        .map(parseScorecardMatchLine)
        .filter(Boolean);

      // Team score = sum of match wins (1 pt per win, 0 for draw), based on the
      // scores written on either side of 🆚 in every player-vs-player line.
      let score1 = null, score2 = null;
      let winsA = 0, winsB = 0, scoredMatches = 0;
      matches.forEach(m => {
        if (m.scoreA === null || m.scoreB === null) return;
        scoredMatches++;
        const a = parseInt(m.scoreA, 10), b = parseInt(m.scoreB, 10);
        if (a > b) winsA++;
        else if (b > a) winsB++;
        // draw: no points awarded to either side
      });

      if (scoredMatches > 0) {
        score1 = winsA;
        score2 = winsB;
      } else {
        // Fallback: no per-match scores found — try a "TeamName : number" line (POINTS section)
        const scoreLineRegex = /^(.+?)[\t ]*[:：][\t ]*(\d+)\s*$/;
        lines.forEach(line => {
          const m = line.match(scoreLineRegex);
          if (!m) return;
          const lineName = cleanScorecardName(m[1]);
          const val = parseInt(m[2], 10);
          if (lineName.toLowerCase() === team1.toLowerCase()) score1 = val;
          else if (lineName.toLowerCase() === team2.toLowerCase()) score2 = val;
        });
      }

      // Look up registered team logos by matching team name (case-insensitive)
      const teamsSnap = await db.ref('teams').once('value');
      const teams = Object.values(teamsSnap.val() || {});
      const findLogo = teamName => {
        const match = teams.find(t => t.name && t.name.trim().toLowerCase() === teamName.trim().toLowerCase());
        return match ? match.logo : '';
      };
      const logo1 = findLogo(team1);
      const logo2 = findLogo(team2);
      const tourneyLogo = document.getElementById('scLogoInput').value.trim() || scConfig.logo || '';

      const name = document.getElementById('scNameInput').value || scConfig.name;
      const stage = document.getElementById('scStageInput').value || scConfig.stage;
      const winnerText = (score1 === null || score2 === null) ? 'Winner: -'
        : score1 === score2 ? `Winner: Draw (${score1}-${score2})`
        : score1 > score2 ? `Winner: ${team1} 🏆` : `Winner: ${team2} 🏆`;

      ['sc-dark', 'sc-light'].forEach(prefix => {
        document.getElementById(`${prefix}-name`).textContent = name;
        document.getElementById(`${prefix}-stage`).textContent = stage;
        document.getElementById(`${prefix}-t1-name`).textContent = team1;
        document.getElementById(`${prefix}-t2-name`).textContent = team2;
        document.getElementById(`${prefix}-s1`).textContent = score1 !== null ? score1 : '0';
        document.getElementById(`${prefix}-s2`).textContent = score2 !== null ? score2 : '0';
        document.getElementById(`${prefix}-winner`).textContent = winnerText;
        setLogoImg(`${prefix}-logo`, tourneyLogo);
        setLogoImg(`${prefix}-t1-logo`, logo1);
        setLogoImg(`${prefix}-t2-logo`, logo2);
        renderScorecardMatches(`${prefix}-matches`, matches);
      });

      showToast(matches.length ? `✅ Scorecard generated! (${matches.length} matches found)` : '✅ Scorecard generated!');
    }

    function downloadScorecard(elementId) {
      html2canvas(document.getElementById(elementId), { scale: 2, backgroundColor: '#ffffff', useCORS: true }).then(canvas => {
        const link = document.createElement('a');
        link.download = `scorecard-${Date.now()}.png`;
        link.href = canvas.toDataURL();
        link.click();
        showToast('✅ Downloaded!');
      }).catch(() => showToast('Download failed', 'error'));
    }

    function previewTourneyLogo() {
      const url = document.getElementById('scConfigLogo').value.trim();
      if (!url) return showToast('Enter URL first', 'error');
      const preview = document.getElementById('scConfigLogoPreview');
      preview.src = url;
      preview.style.display = 'block';
      preview.onload = () => showToast('✅ Preview loaded!');
      preview.onerror = () => showToast('Invalid image URL', 'error');
    }

    function loadTournamentConfig() {
      db.ref('tournamentConfig').once('value', snapshot => {
        const cfg = snapshot.val();
        if (cfg) scConfig = { name: cfg.name || scConfig.name, stage: cfg.stage || scConfig.stage, logo: cfg.logo || '' };
      });
    }

    function saveConfig() {
      const name = document.getElementById('scConfigName').value.trim();
      const stage = document.getElementById('scConfigStage').value.trim();
      const logo = document.getElementById('scConfigLogo').value.trim();
      if (!name || !stage) return showToast('Fill fields', 'error');

      const cfg = { name, stage, logo };
      db.ref('tournamentConfig').set(cfg).then(() => {
        scConfig = cfg;
        showToast('✅ Config saved!');
      }).catch(err => showToast('Save failed: ' + err.message, 'error'));
    }

    // ========== DANGER ZONE: FULL STORAGE RESET ==========
    function deleteAllStorage() {
      if (!confirm('This will permanently delete ALL data — teams, squads, fixtures, tournaments, submissions, everything. This cannot be undone. Continue?')) return;

      const typed = prompt('Type DELETE (all caps) to confirm wiping all storage:');
      if (typed !== 'DELETE') {
        showToast('Cancelled — text did not match', 'error');
        return;
      }

      db.ref('/').remove().then(() => {
        scConfig = { name: 'La Viola Cup', stage: 'Group Stage', logo: '' };
        showToast('✅ All storage deleted');
        showAdminTab(1);
        loadAdminTeams();
      }).catch(err => showToast('Delete failed: ' + err.message, 'error'));
    }

    // ========== CAPTAIN FUNCTIONS ==========
    function captainLogin() {
      const teamId = document.getElementById('captainTeamSelect').value;
      const pass = document.getElementById('teamPasswordLogin').value;
      if (!teamId) return showToast('Select team', 'error');
      
      db.ref('teams/' + teamId).once('value', snapshot => {
        const team = snapshot.val();
        if (!team || team.password !== pass) return showToast('Wrong password', 'error');
        
        currentTeamId = teamId;
        document.getElementById('captain-login-area').style.display = 'none';
        document.getElementById('captain-content').style.display = 'block';
        document.getElementById('currentTeamName').textContent = '🎖️ ' + team.name + ' Squad Management';
        loadCaptainPlayers();
      });
    }

    function showCaptainTab(tab) {
      for (let i = 1; i <= 4; i++) {
        const el = document.getElementById('captain-tab' + i);
        if (el) el.style.display = tab === i ? 'block' : 'none';
      }
      const btns = document.querySelectorAll('#captain-content .admin-tab-btn');
      btns.forEach((b, i) => b.classList.toggle('active', i === tab - 1));
      if (tab === 2) { renderSquadSubmissionForm(); loadSquadSubmissionHistory(); }
      if (tab === 3) loadCaptainChangelog();
    }

    function addCaptainPlayer() {
      const slot = document.getElementById('playerSlot').value;
      const name = document.getElementById('playerName').value.trim();
      const uid = document.getElementById('playerUid').value.trim();
      const device = document.getElementById('playerDevice').value.trim();

      if (!slot) return showToast('Select slot', 'error');
      if (!name) return showToast('Enter player name', 'error');

      const playerId = 'player_' + currentTeamId + '_' + slot;
      
      db.ref('squads/' + currentTeamId + '/' + slot).set({
        slot: parseInt(slot),
        name: name,
        uid: uid,
        device: device,
        status: 'registered',
        registeredAt: Date.now(),
        changes: {}
      }).then(() => {
        showToast('✅ Player added!');
        ['playerSlot','playerName','playerUid','playerDevice'].forEach(id => document.getElementById(id).value = '');
        loadCaptainPlayers();
      }).catch(err => showToast('Error: ' + err.message, 'error'));
    }

    function loadCaptainPlayers() {
      db.ref('squads/' + currentTeamId).once('value', snapshot => {
        const players = snapshot.val() || {};
        const slots = Object.keys(players).length;

        if (slots === 0) {
          document.getElementById('captain-players-list').innerHTML = '<div style="text-align:center;padding:2rem;color:#6b7280;">No players registered yet</div>';
          return;
        }

        let html = `<div style="background:white;border-radius:8px;overflow:hidden;box-shadow:0 2px 8px rgba(0,0,0,0.1);">
          <div style="background:linear-gradient(135deg, #3b82f6, #60a5fa);color:white;padding:1rem;font-weight:700;">
            📊 Squad: ${slots}/30 Players
          </div>
          <table style="width:100%;border-collapse:collapse;">
            <thead>
              <tr style="background:#f3f4f6;border-bottom:2px solid var(--border);">
                <th style="padding:1rem;text-align:left;">Slot</th>
                <th style="padding:1rem;text-align:left;">Player Name</th>
                <th style="padding:1rem;text-align:left;">User ID</th>
                <th style="padding:1rem;text-align:left;">Device</th>
                <th style="padding:1rem;text-align:center;">Action</th>
              </tr>
            </thead>
            <tbody>`;

        Object.entries(players).forEach(([slot, player]) => {
          html += `<tr style="border-bottom:1px solid var(--border);">
            <td style="padding:1rem;"><span style="background:var(--primary);color:white;padding:4px 8px;border-radius:4px;font-weight:700;">${player.slot}</span></td>
            <td style="padding:1rem;">${player.name}</td>
            <td style="padding:1rem;color:#6b7280;font-family:monospace;">${player.uid || '-'}</td>
            <td style="padding:1rem;font-size:0.9rem;">${player.device || '-'}</td>
            <td style="padding:1rem;text-align:center;">
              <button class="btn-primary" onclick="openPlayerEditModal('${slot}')" style="padding:6px 12px;font-size:0.85rem;">✏️ Edit</button>
            </td>
          </tr>`;
        });

        html += `</tbody></table></div>`;
        document.getElementById('captain-players-list').innerHTML = html;
      });
    }

    function openPlayerEditModal(slot) {
      db.ref('squads/' + currentTeamId + '/' + slot).once('value', snapshot => {
        const player = snapshot.val();
        if (!player) return;

        let html = `<div style="position:fixed;top:0;left:0;right:0;bottom:0;background:rgba(0,0,0,0.7);z-index:9999;display:flex;align-items:center;justify-content:center;">
          <div style="background:white;border-radius:16px;padding:2rem;max-width:500px;width:90%;">
            <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:1.5rem;">
              <h2 style="margin:0;color:var(--primary);">✏️ Edit Player Slot ${player.slot}</h2>
              <button onclick="closePlayerModal()" style="background:none;border:none;font-size:2rem;cursor:pointer;">×</button>
            </div>

            <div style="background:var(--card);border:2px solid var(--border);border-radius:8px;padding:1rem;margin-bottom:1rem;">
              <p style="margin:0;color:#6b7280;font-size:0.9rem;">Original Name: <span style="color:var(--primary);font-weight:700;">${player.name}</span></p>
            </div>

            <div class="form-grid" style="gap:1rem;">
              <div>
                <label style="color:var(--primary);font-weight:700;display:block;margin-bottom:0.5rem;">🔤 Player Name (Requires Admin Review)</label>
                <input type="text" id="edit-playerName" value="${player.name}" placeholder="New player name" style="border:2px solid #fbbf24;">
              </div>
              <div>
                <label style="color:var(--primary);font-weight:700;display:block;margin-bottom:0.5rem;">🆔 User ID</label>
                <input type="text" id="edit-playerUid" value="${player.uid || ''}" placeholder="In-game UID">
              </div>
              <div>
                <label style="color:var(--primary);font-weight:700;display:block;margin-bottom:0.5rem;">📱 Device Name</label>
                <input type="text" id="edit-playerDevice" value="${player.device || ''}" placeholder="e.g., iPhone 14">
              </div>
            </div>

            <div style="margin-top:1.5rem;padding:1rem;background:#eff6ff;border-left:4px solid var(--primary);border-radius:4px;">
              <p style="margin:0;font-size:0.85rem;color:var(--text-light);">
                ⚠️ If you change the player name, it will be reviewed by admin before approval.
              </p>
            </div>

            <div style="display:grid;grid-template-columns:1fr 1fr;gap:1rem;margin-top:1.5rem;">
              <button class="btn-danger" onclick="deletePlayerSlot('${slot}')">🗑️ Delete</button>
              <button class="btn-success" onclick="savePlayerEdit('${slot}')">💾 Save Changes</button>
            </div>

            <button onclick="closePlayerModal()" class="btn-warning" style="width:100%;padding:12px;margin-top:1rem;">Cancel</button>
          </div>
        </div>`;

        document.body.insertAdjacentHTML('beforeend', html);
      });
    }

    function savePlayerEdit(slot) {
      const name = document.getElementById('edit-playerName').value.trim();
      const uid = document.getElementById('edit-playerUid').value.trim();
      const device = document.getElementById('edit-playerDevice').value.trim();

      if (!name) return showToast('Player name required', 'error');

      db.ref('squads/' + currentTeamId + '/' + slot).once('value', snapshot => {
        const player = snapshot.val();
        const nameChanged = name !== player.name;

        const updates = {
          uid: uid,
          device: device
        };

        // If name changed, create change log entry for admin review
        if (nameChanged) {
          const changeLog = {
            timestamp: Date.now(),
            teamId: currentTeamId,
            slot: slot,
            fieldChanged: 'playerName',
            oldValue: player.name,
            newValue: name,
            status: 'pending',
            reason: 'Name change requested by captain'
          };

          // Save change log
          db.ref('changeLogs/playerName/' + Date.now()).set(changeLog);

          // Mark player as pending review
          updates.nameChangeRequest = {
            newName: name,
            status: 'pending',
            requestedAt: Date.now()
          };

          showToast('✅ Changes saved! Name change pending admin review.', 'success');
        } else {
          showToast('✅ Player info updated!');
        }

        db.ref('squads/' + currentTeamId + '/' + slot).update(updates).then(() => {
          closePlayerModal();
          loadCaptainPlayers();
        });
      });
    }

    function deletePlayerSlot(slot) {
      if (confirm('Delete this player slot?')) {
        db.ref('squads/' + currentTeamId + '/' + slot).remove().then(() => {
          showToast('✅ Player deleted');
          closePlayerModal();
          loadCaptainPlayers();
        });
      }
    }

    function closePlayerModal() {
      const modal = document.querySelector('[style*="position:fixed"][style*="z-index:9999"]');
      if (modal) modal.remove();
    }

    function loadCaptainChangelog() {
      db.ref('changeLogs/playerName').once('value', snapshot => {
        const logs = snapshot.val() || {};
        const myTeamLogs = Object.entries(logs).filter(([_, log]) => log.teamId === currentTeamId);

        if (myTeamLogs.length === 0) {
          document.getElementById('captain-changelog-list').innerHTML = '<div style="text-align:center;padding:2rem;color:#6b7280;">No changes yet</div>';
          return;
        }

        let html = '<table style="width:100%;border-collapse:collapse;background:white;border-radius:12px;overflow:hidden;box-shadow:0 2px 8px rgba(0,0,0,0.1);">';
        html += '<thead><tr style="background:#f3f4f6;border-bottom:2px solid var(--border);"><th style="padding:1rem;text-align:left;">Slot</th><th style="padding:1rem;text-align:left;">Old Name</th><th style="padding:1rem;text-align:left;">New Name</th><th style="padding:1rem;text-align:center;">Status</th><th style="padding:1rem;text-align:left;">Date</th></tr></thead><tbody>';

        myTeamLogs.forEach(([id, log]) => {
          const statusBadge = log.status === 'pending' ? '🟡 Pending' : log.status === 'approved' ? '✅ Approved' : '❌ Rejected';
          html += `<tr style="border-bottom:1px solid var(--border);">
            <td style="padding:1rem;"><span style="background:var(--primary);color:white;padding:4px 8px;border-radius:4px;font-weight:700;">${log.slot}</span></td>
            <td style="padding:1rem;">${log.oldValue}</td>
            <td style="padding:1rem;font-weight:700;color:var(--primary);">${log.newValue}</td>
            <td style="padding:1rem;text-align:center;">${statusBadge}</td>
            <td style="padding:1rem;color:#6b7280;font-size:0.9rem;">${new Date(log.timestamp).toLocaleDateString()}</td>
          </tr>`;
        });

        html += '</tbody></table>';
        document.getElementById('captain-changelog-list').innerHTML = html;
      });
    }

    // ========== SQUAD SUBMISSION (14-MAN, FIXED FORM) ==========
    // 🔑 = VPN. Every slot is split exactly half VPN / half non-VPN.
    // FIRST DAY 4 (2🔑+2), STAR 2 (1🔑+1), MID NIGHT 2 (1🔑+1), LAST NIGHT 2 (1🔑+1), LATE NIGHT 4 (2🔑+2) = 14 total.
    const TIME_SLOTS = [
      { key: 'first_day',  label: 'FIRST DAY',  announceLabel: 'FIRST NIGHT', quota: 4, time: '12:00', dateOffset: 0, response: '11:50 PM- 12:00 AM', headerColon: true },
      { key: 'star',       label: 'STAR',       announceLabel: 'STAR',       quota: 2, time: '10:00', dateOffset: 1, response: '9:50 PM- 10:00 PM',  headerColon: false },
      { key: 'mid_night',  label: 'MID NIGHT',  announceLabel: 'MID NIGHT',  quota: 2, time: '11:00', dateOffset: 1, response: '10:50 PM- 11:00 PM', headerColon: false },
      { key: 'last_night', label: 'LAST NIGHT', announceLabel: 'LAST NIGHT', quota: 2, time: '12:00', dateOffset: 1, response: '11:50 PM- 12:00 AM', headerColon: false },
      { key: 'late_night', label: 'LATE NIGHT', announceLabel: 'LATE NIGHT', quota: 4, time: '1:00',  dateOffset: 2, response: '12:50 AM- 1:00 AM',  headerColon: false }
    ];
    const TOTAL_SQUAD_SLOTS = TIME_SLOTS.reduce((sum, s) => sum + s.quota, 0); // 14

    function buildPlayerOptionsHtml(players, selectedSlot) {
      let opts = `<option value="">Select player</option>`;
      players.forEach(p => {
        opts += `<option value="${p.slot}" ${String(selectedSlot) === String(p.slot) ? 'selected' : ''}>#${p.slot} ${p.name}</option>`;
      });
      return opts;
    }

    function renderSquadSubmissionForm() {
      db.ref('squads/' + currentTeamId).once('value', snapshot => {
        const players = snapshot.val() || {};
        const entries = Object.values(players).sort((a, b) => a.slot - b.slot);

        if (entries.length === 0) {
          document.getElementById('squad-submission-form').innerHTML =
            '<div style="text-align:center;padding:1.5rem;color:#6b7280;">Register players first (Players tab) before submitting a squad.</div>';
          document.getElementById('squad-slot-counter').innerHTML = '';
          return;
        }

        if (entries.length < TOTAL_SQUAD_SLOTS) {
          showToast(`⚠️ Only ${entries.length} players registered — need ${TOTAL_SQUAD_SLOTS} to fill the squad`, 'error');
        }

        let html = '';
        TIME_SLOTS.forEach(s => {
          const half = s.quota / 2;
          html += `<div style="background:white;border:2px solid var(--border);border-radius:10px;padding:1rem;margin-bottom:1rem;">
            <h4 style="color:var(--primary);margin-bottom:0.75rem;">📌 ${s.label} — ${s.quota} Players (${half} 🔑 VPN + ${half} Non-VPN)</h4>
            <div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(220px,1fr));gap:0.75rem;">`;

          for (let i = 0; i < half; i++) {
            html += `<div>
              <label style="font-size:0.8rem;font-weight:700;color:#2563eb;display:block;margin-bottom:2px;">🔑 VPN Player ${i + 1}</label>
              <select id="sub-${s.key}-vpn-${i}" onchange="updateSquadSlotCounter()">${buildPlayerOptionsHtml(entries, '')}</select>
            </div>`;
          }
          for (let i = 0; i < half; i++) {
            html += `<div>
              <label style="font-size:0.8rem;font-weight:700;color:#6b7280;display:block;margin-bottom:2px;">Non-VPN Player ${i + 1}</label>
              <select id="sub-${s.key}-novpn-${i}" onchange="updateSquadSlotCounter()">${buildPlayerOptionsHtml(entries, '')}</select>
            </div>`;
          }

          html += `</div></div>`;
        });

        document.getElementById('squad-submission-form').innerHTML = html;
        updateSquadSlotCounter();
      });
    }

    // Returns every fixed-slot select id paired with its {timeSlot, vpn} meaning
    function allSquadSelectDescriptors() {
      const list = [];
      TIME_SLOTS.forEach(s => {
        const half = s.quota / 2;
        for (let i = 0; i < half; i++) list.push({ id: `sub-${s.key}-vpn-${i}`, timeSlot: s.key, vpn: true });
        for (let i = 0; i < half; i++) list.push({ id: `sub-${s.key}-novpn-${i}`, timeSlot: s.key, vpn: false });
      });
      return list;
    }

    function updateSquadSlotCounter() {
      const descriptors = allSquadSelectDescriptors();
      const chosen = {}; // playerSlot -> count of selects using it
      let filled = 0;

      descriptors.forEach(d => {
        const el = document.getElementById(d.id);
        if (!el) return;
        el.style.borderColor = '';
        if (el.value) {
          filled++;
          chosen[el.value] = (chosen[el.value] || 0) + 1;
        }
      });

      let hasDuplicates = false;
      descriptors.forEach(d => {
        const el = document.getElementById(d.id);
        if (el && el.value && chosen[el.value] > 1) {
          el.style.borderColor = '#ef4444';
          hasDuplicates = true;
        }
      });

      const bg = hasDuplicates ? '#ef4444' : (filled === TOTAL_SQUAD_SLOTS ? '#10b981' : '#f59e0b');
      const msg = hasDuplicates ? '⚠️ Same player picked more than once' : `${filled} / ${TOTAL_SQUAD_SLOTS} slots filled`;
      document.getElementById('squad-slot-counter').innerHTML =
        `<div style="background:${bg};color:white;border-radius:8px;padding:10px 14px;text-align:center;font-weight:700;">${msg}</div>`;

      return { filled, hasDuplicates };
    }

    function submitSquadSubmission() {
      const captainName = document.getElementById('submissionCaptainName').value.trim();
      if (!captainName) return showToast('Captain Name is required', 'error');

      const { filled, hasDuplicates } = updateSquadSlotCounter();
      if (hasDuplicates) return showToast('Each player can only be assigned to one slot', 'error');
      if (filled !== TOTAL_SQUAD_SLOTS) return showToast(`Fill all ${TOTAL_SQUAD_SLOTS} slots before submitting`, 'error');

      db.ref('squads/' + currentTeamId).once('value', snapshot => {
        const players = snapshot.val() || {};
        const assignments = {};

        allSquadSelectDescriptors().forEach(d => {
          const el = document.getElementById(d.id);
          const playerSlot = el ? el.value : '';
          if (!playerSlot) return;
          const player = players[playerSlot];
          if (!player) return;
          assignments[playerSlot] = { name: player.name, timeSlot: d.timeSlot, vpn: d.vpn };
        });

        db.ref('squadSubmissions/' + currentTeamId + '/' + Date.now()).set({
          captainName,
          assignments,
          submittedAt: Date.now()
        }).then(() => {
          showToast('✅ Squad submitted!');
          loadSquadSubmissionHistory();
        }).catch(err => showToast('Error: ' + err.message, 'error'));
      });
    }

    function loadSquadSubmissionHistory() {
      db.ref('squadSubmissions/' + currentTeamId).once('value', snapshot => {
        const subs = snapshot.val() || {};
        const entries = Object.entries(subs).sort((a, b) => b[1].submittedAt - a[1].submittedAt);

        if (entries.length === 0) {
          document.getElementById('squad-submission-history').innerHTML =
            '<div style="text-align:center;padding:1.5rem;color:#6b7280;">No submissions yet</div>';
          return;
        }

        const slotLabel = key => {
          const s = TIME_SLOTS.find(t => t.key === key);
          return s ? s.label : key;
        };

        let html = '';
        entries.forEach(([id, sub]) => {
          const rows = Object.entries(sub.assignments || {}).sort((a, b) => a[0] - b[0]);
          html += `<div style="background:white;border:2px solid var(--border);border-radius:10px;padding:1rem;margin-bottom:1rem;">
            <div style="font-weight:700;color:var(--primary);margin-bottom:0.5rem;">🎖️ ${sub.captainName} • ${new Date(sub.submittedAt).toLocaleString()}</div>
            <table style="width:100%;border-collapse:collapse;">
              <thead><tr style="background:#f3f4f6;"><th style="padding:8px;text-align:left;">Slot</th><th style="padding:8px;text-align:left;">Player</th><th style="padding:8px;text-align:left;">Time Slot</th></tr></thead>
              <tbody>`;
          rows.forEach(([slot, a]) => {
            html += `<tr style="border-bottom:1px solid var(--border);">
              <td style="padding:8px;"><span style="background:var(--primary);color:white;padding:4px 8px;border-radius:4px;">${slot}</span></td>
              <td style="padding:8px;">${a.name}</td>
              <td style="padding:8px;">${slotLabel(a.timeSlot)}${a.vpn ? ' 🔑' : ''}</td>
            </tr>`;
          });
          html += `</tbody></table></div>`;
        });

        document.getElementById('squad-submission-history').innerHTML = html;
      });
    }

    function changeTeamPassword() {
      const newPass = document.getElementById('newTeamPass').value.trim();
      const confirmPass = document.getElementById('confirmTeamPass').value.trim();

      if (!newPass || !confirmPass) return showToast('Enter passwords', 'error');
      if (newPass !== confirmPass) return showToast('Passwords do not match', 'error');
      if (newPass.length < 6) return showToast('Password must be 6+ characters', 'error');

      db.ref('teams/' + currentTeamId).update({ password: newPass }).then(() => {
        showToast('✅ Password changed!');
        document.getElementById('newTeamPass').value = '';
        document.getElementById('confirmTeamPass').value = '';
      });
    }

    // Initialize
    document.addEventListener('DOMContentLoaded', () => {
      loadTournamentConfig();

      // Populate player slot dropdown (1-30) — was broken because template literal
      // syntax doesn't execute inside raw HTML markup, leaving the slot select empty.
      const slotSelect = document.getElementById('playerSlot');
      for (let i = 1; i <= 30; i++) {
        const opt = document.createElement('option');
        opt.value = i;
        opt.textContent = i;
        slotSelect.appendChild(opt);
      }

      db.ref('teams').on('value', snapshot => {
        const teams = snapshot.val() || {};
        let options = '<option>Select</option>';
        Object.entries(teams).forEach(([id, team]) => {
          options += `<option value="${id}">${team.name}</option>`;
        });
        document.getElementById('captainTeamSelect').innerHTML = options;
      });
    });
  </script>
</body>
</html>
