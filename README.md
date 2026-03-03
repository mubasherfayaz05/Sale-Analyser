<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>MR Smart Sales & Unit Analyzer</title>
<link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700;800&family=Nunito:wght@400;600;700;800;900&display=swap" rel="stylesheet"/>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.min.js"></script>
<style>
  /* ===== CSS VARIABLES ===== */
  :root {
    --primary:       #0a6e4f;
    --primary-light: #12a374;
    --primary-dark:  #074d38;
    --accent:        #00c98d;
    --accent2:       #1a7fbf;
    --accent2-light: #2799df;
    --bg:            #f0f4f2;
    --surface:       #ffffff;
    --surface2:      #e8f2ed;
    --border:        #c6ddd5;
    --text:          #0d2b20;
    --text-muted:    #557a6a;
    --danger:        #e03b3b;
    --warn:          #e8890c;
    --success:       #14a356;
    --sidebar-w:     270px;
    --radius:        12px;
    --shadow:        0 4px 24px rgba(10,110,79,.10);
    --shadow-lg:     0 8px 40px rgba(10,110,79,.16);
    --font-head:     'Nunito', 'Plus Jakarta Sans', sans-serif;
    --font-body:     'Plus Jakarta Sans', 'Nunito', sans-serif;
  }
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body {
    font-family: var(--font-body);
    background: var(--bg);
    color: var(--text);
    min-height: 100vh;
    display: flex;
  }

  /* ===== SIDEBAR ===== */
  #sidebar {
    width: var(--sidebar-w);
    background: linear-gradient(160deg, var(--primary-dark) 0%, var(--primary) 60%, var(--primary-light) 100%);
    display: flex;
    flex-direction: column;
    position: fixed;
    top: 0; left: 0; bottom: 0;
    z-index: 100;
    transition: transform .3s ease;
    overflow-y: auto;
  }
  .sidebar-brand {
    padding: 28px 22px 20px;
    border-bottom: 1px solid rgba(255,255,255,.12);
  }
  .brand-icon {
    width: 44px; height: 44px;
    background: var(--accent);
    border-radius: 10px;
    display: flex; align-items: center; justify-content: center;
    font-size: 20px;
    margin-bottom: 10px;
    box-shadow: 0 4px 14px rgba(0,201,141,.4);
  }
  .brand-name {
    font-family: var(--font-head);
    font-size: 15px; font-weight: 800;
    color: #fff;
    line-height: 1.2;
  }
  .brand-sub { font-size: 11px; color: rgba(255,255,255,.55); margin-top: 2px; }

  .nav-section-label {
    font-size: 10px; font-weight: 600;
    color: rgba(255,255,255,.4);
    letter-spacing: .12em;
    text-transform: uppercase;
    padding: 18px 22px 6px;
  }
  .nav-item {
    display: flex; align-items: center; gap: 12px;
    padding: 11px 22px;
    color: rgba(255,255,255,.75);
    cursor: pointer;
    border-radius: 0;
    transition: background .2s, color .2s;
    font-size: 14px; font-weight: 500;
    border-left: 3px solid transparent;
    text-decoration: none;
  }
  .nav-item:hover { background: rgba(255,255,255,.08); color: #fff; }
  .nav-item.active {
    background: rgba(0,201,141,.18);
    color: var(--accent);
    border-left-color: var(--accent);
  }
  .nav-item .nav-icon { font-size: 18px; width: 22px; text-align: center; }

  .sidebar-footer {
    margin-top: auto;
    padding: 16px 22px;
    border-top: 1px solid rgba(255,255,255,.10);
    font-size: 11px;
    color: rgba(255,255,255,.35);
  }

  /* ===== MAIN CONTENT ===== */
  #main {
    margin-left: var(--sidebar-w);
    flex: 1;
    display: flex;
    flex-direction: column;
    min-height: 100vh;
  }
  .topbar {
    background: var(--surface);
    border-bottom: 1px solid var(--border);
    padding: 14px 28px;
    display: flex; align-items: center; justify-content: space-between;
    position: sticky; top: 0; z-index: 50;
    box-shadow: 0 2px 8px rgba(10,110,79,.06);
  }
  .topbar-title {
    font-family: var(--font-head);
    font-size: 18px; font-weight: 700;
    color: var(--primary);
  }
  .topbar-date {
    font-size: 13px; color: var(--text-muted);
    background: var(--surface2);
    padding: 5px 12px; border-radius: 20px;
  }
  #menu-toggle {
    display: none;
    background: none; border: none; cursor: pointer;
    font-size: 22px; color: var(--primary);
    padding: 4px 8px;
  }
  .page-content { padding: 28px; flex: 1; }
  .page { display: none; }
  .page.active { display: block; }

  /* ===== SECTION HEADERS ===== */
  .section-header {
    display: flex; align-items: center; justify-content: space-between;
    margin-bottom: 22px;
  }
  .section-title {
    font-family: var(--font-head);
    font-size: 22px; font-weight: 800;
    color: var(--primary-dark);
  }
  .section-title span {
    color: var(--accent);
  }

  /* ===== CARDS ===== */
  .cards-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
    gap: 16px;
    margin-bottom: 28px;
  }
  .card {
    background: var(--surface);
    border-radius: var(--radius);
    padding: 20px 22px;
    box-shadow: var(--shadow);
    border: 1px solid var(--border);
    position: relative;
    overflow: hidden;
    transition: transform .2s, box-shadow .2s;
  }
  .card:hover { transform: translateY(-2px); box-shadow: var(--shadow-lg); }
  .card::before {
    content: '';
    position: absolute;
    top: 0; left: 0; right: 0;
    height: 3px;
    background: linear-gradient(90deg, var(--primary), var(--accent));
  }
  .card-icon {
    font-size: 26px; margin-bottom: 10px;
    display: block;
  }
  .card-label {
    font-size: 11px; font-weight: 600;
    color: var(--text-muted);
    text-transform: uppercase;
    letter-spacing: .06em;
    margin-bottom: 4px;
  }
  .card-value {
    font-family: var(--font-head);
    font-size: 22px; font-weight: 800;
    color: var(--primary-dark);
    line-height: 1;
  }
  .card-sub { font-size: 12px; color: var(--text-muted); margin-top: 4px; }
  .card.accent::before { background: linear-gradient(90deg, var(--accent2), var(--accent2-light)); }
  .card.green::before { background: linear-gradient(90deg, var(--success), var(--accent)); }
  .card.warn::before  { background: linear-gradient(90deg, var(--warn), #f5c842); }

  /* ===== PROGRESS BAR ===== */
  .progress-wrap { margin-top: 8px; }
  .progress-bar-bg {
    height: 6px; border-radius: 3px;
    background: var(--surface2);
    overflow: hidden;
  }
  .progress-bar-fill {
    height: 100%; border-radius: 3px;
    background: linear-gradient(90deg, var(--primary-light), var(--accent));
    transition: width .6s ease;
  }
  .progress-bar-fill.danger { background: linear-gradient(90deg, var(--danger), #f55); }
  .progress-bar-fill.success { background: linear-gradient(90deg, var(--success), var(--accent)); }
  .progress-label { font-size: 11px; color: var(--text-muted); margin-top: 3px; }

  /* ===== TABLE ===== */
  .table-wrap {
    background: var(--surface);
    border-radius: var(--radius);
    box-shadow: var(--shadow);
    border: 1px solid var(--border);
    overflow-x: auto;
    margin-bottom: 28px;
  }
  .table-header {
    padding: 16px 20px;
    border-bottom: 1px solid var(--border);
    display: flex; align-items: center; justify-content: space-between;
  }
  .table-title {
    font-family: var(--font-head);
    font-size: 15px; font-weight: 700;
    color: var(--primary-dark);
  }
  table {
    width: 100%;
    border-collapse: collapse;
    font-size: 13px;
  }
  thead th {
    background: var(--surface2);
    padding: 10px 14px;
    text-align: left;
    font-size: 11px; font-weight: 700;
    color: var(--text-muted);
    text-transform: uppercase;
    letter-spacing: .06em;
    white-space: nowrap;
  }
  tbody tr { border-bottom: 1px solid var(--border); transition: background .15s; }
  tbody tr:last-child { border-bottom: none; }
  tbody tr:hover { background: var(--surface2); }
  tbody td { padding: 10px 14px; color: var(--text); vertical-align: middle; }
  .badge {
    display: inline-block;
    padding: 2px 9px; border-radius: 20px;
    font-size: 11px; font-weight: 600;
  }
  .badge-success { background: #d4f5e6; color: #0e7a3d; }
  .badge-warn    { background: #fef3dc; color: #c07000; }
  .badge-danger  { background: #fde8e8; color: #b82020; }
  .badge-info    { background: #ddeeff; color: #1a5fa0; }

  /* ===== FORMS ===== */
  .form-card {
    background: var(--surface);
    border-radius: var(--radius);
    box-shadow: var(--shadow);
    border: 1px solid var(--border);
    padding: 28px;
    margin-bottom: 28px;
    max-width: 600px;
  }
  .form-title {
    font-family: var(--font-head);
    font-size: 17px; font-weight: 700;
    color: var(--primary-dark);
    margin-bottom: 20px;
    padding-bottom: 12px;
    border-bottom: 1px solid var(--border);
  }
  .form-group { margin-bottom: 18px; }
  .form-label {
    display: block;
    font-size: 12px; font-weight: 600;
    color: var(--text-muted);
    text-transform: uppercase;
    letter-spacing: .06em;
    margin-bottom: 6px;
  }
  .form-control {
    width: 100%;
    padding: 10px 14px;
    border: 1.5px solid var(--border);
    border-radius: 8px;
    font-family: var(--font-body);
    font-size: 14px;
    color: var(--text);
    background: var(--surface);
    transition: border .2s, box-shadow .2s;
    outline: none;
  }
  .form-control:focus {
    border-color: var(--primary-light);
    box-shadow: 0 0 0 3px rgba(18,163,116,.12);
  }
  select.form-control { cursor: pointer; }
  .btn {
    padding: 10px 22px;
    border: none; border-radius: 8px;
    font-family: var(--font-body);
    font-size: 14px; font-weight: 600;
    cursor: pointer;
    transition: background .2s, transform .15s, box-shadow .2s;
    display: inline-flex; align-items: center; gap: 7px;
  }
  .btn:active { transform: scale(.97); }
  .btn-primary {
    background: linear-gradient(135deg, var(--primary), var(--primary-light));
    color: #fff;
    box-shadow: 0 4px 14px rgba(10,110,79,.25);
  }
  .btn-primary:hover { box-shadow: 0 6px 20px rgba(10,110,79,.35); }
  .btn-accent {
    background: linear-gradient(135deg, var(--accent2), var(--accent2-light));
    color: #fff;
    box-shadow: 0 4px 14px rgba(26,127,191,.25);
  }
  .btn-danger {
    background: linear-gradient(135deg, #c0392b, var(--danger));
    color: #fff;
    box-shadow: 0 4px 14px rgba(224,59,59,.25);
  }
  .btn-sm { padding: 5px 12px; font-size: 12px; }

  /* ===== TOAST ===== */
  #toast {
    position: fixed;
    bottom: 28px; right: 28px;
    background: var(--primary-dark);
    color: #fff;
    padding: 12px 20px;
    border-radius: 10px;
    font-size: 13px; font-weight: 500;
    box-shadow: var(--shadow-lg);
    z-index: 9999;
    display: none;
    align-items: center; gap: 8px;
    animation: slideUp .3s ease;
  }
  @keyframes slideUp { from { transform: translateY(20px); opacity: 0; } to { transform: translateY(0); opacity: 1; } }

  /* ===== CHARTS ===== */
  .charts-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 20px;
    margin-bottom: 28px;
  }
  .chart-card {
    background: var(--surface);
    border-radius: var(--radius);
    box-shadow: var(--shadow);
    border: 1px solid var(--border);
    padding: 20px;
  }
  .chart-card-title {
    font-family: var(--font-head);
    font-size: 14px; font-weight: 700;
    color: var(--primary-dark);
    margin-bottom: 16px;
  }

  /* ===== OVERALL TARGET TRACKER ===== */
  .target-tracker {
    background: linear-gradient(135deg, var(--primary-dark), var(--primary));
    border-radius: var(--radius);
    padding: 24px 28px;
    color: #fff;
    margin-bottom: 28px;
    box-shadow: var(--shadow-lg);
    display: flex; flex-wrap: wrap; gap: 20px; align-items: center;
  }
  .tt-label { font-size: 11px; color: rgba(255,255,255,.6); text-transform: uppercase; letter-spacing: .07em; }
  .tt-value { font-family: var(--font-head); font-size: 22px; font-weight: 800; }
  .tt-item { flex: 1; min-width: 120px; }
  .tt-progress-bg {
    height: 8px; background: rgba(255,255,255,.2);
    border-radius: 4px; margin-top: 12px; overflow: hidden;
  }
  .tt-progress-fill {
    height: 100%; border-radius: 4px;
    background: linear-gradient(90deg, var(--accent), #a8ffe0);
    transition: width .8s ease;
  }

  /* ===== EDITABLE TABLE ===== */
  .edit-input {
    width: 90px;
    padding: 5px 8px;
    border: 1.5px solid var(--border);
    border-radius: 6px;
    font-size: 13px;
    color: var(--text);
    background: var(--surface);
    outline: none;
    transition: border .2s;
  }
  .edit-input:focus { border-color: var(--primary-light); }

  /* ===== SALES LOG ===== */
  .log-entry {
    display: grid;
    grid-template-columns: 130px 1fr 80px 100px 100px 100px auto;
    gap: 8px;
    align-items: center;
    padding: 10px 14px;
    border-bottom: 1px solid var(--border);
    font-size: 13px;
    transition: background .15s;
  }
  .log-entry:hover { background: var(--surface2); }
  .log-entry:last-child { border-bottom: none; }

  /* ===== RESPONSIVE ===== */
  /* ── 900px: stack charts ── */
  @media (max-width: 900px) {
    .charts-grid { grid-template-columns: 1fr; }
  }

  /* ── 768px: mobile layout ── */
  @media (max-width: 768px) {
    :root { --sidebar-w: 260px; }
    #sidebar { transform: translateX(-100%); box-shadow: var(--shadow-lg); }
    #sidebar.open { transform: translateX(0); }
    #main { margin-left: 0; }
    #menu-toggle { display: block; }
    .page-content { padding: 14px; }
    .topbar { padding: 10px 14px; }
    .topbar-title { font-size: 15px; }
    .topbar-powered { display: none; }
    .topbar-date { font-size: 11px; padding: 4px 9px; }
    .cards-grid { grid-template-columns: repeat(2, 1fr); gap: 12px; }
    .card { padding: 15px 16px; }
    .card-value { font-size: 18px; }
    .tracker { flex-direction: column; gap: 10px; padding: 18px 16px; }
    .tt-item { min-width: unset; }
    .section-title, .sec-title { font-size: 18px; }
    .form-card { padding: 18px 16px; max-width: 100%; }
    .table-wrap { border-radius: 8px; }
    .table-hdr, .table-header { padding: 12px 14px; flex-wrap: wrap; gap: 8px; }
    .table-title { font-size: 13px; }
    table { font-size: 12px; }
    thead th { padding: 8px 10px; font-size: 10px; }
    tbody td { padding: 8px 10px; }
    .btn { font-size: 12px; padding: 8px 14px; }
    .btn-sm { font-size: 11px; padding: 5px 10px; }
    .charts-grid { gap: 14px; }
    .chart-card { padding: 14px; }
    .page-footer { font-size: 11px; padding: 12px 16px; }
  }

  /* ── 480px: single column ── */
  @media (max-width: 480px) {
    .cards-grid { grid-template-columns: 1fr; gap: 10px; }
    .card-value { font-size: 20px; }
    .topbar-date { display: none; }
    .section-title, .sec-title { font-size: 16px; }
    .sec-hdr, .section-header { flex-wrap: wrap; gap: 8px; }
    .form-card { padding: 14px 12px; }
    .tracker { padding: 14px 12px; }
    .tt-val { font-size: 17px; }
    table { font-size: 11px; }
    thead th { padding: 7px 8px; }
    tbody td { padding: 7px 8px; }
    .edit-input, .ei { width: 75px; font-size: 12px; }
    #toast { bottom: 14px; right: 14px; left: 14px; font-size: 12px; }
  }

  /* ===== OVERLAY for mobile sidebar ===== */
  #overlay {
    display: none;
    position: fixed; inset: 0;
    background: rgba(0,0,0,.45);
    z-index: 99;
  }
  #overlay.show { display: block; }

  /* ===== MISC ===== */
  .text-right { text-align: right; }
  .text-center { text-align: center; }
  .color-success { color: var(--success); font-weight: 600; }
  .color-danger  { color: var(--danger);  font-weight: 600; }
  .color-warn    { color: var(--warn);    font-weight: 600; }
  .mt-8  { margin-top: 8px; }
  .mb-16 { margin-bottom: 16px; }
  .flex  { display: flex; }
  .gap-8 { gap: 8px; }
  .items-center { align-items: center; }
  .empty-state {
    text-align: center; padding: 40px 20px;
    color: var(--text-muted); font-size: 14px;
  }
  .empty-state .empty-icon { font-size: 40px; margin-bottom: 10px; }
</style>
</head>
<body>

<!-- SIDEBAR -->
<nav id="sidebar">
  <div class="sidebar-brand">
    <div class="brand-icon">💊</div>
    <div class="brand-name">MR Smart Sales<br>& Unit Analyzer</div>
    <div class="brand-sub">Medical Representative Tool</div><div style="margin-top:9px;padding-top:9px;border-top:1px solid rgba(255,255,255,.15);font-size:11px;color:rgba(255,255,255,.8);font-weight:800;">&#9889; Powered by Mubasher Fayaz</div>
  </div>

  <div class="nav-section-label">Navigation</div>
  <a class="nav-item active" onclick="navigate('dashboard')" href="#">
    <span class="nav-icon">📊</span> Dashboard
  </a>
  <a class="nav-item" onclick="navigate('sales')" href="#">
    <span class="nav-icon">➕</span> Daily Sales Entry
  </a>
  <a class="nav-item" onclick="navigate('target')" href="#">
    <span class="nav-icon">🎯</span> Set Target
  </a>
  <a class="nav-item" onclick="navigate('products')" href="#">
    <span class="nav-icon">📦</span> Product Master
  </a>
  <a class="nav-item" onclick="navigate('log')" href="#">
    <span class="nav-icon">📋</span> Sales Log
  </a>

  <div class="sidebar-footer">
    <div style="font-size:12px;color:rgba(255,255,255,.7);font-weight:600;margin-bottom:3px;">⚡ Powered by Mubasher Fayaz</div>
    <div>&#9889; Powered by Mubasher Fayaz<br>v1.0 · Client-side only · LocalStorage</div>
  </div>
</nav>

<!-- MOBILE OVERLAY -->
<div id="overlay" onclick="closeSidebar()"></div>

<!-- MAIN -->
<div id="main">
  <!-- TOPBAR -->
  <div class="topbar">
    <div class="flex items-center gap-8">
      <button id="menu-toggle" onclick="toggleSidebar()">☰</button>
      <span class="topbar-title" id="topbar-title">Dashboard</span>
    </div>
    <span class="topbar-date" id="today-date"></span>
    <span style="font-size:11px;color:var(--text-muted);font-weight:600;display:none" class="powered-tag">⚡ Mubasher Fayaz</span>
  </div>

  <div class="page-content">

    <!-- ===== DASHBOARD PAGE ===== -->
    <div class="page active" id="page-dashboard">
      <div class="section-header">
        <div class="section-title">Sales <span>Dashboard</span></div>
        <button class="btn btn-primary btn-sm" onclick="refreshDashboard()">🔄 Refresh</button>
      </div>

      <!-- SUMMARY CARDS -->
      <div class="cards-grid" id="summary-cards">
        <!-- filled by JS -->
      </div>

      <!-- OVERALL TARGET TRACKER -->
      <div class="target-tracker" id="overall-tracker">
        <!-- filled by JS -->
      </div>

      <!-- CHARTS -->
      <div class="charts-grid">
        <div class="chart-card">
          <div class="chart-card-title">📊 Product-wise TP Sales (₹)</div>
          <canvas id="barChart" height="260"></canvas>
        </div>
        <div class="chart-card">
          <div class="chart-card-title">🥧 Unit Contribution by Product (%)</div>
          <canvas id="pieChart" height="260"></canvas>
        </div>
      </div>

      <!-- PRODUCT-WISE TABLE -->
      <div class="table-wrap">
        <div class="table-header">
          <span class="table-title">📦 Product-wise Sales vs. Unit Targets</span>
          <span style="font-size:12px;color:var(--text-muted)" id="month-label"></span>
        </div>
        <table id="product-table">
          <thead>
            <tr>
              <th>Product</th>
              <th class="text-right">Units Sold</th>
              <th class="text-right">TP Total (₹)</th>
              <th class="text-right">MRP Total (₹)</th>
              <th class="text-right">Gross Gap (₹)</th>
              <th class="text-right">Unit Target</th>
              <th class="text-right">% Achieved</th>
              <th class="text-right">Units Rem.</th>
              <th class="text-right">Req. Daily</th>
            </tr>
          </thead>
          <tbody id="product-table-body"></tbody>
        </table>
      </div>
    </div>

    <!-- ===== DAILY SALES PAGE ===== -->
    <div class="page" id="page-sales">
      <div class="section-title mb-16">Daily <span style="color:var(--accent)">Sales Entry</span></div>

      <div class="form-card">
        <div class="form-title">➕ Record New Sale</div>
        <div class="form-group">
          <label class="form-label">Product</label>
          <select class="form-control" id="sale-product"></select>
        </div>
        <div class="form-group">
          <label class="form-label">Units Sold</label>
          <input type="number" class="form-control" id="sale-units" min="1" placeholder="Enter quantity" />
        </div>
        <div class="form-group">
          <label class="form-label">Date (Auto)</label>
          <input type="date" class="form-control" id="sale-date" />
        </div>
        <div id="sale-preview" style="display:none; background:var(--surface2); border-radius:8px; padding:14px 16px; margin-bottom:18px; font-size:13px;"></div>
        <button class="btn btn-primary" onclick="saveSale()">💾 Save Sale</button>
      </div>
    </div>

    <!-- ===== SET TARGET PAGE ===== -->
    <div class="page" id="page-target">
      <div class="section-title mb-16">Set <span style="color:var(--accent)">Targets</span></div>

      <div class="form-card">
        <div class="form-title">💰 Overall Monthly TP Value Target</div>
        <div class="form-group">
          <label class="form-label">Monthly TP Target (₹)</label>
          <input type="number" class="form-control" id="tp-target-input" placeholder="e.g. 500000" min="0" />
        </div>
        <button class="btn btn-accent" onclick="saveTPTarget()">💾 Save TP Target</button>
      </div>

      <div class="table-wrap">
        <div class="table-header">
          <span class="table-title">🎯 Product-wise Unit Targets</span>
          <button class="btn btn-primary btn-sm" onclick="saveUnitTargets()">💾 Save All Unit Targets</button>
        </div>
        <table>
          <thead>
            <tr>
              <th>Product</th>
              <th class="text-right">MRP (₹)</th>
              <th class="text-right">TP (₹)</th>
              <th class="text-right">Monthly Unit Target</th>
            </tr>
          </thead>
          <tbody id="target-table-body"></tbody>
        </table>
      </div>
    </div>

    <!-- ===== PRODUCT MASTER PAGE ===== -->
    <div class="page" id="page-products">
      <div class="section-title mb-16">Product <span style="color:var(--accent)">Master</span></div>
      <div class="table-wrap">
        <div class="table-header">
          <span class="table-title">📦 All Products</span>
        </div>
        <table>
          <thead>
            <tr>
              <th>#</th>
              <th>Product Name</th>
              <th class="text-right">MRP (₹)</th>
              <th class="text-right">TP (₹)</th>
              <th class="text-right">Gross Gap/Unit (₹)</th>
              <th class="text-right">Gross Margin %</th>
              <th class="text-right">Monthly Unit Target</th>
            </tr>
          </thead>
          <tbody id="master-table-body"></tbody>
        </table>
      </div>
    </div>

    <!-- ===== SALES LOG PAGE ===== -->
    <div class="page" id="page-log">
      <div class="section-header">
        <div class="section-title">Sales <span style="color:var(--accent)">Log</span></div>
        <button class="btn btn-danger btn-sm" onclick="clearAllSales()">🗑 Clear All Sales</button>
      </div>
      <div class="table-wrap">
        <div class="table-header">
          <span class="table-title">📋 All Recorded Sales</span>
        </div>
        <table>
          <thead>
            <tr>
              <th>Date</th>
              <th>Product</th>
              <th class="text-right">Units</th>
              <th class="text-right">TP Total (₹)</th>
              <th class="text-right">MRP Total (₹)</th>
              <th class="text-right">Gross Gap (₹)</th>
              <th class="text-center">Action</th>
            </tr>
          </thead>
          <tbody id="log-table-body"></tbody>
        </table>
      </div>
    </div>

  </div><!-- /page-content -->
<div style="text-align:center;padding:15px 28px;border-top:1px solid #c6ddd5;font-size:12px;color:#557a6a;font-weight:700;background:#fff;">&#9889; Powered by <span style="color:#0a6e4f;">Mubasher Fayaz</span> &nbsp;&middot;&nbsp; MR Smart Sales &amp; Unit Analyzer v1.0</div></div><!-- /main -->

<!-- TOAST -->
<div id="toast"></div>

<script>
/* =====================================================================
   MR SMART SALES & UNIT ANALYZER
   Pure JS / LocalStorage / Chart.js
   ===================================================================== */

// ───────────────────────────────────────────────
// 1. PRODUCT MASTER DATA
// ───────────────────────────────────────────────
const DEFAULT_PRODUCTS = [
  { id:1,  name:"Syp CARBEX PLUS (120ml)",               mrp:230,   tp:195.50,  unitTarget:1437 },
  { id:2,  name:"Syp CARBEX SYP 100mg 2% (120ml)",       mrp:180,   tp:153.00,  unitTarget:823  },
  { id:3,  name:"Syp CARBEX SYP 250mg 5% (120ml)",       mrp:265,   tp:225.25,  unitTarget:1776 },
  { id:4,  name:"Cap CEDROX Caps 500mg (12)",             mrp:450,   tp:382.50,  unitTarget:239  },
  { id:5,  name:"Susp CEDROX Susp 125mg (60ml)",         mrp:210,   tp:178.50,  unitTarget:117  },
  { id:6,  name:"Susp CEDROX Susp 250mg (60ml)",         mrp:380,   tp:323.00,  unitTarget:122  },
  { id:7,  name:"Tab CEDROX Tab. 1gm (10)",               mrp:574.5, tp:488.33,  unitTarget:5    },
  { id:8,  name:"Salbeclo Inhaler 100mcg/50mcg",          mrp:700,   tp:595.00,  unitTarget:13   },
  { id:9,  name:"Syp UNIFYLINE Syp 100mg/5ml (60ml)",    mrp:170,   tp:144.50,  unitTarget:916  },
  { id:10, name:"Syp UNIFYLINE Syp 100mg/5ml (120ml)",   mrp:295,   tp:250.75,  unitTarget:1543 },
  { id:11, name:"Tab UNIFYLINE Tab 400mg 20s",            mrp:650,   tp:552.50,  unitTarget:488  },
  { id:12, name:"Tab SENIGRA TABLET 100MG 1x4's",        mrp:700,   tp:595.00,  unitTarget:51   },
  { id:13, name:"Tab SENIGRA TABLET 50MG 1x4's",         mrp:350,   tp:297.50,  unitTarget:113  }
];

// ───────────────────────────────────────────────
// 2. LOCALSTORAGE HELPERS
// ───────────────────────────────────────────────
const LS_PRODUCTS  = 'mr_products';
const LS_SALES     = 'mr_sales';
const LS_TP_TARGET = 'mr_tp_target';

function loadProducts() {
  const raw = localStorage.getItem(LS_PRODUCTS);
  return raw ? JSON.parse(raw) : JSON.parse(JSON.stringify(DEFAULT_PRODUCTS));
}
function saveProducts(arr) { localStorage.setItem(LS_PRODUCTS, JSON.stringify(arr)); }

function loadSales() {
  const raw = localStorage.getItem(LS_SALES);
  return raw ? JSON.parse(raw) : [];
}
function saveSalesData(arr) { localStorage.setItem(LS_SALES, JSON.stringify(arr)); }

function loadTPTarget() {
  return parseFloat(localStorage.getItem(LS_TP_TARGET) || '0');
}
function saveTPTargetData(v) { localStorage.setItem(LS_TP_TARGET, v); }

// ───────────────────────────────────────────────
// 3. DATE UTILITIES
// ───────────────────────────────────────────────
function todayStr() {
  const d = new Date();
  return d.toISOString().split('T')[0]; // YYYY-MM-DD
}
function currentYearMonth() {
  const d = new Date();
  return `${d.getFullYear()}-${String(d.getMonth()+1).padStart(2,'0')}`;
}
function daysInMonth(ym) {
  const [y,m] = ym.split('-').map(Number);
  return new Date(y, m, 0).getDate();
}
function dayOfMonth(ym) {
  return new Date().getDate();
}
function remainingDays(ym) {
  const total = daysInMonth(ym);
  const today = dayOfMonth(ym);
  return Math.max(1, total - today + 1);
}
function formatCurrency(n) {
  return '₹' + Number(n).toLocaleString('en-IN', { minimumFractionDigits:2, maximumFractionDigits:2 });
}
function formatNum(n) {
  return Number(n).toLocaleString('en-IN');
}
function formatDate(str) {
  const d = new Date(str);
  return d.toLocaleDateString('en-IN', { day:'2-digit', month:'short', year:'numeric' });
}

// ───────────────────────────────────────────────
// 4. NAVIGATION
// ───────────────────────────────────────────────
const PAGE_TITLES = {
  dashboard: 'Dashboard',
  sales: 'Daily Sales Entry',
  target: 'Set Target',
  products: 'Product Master',
  log: 'Sales Log'
};
function navigate(page) {
  document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
  document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
  document.getElementById('page-' + page).classList.add('active');
  document.querySelectorAll('.nav-item').forEach(n => {
    if (n.getAttribute('onclick') && n.getAttribute('onclick').includes(page))
      n.classList.add('active');
  });
  document.getElementById('topbar-title').textContent = PAGE_TITLES[page];
  closeSidebar();
  if (page === 'dashboard') refreshDashboard();
  if (page === 'sales') initSalesPage();
  if (page === 'target') initTargetPage();
  if (page === 'products') initProductsPage();
  if (page === 'log') initLogPage();
}
function toggleSidebar() {
  document.getElementById('sidebar').classList.toggle('open');
  document.getElementById('overlay').classList.toggle('show');
}
function closeSidebar() {
  document.getElementById('sidebar').classList.remove('open');
  document.getElementById('overlay').classList.remove('show');
}

// ───────────────────────────────────────────────
// 5. TOAST
// ───────────────────────────────────────────────
let toastTimer;
function showToast(msg, icon='✅') {
  const el = document.getElementById('toast');
  el.innerHTML = `${icon} ${msg}`;
  el.style.display = 'flex';
  clearTimeout(toastTimer);
  toastTimer = setTimeout(() => { el.style.display = 'none'; }, 3000);
}

// ───────────────────────────────────────────────
// 6. DAILY SALES PAGE
// ───────────────────────────────────────────────
function initSalesPage() {
  const products = loadProducts();
  const sel = document.getElementById('sale-product');
  sel.innerHTML = '<option value="">-- Select Product --</option>' +
    products.map(p => `<option value="${p.id}">${p.name} (MRP: ₹${p.mrp})</option>`).join('');
  document.getElementById('sale-date').value = todayStr();
  document.getElementById('sale-units').value = '';
  document.getElementById('sale-preview').style.display = 'none';

  sel.onchange = previewSale;
  document.getElementById('sale-units').oninput = previewSale;
}
function previewSale() {
  const products = loadProducts();
  const pid = parseInt(document.getElementById('sale-product').value);
  const units = parseFloat(document.getElementById('sale-units').value || 0);
  const prev = document.getElementById('sale-preview');
  if (!pid || units <= 0) { prev.style.display = 'none'; return; }
  const p = products.find(x => x.id === pid);
  if (!p) return;
  const tpTotal  = p.tp * units;
  const mrpTotal = p.mrp * units;
  const grossGap = (p.mrp - p.tp) * units;
  prev.style.display = 'block';
  prev.innerHTML = `
    <div style="font-weight:700;color:var(--primary-dark);margin-bottom:8px;">📋 Preview</div>
    <div style="display:grid;grid-template-columns:1fr 1fr;gap:6px;">
      <span>TP Total:</span><strong>${formatCurrency(tpTotal)}</strong>
      <span>MRP Total:</span><strong>${formatCurrency(mrpTotal)}</strong>
      <span>Gross Gap:</span><strong style="color:var(--success)">${formatCurrency(grossGap)}</strong>
    </div>`;
}
function saveSale() {
  const products = loadProducts();
  const pid   = parseInt(document.getElementById('sale-product').value);
  const units = parseFloat(document.getElementById('sale-units').value);
  const date  = document.getElementById('sale-date').value;
  if (!pid)          { showToast('Please select a product', '⚠️'); return; }
  if (!units || units <= 0) { showToast('Enter valid units', '⚠️'); return; }
  const p = products.find(x => x.id === pid);
  const sales = loadSales();
  sales.push({
    id: Date.now(),
    date,
    productId: p.id,
    productName: p.name,
    units,
    tp: p.tp,
    mrp: p.mrp,
    tpTotal:  +(p.tp  * units).toFixed(2),
    mrpTotal: +(p.mrp * units).toFixed(2),
    grossGap: +((p.mrp - p.tp) * units).toFixed(2)
  });
  saveSalesData(sales);
  showToast(`Sale saved! ${units} units of ${p.name}`, '✅');
  initSalesPage();
}

// ───────────────────────────────────────────────
// 7. SET TARGET PAGE
// ───────────────────────────────────────────────
function initTargetPage() {
  const products = loadProducts();
  document.getElementById('tp-target-input').value = loadTPTarget() || '';
  const tbody = document.getElementById('target-table-body');
  tbody.innerHTML = products.map(p => `
    <tr>
      <td>${p.name}</td>
      <td class="text-right">${formatCurrency(p.mrp)}</td>
      <td class="text-right">${formatCurrency(p.tp)}</td>
      <td class="text-right">
        <input class="edit-input" type="number" min="0"
          id="unit-target-${p.id}" value="${p.unitTarget}" />
      </td>
    </tr>`).join('');
}
function saveTPTarget() {
  const v = parseFloat(document.getElementById('tp-target-input').value);
  if (isNaN(v) || v < 0) { showToast('Enter valid TP target', '⚠️'); return; }
  saveTPTargetData(v);
  showToast(`TP Target saved: ${formatCurrency(v)}`, '✅');
}
function saveUnitTargets() {
  const products = loadProducts();
  let changed = 0;
  products.forEach(p => {
    const inp = document.getElementById('unit-target-' + p.id);
    if (inp) {
      const v = parseFloat(inp.value);
      if (!isNaN(v) && v >= 0) { p.unitTarget = v; changed++; }
    }
  });
  saveProducts(products);
  showToast(`Unit targets saved for ${changed} products`, '✅');
}

// ───────────────────────────────────────────────
// 8. PRODUCT MASTER PAGE
// ───────────────────────────────────────────────
function initProductsPage() {
  const products = loadProducts();
  const tbody = document.getElementById('master-table-body');
  tbody.innerHTML = products.map((p,i) => {
    const gap    = p.mrp - p.tp;
    const margin = ((gap / p.mrp) * 100).toFixed(1);
    return `
      <tr>
        <td>${i+1}</td>
        <td><strong>${p.name}</strong></td>
        <td class="text-right">${formatCurrency(p.mrp)}</td>
        <td class="text-right">${formatCurrency(p.tp)}</td>
        <td class="text-right">${formatCurrency(gap)}</td>
        <td class="text-right"><span class="badge badge-success">${margin}%</span></td>
        <td class="text-right"><strong>${formatNum(p.unitTarget)}</strong></td>
      </tr>`;
  }).join('');
}

// ───────────────────────────────────────────────
// 9. SALES LOG PAGE
// ───────────────────────────────────────────────
function initLogPage() {
  const sales = loadSales().sort((a,b) => b.date.localeCompare(a.date));
  const tbody = document.getElementById('log-table-body');
  if (!sales.length) {
    tbody.innerHTML = `<tr><td colspan="7"><div class="empty-state"><div class="empty-icon">📋</div>No sales recorded yet.</div></td></tr>`;
    return;
  }
  tbody.innerHTML = sales.map(s => `
    <tr>
      <td>${formatDate(s.date)}</td>
      <td>${s.productName}</td>
      <td class="text-right"><strong>${s.units}</strong></td>
      <td class="text-right">${formatCurrency(s.tpTotal)}</td>
      <td class="text-right">${formatCurrency(s.mrpTotal)}</td>
      <td class="text-right color-success">${formatCurrency(s.grossGap)}</td>
      <td class="text-center">
        <button class="btn btn-danger btn-sm" onclick="deleteSale(${s.id})">🗑</button>
      </td>
    </tr>`).join('');
}
function deleteSale(id) {
  if (!confirm('Delete this sale entry?')) return;
  const sales = loadSales().filter(s => s.id !== id);
  saveSalesData(sales);
  initLogPage();
  showToast('Sale entry deleted', '🗑');
}
function clearAllSales() {
  if (!confirm('Clear ALL sales data? This cannot be undone.')) return;
  saveSalesData([]);
  initLogPage();
  showToast('All sales cleared', '🗑');
}

// ───────────────────────────────────────────────
// 10. DASHBOARD
// ───────────────────────────────────────────────
let barChartInst = null;
let pieChartInst = null;

function refreshDashboard() {
  const ym       = currentYearMonth();
  const products = loadProducts();
  const allSales = loadSales();
  const tpTarget = loadTPTarget();
  const remDays  = remainingDays(ym);
  const totDays  = daysInMonth(ym);

  // Filter this month's sales
  const mSales = allSales.filter(s => s.date.startsWith(ym));

  // Aggregate per product
  const aggMap = {};
  products.forEach(p => {
    aggMap[p.id] = { units:0, tpTotal:0, mrpTotal:0, grossGap:0 };
  });
  mSales.forEach(s => {
    if (aggMap[s.productId]) {
      aggMap[s.productId].units    += s.units;
      aggMap[s.productId].tpTotal  += s.tpTotal;
      aggMap[s.productId].mrpTotal += s.mrpTotal;
      aggMap[s.productId].grossGap += s.grossGap;
    }
  });

  // Totals
  const totUnits  = Object.values(aggMap).reduce((a,v) => a + v.units,    0);
  const totTP     = Object.values(aggMap).reduce((a,v) => a + v.tpTotal,  0);
  const totMRP    = Object.values(aggMap).reduce((a,v) => a + v.mrpTotal, 0);
  const totGross  = Object.values(aggMap).reduce((a,v) => a + v.grossGap, 0);
  const tpPct     = tpTarget > 0 ? Math.min(100, (totTP / tpTarget * 100)).toFixed(1) : 0;

  // ---- Summary Cards ----
  const cardsEl = document.getElementById('summary-cards');
  cardsEl.innerHTML = `
    <div class="card">
      <span class="card-icon">📦</span>
      <div class="card-label">Total Units Sold</div>
      <div class="card-value">${formatNum(totUnits)}</div>
      <div class="card-sub">Month-to-date</div>
    </div>
    <div class="card accent">
      <span class="card-icon">💵</span>
      <div class="card-label">Total TP Value</div>
      <div class="card-value" style="font-size:17px">${formatCurrency(totTP)}</div>
      <div class="card-sub">This month</div>
    </div>
    <div class="card green">
      <span class="card-icon">🏷️</span>
      <div class="card-label">Total MRP Value</div>
      <div class="card-value" style="font-size:17px">${formatCurrency(totMRP)}</div>
      <div class="card-sub">This month</div>
    </div>
    <div class="card warn">
      <span class="card-icon">📈</span>
      <div class="card-label">Total Gross Margin</div>
      <div class="card-value" style="font-size:17px">${formatCurrency(totGross)}</div>
      <div class="card-sub">MRP − TP</div>
    </div>
    <div class="card">
      <span class="card-icon">🎯</span>
      <div class="card-label">TP Target</div>
      <div class="card-value" style="font-size:17px">${tpTarget > 0 ? formatCurrency(tpTarget) : 'Not Set'}</div>
      <div class="card-sub">${tpPct}% achieved</div>
      ${tpTarget > 0 ? `
      <div class="progress-wrap">
        <div class="progress-bar-bg">
          <div class="progress-bar-fill ${tpPct >= 100 ? 'success' : tpPct < 50 ? 'danger' : ''}"
               style="width:${tpPct}%"></div>
        </div>
      </div>` : ''}
    </div>`;

  // ---- Overall Target Tracker ----
  const tpRem   = Math.max(0, tpTarget - totTP);
  const reqDayTP = remDays > 0 ? (tpRem / remDays).toFixed(0) : 0;
  const trackerEl = document.getElementById('overall-tracker');
  trackerEl.innerHTML = `
    <div class="tt-item">
      <div class="tt-label">Monthly TP Target</div>
      <div class="tt-value">${tpTarget > 0 ? formatCurrency(tpTarget) : '—'}</div>
    </div>
    <div class="tt-item">
      <div class="tt-label">TP Achieved</div>
      <div class="tt-value">${formatCurrency(totTP)}</div>
    </div>
    <div class="tt-item">
      <div class="tt-label">TP Remaining</div>
      <div class="tt-value">${formatCurrency(tpRem)}</div>
    </div>
    <div class="tt-item">
      <div class="tt-label">Required Daily TP</div>
      <div class="tt-value">${tpTarget > 0 ? formatCurrency(reqDayTP) : '—'}</div>
    </div>
    <div class="tt-item">
      <div class="tt-label">Days Remaining</div>
      <div class="tt-value">${remDays} / ${totDays}</div>
    </div>
    <div style="flex-basis:100%">
      <div class="tt-progress-bg">
        <div class="tt-progress-fill" style="width:${tpPct}%"></div>
      </div>
      <div style="font-size:11px;color:rgba(255,255,255,.6);margin-top:5px">${tpPct}% of TP Target Achieved</div>
    </div>`;

  // ---- Month label ----
  const d = new Date();
  document.getElementById('month-label').textContent =
    d.toLocaleString('en-IN', { month:'long', year:'numeric' });

  // ---- Product Table ----
  const tbody = document.getElementById('product-table-body');
  tbody.innerHTML = products.map(p => {
    const agg   = aggMap[p.id];
    const pct   = p.unitTarget > 0 ? (agg.units / p.unitTarget * 100) : 0;
    const pctD  = pct.toFixed(1);
    const remU  = Math.max(0, p.unitTarget - agg.units);
    const reqDU = remDays > 0 ? Math.ceil(remU / remDays) : 0;
    const badgeClass = pct >= 100 ? 'badge-success' : pct >= 50 ? 'badge-warn' : 'badge-danger';
    return `
      <tr>
        <td><strong>${p.name}</strong></td>
        <td class="text-right">${formatNum(agg.units)}</td>
        <td class="text-right">${formatCurrency(agg.tpTotal)}</td>
        <td class="text-right">${formatCurrency(agg.mrpTotal)}</td>
        <td class="text-right color-success">${formatCurrency(agg.grossGap)}</td>
        <td class="text-right">${formatNum(p.unitTarget)}</td>
        <td class="text-right"><span class="badge ${badgeClass}">${pctD}%</span></td>
        <td class="text-right ${remU > 0 ? 'color-warn' : 'color-success'}">${formatNum(remU)}</td>
        <td class="text-right ${reqDU > 0 ? 'color-danger' : 'color-success'}">${formatNum(reqDU)}</td>
      </tr>`;
  }).join('');

  // ---- Charts ----
  renderCharts(products, aggMap);
}

// ───────────────────────────────────────────────
// 11. CHARTS
// ───────────────────────────────────────────────
const CHART_COLORS = [
  '#0a6e4f','#12a374','#00c98d','#1a7fbf','#2799df','#14a356',
  '#f5a623','#e03b3b','#8e44ad','#2980b9','#16a085','#d35400','#27ae60'
];

function renderCharts(products, aggMap) {
  const labels   = products.map(p => p.name.substring(0,20) + (p.name.length > 20 ? '…' : ''));
  const tpVals   = products.map(p => aggMap[p.id].tpTotal);
  const unitVals = products.map(p => aggMap[p.id].units);

  // Bar Chart
  if (barChartInst) barChartInst.destroy();
  const barCtx = document.getElementById('barChart').getContext('2d');
  barChartInst = new Chart(barCtx, {
    type: 'bar',
    data: {
      labels,
      datasets: [{
        label: 'TP Sales (₹)',
        data: tpVals,
        backgroundColor: CHART_COLORS,
        borderRadius: 5,
        borderSkipped: false
      }]
    },
    options: {
      responsive: true,
      plugins: {
        legend: { display: false },
        tooltip: {
          callbacks: {
            label: ctx => ' ₹' + ctx.parsed.y.toLocaleString('en-IN', {minimumFractionDigits:2})
          }
        }
      },
      scales: {
        y: {
          beginAtZero: true,
          ticks: {
            callback: v => '₹' + (v/1000).toFixed(0) + 'K',
            font: { family: 'DM Sans', size: 11 }
          },
          grid: { color: 'rgba(0,0,0,.05)' }
        },
        x: {
          ticks: { font: { family: 'DM Sans', size: 10 }, maxRotation: 45 },
          grid: { display: false }
        }
      }
    }
  });

  // Pie Chart (only products with units sold)
  const activeLabels = [];
  const activeData   = [];
  const activeColors = [];
  products.forEach((p, i) => {
    if (aggMap[p.id].units > 0) {
      activeLabels.push(p.name.substring(0,22) + (p.name.length > 22 ? '…' : ''));
      activeData.push(aggMap[p.id].units);
      activeColors.push(CHART_COLORS[i % CHART_COLORS.length]);
    }
  });

  if (pieChartInst) pieChartInst.destroy();
  const pieCtx = document.getElementById('pieChart').getContext('2d');
  if (activeData.length === 0) {
    pieChartInst = new Chart(pieCtx, {
      type: 'doughnut',
      data: {
        labels: ['No data'],
        datasets: [{ data: [1], backgroundColor: ['#e0e0e0'] }]
      },
      options: { responsive: true, plugins: { legend: { display: false } } }
    });
  } else {
    pieChartInst = new Chart(pieCtx, {
      type: 'doughnut',
      data: {
        labels: activeLabels,
        datasets: [{
          data: activeData,
          backgroundColor: activeColors,
          borderWidth: 2,
          borderColor: '#fff',
          hoverOffset: 8
        }]
      },
      options: {
        responsive: true,
        plugins: {
          legend: {
            position: 'right',
            labels: { font: { family: 'DM Sans', size: 10 }, boxWidth: 12, padding: 8 }
          },
          tooltip: {
            callbacks: {
              label: ctx => ` ${ctx.label}: ${ctx.parsed} units (${((ctx.parsed / activeData.reduce((a,b)=>a+b,0))*100).toFixed(1)}%)`
            }
          }
        }
      }
    });
  }
}

// ───────────────────────────────────────────────
// 12. INIT
// ───────────────────────────────────────────────
function init() {
  // Ensure products are in localStorage (seeded once)
  if (!localStorage.getItem(LS_PRODUCTS)) {
    saveProducts(JSON.parse(JSON.stringify(DEFAULT_PRODUCTS)));
  }

  // Set today's date display
  const d = new Date();
  document.getElementById('today-date').textContent =
    d.toLocaleDateString('en-IN', { weekday:'short', day:'2-digit', month:'short', year:'numeric' });

  refreshDashboard();
}

window.addEventListener('DOMContentLoaded', init);
</script>
</body>
</html>
