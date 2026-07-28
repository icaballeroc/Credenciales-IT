<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>PAYMENT · Credenciales IT</title>
<link rel="manifest" href="manifest.json">
<meta name="theme-color" content="#14b8a6">
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800;900&display=swap" rel="stylesheet">
<script src="https://cdn.jsdelivr.net/npm/qrcodejs@1.0.0/qrcode.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/html2canvas@1.4.1/dist/html2canvas.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/xlsx@0.18.5/dist/xlsx.full.min.js"></script>
<style>
/* ────────────────────────────────────────────────────────────
   DESIGN TOKENS
──────────────────────────────────────────────────────────── */
:root{
  --bg:#060d1a; --surface:#0d1625; --surface2:#162035; --border:#1e2d42;
  --text:#eef2f7; --muted:#7b93b0; --accent:#14b8a6; --accent-ink:#04352f;
  --green:#22c55e; --red:#f04444; --amber:#f59e0b;
  --shadow-sm:0 2px 8px rgba(0,0,0,.2); --shadow-md:0 4px 20px rgba(0,0,0,.3); --shadow-lg:0 8px 40px rgba(0,0,0,.4);
  --glow-green:0 0 24px rgba(34,197,94,.2); --glow-red:0 0 24px rgba(240,68,68,.2);
  --r-sm:8px; --r-md:12px; --r-lg:16px; --r-xl:20px; --r-2xl:24px;
  color-scheme: dark;
}
html[data-theme="light"]{
  --bg:#f0f5fb; --surface:#ffffff; --surface2:#f4f8fd; --border:#dce6f0;
  --text:#0c1929; --muted:#5a7490; --accent:#0d9488; --accent-ink:#e6fbf6;
  color-scheme: light;
}
*,*::before,*::after{ box-sizing:border-box; }
html,body{ height:100%; }
body{
  margin:0; background:var(--bg); color:var(--text);
  font-family:'Inter',system-ui,sans-serif; font-size:14px; line-height:1.5;
  -webkit-font-smoothing:antialiased;
  transition: background .3s ease, color .3s ease;
}
*{ transition: background-color .2s ease, border-color .2s ease, color .2s ease; }
h1,h2,h3{ letter-spacing:-.5px; margin:0; }
button{ font-family:inherit; }
a{ color: var(--accent); }
::selection{ background: var(--accent); color:#fff; }
.hidden{ display:none !important; }
.sr-only{ position:absolute; width:1px;height:1px; overflow:hidden; clip:rect(0,0,0,0); }
:focus-visible{ outline:2px solid var(--accent); outline-offset:2px; }

/* ── LAYOUT ── */
#app{ min-height:100%; display:flex; flex-direction:column; }
.topbar{
  display:flex; align-items:center; gap:16px; padding:12px 20px;
  background:var(--surface); border-bottom:1px solid var(--border);
  position:sticky; top:0; z-index:100;
}
.brand{ display:flex; align-items:center; gap:10px; font-weight:800; font-size:15px; }
.brand-dot{ width:10px;height:10px;border-radius:50%; background:var(--accent); box-shadow:0 0 12px var(--accent); }
.tabs{ display:flex; gap:4px; margin-left:8px; overflow-x:auto; }
.tab-btn{
  border:none; background:transparent; color:var(--muted); padding:8px 14px;
  border-radius:var(--r-sm); font-weight:600; font-size:13px; cursor:pointer;
  display:flex; align-items:center; gap:6px; white-space:nowrap; min-height:36px;
}
.tab-btn:hover{ background:var(--surface2); color:var(--text); }
.tab-btn.active{ background:var(--accent); color:var(--accent-ink); }
.top-actions{ margin-left:auto; display:flex; align-items:center; gap:8px; }
.icon-btn{
  width:38px;height:38px; border-radius:var(--r-sm); border:1px solid var(--border);
  background:var(--surface2); color:var(--text); cursor:pointer; display:flex;
  align-items:center; justify-content:center; font-size:16px; min-width:44px; min-height:44px;
}
.icon-btn:hover{ border-color:var(--accent); }
main{ flex:1; padding:20px; max-width:1400px; margin:0 auto; width:100%; }
.view{ display:none; }
.view.active{ display:block; animation: fadein .2s ease; }
@keyframes fadein{ from{ opacity:0; transform:translateY(4px);} to{opacity:1; transform:translateY(0);} }

/* ── LOGIN ── */
.login-wrap{ min-height:100vh; display:flex; align-items:center; justify-content:center; padding:20px; }
.login-card{
  background:var(--surface); border:1px solid var(--border); border-radius:var(--r-xl);
  padding:32px; width:100%; max-width:380px; box-shadow:var(--shadow-lg);
}
.login-card h1{ font-size:20px; font-weight:800; margin-bottom:4px; }
.login-card p.sub{ color:var(--muted); font-size:13px; margin:0 0 24px; }
.field{ margin-bottom:16px; }
.field label{ display:block; font-size:12px; font-weight:600; color:var(--muted); margin-bottom:6px; }
.field input, .field select, .field textarea{
  width:100%; background:var(--surface2); border:1px solid var(--border); color:var(--text);
  padding:11px 12px; border-radius:var(--r-sm); font-size:14px; font-family:inherit; min-height:44px;
}
.field input:focus, .field select:focus, .field textarea:focus{ border-color:var(--accent); }
.field.invalid input{ border-color:var(--red); }
.field.valid input{ border-color:var(--green); }
.field .err-msg{ color:var(--red); font-size:11px; margin-top:4px; display:none; }
.field.invalid .err-msg{ display:block; }
.field .char-count{ font-size:11px; color:var(--muted); text-align:right; margin-top:2px; }
.btn{
  border:none; border-radius:var(--r-sm); padding:11px 18px; font-weight:700; font-size:13px;
  cursor:pointer; display:inline-flex; align-items:center; gap:8px; min-height:44px; justify-content:center;
}
.btn-primary{ background:var(--accent); color:var(--accent-ink); }
.btn-primary:hover{ filter:brightness(1.08); }
.btn-secondary{ background:var(--surface2); color:var(--text); border:1px solid var(--border); }
.btn-danger{ background:var(--red); color:#fff; }
.btn-ghost{ background:transparent; color:var(--muted); }
.btn:disabled{ opacity:.55; cursor:not-allowed; }
.btn-block{ width:100%; }
.spinner{
  width:14px;height:14px; border:2px solid rgba(255,255,255,.35); border-top-color:#fff;
  border-radius:50%; animation:spin .6s linear infinite; display:inline-block;
}
@keyframes spin{ to{ transform:rotate(360deg); } }
.lockout-box{
  background:rgba(240,68,68,.1); border:1px solid var(--red); border-radius:var(--r-sm);
  padding:12px; font-size:13px; color:var(--red); text-align:center; margin-top:12px;
}

/* ── CARDS / STATS ── */
.stats-row{ display:grid; grid-template-columns:repeat(auto-fit,minmax(140px,1fr)); gap:12px; margin-bottom:20px; }
.stat-card{
  background:var(--surface); border:1px solid var(--border); border-radius:var(--r-lg); padding:16px;
}
.stat-card .num{ font-size:24px; font-weight:800; }
.stat-card .lbl{ font-size:12px; color:var(--muted); margin-top:2px; }
.stat-card.green .num{ color:var(--green); }
.stat-card.red .num{ color:var(--red); }
.stat-card.amber .num{ color:var(--amber); }

.panel{ background:var(--surface); border:1px solid var(--border); border-radius:var(--r-lg); overflow:hidden; }
.panel-head{
  display:flex; align-items:center; gap:12px; padding:16px; border-bottom:1px solid var(--border);
  flex-wrap:wrap;
}
.panel-head h2{ font-size:17px; font-weight:700; margin-right:auto; }
.search-box{ position:relative; flex:1; min-width:180px; max-width:320px; }
.search-box input{
  width:100%; background:var(--surface2); border:1px solid var(--border); color:var(--text);
  padding:9px 12px 9px 34px; border-radius:var(--r-sm); font-size:13px; min-height:40px;
}
.search-box::before{ content:"🔍"; position:absolute; left:10px; top:50%; transform:translateY(-50%); font-size:12px; opacity:.6; }
.filter-pills{ display:flex; gap:6px; flex-wrap:wrap; }
.pill{
  border:1px solid var(--border); background:var(--surface2); color:var(--muted); padding:6px 12px;
  border-radius:999px; font-size:12px; font-weight:600; cursor:pointer;
}
.pill.active{ background:var(--accent); color:var(--accent-ink); border-color:var(--accent); }

/* ── LIST ── */
.cred-list{ list-style:none; margin:0; padding:0; }
.cred-item{
  display:flex; align-items:center; gap:12px; padding:14px 16px; border-bottom:1px solid var(--border);
}
.cred-item:hover{ background:var(--surface2); }
.cred-item input[type=checkbox]{ width:18px;height:18px; accent-color:var(--accent); cursor:pointer; }
.avatar{
  width:38px;height:38px; border-radius:50%; display:flex; align-items:center; justify-content:center;
  font-weight:700; font-size:14px; color:#fff; flex-shrink:0; object-fit:cover;
}
.ci-main{ flex:1; min-width:0; }
.ci-name{ font-weight:600; font-size:14px; white-space:nowrap; overflow:hidden; text-overflow:ellipsis; }
.ci-sub{ font-size:12px; color:var(--muted); white-space:nowrap; overflow:hidden; text-overflow:ellipsis; }
.badge{ padding:4px 10px; border-radius:999px; font-size:11px; font-weight:700; white-space:nowrap; }
.badge.ok{ background:rgba(34,197,94,.15); color:var(--green); }
.badge.no{ background:rgba(240,68,68,.15); color:var(--red); }
.badge.exp{ background:rgba(245,158,11,.15); color:var(--amber); }
.ci-actions{ display:flex; gap:4px; }
.bulk-bar{
  position:fixed; bottom:20px; left:50%; transform:translateX(-50%) translateY(120%);
  background:var(--surface2); border:1px solid var(--border); border-radius:var(--r-lg);
  padding:10px 16px; display:flex; gap:8px; align-items:center; box-shadow:var(--shadow-lg);
  z-index:500; transition:transform .25s ease;
}
.bulk-bar.show{ transform:translateX(-50%) translateY(0); }
.pager{ display:flex; justify-content:center; padding:16px; }

/* ── SKELETON ── */
.skel{ background:linear-gradient(90deg,var(--surface2) 25%,var(--border) 50%,var(--surface2) 75%); background-size:200% 100%; animation:shimmer 1.4s infinite; border-radius:var(--r-sm); }
@keyframes shimmer{ 0%{background-position:200% 0;} 100%{background-position:-200% 0;} }
.skel-row{ display:flex; gap:12px; align-items:center; padding:14px 16px; border-bottom:1px solid var(--border); }
.skel-avatar{ width:38px;height:38px; border-radius:50%; }
.skel-line{ height:12px; border-radius:4px; }

/* ── TOASTS ── */
#toast-region{ position:fixed; top:16px; right:16px; z-index:9999; display:flex; flex-direction:column; gap:8px; max-width:340px; }
.toast{
  background:var(--surface2); border:1px solid var(--border); border-radius:var(--r-md); padding:12px 14px;
  display:flex; gap:10px; align-items:flex-start; box-shadow:var(--shadow-md); position:relative; overflow:hidden;
  animation: toast-in .25s ease;
}
@keyframes toast-in{ from{ transform:translateX(120%);} to{transform:translateX(0);} }
.toast.leaving{ animation: toast-out .2s ease forwards; }
@keyframes toast-out{ to{ transform:translateX(120%); opacity:0; } }
.toast .icon{ font-size:16px; }
.toast .msg{ font-size:13px; flex:1; }
.toast .close{ background:none;border:none;color:var(--muted);cursor:pointer;font-size:14px; }
.toast .bar{ position:absolute; bottom:0; left:0; height:2px; background:var(--accent); animation:toastbar 4s linear forwards; }
@keyframes toastbar{ from{width:100%;} to{width:0%;} }
.toast.success .bar,.toast.success .icon{ color:var(--green); }
.toast.error .bar,.toast.error .icon{ color:var(--red); }
.toast.warning .bar,.toast.warning .icon{ color:var(--amber); }
.toast.info .bar,.toast.info .icon{ color:var(--accent); }

/* ── MODALS ── */
.modal-backdrop{
  position:fixed; inset:0; background:rgba(0,0,0,.55); backdrop-filter:blur(4px);
  display:flex; align-items:center; justify-content:center; z-index:2000; padding:20px;
}
.modal{
  background:var(--surface); border:1px solid var(--border); border-radius:var(--r-lg);
  width:100%; max-width:440px; box-shadow:var(--shadow-lg); animation:modal-in .18s ease;
  max-height:88vh; overflow-y:auto;
}
@keyframes modal-in{ from{ transform:scale(.94); opacity:0;} to{ transform:scale(1); opacity:1;} }
.modal-head{ padding:18px 20px 0; }
.modal-head h3{ font-size:16px; font-weight:700; }
.modal-body{ padding:14px 20px; color:var(--muted); font-size:13px; }
.modal-foot{ padding:14px 20px 20px; display:flex; justify-content:flex-end; gap:8px; }
.quick-days{ display:flex; gap:6px; flex-wrap:wrap; margin:10px 0; }

/* ── OFFLINE BANNER ── */
.offline-banner{
  background:var(--red); color:#fff; text-align:center; padding:8px; font-size:13px; font-weight:600;
  display:none;
}
.offline-banner.show{ display:block; }

/* ── PRESENCE ── */
.presence-bar{ display:flex; align-items:center; gap:-4px; padding-left:4px; }
.presence-avatar{
  width:28px;height:28px; border-radius:50%; display:flex; align-items:center; justify-content:center;
  font-size:11px; font-weight:700; color:#fff; border:2px solid var(--surface); margin-left:-8px; position:relative;
}
.presence-avatar:hover .ptip{ display:block; }
.ptip{ display:none; position:absolute; top:32px; left:50%; transform:translateX(-50%); background:var(--surface2);
  border:1px solid var(--border); padding:4px 8px; border-radius:6px; font-size:11px; white-space:nowrap; z-index:10; }

/* ── FORM CARD ── */
.form-grid{ display:grid; grid-template-columns:repeat(auto-fit,minmax(220px,1fr)); gap:16px; padding:20px; }
.photo-drop{
  width:100px;height:100px; border-radius:50%; border:2px dashed var(--border); display:flex;
  align-items:center; justify-content:center; cursor:pointer; margin:0 auto 10px; overflow:hidden;
  background:var(--surface2); color:var(--muted); font-size:12px; text-align:center; flex-direction:column; gap:4px;
}
.photo-drop img{ width:100%;height:100%; object-fit:cover; }

/* ── INVENTORY ── */
.dropzone{
  border:2px dashed var(--border); border-radius:var(--r-lg); padding:32px; text-align:center;
  color:var(--muted); cursor:pointer; margin:16px 20px;
}
.dropzone.drag{ border-color:var(--accent); background:rgba(20,184,166,.06); }
.progress-track{ height:8px; background:var(--surface2); border-radius:999px; overflow:hidden; margin:10px 20px; }
.progress-fill{ height:100%; background:var(--accent); width:0%; transition:width .2s ease; }
table.data-table{ width:100%; border-collapse:collapse; font-size:13px; }
table.data-table th{ text-align:left; padding:10px 12px; color:var(--muted); font-size:11px; text-transform:uppercase; border-bottom:1px solid var(--border); position:sticky; top:0; background:var(--surface); }
table.data-table td{ padding:10px 12px; border-bottom:1px solid var(--border); }
.table-wrap{ overflow-x:auto; max-height:520px; overflow-y:auto; }

/* ── DASHBOARD ── */
.dash-grid{ display:grid; grid-template-columns:2fr 1fr; gap:16px; }
@media (max-width:800px){ .dash-grid{ grid-template-columns:1fr; } }
.bar-chart{ display:flex; align-items:flex-end; gap:10px; height:160px; padding:16px; }
.bar-col{ flex:1; display:flex; flex-direction:column; align-items:center; gap:6px; }
.bar-fill{ width:100%; background:var(--accent); border-radius:4px 4px 0 0; min-height:4px; }
.bar-lbl{ font-size:11px; color:var(--muted); }
.leaderboard{ list-style:none; margin:0; padding:0 16px 16px; }
.leaderboard li{ display:flex; justify-content:space-between; padding:8px 0; border-bottom:1px solid var(--border); font-size:13px; }

/* ── PRINTABLE CRED ── */
.cred-print{
  width:280px; background:#fff; color:#111; border-radius:var(--r-lg); padding:20px; text-align:center;
  margin:0 auto; box-shadow:var(--shadow-md);
}
.cred-print .logo{ font-size:11px; font-weight:800; letter-spacing:1px; color:#0d9488; margin-bottom:8px; }
.cred-print .photo{ width:64px;height:64px; border-radius:50%; object-fit:cover; margin:0 auto 8px; display:block; border:2px solid #eee; }
.cred-print .qr-wrap{ display:flex; justify-content:center; margin:12px 0; }
.cred-print .cid{ font-family:'Courier New',monospace; font-size:11px; color:#000; letter-spacing:1px; }
@media print{
  body *{ visibility:hidden; }
  .cred-print, .cred-print *{ visibility:visible; }
  .cred-print{ position:absolute; top:0; left:0; box-shadow:none; max-width:75mm; }
}

/* Empty / util */
.empty-state{ text-align:center; padding:48px 20px; color:var(--muted); }
.empty-state .big{ font-size:32px; margin-bottom:8px; }
.mono{ font-family:'Courier New',monospace; }
@media (max-width:700px){
  .topbar{ flex-wrap:wrap; }
  main{ padding:12px; }
  .form-grid{ grid-template-columns:1fr; }
}
</style>
</head>
<body>
<div id="offline-banner" class="offline-banner" role="status">📡 Sin conexión — trabajando en modo offline. Los cambios se reintentarán al reconectar.</div>
<div id="toast-region" aria-live="polite" aria-atomic="false"></div>

<!-- ── LOGIN VIEW ── -->
<div id="login-view" class="login-wrap">
  <div class="login-card">
    <h1>PAYMENT · Credenciales IT</h1>
    <p class="sub">Ingresá tu nombre y PIN de administrador para continuar.</p>
    <div id="lockout-box" class="lockout-box hidden"></div>
    <form id="login-form" autocomplete="off">
      <div class="field" id="f-nombre">
        <label for="in-nombre">Nombre</label>
        <input id="in-nombre" type="text" maxlength="60" required>
        <div class="err-msg">Ingresá tu nombre.</div>
      </div>
      <div class="field" id="f-pin">
        <label for="in-pin">PIN</label>
        <input id="in-pin" type="password" maxlength="30" required>
        <div class="err-msg">PIN incorrecto o vacío.</div>
      </div>
      <button type="submit" id="login-btn" class="btn btn-primary btn-block">Ingresar</button>
    </form>
  </div>
</div>

<!-- ── APP SHELL ── -->
<div id="app" class="hidden">
  <div class="topbar">
    <div class="brand"><span class="brand-dot"></span> PAYMENT · IT</div>
    <nav class="tabs" role="tablist">
      <button class="tab-btn active" data-tab="dashboard" role="tab">📊 Dashboard</button>
      <button class="tab-btn" data-tab="credenciales" role="tab">🪪 Credenciales</button>
      <button class="tab-btn" data-tab="inventario" role="tab">📦 Inventario</button>
    </nav>
    <div class="presence-bar" id="presence-bar"></div>
    <div class="top-actions">
      <button class="icon-btn" id="theme-toggle" aria-label="Cambiar tema">🌙</button>
      <button class="icon-btn" id="logout-btn" aria-label="Cerrar sesión">🚪</button>
    </div>
  </div>

  <main>
    <!-- DASHBOARD -->
    <section id="view-dashboard" class="view active">
      <div class="stats-row" id="dash-stats"></div>
      <div class="dash-grid">
        <div class="panel">
          <div class="panel-head"><h2>Retiros — últimos 7 días</h2></div>
          <div class="bar-chart" id="bar-chart"></div>
        </div>
        <div class="panel">
          <div class="panel-head"><h2>Top agentes del mes</h2></div>
          <ul class="leaderboard" id="leaderboard"></ul>
        </div>
      </div>
    </section>

    <!-- CREDENCIALES -->
    <section id="view-credenciales" class="view">
      <div class="stats-row" id="cred-stats"></div>
      <div class="panel">
        <div class="panel-head">
          <h2>Credenciales</h2>
          <div class="search-box"><input id="cred-search" placeholder="Buscar por nombre, cédula, equipo..." aria-label="Buscar credenciales"></div>
          <div class="filter-pills" id="cred-filters">
            <button class="pill active" data-f="todas">Todas</button>
            <button class="pill" data-f="activas">Activas</button>
            <button class="pill" data-f="vencidas">Vencidas</button>
          </div>
          <button class="btn btn-secondary" id="btn-adv-search">🔎 Búsqueda avanzada</button>
          <button class="btn btn-secondary" id="btn-export">⬇️ Exportar CSV</button>
          <button class="btn btn-primary" id="btn-new-cred">＋ Nueva credencial</button>
        </div>
        <div id="cred-list-wrap" aria-busy="true">
          <ul class="cred-list" id="cred-list"></ul>
        </div>
        <div class="pager" id="cred-pager"></div>
      </div>
    </section>

    <!-- INVENTARIO -->
    <section id="view-inventario" class="view">
      <div class="stats-row" id="inv-stats"></div>
      <div class="panel">
        <div class="panel-head">
          <h2>Inventario</h2>
          <div class="search-box"><input id="inv-search" placeholder="Buscar # inv, serie, responsable..." aria-label="Buscar inventario"></div>
        </div>
        <div class="dropzone" id="inv-dropzone" tabindex="0" role="button" aria-label="Subir archivo Excel de inventario">
          📁 Arrastrá el Excel de Contact Finance acá, o hacé click para elegir un archivo
          <input type="file" id="inv-file" accept=".xlsx,.xls" class="hidden">
        </div>
        <div class="progress-track hidden" id="inv-progress-track"><div class="progress-fill" id="inv-progress-fill"></div></div>
        <div class="table-wrap">
          <table class="data-table">
            <thead><tr><th># Inv</th><th>Tipo</th><th>Modelo</th><th>Serie</th><th>Área</th><th>Responsable</th></tr></thead>
            <tbody id="inv-tbody" aria-busy="true"></tbody>
          </table>
        </div>
      </div>
    </section>
  </main>

  <div class="bulk-bar" id="bulk-bar">
    <span id="bulk-count" style="font-size:13px;font-weight:600;"></span>
    <button class="btn btn-secondary" data-bulk="auth">✅ Autorizar</button>
    <button class="btn btn-secondary" data-bulk="deauth">⛔ Desautorizar</button>
    <button class="btn btn-secondary" data-bulk="export">⬇️ Exportar selección</button>
    <button class="btn btn-danger" data-bulk="delete">🗑️ Eliminar</button>
  </div>
</div>

<!-- ── modal / template holders (populated by JS) ── -->
<div id="modal-root"></div>

<script>
'use strict';
/* ══════════════════════════════════════════════════════════
   CONFIG
══════════════════════════════════════════════════════════ */
const SUPABASE_URL = "https://xwbzagfyioommxalialq.supabase.co";
const SUPABASE_KEY = "sb_publishable_wcnhCVAVfWhCUECAabsW8A_aPcgQxmz";
const ADMIN_PIN    = "Paymentsa@CDE"; // ⚠️ ver nota de seguridad al final de la respuesta
const SB = { apikey: SUPABASE_KEY, Authorization: `Bearer ${SUPABASE_KEY}`, 'Content-Type':'application/json' };
const PAGE_SIZE = 50;
const AVATAR_COLORS = ['#14b8a6','#f59e0b','#ef4444','#8b5cf6','#3b82f6','#22c55e','#ec4899','#0ea5e9'];

/* ══════════════════════════════════════════════════════════
   UTIL — escape, debounce, retryFetch, colores
══════════════════════════════════════════════════════════ */
function esc(s){
  return String(s ?? '')
    .replace(/&/g,'&amp;').replace(/</g,'&lt;')
    .replace(/>/g,'&gt;').replace(/"/g,'&quot;')
    .replace(/'/g,'&#39;');
}
function debounce(fn, ms = 300){
  let t;
  return (...args) => { clearTimeout(t); t = setTimeout(() => fn(...args), ms); };
}
async function retryFetch(url, opts = {}, max = 3){
  let lastErr;
  for(let i = 0; i < max; i++){
    try{
      const res = await fetch(url, opts);
      if(!res.ok && res.status >= 500 && i < max - 1){
        await sleep(1000 * 2 ** i); continue;
      }
      return res;
    }catch(e){
      lastErr = e;
      if(i === max - 1) throw e;
      await sleep(1000 * 2 ** i);
    }
  }
  throw lastErr;
}
function sleep(ms){ return new Promise(r => setTimeout(r, ms)); }
function colorFor(str){
  let h = 0; for(const c of String(str)) h = c.charCodeAt(0) + ((h << 5) - h);
  return AVATAR_COLORS[Math.abs(h) % AVATAR_COLORS.length];
}
function initials(name){
  return String(name||'?').trim().split(/\s+/).slice(0,2).map(w=>w[0]?.toUpperCase()||'').join('');
}
function fmtDate(d){
  if(!d) return '—';
  try{ return new Date(d).toLocaleDateString('es-PY',{day:'2-digit',month:'2-digit',year:'numeric'}); }
  catch{ return String(d); }
}
function daysUntil(dateStr){
  if(!dateStr) return Infinity;
  const diff = new Date(dateStr).getTime() - Date.now();
  return Math.ceil(diff / 86400000);
}

/* ══════════════════════════════════════════════════════════
   TOAST SYSTEM
══════════════════════════════════════════════════════════ */
const TOAST_ICONS = { success:'✅', error:'⛔', warning:'⚠️', info:'ℹ️' };
function toast(message, type = 'info', duration = 4000){
  const region = document.getElementById('toast-region');
  const el = document.createElement('div');
  el.className = `toast ${type}`;
  el.setAttribute('role','status');
  el.innerHTML = `
    <span class="icon">${TOAST_ICONS[type]||'ℹ️'}</span>
    <span class="msg">${esc(message)}</span>
    <button class="close" aria-label="Cerrar notificación">✕</button>
    <div class="bar" style="animation-duration:${duration}ms"></div>`;
  region.appendChild(el);
  const remove = () => {
    el.classList.add('leaving');
    setTimeout(() => el.remove(), 200);
  };
  el.querySelector('.close').addEventListener('click', remove);
  const timer = setTimeout(remove, duration);
  el.addEventListener('mouseenter', () => clearTimeout(timer));
}

/* ══════════════════════════════════════════════════════════
   MODAL SYSTEM (Confirm / Input / Reactivar)
══════════════════════════════════════════════════════════ */
function trapFocus(container){
  const focusables = container.querySelectorAll('button,[href],input,select,textarea,[tabindex]:not([tabindex="-1"])');
  if(!focusables.length) return () => {};
  const first = focusables[0], last = focusables[focusables.length-1];
  function handler(e){
    if(e.key === 'Tab'){
      if(e.shiftKey && document.activeElement === first){ e.preventDefault(); last.focus(); }
      else if(!e.shiftKey && document.activeElement === last){ e.preventDefault(); first.focus(); }
    }
    if(e.key === 'Escape'){ closeModal(); }
  }
  container.addEventListener('keydown', handler);
  first.focus();
  return () => container.removeEventListener('keydown', handler);
}
let modalCleanup = null;
function closeModal(){
  const root = document.getElementById('modal-root');
  root.innerHTML = '';
  if(modalCleanup){ modalCleanup(); modalCleanup = null; }
}
function openModal(html, onMount){
  const root = document.getElementById('modal-root');
  root.innerHTML = `<div class="modal-backdrop" id="modal-backdrop"><div class="modal" role="dialog" aria-modal="true">${html}</div></div>`;
  root.querySelector('#modal-backdrop').addEventListener('click', e => { if(e.target.id === 'modal-backdrop') closeModal(); });
  modalCleanup = trapFocus(root.querySelector('.modal'));
  onMount?.(root);
}
function confirmModal(title, body, onConfirm, destructive = false){
  openModal(`
    <div class="modal-head"><h3>${esc(title)}</h3></div>
    <div class="modal-body">${esc(body)}</div>
    <div class="modal-foot">
      <button class="btn btn-ghost" id="cm-cancel">Cancelar</button>
      <button class="btn ${destructive?'btn-danger':'btn-primary'}" id="cm-ok">${destructive?'Eliminar':'Confirmar'}</button>
    </div>`, root => {
    root.querySelector('#cm-cancel').addEventListener('click', closeModal);
    root.querySelector('#cm-ok').addEventListener('click', () => { closeModal(); onConfirm(); });
  });
}
function inputModal(title, placeholder, onSubmit, defaultValue = ''){
  openModal(`
    <div class="modal-head"><h3>${esc(title)}</h3></div>
    <div class="modal-body"><input id="im-input" placeholder="${esc(placeholder)}" value="${esc(defaultValue)}"
      style="width:100%;background:var(--surface2);border:1px solid var(--border);color:var(--text);padding:11px;border-radius:8px;font-size:14px;"></div>
    <div class="modal-foot">
      <button class="btn btn-ghost" id="im-cancel">Cancelar</button>
      <button class="btn btn-primary" id="im-ok">Aceptar</button>
    </div>`, root => {
    const input = root.querySelector('#im-input');
    root.querySelector('#im-cancel').addEventListener('click', closeModal);
    const submit = () => { const v = input.value.trim(); closeModal(); onSubmit(v); };
    root.querySelector('#im-ok').addEventListener('click', submit);
    input.addEventListener('keydown', e => { if(e.key === 'Enter') submit(); });
  });
}
function reactivarModal(cred, onSubmit){
  const options = [1,3,7,30];
  openModal(`
    <div class="modal-head"><h3>Reactivar credencial</h3></div>
    <div class="modal-body">
      <p>Elegí la nueva validez para <strong>${esc(cred.responsable)}</strong>.</p>
      <div class="quick-days" id="qd-group">
        ${options.map(d => `<button class="pill" data-days="${d}">${d} día${d>1?'s':''}</button>`).join('')}
        <button class="pill" data-days="0">Sin límite</button>
      </div>
      <div class="field"><label>Días específicos</label><input id="qd-custom" type="number" min="0" max="3650"></div>
      <p style="font-size:12px;">Vence: <strong id="qd-preview">—</strong></p>
    </div>
    <div class="modal-foot">
      <button class="btn btn-ghost" id="qd-cancel">Cancelar</button>
      <button class="btn btn-primary" id="qd-ok">Reactivar</button>
    </div>`, root => {
    let chosen = null;
    const preview = root.querySelector('#qd-preview');
    const updatePreview = () => {
      if(chosen === null){ preview.textContent = '—'; return; }
      if(chosen === 0){ preview.textContent = 'Sin límite'; return; }
      const d = new Date(); d.setDate(d.getDate() + Number(chosen));
      preview.textContent = fmtDate(d);
    };
    root.querySelectorAll('#qd-group .pill').forEach(btn => {
      btn.addEventListener('click', () => {
        root.querySelectorAll('#qd-group .pill').forEach(b => b.classList.remove('active'));
        btn.classList.add('active'); chosen = Number(btn.dataset.days); updatePreview();
      });
    });
    root.querySelector('#qd-custom').addEventListener('input', e => {
      root.querySelectorAll('#qd-group .pill').forEach(b => b.classList.remove('active'));
      chosen = e.target.value === '' ? null : Number(e.target.value); updatePreview();
    });
    root.querySelector('#qd-cancel').addEventListener('click', closeModal);
    root.querySelector('#qd-ok').addEventListener('click', () => {
      if(chosen === null){ toast('Elegí una validez', 'warning'); return; }
      closeModal(); onSubmit(chosen);
    });
  });
}

/* ══════════════════════════════════════════════════════════
   SESSION / AUTH  (PIN lockout, session expiry, currentUser freeze)
══════════════════════════════════════════════════════════ */
const LOCKOUT_KEY = 'it_lockout';
const LOCKOUT_MS = 5 * 60 * 1000;
const MAX_ATTEMPTS = 5;
let currentUser = null;

function getLockout(){
  try{ return JSON.parse(localStorage.getItem(LOCKOUT_KEY)) || { attempts:0, until:0 }; }
  catch{ return { attempts:0, until:0 }; }
}
function setLockout(v){ localStorage.setItem(LOCKOUT_KEY, JSON.stringify(v)); }

function renderLockoutState(){
  const box = document.getElementById('lockout-box');
  const state = getLockout();
  const remaining = state.until - Date.now();
  if(remaining > 0){
    box.classList.remove('hidden');
    document.getElementById('login-btn').disabled = true;
    const tick = () => {
      const r = getLockout().until - Date.now();
      if(r <= 0){
        box.classList.add('hidden');
        document.getElementById('login-btn').disabled = false;
        clearInterval(iv);
        return;
      }
      const m = Math.floor(r/60000), s = Math.floor((r%60000)/1000);
      box.textContent = `🔒 Demasiados intentos. Probá de nuevo en ${m}:${String(s).padStart(2,'0')}`;
    };
    tick();
    const iv = setInterval(tick, 1000);
  }else{
    box.classList.add('hidden');
    document.getElementById('login-btn').disabled = false;
  }
}
renderLockoutState();

document.getElementById('login-form').addEventListener('submit', async e => {
  e.preventDefault();
  const nameField = document.getElementById('f-nombre');
  const pinField = document.getElementById('f-pin');
  const name = document.getElementById('in-nombre').value.trim();
  const pin = document.getElementById('in-pin').value;
  nameField.classList.toggle('invalid', !name);
  nameField.classList.toggle('valid', !!name);
  if(!name) return;

  const state = getLockout();
  if(state.until > Date.now()){ renderLockoutState(); return; }

  const btn = document.getElementById('login-btn');
  btn.disabled = true; btn.innerHTML = '<span class="spinner"></span> Verificando...';
  await sleep(250);

  if(pin !== ADMIN_PIN){
    const attempts = state.attempts + 1;
    if(attempts >= MAX_ATTEMPTS){
      setLockout({ attempts, until: Date.now() + LOCKOUT_MS });
      toast('Bloqueado por 5 minutos tras 5 intentos fallidos', 'error');
    }else{
      setLockout({ attempts, until: 0 });
      toast(`PIN incorrecto (${attempts}/${MAX_ATTEMPTS})`, 'error');
    }
    pinField.classList.add('invalid');
    btn.disabled = false; btn.innerHTML = 'Ingresar';
    renderLockoutState();
    return;
  }

  setLockout({ attempts:0, until:0 });
  currentUser = Object.freeze({ nombre: name, loginAt: Date.now() });
  sessionStorage.setItem('it_user', JSON.stringify(currentUser));
  enterApp();
});

function enterApp(){
  document.getElementById('login-view').classList.add('hidden');
  document.getElementById('app').classList.remove('hidden');
  registerPresence();
  resetInactivityTimer();
  loadAllData();
}

/* Restore session on load if not expired */
(function tryRestoreSession(){
  try{
    const saved = JSON.parse(sessionStorage.getItem('it_user'));
    if(saved && (Date.now() - saved.loginAt) < 8*60*60*1000){
      currentUser = Object.freeze(saved);
      enterApp();
    }
  }catch{}
})();

/* ── Inactivity / session expiry (8h) ── */
let inactivityTimer = null;
function resetInactivityTimer(){
  clearTimeout(inactivityTimer);
  inactivityTimer = setTimeout(() => {
    toast('Sesión cerrada por inactividad', 'warning');
    logout();
  }, 8 * 60 * 60 * 1000);
}
['mousemove','keydown','touchstart','click'].forEach(evt =>
  document.addEventListener(evt, () => { if(currentUser) resetInactivityTimer(); }, { passive:true })
);
function logout(){
  currentUser = null;
  sessionStorage.removeItem('it_user');
  document.getElementById('app').classList.add('hidden');
  document.getElementById('login-view').classList.remove('hidden');
  document.getElementById('in-pin').value = '';
}
document.getElementById('logout-btn').addEventListener('click', () => {
  confirmModal('Cerrar sesión', '¿Seguro que querés salir?', logout);
});

/* ══════════════════════════════════════════════════════════
   THEME
══════════════════════════════════════════════════════════ */
function applyTheme(mode){
  document.documentElement.setAttribute('data-theme', mode);
  document.getElementById('theme-toggle').textContent = mode === 'light' ? '☀️' : '🌙';
  localStorage.setItem('it_theme', mode);
}
(function initTheme(){
  const saved = localStorage.getItem('it_theme');
  const system = window.matchMedia('(prefers-color-scheme: light)').matches ? 'light' : 'dark';
  applyTheme(saved || system);
})();
document.getElementById('theme-toggle').addEventListener('click', () => {
  const cur = document.documentElement.getAttribute('data-theme') === 'light' ? 'dark' : 'light';
  applyTheme(cur);
});

/* ══════════════════════════════════════════════════════════
   OFFLINE HANDLING + operation queue
══════════════════════════════════════════════════════════ */
const QUEUE_KEY = 'it_pending_ops';
function getQueue(){ try{ return JSON.parse(localStorage.getItem(QUEUE_KEY)) || []; }catch{ return []; } }
function setQueue(q){ localStorage.setItem(QUEUE_KEY, JSON.stringify(q)); }
function queueOp(op){ const q = getQueue(); q.push(op); setQueue(q); }

async function flushQueue(){
  const q = getQueue();
  if(!q.length) return;
  let synced = 0;
  const remaining = [];
  for(const op of q){
    try{
      await retryFetch(op.url, op.opts, 1);
      synced++;
    }catch{ remaining.push(op); }
  }
  setQueue(remaining);
  if(synced) toast(`${synced} cambio${synced>1?'s':''} sincronizado${synced>1?'s':''}`, 'success');
}
window.addEventListener('offline', () => document.getElementById('offline-banner').classList.add('show'));
window.addEventListener('online', () => {
  document.getElementById('offline-banner').classList.remove('show');
  toast('Conexión restablecida', 'success');
  flushQueue();
});
if(!navigator.onLine) document.getElementById('offline-banner').classList.add('show');

/* ══════════════════════════════════════════════════════════
   PRESENCE
══════════════════════════════════════════════════════════ */
async function registerPresence(){
  try{
    await retryFetch(`${SUPABASE_URL}/rest/v1/sesiones_activas`, {
      method:'POST', headers:{...SB, Prefer:'resolution=merge-duplicates'},
      body: JSON.stringify([{ id: currentUser.nombre, nombre: currentUser.nombre, last_seen: new Date().toISOString() }])
    });
  }catch(e){ /* silent — presence is best-effort */ }
  refreshPresence();
  setInterval(async () => {
    if(!currentUser) return;
    try{
      await retryFetch(`${SUPABASE_URL}/rest/v1/sesiones_activas`, {
        method:'POST', headers:{...SB, Prefer:'resolution=merge-duplicates'},
        body: JSON.stringify([{ id: currentUser.nombre, nombre: currentUser.nombre, last_seen: new Date().toISOString() }])
      });
    }catch{}
    refreshPresence();
  }, 20000);
}
async function refreshPresence(){
  try{
    const cutoff = new Date(Date.now() - 60000).toISOString();
    const res = await retryFetch(`${SUPABASE_URL}/rest/v1/sesiones_activas?select=*&last_seen=gte.${cutoff}`, { headers: SB });
    const rows = await res.json();
    const bar = document.getElementById('presence-bar');
    if(rows.length <= 1){ bar.innerHTML = `<span style="font-size:12px;color:var(--muted);">Solo vos</span>`; return; }
    bar.innerHTML = rows.map(r => `
      <div class="presence-avatar" style="background:${colorFor(r.nombre)}">
        ${esc(initials(r.nombre))}
        <span class="ptip">${esc(r.nombre)}</span>
      </div>`).join('');
  }catch{ /* ignore presence errors */ }
}

/* ══════════════════════════════════════════════════════════
   DATA STORE (single load, in-memory + sessionStorage cache)
══════════════════════════════════════════════════════════ */
let credCache = [];
let invCache = [];
let scanCache = [];
let credPage = 1;
let selectedIds = new Set();
let credFilter = 'todas';
let credSearchTerm = '';

function fromRow(r){ return r; }
function toRow(c){ return c; }

async function loadAllData(){
  await Promise.all([loadAllCreds(), loadInventory(), loadScans()]);
  renderDashboard();
  renderCredList();
  renderInventory();
}

/* Un solo SELECT * — sin loops N+1 */
async function loadAllCreds(){
  try{
    const res = await retryFetch(`${SUPABASE_URL}/rest/v1/credenciales?select=*&order=created_at.desc`, { headers: SB });
    if(!res.ok) throw new Error('HTTP ' + res.status);
    credCache = (await res.json()).map(fromRow);
  }catch(e){
    toast('No se pudo cargar credenciales — usando caché', 'warning');
    try{ credCache = JSON.parse(sessionStorage.getItem('it_cred_cache')) || []; }catch{ credCache = []; }
  }
  document.getElementById('cred-list-wrap').setAttribute('aria-busy','false');
}

async function loadInventory(){
  try{
    const cached = sessionStorage.getItem('it_inv_cache');
    if(cached){ invCache = JSON.parse(cached); }
    const res = await retryFetch(`${SUPABASE_URL}/rest/v1/inventario?select=*`, { headers: SB });
    if(res.ok){
      invCache = await res.json();
      sessionStorage.setItem('it_inv_cache', JSON.stringify(invCache));
    }
  }catch(e){
    toast('No se pudo actualizar inventario — usando caché local', 'warning');
  }
  document.getElementById('inv-tbody').setAttribute('aria-busy','false');
}

async function loadScans(){
  try{
    const res = await retryFetch(`${SUPABASE_URL}/rest/v1/escaneos_log?select=*&order=fecha.desc&limit=500`, { headers: SB });
    if(res.ok) scanCache = await res.json();
  }catch{ scanCache = []; }
}

/* ══════════════════════════════════════════════════════════
   TABS
══════════════════════════════════════════════════════════ */
document.querySelectorAll('.tab-btn').forEach(btn => {
  btn.addEventListener('click', () => {
    document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    btn.classList.add('active');
    document.getElementById('view-' + btn.dataset.tab).classList.add('active');
  });
});

/* ══════════════════════════════════════════════════════════
   DASHBOARD
══════════════════════════════════════════════════════════ */
function renderDashboard(){
  const today = new Date().toDateString();
  const createdToday = credCache.filter(c => new Date(c.created_at).toDateString() === today).length;
  const authToday = credCache.filter(c => c.estado === 'AUTORIZADO' && new Date(c.created_at).toDateString() === today).length;
  const scannedToday = scanCache.filter(s => new Date(s.fecha).toDateString() === today).length;

  document.getElementById('dash-stats').innerHTML = `
    ${statCard(createdToday,'Creadas hoy')}
    ${statCard(authToday,'Autorizadas hoy','green')}
    ${statCard(scannedToday,'Escaneadas hoy','amber')}
    ${statCard(credCache.length,'Total credenciales')}
  `;

  // bar chart — últimos 7 días
  const days = [...Array(7)].map((_,i) => {
    const d = new Date(); d.setDate(d.getDate() - (6-i));
    return d;
  });
  const counts = days.map(d => scanCache.filter(s => new Date(s.fecha).toDateString() === d.toDateString()).length);
  const max = Math.max(...counts, 1);
  document.getElementById('bar-chart').innerHTML = days.map((d,i) => `
    <div class="bar-col">
      <div class="bar-fill" style="height:${Math.max(4,(counts[i]/max)*120)}px"></div>
      <div class="bar-lbl">${d.toLocaleDateString('es-PY',{weekday:'short'})}</div>
    </div>`).join('');

  // leaderboard
  const monthCutoff = new Date(); monthCutoff.setDate(1);
  const counts2 = {};
  scanCache.filter(s => new Date(s.fecha) >= monthCutoff).forEach(s => {
    counts2[s.responsable] = (counts2[s.responsable]||0) + 1;
  });
  const top = Object.entries(counts2).sort((a,b)=>b[1]-a[1]).slice(0,6);
  document.getElementById('leaderboard').innerHTML = top.length
    ? top.map(([name,n]) => `<li><span>${esc(name)}</span><strong>${n}</strong></li>`).join('')
    : `<li style="color:var(--muted);">Sin datos este mes</li>`;
}
function statCard(num,label,tone=''){
  return `<div class="stat-card ${tone}"><div class="num">${num}</div><div class="lbl">${esc(label)}</div></div>`;
}

/* ══════════════════════════════════════════════════════════
   CREDENCIALES — filtering, search, render, pagination
══════════════════════════════════════════════════════════ */
function computeStatus(c){
  if(c.estado === 'NO AUTORIZADO') return 'no';
  if(c.vencimiento && daysUntil(c.vencimiento) < 0) return 'exp';
  return 'ok';
}
function filteredCreds(){
  let list = credCache;
  if(credFilter === 'activas') list = list.filter(c => computeStatus(c) === 'ok');
  if(credFilter === 'vencidas') list = list.filter(c => computeStatus(c) === 'exp');
  if(credSearchTerm){
    const q = credSearchTerm.toLowerCase();
    list = list.filter(c => [c.responsable,c.cedula,c.equipo,c.modelo,c.serie].some(f => String(f||'').toLowerCase().includes(q)));
  }
  return list;
}
function renderCredStats(){
  const total = credCache.length;
  const auth = credCache.filter(c => c.estado === 'AUTORIZADO').length;
  const noauth = credCache.filter(c => c.estado === 'NO AUTORIZADO').length;
  const soon = credCache.filter(c => { const d = daysUntil(c.vencimiento); return d >= 0 && d <= 3; }).length;
  document.getElementById('cred-stats').innerHTML = `
    ${statCard(total,'Total')}
    ${statCard(auth,'Autorizadas','green')}
    ${statCard(noauth,'No autorizadas','red')}
    ${statCard(soon,'Vencen pronto','amber')}
  `;
}
function renderCredList(){
  renderCredStats();
  const list = filteredCreds();
  const start = 0, end = credPage * PAGE_SIZE;
  const pageItems = list.slice(start, end);
  const ul = document.getElementById('cred-list');

  if(!list.length){
    ul.innerHTML = '';
    document.getElementById('cred-list-wrap').innerHTML = `
      <div class="empty-state"><div class="big">🗂️</div>Ninguna credencial coincide con la búsqueda actual.</div>`;
  } else {
    document.getElementById('cred-list-wrap').innerHTML = `<ul class="cred-list" id="cred-list"></ul>`;
    const freshUl = document.getElementById('cred-list');
    freshUl.innerHTML = pageItems.map(c => {
      const st = computeStatus(c);
      const badge = st === 'ok' ? '<span class="badge ok">AUTORIZADO</span>' : st === 'no' ? '<span class="badge no">NO AUTORIZADO</span>' : '<span class="badge exp">VENCIDA</span>';
      return `
      <li class="cred-item" data-id="${esc(c.id)}">
        <input type="checkbox" class="cred-check" data-id="${esc(c.id)}" aria-label="Seleccionar ${esc(c.responsable)}" ${selectedIds.has(c.id)?'checked':''}>
        <div class="avatar" style="background:${colorFor(c.responsable)}">
          ${c.foto ? `<img src="${c.foto}" alt="" style="width:100%;height:100%;border-radius:50%;object-fit:cover;">` : esc(initials(c.responsable))}
        </div>
        <div class="ci-main">
          <div class="ci-name">${esc(c.responsable)}</div>
          <div class="ci-sub">${esc(c.equipo||'')} · ${esc(c.modelo||'')} · vence ${fmtDate(c.vencimiento)}</div>
        </div>
        ${badge}
        <div class="ci-actions">
          <button class="icon-btn" data-act="toggle" data-id="${esc(c.id)}" aria-label="Autorizar o desautorizar">${st==='no'?'✅':'⛔'}</button>
          <button class="icon-btn" data-act="reactivate" data-id="${esc(c.id)}" aria-label="Reactivar">🔄</button>
          <button class="icon-btn" data-act="print" data-id="${esc(c.id)}" aria-label="Ver credencial imprimible">🖨️</button>
          <button class="icon-btn" data-act="delete" data-id="${esc(c.id)}" aria-label="Eliminar">🗑️</button>
        </div>
      </li>`;
    }).join('');
  }

  document.getElementById('cred-pager').innerHTML = end < list.length
    ? `<button class="btn btn-secondary" id="load-more">Cargar más (${list.length - end} restantes)</button>` : '';
  document.getElementById('load-more')?.addEventListener('click', () => { credPage++; renderCredList(); });

  bindCredListEvents();
  updateBulkBar();
}
function bindCredListEvents(){
  document.querySelectorAll('.cred-check').forEach(cb => {
    cb.addEventListener('change', () => {
      const id = cb.dataset.id;
      if(cb.checked) selectedIds.add(id); else selectedIds.delete(id);
      updateBulkBar();
    });
  });
  document.querySelectorAll('[data-act]').forEach(btn => {
    btn.addEventListener('click', () => handleCredAction(btn.dataset.act, btn.dataset.id));
  });
}
function updateBulkBar(){
  const bar = document.getElementById('bulk-bar');
  if(selectedIds.size > 0){
    bar.classList.add('show');
    document.getElementById('bulk-count').textContent = `${selectedIds.size} seleccionada(s)`;
  }else{
    bar.classList.remove('show');
  }
}
document.getElementById('cred-search').addEventListener('input', debounce(e => {
  credSearchTerm = e.target.value; credPage = 1; renderCredList();
}, 300));
document.querySelectorAll('#cred-filters .pill').forEach(btn => {
  btn.addEventListener('click', () => {
    document.querySelectorAll('#cred-filters .pill').forEach(b => b.classList.remove('active'));
    btn.classList.add('active'); credFilter = btn.dataset.f; credPage = 1; renderCredList();
  });
});

function handleCredAction(act, id){
  const c = credCache.find(x => String(x.id) === String(id));
  if(!c) return;
  if(act === 'toggle'){
    const newState = c.estado === 'AUTORIZADO' ? 'NO AUTORIZADO' : 'AUTORIZADO';
    updateCredField(c, { estado: newState }, `${c.responsable}: ${newState}`);
  }
  if(act === 'reactivate'){
    reactivarModal(c, days => {
      const venc = days === 0 ? null : (() => { const d = new Date(); d.setDate(d.getDate()+days); return d.toISOString(); })();
      updateCredField(c, { estado:'AUTORIZADO', dias_validez: days, vencimiento: venc }, 'Credencial reactivada');
    });
  }
  if(act === 'delete'){
    confirmModal('Eliminar credencial', `Esto eliminará la credencial de ${c.responsable} permanentemente.`, () => deleteCred(c), true);
  }
  if(act === 'print'){
    showPrintableCred(c);
  }
}
async function updateCredField(c, patch, successMsg){
  const updated = { ...c, ...patch };
  const idx = credCache.findIndex(x => x.id === c.id);
  credCache[idx] = updated;
  renderCredList();
  const body = JSON.stringify([toRow(updated)]);
  const opts = { method:'POST', headers:{...SB, Prefer:'resolution=merge-duplicates'}, body };
  const url = `${SUPABASE_URL}/rest/v1/credenciales`;
  try{
    if(!navigator.onLine) throw new Error('offline');
    await retryFetch(url, opts);
    toast(successMsg, 'success');
  }catch{
    queueOp({ url, opts });
    toast('Sin conexión — el cambio se aplicará al reconectar', 'warning');
  }
}
async function deleteCred(c){
  credCache = credCache.filter(x => x.id !== c.id);
  selectedIds.delete(c.id);
  renderCredList();
  const url = `${SUPABASE_URL}/rest/v1/credenciales?id=eq.${encodeURIComponent(c.id)}`;
  const opts = { method:'DELETE', headers: SB };
  try{
    if(!navigator.onLine) throw new Error('offline');
    await retryFetch(url, opts);
    toast('Credencial eliminada', 'success');
  }catch{
    queueOp({ url, opts });
    toast('Sin conexión — se eliminará al reconectar', 'warning');
  }
}

/* ── bulk actions ── */
document.querySelectorAll('[data-bulk]').forEach(btn => {
  btn.addEventListener('click', () => {
    const ids = [...selectedIds];
    if(!ids.length) return;
    const action = btn.dataset.bulk;
    if(action === 'delete'){
      confirmModal('Eliminar seleccionadas', `Se eliminarán ${ids.length} credenciales.`, () => bulkProcess(ids, 'delete'), true);
    }else if(action === 'export'){
      exportCSV(credCache.filter(c => ids.includes(c.id)));
    }else{
      bulkProcess(ids, action);
    }
  });
});
async function bulkProcess(ids, action){
  let done = 0;
  for(const id of ids){
    const c = credCache.find(x => x.id === id);
    if(!c) continue;
    if(action === 'auth') await updateCredField(c, { estado:'AUTORIZADO' }, '');
    if(action === 'deauth') await updateCredField(c, { estado:'NO AUTORIZADO' }, '');
    if(action === 'delete') await deleteCred(c);
    done++;
    toast(`Procesando ${done} de ${ids.length}...`, 'info', 1200);
  }
  selectedIds.clear();
  renderCredList();
  toast(`${done} credencial(es) procesadas`, 'success');
}

/* ── CSV export ── */
document.getElementById('btn-export').addEventListener('click', () => exportCSV(filteredCreds()));
function exportCSV(rows){
  if(!rows.length){ toast('Nada para exportar', 'warning'); return; }
  const cols = ['id','equipo','marca','modelo','serie','inventario','responsable','cedula','estado','vencimiento','created_at'];
  const csv = [cols.join(',')].concat(rows.map(r => cols.map(c => `"${String(r[c]??'').replace(/"/g,'""')}"`).join(','))).join('\n');
  const blob = new Blob([csv], { type:'text/csv' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a'); a.href = url; a.download = `credenciales_${Date.now()}.csv`; a.click();
  URL.revokeObjectURL(url);
  toast('CSV exportado', 'success');
}

/* ══════════════════════════════════════════════════════════
   BÚSQUEDA AVANZADA (modal)
══════════════════════════════════════════════════════════ */
document.getElementById('btn-adv-search').addEventListener('click', () => {
  openModal(`
    <div class="modal-head"><h3>Búsqueda avanzada</h3></div>
    <div class="modal-body">
      <div class="field"><label>Nombre / cédula</label><input id="as-name"></div>
      <div class="field"><label>Estado</label>
        <select id="as-estado"><option value="">Todos</option><option>AUTORIZADO</option><option>NO AUTORIZADO</option></select>
      </div>
      <div class="field"><label>Vencimiento desde</label><input id="as-from" type="date"></div>
      <div class="field"><label>Vencimiento hasta</label><input id="as-to" type="date"></div>
      <div id="as-results" style="max-height:240px;overflow-y:auto;"></div>
    </div>
    <div class="modal-foot"><button class="btn btn-secondary" id="as-close">Cerrar</button></div>`,
    root => {
      const run = debounce(() => {
        const name = root.querySelector('#as-name').value.toLowerCase();
        const estado = root.querySelector('#as-estado').value;
        const from = root.querySelector('#as-from').value;
        const to = root.querySelector('#as-to').value;
        let res = credCache.filter(c => {
          if(name && !`${c.responsable} ${c.cedula}`.toLowerCase().includes(name)) return false;
          if(estado && c.estado !== estado) return false;
          if(from && (!c.vencimiento || c.vencimiento < from)) return false;
          if(to && (!c.vencimiento || c.vencimiento > to)) return false;
          return true;
        }).slice(0,30);
        root.querySelector('#as-results').innerHTML = res.map(c => `
          <div style="display:flex;justify-content:space-between;padding:6px 0;border-bottom:1px solid var(--border);font-size:13px;">
            <span>${esc(c.responsable)}</span><span>${esc(c.estado)}</span>
          </div>`).join('') || '<p style="color:var(--muted);font-size:12px;">Sin resultados</p>';
      }, 300);
      ['as-name','as-estado','as-from','as-to'].forEach(id => root.querySelector('#'+id).addEventListener('input', run));
      root.querySelector('#as-close').addEventListener('click', closeModal);
      run();
    });
});

/* ══════════════════════════════════════════════════════════
   NUEVA CREDENCIAL — form, validación, compresión, autosave
══════════════════════════════════════════════════════════ */
const AUTOSAVE_KEY = 'it_new_cred_draft';
document.getElementById('btn-new-cred').addEventListener('click', openNewCredForm);
function openNewCredForm(){
  let draft = {};
  try{ draft = JSON.parse(localStorage.getItem(AUTOSAVE_KEY)) || {}; }catch{}
  openModal(`
    <div class="modal-head"><h3>Nueva credencial</h3></div>
    <div class="modal-body">
      <div class="photo-drop" id="nc-photo-drop" tabindex="0" role="button" aria-label="Subir foto">
        <span id="nc-photo-placeholder">📷<br>Foto</span>
        <input type="file" id="nc-photo-input" accept="image/*" class="hidden">
      </div>
      <div class="field" id="nc-f-responsable"><label>Responsable *</label><input id="nc-responsable" maxlength="80" value="${esc(draft.responsable||'')}"><div class="err-msg">Campo requerido</div></div>
      <div class="field" id="nc-f-cedula"><label>Cédula *</label><input id="nc-cedula" maxlength="20" value="${esc(draft.cedula||'')}"><div class="err-msg">Campo requerido</div></div>
      <div class="field" id="nc-f-equipo"><label>Equipo *</label><input id="nc-equipo" maxlength="60" value="${esc(draft.equipo||'')}"><div class="err-msg">Campo requerido</div></div>
      <div class="field"><label>Marca</label><input id="nc-marca" maxlength="40" value="${esc(draft.marca||'')}"></div>
      <div class="field"><label>Modelo</label><input id="nc-modelo" maxlength="40" value="${esc(draft.modelo||'')}"></div>
      <div class="field"><label>Serie</label><input id="nc-serie" maxlength="40" value="${esc(draft.serie||'')}"></div>
      <div class="field"><label>N° Inventario</label><input id="nc-inventario" maxlength="20" value="${esc(draft.inventario||'')}"></div>
      <div class="field"><label>Motivo</label><textarea id="nc-motivo" maxlength="200" rows="2">${esc(draft.motivo||'')}</textarea><div class="char-count"><span id="nc-motivo-count">0</span>/200</div></div>
    </div>
    <div class="modal-foot">
      <button class="btn btn-ghost" id="nc-cancel">Cancelar</button>
      <button class="btn btn-primary" id="nc-submit">Crear credencial</button>
    </div>`, root => {
    let photoData = draft.foto || null;
    const fields = ['responsable','cedula','equipo','marca','modelo','serie','inventario','motivo'];

    function saveDraft(){
      const d = {}; fields.forEach(f => d[f] = root.querySelector('#nc-'+f)?.value || '');
      d.foto = photoData;
      localStorage.setItem(AUTOSAVE_KEY, JSON.stringify(d));
    }
    fields.forEach(f => root.querySelector('#nc-'+f)?.addEventListener('input', debounce(saveDraft, 400)));

    root.querySelector('#nc-motivo').addEventListener('input', e => {
      root.querySelector('#nc-motivo-count').textContent = e.target.value.length;
    });

    ['responsable','cedula','equipo'].forEach(f => {
      const input = root.querySelector('#nc-'+f);
      const wrap = root.querySelector('#nc-f-'+f);
      input.addEventListener('blur', () => {
        const ok = input.value.trim().length > 0;
        wrap.classList.toggle('invalid', !ok);
        wrap.classList.toggle('valid', ok);
      });
    });

    const dropEl = root.querySelector('#nc-photo-drop');
    const fileInput = root.querySelector('#nc-photo-input');
    dropEl.addEventListener('click', () => fileInput.click());
    dropEl.addEventListener('keydown', e => { if(e.key === 'Enter') fileInput.click(); });
    fileInput.addEventListener('change', async () => {
      const file = fileInput.files[0]; if(!file) return;
      dropEl.querySelector('#nc-photo-placeholder')?.remove();
      dropEl.innerHTML = 'Comprimiendo...';
      photoData = await compressImage(file);
      dropEl.innerHTML = `<img src="${photoData}" alt="Vista previa">`;
      saveDraft();
    });
    if(photoData) dropEl.innerHTML = `<img src="${photoData}" alt="Vista previa">`;

    root.querySelector('#nc-cancel').addEventListener('click', closeModal);
    root.querySelector('#nc-submit').addEventListener('click', async () => {
      const vals = {}; fields.forEach(f => vals[f] = root.querySelector('#nc-'+f).value.trim());
      const missing = ['responsable','cedula','equipo'].filter(f => !vals[f]);
      if(missing.length){
        missing.forEach(f => { root.querySelector('#nc-f-'+f).classList.add('invalid'); });
        toast('Completá los campos requeridos', 'warning');
        return;
      }
      const submitBtn = root.querySelector('#nc-submit');
      submitBtn.disabled = true; submitBtn.innerHTML = '<span class="spinner"></span> Creando...';
      const newCred = {
        id: crypto.randomUUID(), ...vals, foto: photoData, estado: 'AUTORIZADO',
        fecha: new Date().toISOString(), creado_por: currentUser.nombre, historial: [],
        dias_validez: null, vencimiento: null, created_at: new Date().toISOString()
      };
      credCache.unshift(newCred);
      renderCredList();
      const url = `${SUPABASE_URL}/rest/v1/credenciales`;
      const opts = { method:'POST', headers:{...SB, Prefer:'resolution=merge-duplicates'}, body: JSON.stringify([toRow(newCred)]) };
      try{
        if(!navigator.onLine) throw new Error('offline');
        await retryFetch(url, opts);
        toast('Credencial creada', 'success');
      }catch{
        queueOp({ url, opts });
        toast('Sin conexión — se sincronizará al reconectar', 'warning');
      }
      localStorage.removeItem(AUTOSAVE_KEY);
      closeModal();
    });
  });
}

/* Compresión de foto — Canvas API, max 400x400, jpeg 0.75 */
async function compressImage(file, maxW = 400, quality = 0.75){
  return new Promise((resolve, reject) => {
    const img = new Image();
    img.onload = () => {
      const canvas = document.createElement('canvas');
      const scale = Math.min(maxW / img.width, maxW / img.height, 1);
      canvas.width = img.width * scale;
      canvas.height = img.height * scale;
      canvas.getContext('2d').drawImage(img, 0, 0, canvas.width, canvas.height);
      resolve(canvas.toDataURL('image/jpeg', quality));
    };
    img.onerror = reject;
    img.src = URL.createObjectURL(file);
  });
}

/* ══════════════════════════════════════════════════════════
   CREDENCIAL IMPRIMIBLE
══════════════════════════════════════════════════════════ */
function showPrintableCred(c){
  openModal(`
    <div class="modal-head"><h3>Credencial imprimible</h3></div>
    <div class="modal-body">
      <div class="cred-print" id="print-target">
        <div class="logo">PAYMENT · IT</div>
        ${c.foto ? `<img class="photo" src="${c.foto}" alt="">` : `<div class="photo" style="display:flex;align-items:center;justify-content:center;background:#eee;font-weight:700;">${esc(initials(c.responsable))}</div>`}
        <div style="font-weight:700;font-size:14px;">${esc(c.responsable)}</div>
        <div style="font-size:11px;color:#555;">${esc(c.equipo||'')}</div>
        <div class="qr-wrap"><div id="qr-box"></div></div>
        <div class="cid">${esc(c.id)}</div>
      </div>
    </div>
    <div class="modal-foot">
      <button class="btn btn-secondary" id="print-close">Cerrar</button>
      <button class="btn btn-secondary" id="print-download">⬇️ Descargar imagen</button>
      <button class="btn btn-primary" id="print-print">🖨️ Imprimir</button>
    </div>`, root => {
    try{ new QRCode(root.querySelector('#qr-box'), { text: String(c.id), width: 300, height: 300 }); }
    catch{ root.querySelector('#qr-box').textContent = '[QR no disponible]'; }
    root.querySelector('#print-close').addEventListener('click', closeModal);
    root.querySelector('#print-print').addEventListener('click', () => window.print());
    root.querySelector('#print-download').addEventListener('click', async () => {
      try{
        const canvas = await html2canvas(root.querySelector('#print-target'), { scale: 4 });
        const a = document.createElement('a');
        a.download = `credencial_${c.responsable}.png`;
        a.href = canvas.toDataURL('image/png');
        a.click();
      }catch{ toast('No se pudo generar la imagen', 'error'); }
    });
  });
}

/* ══════════════════════════════════════════════════════════
   INVENTARIO — upload, preview, batch upsert, tabla
══════════════════════════════════════════════════════════ */
function renderInvStats(){
  const total = invCache.length;
  const asignados = invCache.filter(i => i.resp).length;
  document.getElementById('inv-stats').innerHTML = `
    ${statCard(total,'Total')}
    ${statCard(total-asignados,'Disponibles','green')}
    ${statCard(asignados,'Asignados','amber')}
  `;
}
let invSearchTerm = '';
function renderInventory(){
  renderInvStats();
  let rows = invCache;
  if(invSearchTerm){
    const q = invSearchTerm.toLowerCase();
    rows = rows.filter(r => [r.inv,r.serie,r.resp,r.area,r.tipo].some(f => String(f||'').toLowerCase().includes(q)));
  }
  const tbody = document.getElementById('inv-tbody');
  tbody.setAttribute('aria-busy','false');
  tbody.innerHTML = rows.slice(0,300).map(r => `
    <tr><td>${esc(r.inv)}</td><td>${esc(r.tipo)}</td><td>${esc(r.modelo)}</td><td class="mono">${esc(r.serie)}</td><td>${esc(r.area)}</td><td>${esc(r.resp)}</td></tr>
  `).join('') || `<tr><td colspan="6" style="text-align:center;color:var(--muted);padding:24px;">Sin registros de inventario</td></tr>`;
}
document.getElementById('inv-search').addEventListener('input', debounce(e => { invSearchTerm = e.target.value; renderInventory(); }, 300));

const dropzone = document.getElementById('inv-dropzone');
const invFileInput = document.getElementById('inv-file');
dropzone.addEventListener('click', () => invFileInput.click());
dropzone.addEventListener('keydown', e => { if(e.key === 'Enter') invFileInput.click(); });
['dragover','dragenter'].forEach(evt => dropzone.addEventListener(evt, e => { e.preventDefault(); dropzone.classList.add('drag'); }));
['dragleave','drop'].forEach(evt => dropzone.addEventListener(evt, e => { e.preventDefault(); dropzone.classList.remove('drag'); }));
dropzone.addEventListener('drop', e => { const f = e.dataTransfer.files[0]; if(f) handleInvFile(f); });
invFileInput.addEventListener('change', () => { const f = invFileInput.files[0]; if(f) handleInvFile(f); });

function handleInvFile(file){
  const reader = new FileReader();
  reader.onload = e => {
    try{
      const wb = XLSX.read(e.target.result, { type:'array' });
      const sheet = wb.Sheets[wb.SheetNames[0]];
      const data = XLSX.utils.sheet_to_json(sheet, { defval:'' });
      showImportPreview(data);
    }catch(err){ toast('No se pudo leer el archivo Excel', 'error'); }
  };
  reader.readAsArrayBuffer(file);
}
function showImportPreview(rows){
  const preview = rows.slice(0,5);
  openModal(`
    <div class="modal-head"><h3>Importar inventario (${rows.length} filas)</h3></div>
    <div class="modal-body">
      <p>Vista previa de las primeras 5 filas:</p>
      <div class="table-wrap" style="max-height:200px;">
        <table class="data-table"><thead><tr>${Object.keys(preview[0]||{}).map(k=>`<th>${esc(k)}</th>`).join('')}</tr></thead>
        <tbody>${preview.map(r=>`<tr>${Object.values(r).map(v=>`<td>${esc(v)}</td>`).join('')}</tr>`).join('')}</tbody></table>
      </div>
    </div>
    <div class="modal-foot">
      <button class="btn btn-ghost" id="imp-cancel">Cancelar</button>
      <button class="btn btn-primary" id="imp-confirm">Confirmar importación</button>
    </div>`, root => {
    root.querySelector('#imp-cancel').addEventListener('click', closeModal);
    root.querySelector('#imp-confirm').addEventListener('click', () => { closeModal(); batchUpsertInventory(rows); });
  });
}
async function batchUpsertInventory(rows){
  const track = document.getElementById('inv-progress-track');
  const fill = document.getElementById('inv-progress-fill');
  track.classList.remove('hidden'); fill.style.width = '0%';
  const batchSize = 50;
  const mapped = rows.map(r => ({
    inv: r['# Inv'] || r.inv || r.Inventario || '',
    serie: r.Serie || r.serie || '',
    area: r.Área || r.Area || r.area || '',
    tipo: r.Tipo || r.tipo || '',
    modelo: r.Modelo || r.modelo || '',
    proc: r.Procesador || r.proc || '',
    ram: r.RAM || r.ram || '',
    alm: r.Almacenamiento || r.alm || '',
    resp: r.Responsable || r.resp || '',
    updated_at: new Date().toISOString()
  }));
  for(let i = 0; i < mapped.length; i += batchSize){
    const batch = mapped.slice(i, i + batchSize);
    try{
      await retryFetch(`${SUPABASE_URL}/rest/v1/inventario`, {
        method:'POST', headers:{...SB, Prefer:'resolution=merge-duplicates'}, body: JSON.stringify(batch)
      });
    }catch{ toast('Error subiendo un lote — continuando', 'warning'); }
    fill.style.width = `${Math.min(100, ((i+batchSize)/mapped.length)*100)}%`;
    await sleep(30);
  }
  toast('Importación completada', 'success');
  setTimeout(() => track.classList.add('hidden'), 800);
  await loadInventory();
  renderInventory();
}

/* ══════════════════════════════════════════════════════════
   KEYBOARD SHORTCUTS
══════════════════════════════════════════════════════════ */
document.addEventListener('keydown', e => {
  if(!currentUser) return;
  const ctrl = e.ctrlKey || e.metaKey;
  if(ctrl && e.key.toLowerCase() === 'n'){ e.preventDefault(); openNewCredForm(); }
  if(ctrl && e.key.toLowerCase() === 'f'){ e.preventDefault(); document.getElementById('btn-adv-search').click(); }
  if(ctrl && e.key.toLowerCase() === 'e'){ e.preventDefault(); document.getElementById('btn-export').click(); }
  if(e.key === 'Escape'){ closeModal(); }
});

/* ══════════════════════════════════════════════════════════
   SKELETON — initial loading state before data arrives
══════════════════════════════════════════════════════════ */
(function initSkeletons(){
  const skelHtml = [...Array(6)].map(() => `
    <li class="skel-row">
      <div class="skel skel-avatar"></div>
      <div style="flex:1;"><div class="skel skel-line" style="width:40%;margin-bottom:6px;"></div><div class="skel skel-line" style="width:70%;"></div></div>
    </li>`).join('');
  document.getElementById('cred-list').innerHTML = skelHtml;
  document.getElementById('inv-tbody').innerHTML = `<tr><td colspan="6"><div class="skel skel-line" style="width:100%;height:16px;"></div></td></tr>`.repeat(6);
})();
</script>
</body>
</html>
