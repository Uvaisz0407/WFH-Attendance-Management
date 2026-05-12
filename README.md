<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>WorkTrack — Attendance Management</title>
<link href="https://fonts.googleapis.com/css2?family=Manrope:wght@300;400;500;600;700;800&family=Sora:wght@300;400;500;600;700&display=swap" rel="stylesheet">
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.28/jspdf.plugin.autotable.min.js"></script>
<style>
  :root {
    --bg: #0a0b0f;
    --bg2: #111318;
    --bg3: #181b22;
    --bg4: #1e2230;
    --border: rgba(255,255,255,0.07);
    --border2: rgba(255,255,255,0.12);
    --text: #f0f2f8;
    --text2: #8a90a8;
    --text3: #555d78;
    --accent: #4f7fff;
    --accent2: #7c5fff;
    --green: #22c98a;
    --yellow: #f5c842;
    --red: #ff5f7e;
    --orange: #ff9645;
    --radius: 12px;
    --radius2: 8px;
    --shadow: 0 4px 24px rgba(0,0,0,0.4);
  }
  * { box-sizing: border-box; margin: 0; padding: 0; }
  body { font-family: 'Manrope', sans-serif; background: var(--bg); color: var(--text); min-height: 100vh; overflow-x: hidden; }
  #app { min-height: 100vh; }

  /* Auth */
  .auth-screen { min-height: 100vh; display: flex; align-items: center; justify-content: center; background: var(--bg); position: relative; overflow: hidden; }
  .auth-bg { position: absolute; inset: 0; background: radial-gradient(ellipse 80% 60% at 60% -10%, rgba(79,127,255,0.12) 0%, transparent 70%), radial-gradient(ellipse 60% 40% at 10% 80%, rgba(124,95,255,0.08) 0%, transparent 60%); pointer-events: none; }
  .auth-card { background: var(--bg2); border: 1px solid var(--border2); border-radius: 20px; padding: 48px 44px; width: 100%; max-width: 460px; position: relative; z-index: 1; box-shadow: 0 32px 80px rgba(0,0,0,0.6); }
  .auth-logo { display: flex; align-items: center; gap: 10px; margin-bottom: 36px; }
  .auth-logo-icon { width: 36px; height: 36px; background: linear-gradient(135deg, var(--accent), var(--accent2)); border-radius: 10px; display: flex; align-items: center; justify-content: center; font-size: 18px; }
  .auth-logo-text { font-family: 'Sora', sans-serif; font-size: 20px; font-weight: 700; letter-spacing: -0.5px; }
  .auth-logo-text span { color: var(--accent); }
  .auth-tabs { display: flex; gap: 4px; background: var(--bg3); border-radius: var(--radius2); padding: 4px; margin-bottom: 32px; }
  .auth-tab { flex: 1; padding: 10px; text-align: center; border-radius: 6px; font-size: 14px; font-weight: 600; cursor: pointer; transition: all 0.2s; color: var(--text2); }
  .auth-tab.active { background: var(--bg4); color: var(--text); }
  .form-group { margin-bottom: 18px; }
  .form-group label { display: block; font-size: 13px; font-weight: 600; color: var(--text2); margin-bottom: 8px; letter-spacing: 0.3px; }
  .form-group input, .form-group select { width: 100%; background: var(--bg3); border: 1px solid var(--border); border-radius: var(--radius2); padding: 12px 16px; color: var(--text); font-family: 'Manrope', sans-serif; font-size: 14px; outline: none; transition: border 0.2s; }
  .form-group input:focus, .form-group select:focus { border-color: var(--accent); }
  .form-group select option { background: var(--bg3); }
  .form-row { display: grid; grid-template-columns: 1fr 1fr; gap: 14px; }
  .btn { display: inline-flex; align-items: center; justify-content: center; gap: 8px; padding: 13px 24px; border-radius: var(--radius2); font-family: 'Manrope', sans-serif; font-size: 14px; font-weight: 700; cursor: pointer; border: none; transition: all 0.2s; letter-spacing: 0.3px; }
  .btn-primary { background: linear-gradient(135deg, var(--accent), var(--accent2)); color: #fff; width: 100%; }
  .btn-primary:hover { opacity: 0.9; transform: translateY(-1px); }
  .btn-secondary { background: var(--bg3); color: var(--text); border: 1px solid var(--border2); }
  .btn-secondary:hover { background: var(--bg4); }
  .btn-danger { background: rgba(255,95,126,0.15); color: var(--red); border: 1px solid rgba(255,95,126,0.3); }
  .btn-success { background: rgba(34,201,138,0.15); color: var(--green); border: 1px solid rgba(34,201,138,0.3); }
  .btn-sm { padding: 7px 14px; font-size: 12px; }
  .error-msg { background: rgba(255,95,126,0.1); border: 1px solid rgba(255,95,126,0.3); border-radius: var(--radius2); padding: 12px 16px; font-size: 13px; color: var(--red); margin-bottom: 16px; }
  .success-msg { background: rgba(34,201,138,0.1); border: 1px solid rgba(34,201,138,0.3); border-radius: var(--radius2); padding: 12px 16px; font-size: 13px; color: var(--green); margin-bottom: 16px; }

  /* Layout */
  .app-layout { display: flex; min-height: 100vh; }
  .sidebar { width: 240px; background: var(--bg2); border-right: 1px solid var(--border); padding: 24px 16px; display: flex; flex-direction: column; position: fixed; top: 0; left: 0; bottom: 0; z-index: 100; transition: transform 0.3s; }
  .sidebar-logo { display: flex; align-items: center; gap: 8px; padding: 0 8px; margin-bottom: 32px; }
  .sidebar-logo-icon { width: 30px; height: 30px; background: linear-gradient(135deg, var(--accent), var(--accent2)); border-radius: 8px; display: flex; align-items: center; justify-content: center; font-size: 14px; }
  .sidebar-logo-text { font-family: 'Sora', sans-serif; font-size: 16px; font-weight: 700; }
  .sidebar-logo-text span { color: var(--accent); }
  .nav-section { margin-bottom: 8px; }
  .nav-label { font-size: 10px; font-weight: 700; color: var(--text3); letter-spacing: 1.2px; text-transform: uppercase; padding: 0 12px; margin-bottom: 6px; margin-top: 16px; }
  .nav-item { display: flex; align-items: center; gap: 10px; padding: 10px 12px; border-radius: var(--radius2); font-size: 14px; font-weight: 500; color: var(--text2); cursor: pointer; transition: all 0.2s; margin-bottom: 2px; }
  .nav-item:hover { background: var(--bg3); color: var(--text); }
  .nav-item.active { background: rgba(79,127,255,0.12); color: var(--accent); }
  .nav-item svg { width: 18px; height: 18px; flex-shrink: 0; }
  .sidebar-bottom { margin-top: auto; padding-top: 16px; border-top: 1px solid var(--border); }
  .user-chip { display: flex; align-items: center; gap: 10px; padding: 10px 12px; border-radius: var(--radius2); cursor: pointer; transition: background 0.2s; }
  .user-chip:hover { background: var(--bg3); }
  .user-avatar { width: 32px; height: 32px; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 13px; font-weight: 700; flex-shrink: 0; }
  .user-name { font-size: 13px; font-weight: 600; }
  .user-role { font-size: 11px; color: var(--text2); }
  .main-content { margin-left: 240px; flex: 1; min-height: 100vh; }
  .topbar { display: flex; align-items: center; justify-content: space-between; padding: 20px 32px; border-bottom: 1px solid var(--border); background: var(--bg); position: sticky; top: 0; z-index: 50; }
  .topbar-title { font-size: 20px; font-weight: 700; }
  .topbar-actions { display: flex; align-items: center; gap: 12px; }
  .page-body { padding: 32px; }

  /* KPI Cards */
  .kpi-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 16px; margin-bottom: 28px; }
  .kpi-card { background: var(--bg2); border: 1px solid var(--border); border-radius: var(--radius); padding: 20px 24px; }
  .kpi-label { font-size: 12px; font-weight: 600; color: var(--text2); letter-spacing: 0.5px; text-transform: uppercase; margin-bottom: 10px; }
  .kpi-value { font-size: 28px; font-weight: 800; font-family: 'Sora', sans-serif; letter-spacing: -1px; }
  .kpi-sub { font-size: 12px; color: var(--text2); margin-top: 4px; }
  .kpi-card.green .kpi-value { color: var(--green); }
  .kpi-card.accent .kpi-value { color: var(--accent); }
  .kpi-card.yellow .kpi-value { color: var(--yellow); }
  .kpi-card.red .kpi-value { color: var(--red); }

  /* Timer Card */
  .timer-card { background: var(--bg2); border: 1px solid var(--border2); border-radius: 20px; padding: 40px; text-align: center; margin-bottom: 28px; position: relative; overflow: hidden; }
  .timer-card::before { content: ''; position: absolute; top: -60px; right: -60px; width: 200px; height: 200px; background: radial-gradient(circle, rgba(79,127,255,0.08) 0%, transparent 70%); border-radius: 50%; }
  .timer-greeting { font-size: 14px; color: var(--text2); margin-bottom: 8px; }
  .timer-name { font-size: 22px; font-weight: 700; margin-bottom: 32px; }
  .timer-display { font-family: 'Sora', sans-serif; font-size: 52px; font-weight: 700; letter-spacing: -2px; color: var(--accent); margin-bottom: 8px; }
  .timer-label { font-size: 13px; color: var(--text2); margin-bottom: 32px; }
  .timer-actions { display: flex; gap: 12px; justify-content: center; flex-wrap: wrap; }
  .timer-btn { padding: 14px 28px; border-radius: 10px; font-size: 14px; font-weight: 700; cursor: pointer; border: none; font-family: 'Manrope', sans-serif; transition: all 0.2s; letter-spacing: 0.3px; }
  .timer-btn:hover { transform: translateY(-2px); }
  .timer-btn.signin { background: linear-gradient(135deg, var(--green), #1aaa72); color: #fff; box-shadow: 0 4px 20px rgba(34,201,138,0.3); }
  .timer-btn.break { background: linear-gradient(135deg, var(--yellow), #d4a820); color: #111; box-shadow: 0 4px 20px rgba(245,200,66,0.3); }
  .timer-btn.end-break { background: linear-gradient(135deg, var(--orange), #e07a20); color: #fff; }
  .timer-btn.signout { background: linear-gradient(135deg, var(--red), #e0405a); color: #fff; box-shadow: 0 4px 20px rgba(255,95,126,0.3); }
  .status-badge { display: inline-flex; align-items: center; gap: 6px; padding: 6px 14px; border-radius: 20px; font-size: 13px; font-weight: 600; }
  .status-badge.active { background: rgba(34,201,138,0.12); color: var(--green); }
  .status-badge.break { background: rgba(245,200,66,0.12); color: var(--yellow); }
  .status-badge.offline { background: rgba(138,144,168,0.12); color: var(--text2); }
  .status-badge.late { background: rgba(255,150,69,0.12); color: var(--orange); }
  .status-dot { width: 7px; height: 7px; border-radius: 50%; background: currentColor; animation: pulse 2s infinite; }
  @keyframes pulse { 0%,100%{opacity:1} 50%{opacity:0.4} }

  /* Table */
  .table-wrapper { background: var(--bg2); border: 1px solid var(--border); border-radius: var(--radius); overflow: hidden; }
  .table-header { display: flex; align-items: center; justify-content: space-between; padding: 20px 24px; border-bottom: 1px solid var(--border); gap: 12px; flex-wrap: wrap; }
  .table-title { font-size: 16px; font-weight: 700; }
  .search-box { background: var(--bg3); border: 1px solid var(--border); border-radius: var(--radius2); padding: 9px 14px; font-family: 'Manrope', sans-serif; font-size: 14px; color: var(--text); outline: none; width: 240px; transition: border 0.2s; }
  .search-box:focus { border-color: var(--accent); }
  .filter-select { background: var(--bg3); border: 1px solid var(--border); border-radius: var(--radius2); padding: 9px 14px; font-family: 'Manrope', sans-serif; font-size: 13px; color: var(--text); outline: none; cursor: pointer; }
  table { width: 100%; border-collapse: collapse; }
  thead th { background: var(--bg3); padding: 12px 16px; text-align: left; font-size: 11px; font-weight: 700; color: var(--text3); letter-spacing: 0.8px; text-transform: uppercase; border-bottom: 1px solid var(--border); white-space: nowrap; }
  tbody tr { border-bottom: 1px solid var(--border); transition: background 0.15s; }
  tbody tr:last-child { border-bottom: none; }
  tbody tr:hover { background: var(--bg3); }
  tbody td { padding: 14px 16px; font-size: 13px; color: var(--text); white-space: nowrap; }
  .badge { display: inline-flex; align-items: center; padding: 4px 10px; border-radius: 6px; font-size: 11px; font-weight: 700; letter-spacing: 0.3px; }
  .badge-present { background: rgba(34,201,138,0.12); color: var(--green); }
  .badge-absent { background: rgba(255,95,126,0.12); color: var(--red); }
  .badge-late { background: rgba(255,150,69,0.12); color: var(--orange); }
  .badge-halfday { background: rgba(245,200,66,0.12); color: var(--yellow); }
  .badge-leave { background: rgba(138,144,168,0.12); color: var(--text2); }
  .pagination { display: flex; align-items: center; justify-content: space-between; padding: 16px 24px; border-top: 1px solid var(--border); font-size: 13px; color: var(--text2); }
  .page-btns { display: flex; gap: 6px; }
  .page-btn { background: var(--bg3); border: 1px solid var(--border); border-radius: 6px; padding: 6px 12px; font-size: 13px; color: var(--text); cursor: pointer; font-family: 'Manrope', sans-serif; }
  .page-btn.active { background: var(--accent); border-color: var(--accent); color: #fff; }
  .page-btn:hover:not(.active) { background: var(--bg4); }

  /* Modal */
  .modal-overlay { position: fixed; inset: 0; background: rgba(0,0,0,0.7); z-index: 1000; display: flex; align-items: center; justify-content: center; padding: 20px; backdrop-filter: blur(4px); }
  .modal { background: var(--bg2); border: 1px solid var(--border2); border-radius: 20px; width: 100%; max-width: 540px; max-height: 90vh; overflow-y: auto; padding: 36px; box-shadow: 0 40px 100px rgba(0,0,0,0.7); animation: modalIn 0.25s ease; }
  .modal.wide { max-width: 700px; }
  @keyframes modalIn { from{opacity:0;transform:scale(0.95) translateY(10px)} to{opacity:1;transform:scale(1) translateY(0)} }
  .modal-header { display: flex; align-items: center; justify-content: space-between; margin-bottom: 28px; }
  .modal-title { font-size: 18px; font-weight: 700; }
  .modal-close { background: var(--bg3); border: 1px solid var(--border); border-radius: 8px; width: 32px; height: 32px; display: flex; align-items: center; justify-content: center; cursor: pointer; font-size: 18px; color: var(--text2); transition: all 0.2s; }
  .modal-close:hover { background: var(--bg4); color: var(--text); }
  .modal-footer { display: flex; gap: 12px; justify-content: flex-end; margin-top: 28px; padding-top: 20px; border-top: 1px solid var(--border); }

  /* Summary Modal */
  .summary-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 14px; margin: 24px 0; }
  .summary-item { background: var(--bg3); border-radius: var(--radius2); padding: 16px; text-align: center; }
  .summary-item-label { font-size: 11px; color: var(--text2); text-transform: uppercase; letter-spacing: 0.5px; margin-bottom: 6px; }
  .summary-item-value { font-size: 22px; font-weight: 800; font-family: 'Sora', sans-serif; }

  /* Toast */
  .toast-container { position: fixed; bottom: 24px; right: 24px; z-index: 9999; display: flex; flex-direction: column; gap: 10px; }
  .toast { background: var(--bg2); border: 1px solid var(--border2); border-radius: var(--radius2); padding: 14px 20px; font-size: 14px; font-weight: 500; box-shadow: var(--shadow); animation: toastIn 0.3s ease; min-width: 280px; display: flex; align-items: center; gap: 10px; }
  .toast.success { border-left: 3px solid var(--green); }
  .toast.error { border-left: 3px solid var(--red); }
  .toast.info { border-left: 3px solid var(--accent); }
  @keyframes toastIn { from{opacity:0;transform:translateX(20px)} to{opacity:1;transform:translateX(0)} }
  @keyframes toastOut { to{opacity:0;transform:translateX(20px)} }

  /* Calendar */
  .calendar-grid { display: grid; grid-template-columns: repeat(7, 1fr); gap: 4px; }
  .cal-day-header { text-align: center; padding: 10px; font-size: 11px; font-weight: 700; color: var(--text3); text-transform: uppercase; letter-spacing: 0.5px; }
  .cal-day { aspect-ratio: 1; border-radius: 8px; display: flex; flex-direction: column; align-items: center; justify-content: center; font-size: 13px; font-weight: 600; cursor: pointer; transition: all 0.15s; border: 1px solid transparent; background: var(--bg3); }
  .cal-day:hover { border-color: var(--border2); }
  .cal-day.empty { background: transparent; cursor: default; }
  .cal-day.today { border-color: var(--accent); color: var(--accent); }
  .cal-day.present { background: rgba(34,201,138,0.15); color: var(--green); }
  .cal-day.absent { background: rgba(255,95,126,0.12); color: var(--red); }
  .cal-day.late { background: rgba(255,150,69,0.12); color: var(--orange); }
  .cal-day.halfday { background: rgba(245,200,66,0.12); color: var(--yellow); }
  .cal-day.leave { background: rgba(138,144,168,0.1); color: var(--text2); }
  .cal-dot { width: 5px; height: 5px; border-radius: 50%; background: currentColor; margin-top: 3px; }
  .cal-nav { display: flex; align-items: center; justify-content: space-between; margin-bottom: 20px; }
  .cal-nav-btn { background: var(--bg3); border: 1px solid var(--border); border-radius: 8px; padding: 8px 14px; cursor: pointer; color: var(--text); font-size: 16px; font-family: 'Manrope', sans-serif; }
  .cal-nav-btn:hover { background: var(--bg4); }
  .cal-month { font-size: 16px; font-weight: 700; }

  /* Charts */
  .chart-bar-container { display: flex; align-items: flex-end; gap: 8px; height: 120px; padding: 0 4px; }
  .chart-bar-wrap { flex: 1; display: flex; flex-direction: column; align-items: center; gap: 6px; height: 100%; justify-content: flex-end; }
  .chart-bar { width: 100%; border-radius: 4px 4px 0 0; background: var(--accent); opacity: 0.8; transition: opacity 0.2s; min-height: 4px; }
  .chart-bar:hover { opacity: 1; }
  .chart-label { font-size: 10px; color: var(--text3); }
  .chart-val { font-size: 10px; color: var(--text2); }

  /* Employee status grid */
  .emp-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(240px, 1fr)); gap: 14px; }
  .emp-card { background: var(--bg2); border: 1px solid var(--border); border-radius: var(--radius); padding: 18px 20px; }
  .emp-card-header { display: flex; align-items: center; gap: 12px; margin-bottom: 12px; }
  .emp-avatar { width: 40px; height: 40px; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 15px; font-weight: 700; flex-shrink: 0; }
  .emp-info h4 { font-size: 14px; font-weight: 700; }
  .emp-info p { font-size: 12px; color: var(--text2); }
  .emp-stats { display: grid; grid-template-columns: 1fr 1fr; gap: 8px; }
  .emp-stat { background: var(--bg3); border-radius: 6px; padding: 8px 10px; }
  .emp-stat-label { font-size: 10px; color: var(--text3); }
  .emp-stat-value { font-size: 14px; font-weight: 700; }
  .divider { height: 1px; background: var(--border); margin: 24px 0; }
  .section-title { font-size: 16px; font-weight: 700; margin-bottom: 16px; }
  .flex { display: flex; }
  .gap-2 { gap: 8px; }
  .gap-3 { gap: 12px; }
  .items-center { align-items: center; }
  .justify-between { justify-content: space-between; }
  .mb-4 { margin-bottom: 16px; }
  .mb-6 { margin-bottom: 24px; }
  .text-sm { font-size: 13px; }
  .text-muted { color: var(--text2); }
  .hidden { display: none !important; }
  .legend { display: flex; gap: 16px; flex-wrap: wrap; }
  .legend-item { display: flex; align-items: center; gap: 6px; font-size: 12px; color: var(--text2); }
  .legend-dot { width: 8px; height: 8px; border-radius: 50%; }
  @media (max-width: 768px) {
    .sidebar { transform: translateX(-100%); }
    .sidebar.open { transform: translateX(0); }
    .main-content { margin-left: 0; }
    .form-row { grid-template-columns: 1fr; }
    .auth-card { padding: 32px 24px; }
    .page-body { padding: 20px 16px; }
    .summary-grid { grid-template-columns: 1fr 1fr; }
    .kpi-grid { grid-template-columns: 1fr 1fr; }
  }
  input[type="date"]::-webkit-calendar-picker-indicator, input[type="time"]::-webkit-calendar-picker-indicator { filter: invert(1); opacity: 0.5; }
  .role-badge { font-size: 11px; font-weight: 700; padding: 3px 8px; border-radius: 5px; }
  .role-admin { background: rgba(124,95,255,0.15); color: var(--accent2); }
  .role-hr { background: rgba(79,127,255,0.15); color: var(--accent); }
  .role-manager { background: rgba(245,200,66,0.15); color: var(--yellow); }
  .role-employee { background: rgba(34,201,138,0.15); color: var(--green); }
  .empty-state { text-align: center; padding: 60px 20px; color: var(--text2); }
  .empty-state svg { width: 48px; height: 48px; margin: 0 auto 16px; opacity: 0.3; }
  .empty-state h3 { font-size: 16px; font-weight: 700; color: var(--text); margin-bottom: 8px; }
  .menu-toggle { display: none; background: var(--bg2); border: 1px solid var(--border); border-radius: 8px; padding: 8px; cursor: pointer; color: var(--text); }
  @media (max-width: 768px) { .menu-toggle { display: flex; } }
</style>
</head>
<body>
<div id="app"></div>
<div class="toast-container" id="toastContainer"></div>

<script>
const App = (() => {
  let state = {
    view: 'auth',
    authTab: 'login',
    currentUser: null,
    page: 'dashboard',
    attendance: {},
    users: {},
    timer: { running: false, breakActive: false, startTime: null, breakStart: null, totalBreak: 0, elapsed: 0 },
    timerInterval: null,
    calMonth: new Date().getMonth(),
    calYear: new Date().getFullYear(),
    attPage: 1,
    attSearch: '',
    attFilter: '',
    empPage: 1,
    modal: null
  };

  function save() {
    localStorage.setItem('wt_users', JSON.stringify(state.users));
    localStorage.setItem('wt_attendance', JSON.stringify(state.attendance));
    if (state.currentUser) localStorage.setItem('wt_session', state.currentUser.id);
  }

  const DATA_VERSION = 'v3';

  function purgeStaleData() {
    const storedVersion = localStorage.getItem('wt_version');
    if (storedVersion !== DATA_VERSION) {
      ['wt_users','wt_attendance','wt_session','wt_timer'].forEach(k => localStorage.removeItem(k));
      localStorage.setItem('wt_version', DATA_VERSION);
    }
  }

  function load() {
    purgeStaleData();
    try {
      const raw = JSON.parse(localStorage.getItem('wt_users') || '{}');
      const DEMO_NAMES = ['arjun sharma','rahul gupta','sneha patel','priya singh','test user'];
      const DEMO_DOMAINS = ['@demo.com','@example.com','@test.com','@fake.com'];
      state.users = {};
      Object.entries(raw).forEach(([id, u]) => {
        const nameLower = (u.name||'').toLowerCase();
        const emailLower = (u.email||'').toLowerCase();
        const isDemo = DEMO_NAMES.some(n => nameLower.includes(n)) ||
                       DEMO_DOMAINS.some(d => emailLower.endsWith(d));
        if (!isDemo) state.users[id] = u;
      });
    } catch(e) { state.users = {}; }
    try { state.attendance = JSON.parse(localStorage.getItem('wt_attendance') || '{}'); } catch(e) { state.attendance = {}; }
    Object.keys(state.attendance).forEach(k => {
      if (!state.users[state.attendance[k].userId]) delete state.attendance[k];
    });
    const sid = localStorage.getItem('wt_session');
    if (sid && state.users[sid]) {
      state.currentUser = state.users[sid];
      state.view = 'app';
      restoreTimer();
    }
  }

  function restoreTimer() {
    const ts = localStorage.getItem('wt_timer');
    if (!ts) return;
    try {
      const t = JSON.parse(ts);
      if (t.userId === state.currentUser?.id && t.running) {
        state.timer = { ...t };
        startTimerInterval();
      }
    } catch(e) {}
  }

  function saveTimer() {
    if (state.timer.running) {
      localStorage.setItem('wt_timer', JSON.stringify({ ...state.timer, userId: state.currentUser.id }));
    } else {
      localStorage.removeItem('wt_timer');
    }
  }

  function genId() { return Date.now().toString(36) + Math.random().toString(36).slice(2, 8); }
  function genEmpId() { return 'EMP' + String(Object.keys(state.users).length + 1).padStart(4, '0'); }
  function today() { return new Date().toISOString().slice(0,10); }
  function fmtTime(ts) { if (!ts) return '--'; const d = new Date(ts); return d.toTimeString().slice(0,5); }
  function fmtDur(ms) { if (!ms || ms < 0) return '00:00'; const h = Math.floor(ms/3600000), m = Math.floor((ms%3600000)/60000); return `${String(h).padStart(2,'0')}h ${String(m).padStart(2,'0')}m`; }
  function fmtTimer(ms) { if (!ms || ms < 0) ms = 0; const h = Math.floor(ms/3600000), m = Math.floor((ms%3600000)/60000), s = Math.floor((ms%60000)/1000); return `${String(h).padStart(2,'0')}:${String(m).padStart(2,'0')}:${String(s).padStart(2,'0')}`; }
  function avatarColor(name) { const c = ['#4f7fff','#7c5fff','#22c98a','#f5c842','#ff5f7e','#ff9645']; let h = 0; for (let i=0;i<name.length;i++) h = name.charCodeAt(i) + ((h<<5)-h); return c[Math.abs(h) % c.length]; }
  function initials(name) { return name.split(' ').map(w=>w[0]).join('').toUpperCase().slice(0,2); }

  function toast(msg, type='info') {
    const c = document.getElementById('toastContainer');
    const t = document.createElement('div');
    t.className = `toast ${type}`;
    t.innerHTML = (type==='success'?'✓':type==='error'?'✗':'ℹ') + ' ' + msg;
    c.appendChild(t);
    setTimeout(() => { t.style.animation = 'toastOut 0.3s forwards'; setTimeout(() => t.remove(), 300); }, 3000);
  }

  function getAttendanceKey(uid, date) { return `${uid}__${date}`; }

  function getUserAttendance(uid) {
    return Object.values(state.attendance).filter(a => a.userId === uid);
  }

  function getTodayAtt(uid) {
    return state.attendance[getAttendanceKey(uid, today())];
  }

  function startTimerInterval() {
    if (state.timerInterval) clearInterval(state.timerInterval);
    state.timerInterval = setInterval(() => {
      const now = Date.now();
      let elapsed = now - state.timer.startTime - state.timer.totalBreak;
      if (state.timer.breakActive && state.timer.breakStart) {
        elapsed -= (now - state.timer.breakStart);
      }
      state.timer.elapsed = elapsed;
      const el = document.getElementById('timerDisplay');
      if (el) el.textContent = fmtTimer(elapsed);
    }, 1000);
  }

  function handleSignIn() {
    const att = getTodayAtt(state.currentUser.id);
    if (att) { toast('Already signed in today', 'info'); return; }
    const now = Date.now();
    state.timer = { running: true, breakActive: false, startTime: now, breakStart: null, totalBreak: 0, elapsed: 0 };
    const key = getAttendanceKey(state.currentUser.id, today());
    const signInDate = new Date();
    const hour = signInDate.getHours();
    const isLate = hour >= 10; // after 10am = late
    state.attendance[key] = {
      id: genId(), userId: state.currentUser.id, date: today(),
      signIn: now, signOut: null, breakTime: 0, status: isLate ? 'late' : 'present', notes: ''
    };
    save(); saveTimer(); startTimerInterval(); render();
    toast(`Welcome, ${state.currentUser.name.split(' ')[0]}! Have a great day.`, 'success');
  }

  function handleBreakStart() {
    if (!state.timer.running || state.timer.breakActive) return;
    state.timer.breakActive = true;
    state.timer.breakStart = Date.now();
    saveTimer(); render();
    toast('Break started. Take it easy!', 'info');
  }

  function handleBreakEnd() {
    if (!state.timer.breakActive) return;
    const dur = Date.now() - state.timer.breakStart;
    state.timer.totalBreak += dur;
    state.timer.breakActive = false;
    state.timer.breakStart = null;
    const att = getTodayAtt(state.currentUser.id);
    if (att) att.breakTime = state.timer.totalBreak;
    save(); saveTimer(); render();
    toast(`Break ended. Back to it! (${fmtDur(dur)})`, 'info');
  }

  function handleSignOut() {
    if (!state.timer.running) return;
    if (state.timer.breakActive) handleBreakEnd();
    const now = Date.now();
    const totalWork = now - state.timer.startTime;
    const productive = totalWork - state.timer.totalBreak;
    const att = getTodayAtt(state.currentUser.id);
    if (att) { att.signOut = now; att.breakTime = state.timer.totalBreak; }
    if (state.timerInterval) clearInterval(state.timerInterval);
    state.timerInterval = null;
    const oldTimer = { ...state.timer };
    state.timer = { running: false, breakActive: false, startTime: null, breakStart: null, totalBreak: 0, elapsed: 0 };
    save(); localStorage.removeItem('wt_timer');
    showSummaryModal(totalWork, oldTimer.totalBreak, productive, att);
    render();
  }

  function showSummaryModal(work, brk, prod, att) {
    state.modal = {
      type: 'summary',
      work, brk, prod, att
    };
    render();
  }

  function canManage() {
    return state.currentUser && ['admin','hr','manager'].includes(state.currentUser.role);
  }
  function isAdmin() { return state.currentUser?.role === 'admin'; }

  function render() {
    const app = document.getElementById('app');
    if (state.view === 'auth') { app.innerHTML = renderAuth(); bindAuth(); return; }
    app.innerHTML = renderLayout();
    bindLayout();
    if (state.modal) renderModal();
  }

  function renderAuth() {
    return `
    <div class="auth-screen">
      <div class="auth-bg"></div>
      <div class="auth-card">
        <div class="auth-logo">
          <div class="auth-logo-icon">⏱</div>
          <div class="auth-logo-text">Work<span>Track</span></div>
        </div>
        <div class="auth-tabs">
          <div class="auth-tab ${state.authTab==='login'?'active':''}" onclick="App.setAuthTab('login')">Sign In</div>
          <div class="auth-tab ${state.authTab==='register'?'active':''}" onclick="App.setAuthTab('register')">Create Account</div>
        </div>
        <div id="authMsg"></div>
        ${state.authTab === 'login' ? renderLogin() : renderRegister()}
      </div>
    </div>`;
  }

  function renderLogin() {
    return `
    <div class="form-group"><label>Email Address</label><input type="email" id="loginEmail" placeholder="you@company.com"></div>
    <div class="form-group"><label>Password</label><input type="password" id="loginPass" placeholder="••••••••"></div>
    <button class="btn btn-primary" onclick="App.login()" style="margin-top:8px">Sign In →</button>
    <p style="text-align:center;margin-top:16px;font-size:13px;color:var(--text2)">
      No account? <span onclick="App.setAuthTab('register')" style="color:var(--accent);cursor:pointer;font-weight:600">Create one</span>
    </p>`;
  }

  function renderRegister() {
    return `
    <div class="form-row">
      <div class="form-group"><label>Full Name</label><input type="text" id="regName" placeholder="Jane Smith"></div>
      <div class="form-group"><label>Department</label><input type="text" id="regDept" placeholder="Engineering"></div>
    </div>
    <div class="form-group"><label>Email Address</label><input type="email" id="regEmail" placeholder="you@company.com"></div>
    <div class="form-row">
      <div class="form-group"><label>Password</label><input type="password" id="regPass" placeholder="Min 6 characters"></div>
      <div class="form-group"><label>Confirm Password</label><input type="password" id="regPass2" placeholder="Repeat password"></div>
    </div>
    <div class="form-group"><label>Role</label>
      <select id="regRole">
        <option value="employee">Employee</option>
        <option value="hr">HR</option>
        <option value="manager">Manager</option>
        <option value="admin">Admin</option>
      </select>
    </div>
    <button class="btn btn-primary" onclick="App.register()" style="margin-top:8px">Create Account →</button>
    <p style="text-align:center;margin-top:16px;font-size:13px;color:var(--text2)">
      Already have an account? <span onclick="App.setAuthTab('login')" style="color:var(--accent);cursor:pointer;font-weight:600">Sign in</span>
    </p>`;
  }

  function bindAuth() {}

  function login() {
    const email = document.getElementById('loginEmail')?.value.trim();
    const pass = document.getElementById('loginPass')?.value;
    const msg = document.getElementById('authMsg');
    if (!email || !pass) { msg.innerHTML = '<div class="error-msg">Please fill in all fields.</div>'; return; }
    const user = Object.values(state.users).find(u => u.email === email);
    if (!user || user.password !== btoa(pass)) { msg.innerHTML = '<div class="error-msg">Incorrect email or password.</div>'; return; }
    state.currentUser = user; state.view = 'app'; state.page = 'dashboard';
    localStorage.setItem('wt_session', user.id);
    restoreTimer();
    render(); toast(`Welcome back, ${user.name.split(' ')[0]}!`, 'success');
  }

  function register() {
    const name = document.getElementById('regName')?.value.trim();
    const email = document.getElementById('regEmail')?.value.trim();
    const dept = document.getElementById('regDept')?.value.trim();
    const pass = document.getElementById('regPass')?.value;
    const pass2 = document.getElementById('regPass2')?.value;
    const role = document.getElementById('regRole')?.value;
    const msg = document.getElementById('authMsg');
    if (!name || !email || !dept || !pass || !pass2) { msg.innerHTML = '<div class="error-msg">Please fill in all fields.</div>'; return; }
    if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email)) { msg.innerHTML = '<div class="error-msg">Invalid email address.</div>'; return; }
    if (pass.length < 6) { msg.innerHTML = '<div class="error-msg">Password must be at least 6 characters.</div>'; return; }
    if (pass !== pass2) { msg.innerHTML = '<div class="error-msg">Passwords do not match.</div>'; return; }
    if (Object.values(state.users).find(u => u.email === email)) { msg.innerHTML = '<div class="error-msg">An account with this email already exists.</div>'; return; }
    const id = genId();
    const user = { id, name, email, password: btoa(pass), role, department: dept, employeeId: genEmpId(), createdAt: Date.now() };
    state.users[id] = user; save();
    msg.innerHTML = '<div class="success-msg">Account created! Please sign in.</div>';
    setTimeout(() => { state.authTab = 'login'; render(); }, 1200);
  }

  function logout() {
    if (state.timerInterval) clearInterval(state.timerInterval);
    state.currentUser = null; state.view = 'auth'; state.timer = { running: false, breakActive: false, startTime: null, breakStart: null, totalBreak: 0, elapsed: 0 };
    localStorage.removeItem('wt_session'); localStorage.removeItem('wt_timer');
    render();
  }

  function setAuthTab(t) { state.authTab = t; render(); }
  function setPage(p) { state.page = p; state.attPage = 1; state.attSearch = ''; state.attFilter = ''; render(); }

  function renderLayout() {
    return `
    <div class="app-layout">
      ${renderSidebar()}
      <div class="main-content">
        ${renderTopbar()}
        <div class="page-body">${renderPage()}</div>
      </div>
    </div>`;
  }

  function renderSidebar() {
    const u = state.currentUser;
    const nav = (id, icon, label) => `
      <div class="nav-item ${state.page===id?'active':''}" onclick="App.setPage('${id}')">
        ${icon}<span>${label}</span>
      </div>`;
    const ic = {
      dashboard: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><rect x="3" y="3" width="7" height="7" rx="1"/><rect x="14" y="3" width="7" height="7" rx="1"/><rect x="3" y="14" width="7" height="7" rx="1"/><rect x="14" y="14" width="7" height="7" rx="1"/></svg>',
      attendance: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><rect x="3" y="4" width="18" height="18" rx="2"/><line x1="16" y1="2" x2="16" y2="6"/><line x1="8" y1="2" x2="8" y2="6"/><line x1="3" y1="10" x2="21" y2="10"/></svg>',
      calendar: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><rect x="3" y="4" width="18" height="18" rx="2"/><line x1="16" y1="2" x2="16" y2="6"/><line x1="8" y1="2" x2="8" y2="6"/><line x1="3" y1="10" x2="21" y2="10"/><line x1="8" y1="14" x2="8" y2="14"/><line x1="12" y1="14" x2="12" y2="14"/><line x1="16" y1="14" x2="16" y2="14"/></svg>',
      employees: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M17 21v-2a4 4 0 0 0-4-4H5a4 4 0 0 0-4 4v2"/><circle cx="9" cy="7" r="4"/><path d="M23 21v-2a4 4 0 0 0-3-3.87"/><path d="M16 3.13a4 4 0 0 1 0 7.75"/></svg>',
      reports: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/><polyline points="14 2 14 8 20 8"/><line x1="16" y1="13" x2="8" y2="13"/><line x1="16" y1="17" x2="8" y2="17"/><polyline points="10 9 9 9 8 9"/></svg>',
      analytics: '<svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><line x1="18" y1="20" x2="18" y2="10"/><line x1="12" y1="20" x2="12" y2="4"/><line x1="6" y1="20" x2="6" y2="14"/></svg>'
    };
    return `
    <div class="sidebar" id="sidebar">
      <div class="sidebar-logo">
        <div class="sidebar-logo-icon">⏱</div>
        <div class="sidebar-logo-text">Work<span>Track</span></div>
      </div>
      <div class="nav-label">Main</div>
      ${nav('dashboard', ic.dashboard, 'Dashboard')}
      ${nav('attendance', ic.attendance, 'Attendance')}
      ${nav('calendar', ic.calendar, 'My Calendar')}
      ${canManage() ? `
        <div class="nav-label">Management</div>
        ${nav('employees', ic.employees, 'Employees')}
        ${nav('reports', ic.reports, 'Reports')}
        ${nav('analytics', ic.analytics, 'Analytics')}
      ` : ''}
      <div class="sidebar-bottom">
        <div class="user-chip" onclick="App.setPage('profile')">
          <div class="user-avatar" style="background:${avatarColor(u.name)};color:#fff">${initials(u.name)}</div>
          <div>
            <div class="user-name">${u.name.split(' ')[0]}</div>
            <div class="user-role">${u.role.charAt(0).toUpperCase()+u.role.slice(1)} · ${u.department}</div>
          </div>
        </div>
        <div class="nav-item" onclick="App.logout()" style="margin-top:4px;color:var(--red)">
          <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" style="width:18px;height:18px"><path d="M9 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h4"/><polyline points="16 17 21 12 16 7"/><line x1="21" y1="12" x2="9" y2="12"/></svg>
          Sign Out
        </div>
      </div>
    </div>`;
  }

  function renderTopbar() {
    const titles = { dashboard:'Dashboard', attendance:'Attendance Log', calendar:'My Calendar', employees:'Employees', reports:'Reports', analytics:'Analytics', profile:'My Profile' };
    return `
    <div class="topbar">
      <div style="display:flex;align-items:center;gap:12px">
        <button class="menu-toggle" onclick="document.getElementById('sidebar').classList.toggle('open')">☰</button>
        <div class="topbar-title">${titles[state.page]||'Dashboard'}</div>
      </div>
      <div class="topbar-actions">
        <div class="status-badge ${state.timer.running?(state.timer.breakActive?'break':'active'):'offline'}">
          <span class="status-dot"></span>
          ${state.timer.running?(state.timer.breakActive?'On Break':'Working'):'Offline'}
        </div>
        <button class="btn btn-secondary btn-sm" onclick="App.openManualAttModal()">+ ${canManage() ? 'Manual Entry' : 'Add Past Day'}</button>
      </div>
    </div>`;
  }

  function renderPage() {
    switch(state.page) {
      case 'dashboard': return renderDashboard();
      case 'attendance': return renderAttendancePage();
      case 'calendar': return renderCalendarPage();
      case 'employees': return renderEmployeesPage();
      case 'reports': return renderReportsPage();
      case 'analytics': return renderAnalyticsPage();
      case 'profile': return renderProfilePage();
      default: return renderDashboard();
    }
  }

  function renderDashboard() {
    const u = state.currentUser;
    const userAtt = getUserAttendance(u.id);
    const todayAtt = getTodayAtt(u.id);
    const thisMonth = userAtt.filter(a => a.date.startsWith(new Date().toISOString().slice(0,7)));
    const presentDays = thisMonth.filter(a => ['present','late'].includes(a.status)).length;
    const totalHours = thisMonth.reduce((s,a) => s + (a.signOut ? (a.signOut - a.signIn - a.breakTime) : 0), 0);
    const avgHours = presentDays ? (totalHours / presentDays / 3600000).toFixed(1) : '0';
    const now = new Date();
    const hour = now.getHours();
    const greeting = hour < 12 ? 'Good morning' : hour < 17 ? 'Good afternoon' : 'Good evening';
    const elapsed = state.timer.elapsed;

    return `
    <div class="timer-card">
      <div class="timer-greeting">${greeting},</div>
      <div class="timer-name">${u.name} · <span style="color:var(--text2);font-size:16px;font-weight:500">${u.employeeId}</span></div>
      <div class="timer-display" id="timerDisplay">${state.timer.running ? fmtTimer(elapsed) : '00:00:00'}</div>
      <div class="timer-label">${todayAtt ? (state.timer.running ? (state.timer.breakActive ? '☕ Break Active' : '🟢 Working · since ' + fmtTime(todayAtt.signIn)) : '✓ Signed out for today') : 'Not signed in yet'}</div>
      <div class="timer-actions">
        ${!todayAtt && !state.timer.running ? `<button class="timer-btn signin" onclick="App.handleSignIn()">▶ Sign In</button>` : ''}
        ${state.timer.running && !state.timer.breakActive ? `<button class="timer-btn break" onclick="App.handleBreakStart()">☕ Start Break</button>` : ''}
        ${state.timer.breakActive ? `<button class="timer-btn end-break" onclick="App.handleBreakEnd()">▷ End Break</button>` : ''}
        ${state.timer.running ? `<button class="timer-btn signout" onclick="App.handleSignOut()">⏹ Sign Out</button>` : ''}
      </div>
    </div>
    <div class="kpi-grid">
      <div class="kpi-card green">
        <div class="kpi-label">Present This Month</div>
        <div class="kpi-value">${presentDays}</div>
        <div class="kpi-sub">days</div>
      </div>
      <div class="kpi-card accent">
        <div class="kpi-label">Avg Daily Hours</div>
        <div class="kpi-value">${avgHours}</div>
        <div class="kpi-sub">hours / day</div>
      </div>
      <div class="kpi-card yellow">
        <div class="kpi-label">Total Break This Month</div>
        <div class="kpi-value">${fmtDur(thisMonth.reduce((s,a) => s+(a.breakTime||0), 0))}</div>
        <div class="kpi-sub">accumulated</div>
      </div>
      <div class="kpi-card">
        <div class="kpi-label">Productive Hours</div>
        <div class="kpi-value" style="color:var(--text)">${fmtDur(totalHours)}</div>
        <div class="kpi-sub">this month</div>
      </div>
    </div>
    ${canManage() ? renderLiveStatus() : renderMissingDaysPrompt(u.id)}
    ${renderRecentAttendance(u.id)}`;
  }

  function renderLiveStatus() {
    const all = Object.values(state.users);
    return `
    <div class="section-title">Live Employee Status</div>
    <div class="emp-grid mb-6" style="margin-bottom:28px">
      ${all.map(u => {
        const att = getTodayAtt(u.id);
        const status = att ? (att.signOut ? 'offline' : 'active') : 'offline';
        const breakPerhaps = att && !att.signOut ? 'active' : 'offline';
        return `<div class="emp-card">
          <div class="emp-card-header">
            <div class="user-avatar" style="background:${avatarColor(u.name)};color:#fff">${initials(u.name)}</div>
            <div class="emp-info">
              <h4>${u.name}</h4>
              <p>${u.department} · ${u.employeeId}</p>
            </div>
          </div>
          <div class="emp-stats">
            <div class="emp-stat">
              <div class="emp-stat-label">Status</div>
              <div class="emp-stat-value" style="color:${att&&!att.signOut?'var(--green)':'var(--text2)'}">
                ${att ? (att.signOut ? 'Done' : 'Active') : 'Offline'}
              </div>
            </div>
            <div class="emp-stat">
              <div class="emp-stat-label">Sign In</div>
              <div class="emp-stat-value">${att ? fmtTime(att.signIn) : '--'}</div>
            </div>
          </div>
        </div>`;
      }).join('')}
    </div>`;
  }

  function renderMissingDaysPrompt(uid) {
    const now = new Date();
    const year = now.getFullYear();
    const month = now.getMonth();
    const todayDay = now.getDate();
    const attMap = {};
    getUserAttendance(uid).forEach(a => { attMap[a.date] = a; });
    const missingDays = [];
    for (let d = 1; d < todayDay; d++) {
      const dateStr = `${year}-${String(month+1).padStart(2,'0')}-${String(d).padStart(2,'0')}`;
      const dow = new Date(dateStr + 'T00:00:00').getDay();
      if (dow === 0 || dow === 6) continue; // skip weekends
      if (!attMap[dateStr]) missingDays.push(dateStr);
    }
    if (!missingDays.length) return `<div style="background:rgba(34,201,138,0.08);border:1px solid rgba(34,201,138,0.2);border-radius:var(--radius);padding:16px 20px;margin-bottom:24px;display:flex;align-items:center;gap:12px;font-size:14px">
      <span style="font-size:20px">✅</span>
      <span style="color:var(--green);font-weight:600">All working days this month are logged!</span>
    </div>`;
    return `
    <div style="background:rgba(245,200,66,0.06);border:1px solid rgba(245,200,66,0.2);border-radius:var(--radius);padding:20px 24px;margin-bottom:24px">
      <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:14px;flex-wrap:wrap;gap:10px">
        <div>
          <div style="font-size:15px;font-weight:700;color:var(--yellow)">⚠️ ${missingDays.length} Working Day${missingDays.length>1?'s':''} Not Logged</div>
          <div style="font-size:12px;color:var(--text2);margin-top:3px">Click any date below to add your attendance for that day</div>
        </div>
        <button class="btn btn-secondary btn-sm" onclick="App.setPage('calendar')">View Calendar →</button>
      </div>
      <div style="display:flex;flex-wrap:wrap;gap:8px">
        ${missingDays.slice(0,14).map(dateStr => {
          const dt = new Date(dateStr + 'T00:00:00');
          const label = dt.toLocaleDateString('en-US', { month:'short', day:'numeric' });
          const dow = dt.toLocaleDateString('en-US', { weekday:'short' });
          return `<button onclick="App.openManualAttModal('${dateStr}')" style="background:var(--bg3);border:1px solid rgba(245,200,66,0.25);border-radius:8px;padding:8px 12px;cursor:pointer;font-family:'Manrope',sans-serif;text-align:center;transition:all 0.15s;color:var(--text)" onmouseover="this.style.borderColor='var(--yellow)'" onmouseout="this.style.borderColor='rgba(245,200,66,0.25)'">
            <div style="font-size:10px;color:var(--text3);text-transform:uppercase;letter-spacing:0.5px">${dow}</div>
            <div style="font-size:13px;font-weight:700">${label}</div>
          </button>`;
        }).join('')}
        ${missingDays.length > 14 ? `<div style="display:flex;align-items:center;padding:8px 12px;font-size:12px;color:var(--text2)">+${missingDays.length-14} more</div>` : ''}
      </div>
    </div>`;
  }

  function renderRecentAttendance(uid) {
    const records = getUserAttendance(uid).sort((a,b) => b.date.localeCompare(a.date)).slice(0,5);
    if (!records.length) return `<div class="empty-state"><h3>No attendance records yet</h3><p>Sign in to start tracking your hours</p></div>`;
    return `
    <div class="section-title">Recent Attendance</div>
    <div class="table-wrapper">
      <table>
        <thead><tr><th>Date</th><th>Sign In</th><th>Sign Out</th><th>Work Hours</th><th>Break</th><th>Status</th></tr></thead>
        <tbody>${records.map(a => `
          <tr>
            <td>${formatDate(a.date)}</td>
            <td>${fmtTime(a.signIn)}</td>
            <td>${a.signOut ? fmtTime(a.signOut) : '<span style="color:var(--green)">Active</span>'}</td>
            <td>${a.signOut ? fmtDur(a.signOut - a.signIn - (a.breakTime||0)) : '—'}</td>
            <td>${fmtDur(a.breakTime||0)}</td>
            <td>${statusBadge(a.status)}</td>
          </tr>`).join('')}
        </tbody>
      </table>
    </div>`;
  }

  function formatDate(d) {
    const dt = new Date(d + 'T00:00:00');
    return dt.toLocaleDateString('en-US', { weekday:'short', month:'short', day:'numeric' });
  }

  function statusBadge(s) {
    const map = { present:'badge-present', absent:'badge-absent', late:'badge-late', halfday:'badge-halfday', leave:'badge-leave' };
    return `<span class="badge ${map[s]||'badge-leave'}">${s.charAt(0).toUpperCase()+s.slice(1)}</span>`;
  }

  function renderAttendancePage() {
    const uid = state.currentUser.id;
    const isAdminOrHR = canManage();
    let records = isAdminOrHR
      ? Object.values(state.attendance)
      : getUserAttendance(uid);
    if (state.attSearch) {
      const q = state.attSearch.toLowerCase();
      records = records.filter(a => {
        const u = state.users[a.userId];
        return (u?.name||'').toLowerCase().includes(q) || a.date.includes(q);
      });
    }
    if (state.attFilter) records = records.filter(a => a.status === state.attFilter);
    records.sort((a,b) => b.date.localeCompare(a.date));
    const perPage = 10; const total = records.length;
    const pages = Math.ceil(total / perPage);
    const slice = records.slice((state.attPage-1)*perPage, state.attPage*perPage);
    return `
    <div class="table-wrapper">
      <div class="table-header">
        <div class="table-title">Attendance Records <span style="color:var(--text2);font-size:13px;font-weight:400">(${total})</span></div>
        <div style="display:flex;gap:10px;flex-wrap:wrap;align-items:center">
          <input class="search-box" placeholder="🔍 Search..." value="${state.attSearch}" oninput="App.setAttSearch(this.value)">
          <select class="filter-select" onchange="App.setAttFilter(this.value)">
            <option value="" ${!state.attFilter?'selected':''}>All Status</option>
            <option value="present" ${state.attFilter==='present'?'selected':''}>Present</option>
            <option value="absent" ${state.attFilter==='absent'?'selected':''}>Absent</option>
            <option value="late" ${state.attFilter==='late'?'selected':''}>Late</option>
            <option value="halfday" ${state.attFilter==='halfday'?'selected':''}>Half Day</option>
            <option value="leave" ${state.attFilter==='leave'?'selected':''}>Leave</option>
          </select>
          <button class="btn btn-secondary btn-sm" onclick="App.openManualAttModal()">+ Add Past Day</button>
        </div>
      </div>
      ${slice.length === 0 ? `<div class="empty-state" style="padding:40px"><h3>No records found</h3></div>` : `
      <div style="overflow-x:auto">
      <table>
        <thead><tr>
          ${isAdminOrHR ? '<th>Employee</th>' : ''}
          <th>Date</th><th>Sign In</th><th>Sign Out</th><th>Work Hours</th><th>Break</th><th>Productive</th><th>Status</th><th>Notes</th>
          <th>Actions</th>
        </tr></thead>
        <tbody>${slice.map(a => {
          const u2 = state.users[a.userId];
          const workMs = a.signOut ? (a.signOut - a.signIn - (a.breakTime||0)) : 0;
          const canEdit = canManage() || a.userId === state.currentUser.id;
          return `<tr>
            ${isAdminOrHR ? `<td><div style="display:flex;align-items:center;gap:8px"><div class="user-avatar" style="background:${avatarColor(u2?.name||'?')};color:#fff;width:28px;height:28px;font-size:11px">${initials(u2?.name||'?')}</div>${u2?.name||'Unknown'}</div></td>` : ''}
            <td>${formatDate(a.date)}</td>
            <td>${fmtTime(a.signIn)}</td>
            <td>${a.signOut ? fmtTime(a.signOut) : '<span style="color:var(--green);font-size:12px">Active</span>'}</td>
            <td>${a.signOut ? fmtDur(workMs) : '—'}</td>
            <td>${fmtDur(a.breakTime||0)}</td>
            <td>${a.signOut ? fmtDur(workMs) : '—'}</td>
            <td>${statusBadge(a.status)}</td>
            <td style="max-width:120px;overflow:hidden;text-overflow:ellipsis;color:var(--text2)">${a.notes||''}</td>
            <td>${canEdit ? `<div style="display:flex;gap:6px"><button class="btn btn-sm btn-secondary" onclick="App.editAttModal('${a.id}')">Edit</button><button class="btn btn-sm btn-danger" onclick="App.deleteAtt('${a.id}')">Del</button></div>` : '—'}</td>
          </tr>`;
        }).join('')}</tbody>
      </table></div>
      `}
      <div class="pagination">
        <span>Showing ${Math.min((state.attPage-1)*perPage+1,total)}–${Math.min(state.attPage*perPage,total)} of ${total}</span>
        <div class="page-btns">
          <button class="page-btn" onclick="App.attChangePage(${state.attPage-1})" ${state.attPage<=1?'disabled':''}>‹</button>
          ${Array.from({length:Math.min(pages,5)},(_,i)=>i+1).map(p=>`<button class="page-btn ${p===state.attPage?'active':''}" onclick="App.attChangePage(${p})">${p}</button>`).join('')}
          <button class="page-btn" onclick="App.attChangePage(${state.attPage+1})" ${state.attPage>=pages?'disabled':''}>›</button>
        </div>
      </div>
    </div>`;
  }

  function setAttSearch(v) { state.attSearch = v; state.attPage = 1; render(); }
  function setAttFilter(v) { state.attFilter = v; state.attPage = 1; render(); }
  function attChangePage(p) { const max=Math.ceil((canManage()?Object.values(state.attendance).length:getUserAttendance(state.currentUser.id).length)/10); if(p<1||p>max)return; state.attPage=p; render(); }

  function renderCalendarPage() {
    const uid = state.currentUser.id;
    const userAtt = getUserAttendance(uid);
    const attMap = {};
    userAtt.forEach(a => { attMap[a.date] = a; });
    const firstDay = new Date(state.calYear, state.calMonth, 1).getDay();
    const daysInMonth = new Date(state.calYear, state.calMonth+1, 0).getDate();
    const monthName = new Date(state.calYear, state.calMonth, 1).toLocaleDateString('en-US', {month:'long',year:'numeric'});
    const todayStr = today();
    const days = ['Sun','Mon','Tue','Wed','Thu','Fri','Sat'];
    let cells = '';
    for (let i=0;i<firstDay;i++) cells += '<div class="cal-day empty"></div>';
    for (let d=1;d<=daysInMonth;d++) {
      const dateStr = `${state.calYear}-${String(state.calMonth+1).padStart(2,'0')}-${String(d).padStart(2,'0')}`;
      const a = attMap[dateStr];
      const isToday = dateStr === todayStr;
      const isPast = dateStr <= todayStr;
      let cls = 'cal-day';
      if (isToday) cls += ' today';
      if (a) cls += ' ' + a.status;
      if (!isPast) cls += ' future';
      cells += `<div class="${cls}" title="${isPast?(a?'Edit: '+dateStr:'Add: '+dateStr):dateStr}" onclick="${isPast?`App.calDayClick('${dateStr}')`:''}" style="${isPast?'cursor:pointer':'cursor:default;opacity:0.35'}">${d}${a?'<div class="cal-dot"></div>':''}</div>`;
    }
    return `
    <div style="max-width:700px">
      <div class="cal-nav">
        <button class="cal-nav-btn" onclick="App.calNav(-1)">‹</button>
        <div class="cal-month">${monthName}</div>
        <button class="cal-nav-btn" onclick="App.calNav(1)">›</button>
      </div>
      <div class="legend mb-4">
        ${[['present','var(--green)'],['late','var(--orange)'],['absent','var(--red)'],['halfday','var(--yellow)'],['leave','var(--text2)']].map(([s,c])=>`<div class="legend-item"><div class="legend-dot" style="background:${c}"></div>${s}</div>`).join('')}
      </div>
      <div class="calendar-grid">
        ${days.map(d=>`<div class="cal-day-header">${d}</div>`).join('')}
        ${cells}
      </div>
      <div style="margin-top:20px;display:flex;align-items:center;gap:12px;flex-wrap:wrap">
        <button class="btn btn-secondary" onclick="App.openManualAttModal()">+ Add Past Day</button>
        <span style="font-size:12px;color:var(--text3)">Click any past date on the calendar to add or edit attendance</span>
      </div>
    </div>`;
  }

  function calNav(dir) {
    state.calMonth += dir;
    if (state.calMonth > 11) { state.calMonth = 0; state.calYear++; }
    if (state.calMonth < 0) { state.calMonth = 11; state.calYear--; }
    render();
  }

  function calDayClick(dateStr) {
    if (dateStr > today()) return; // block future dates for everyone
    openManualAttModal(dateStr);
  }

  function renderEmployeesPage() {
    const users = Object.values(state.users);
    const perPage = 10;
    const slice = users.slice((state.empPage-1)*perPage, state.empPage*perPage);
    return `
    <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:20px;flex-wrap:wrap;gap:12px">
      <div class="section-title" style="margin:0">All Employees (${users.length})</div>
      ${isAdmin() ? `<button class="btn btn-primary btn-sm" onclick="App.openAddEmpModal()">+ Add Employee</button>` : ''}
    </div>
    <div class="table-wrapper">
      <div style="overflow-x:auto">
      <table>
        <thead><tr><th>Employee</th><th>ID</th><th>Department</th><th>Role</th><th>Today</th>${isAdmin()?'<th>Actions</th>':''}</tr></thead>
        <tbody>${slice.map(u => {
          const att = getTodayAtt(u.id);
          return `<tr>
            <td><div style="display:flex;align-items:center;gap:10px">
              <div class="user-avatar" style="background:${avatarColor(u.name)};color:#fff;width:34px;height:34px;font-size:13px">${initials(u.name)}</div>
              <div><div style="font-weight:600">${u.name}</div><div style="font-size:12px;color:var(--text2)">${u.email}</div></div>
            </div></td>
            <td style="color:var(--text2)">${u.employeeId}</td>
            <td>${u.department}</td>
            <td><span class="role-badge role-${u.role}">${u.role}</span></td>
            <td>${att ? statusBadge(att.status) : '<span style="color:var(--text3);font-size:12px">Absent</span>'}</td>
            ${isAdmin() ? `<td><div style="display:flex;gap:6px">
              <button class="btn btn-sm btn-secondary" onclick="App.editEmpModal('${u.id}')">Edit</button>
              ${u.id !== state.currentUser.id ? `<button class="btn btn-sm btn-danger" onclick="App.deleteEmp('${u.id}')">Del</button>` : ''}
            </div></td>` : ''}
          </tr>`;
        }).join('')}</tbody>
      </table></div>
    </div>`;
  }

  function renderReportsPage() {
    return `
    <div class="kpi-grid mb-6">
      ${[['Total Employees', Object.keys(state.users).length, 'accent'],
         ['Records Today', Object.values(state.attendance).filter(a=>a.date===today()).length, 'green'],
         ['This Month', Object.values(state.attendance).filter(a=>a.date.startsWith(new Date().toISOString().slice(0,7))).length, 'yellow'],
         ['Total Records', Object.keys(state.attendance).length, '']].map(([l,v,c])=>`
        <div class="kpi-card ${c}"><div class="kpi-label">${l}</div><div class="kpi-value">${v}</div></div>`).join('')}
    </div>
    <div class="table-wrapper" style="margin-bottom:24px">
      <div class="table-header">
        <div class="table-title">Export Reports</div>
      </div>
      <div style="padding:24px;display:flex;gap:12px;flex-wrap:wrap">
        <button class="btn btn-secondary" onclick="App.exportCSV('all')">⬇ Export All CSV</button>
        <button class="btn btn-secondary" onclick="App.exportCSV('month')">⬇ This Month CSV</button>
        <button class="btn btn-secondary" onclick="App.exportPDF()">⬇ Export PDF Report</button>
      </div>
    </div>
    ${renderAttendancePage()}`;
  }

  function renderAnalyticsPage() {
    const allAtt = Object.values(state.attendance);
    const month = new Date().toISOString().slice(0,7);
    const monthAtt = allAtt.filter(a => a.date.startsWith(month));
    const statusCounts = { present:0, absent:0, late:0, halfday:0, leave:0 };
    monthAtt.forEach(a => { statusCounts[a.status] = (statusCounts[a.status]||0)+1; });
    const users = Object.values(state.users);
    const topUsers = users.map(u => {
      const att = getUserAttendance(u.id).filter(a=>a.date.startsWith(month));
      const hours = att.reduce((s,a) => s + (a.signOut ? (a.signOut-a.signIn-(a.breakTime||0))/3600000 : 0), 0);
      return { name: u.name, hours };
    }).sort((a,b) => b.hours-a.hours).slice(0,6);
    const maxH = Math.max(...topUsers.map(u=>u.hours), 1);
    const last7 = Array.from({length:7},(_,i)=>{
      const d = new Date(); d.setDate(d.getDate()-i);
      const ds = d.toISOString().slice(0,10);
      const recs = allAtt.filter(a=>a.date===ds);
      return { date: d.toLocaleDateString('en-US',{weekday:'short'}), count: recs.filter(a=>['present','late'].includes(a.status)).length };
    }).reverse();
    const maxC = Math.max(...last7.map(d=>d.count), 1);
    return `
    <div class="kpi-grid mb-6">
      ${Object.entries(statusCounts).map(([s,v])=>`<div class="kpi-card"><div class="kpi-label">${s}</div><div class="kpi-value" style="color:var(--text)">${v}</div><div class="kpi-sub">this month</div></div>`).join('')}
    </div>
    <div style="display:grid;grid-template-columns:1fr 1fr;gap:20px;margin-bottom:24px;flex-wrap:wrap">
      <div class="table-wrapper" style="padding:20px">
        <div class="table-title" style="margin-bottom:16px">Daily Attendance (Last 7 Days)</div>
        <div class="chart-bar-container">
          ${last7.map(d=>`<div class="chart-bar-wrap">
            <div class="chart-val">${d.count}</div>
            <div class="chart-bar" style="height:${Math.round((d.count/maxC)*100)}%"></div>
            <div class="chart-label">${d.date}</div>
          </div>`).join('')}
        </div>
      </div>
      <div class="table-wrapper" style="padding:20px">
        <div class="table-title" style="margin-bottom:16px">Top Working Hours This Month</div>
        ${topUsers.map(u=>`
          <div style="margin-bottom:12px">
            <div style="display:flex;justify-content:space-between;font-size:13px;margin-bottom:6px">
              <span>${u.name.split(' ')[0]}</span>
              <span style="color:var(--text2)">${u.hours.toFixed(1)}h</span>
            </div>
            <div style="background:var(--bg3);border-radius:4px;height:6px;overflow:hidden">
              <div style="background:var(--accent);height:100%;width:${Math.round((u.hours/maxH)*100)}%;border-radius:4px;transition:width 0.3s"></div>
            </div>
          </div>`).join('')}
      </div>
    </div>`;
  }

  function renderProfilePage() {
    const u = state.currentUser;
    const userAtt = getUserAttendance(u.id);
    const thisMonth = userAtt.filter(a => a.date.startsWith(new Date().toISOString().slice(0,7)));
    return `
    <div style="max-width:600px">
      <div class="table-wrapper" style="padding:32px;margin-bottom:24px">
        <div style="display:flex;align-items:center;gap:20px;margin-bottom:28px">
          <div class="user-avatar" style="background:${avatarColor(u.name)};color:#fff;width:72px;height:72px;font-size:24px">${initials(u.name)}</div>
          <div>
            <div style="font-size:22px;font-weight:700">${u.name}</div>
            <div style="color:var(--text2);font-size:14px">${u.email}</div>
            <div style="display:flex;gap:8px;margin-top:8px">
              <span class="role-badge role-${u.role}">${u.role}</span>
              <span style="color:var(--text2);font-size:12px">${u.department}</span>
            </div>
          </div>
        </div>
        <div class="kpi-grid">
          <div class="kpi-card green"><div class="kpi-label">Days Present</div><div class="kpi-value">${thisMonth.filter(a=>['present','late'].includes(a.status)).length}</div></div>
          <div class="kpi-card accent"><div class="kpi-label">Employee ID</div><div class="kpi-value" style="font-size:18px">${u.employeeId}</div></div>
        </div>
      </div>
      <div class="table-wrapper" style="padding:24px">
        <div class="table-title" style="margin-bottom:20px">Update Password</div>
        <div class="form-group"><label>New Password</label><input type="password" id="newPass" placeholder="New password"></div>
        <div class="form-group"><label>Confirm Password</label><input type="password" id="newPass2" placeholder="Confirm password"></div>
        <div id="profileMsg"></div>
        <button class="btn btn-primary" style="max-width:200px" onclick="App.changePassword()">Update Password</button>
      </div>
    </div>`;
  }

  function changePassword() {
    const p1 = document.getElementById('newPass')?.value;
    const p2 = document.getElementById('newPass2')?.value;
    const msg = document.getElementById('profileMsg');
    if (!p1 || p1.length < 6) { msg.innerHTML = '<div class="error-msg">Password must be at least 6 characters</div>'; return; }
    if (p1 !== p2) { msg.innerHTML = '<div class="error-msg">Passwords do not match</div>'; return; }
    state.users[state.currentUser.id].password = btoa(p1);
    state.currentUser.password = btoa(p1);
    save(); toast('Password updated successfully', 'success');
    msg.innerHTML = '<div class="success-msg">Password updated!</div>';
  }

  function bindLayout() {
    if (state.timer.running) startTimerInterval();
  }

  function renderModal() {
    const m = state.modal;
    if (!m) return;
    const container = document.createElement('div');
    let html = '';
    if (m.type === 'summary') html = renderSummaryModal(m);
    else if (m.type === 'manualAtt') html = renderManualAttModal(m);
    else if (m.type === 'addEmp') html = renderAddEmpModal();
    else if (m.type === 'editEmp') html = renderEditEmpModal(m);
    container.innerHTML = `<div class="modal-overlay" onclick="if(event.target===this)App.closeModal()">${html}</div>`;
    document.body.appendChild(container);
  }

  function closeModal() { state.modal = null; document.querySelector('.modal-overlay')?.parentElement?.remove(); render(); }

  function renderSummaryModal(m) {
    return `
    <div class="modal">
      <div class="modal-header">
        <div class="modal-title">✓ Work Session Complete</div>
        <div class="modal-close" onclick="App.closeModal()">×</div>
      </div>
      <div style="text-align:center;padding:10px 0">
        <div style="font-size:14px;color:var(--text2)">${formatDate(today())}</div>
        <div style="font-size:48px;font-family:'Sora',sans-serif;font-weight:700;color:var(--green);margin:12px 0">${fmtTimer(m.work)}</div>
        <div style="font-size:13px;color:var(--text2)">Total time logged today</div>
      </div>
      <div class="summary-grid">
        <div class="summary-item">
          <div class="summary-item-label">Sign In</div>
          <div class="summary-item-value" style="font-size:18px;color:var(--accent)">${fmtTime(m.att?.signIn)}</div>
        </div>
        <div class="summary-item">
          <div class="summary-item-label">Sign Out</div>
          <div class="summary-item-value" style="font-size:18px;color:var(--accent)">${fmtTime(m.att?.signOut)}</div>
        </div>
        <div class="summary-item">
          <div class="summary-item-label">Break Time</div>
          <div class="summary-item-value" style="font-size:16px;color:var(--yellow)">${fmtDur(m.brk)}</div>
        </div>
        <div class="summary-item">
          <div class="summary-item-label">Productive</div>
          <div class="summary-item-value" style="font-size:16px;color:var(--green)">${fmtDur(m.prod)}</div>
        </div>
      </div>
      <button class="btn btn-primary" onclick="App.closeModal()">Done</button>
    </div>`;
  }

  function openManualAttModal(dateStr) {
    state.modal = { type: 'manualAtt', date: dateStr || today(), editId: null };
    document.querySelector('.modal-overlay')?.parentElement?.remove();
    renderModal();
  }

  function editAttModal(id) {
    const att = Object.values(state.attendance).find(a => a.id === id);
    if (!att) return;
    state.modal = { type: 'manualAtt', date: att.date, editId: id, att };
    document.querySelector('.modal-overlay')?.parentElement?.remove();
    renderModal();
  }

  function renderManualAttModal(m) {
    const users = Object.values(state.users);
    const att = m.att;
    const toTimeVal = ts => ts ? new Date(ts).toTimeString().slice(0,5) : '';
    const msToDurStr = ms => { if (!ms) return ''; const m = Math.round(ms/60000); return m.toString(); };
    const isMgr = canManage();
    const defaultUid = att?.userId || state.currentUser.id;
    return `
    <div class="modal wide">
      <div class="modal-header">
        <div class="modal-title">${att ? '✏️ Edit Attendance' : '📅 Add Past Attendance'}</div>
        <div class="modal-close" onclick="App.closeModal()">×</div>
      </div>
      ${!att ? `<div style="background:rgba(79,127,255,0.08);border:1px solid rgba(79,127,255,0.2);border-radius:8px;padding:12px 16px;margin-bottom:20px;font-size:13px;color:var(--text2)">
        Fill in the times for a past day you missed logging. Leave Sign Out blank if you want to edit it later.
      </div>` : ''}
      <div id="attFormMsg"></div>
      <div class="form-row">
        <div class="form-group"><label>Employee</label>
          ${isMgr
            ? `<select id="mEmp" ${att?'disabled':''}>${users.map(u=>`<option value="${u.id}" ${u.id===defaultUid?'selected':''}>${u.name} (${u.employeeId})</option>`).join('')}</select>`
            : `<input type="text" value="${state.currentUser.name} (${state.currentUser.employeeId})" disabled style="opacity:0.6"><input type="hidden" id="mEmp" value="${state.currentUser.id}">`
          }
        </div>
        <div class="form-group"><label>Date</label>
          <input type="date" id="mDate" value="${att?.date||m.date}" max="${today()}">
          <div style="font-size:11px;color:var(--text3);margin-top:4px">Only today or past dates allowed</div>
        </div>
      </div>
      <div class="form-row">
        <div class="form-group"><label>Sign In Time</label><input type="time" id="mSignIn" value="${toTimeVal(att?.signIn)}"></div>
        <div class="form-group"><label>Sign Out Time <span style="color:var(--text3);font-weight:400">(optional)</span></label><input type="time" id="mSignOut" value="${toTimeVal(att?.signOut)}"></div>
      </div>
      <div class="form-row">
        <div class="form-group"><label>Break Duration (minutes)</label><input type="number" id="mBreak" min="0" value="${msToDurStr(att?.breakTime)}" placeholder="0"></div>
        <div class="form-group"><label>Status</label>
          <select id="mStatus">
            ${['present','absent','halfday','leave','late'].map(s=>`<option value="${s}" ${att?.status===s?'selected':''}>${s.charAt(0).toUpperCase()+s.slice(1)}</option>`).join('')}
          </select>
        </div>
      </div>
      <div class="form-group"><label>Notes <span style="color:var(--text3);font-weight:400">(optional)</span></label><input type="text" id="mNotes" value="${att?.notes||''}" placeholder="e.g. WFH, client visit, sick leave..."></div>
      <div class="modal-footer">
        <button class="btn btn-secondary" onclick="App.closeModal()">Cancel</button>
        <button class="btn btn-primary" onclick="App.saveManualAtt()">${att ? 'Save Changes' : 'Add Entry'}</button>
      </div>
    </div>`;
  }

  function saveManualAtt() {
    const m = state.modal;
    const uid = document.getElementById('mEmp')?.value || m.att?.userId;
    const date = document.getElementById('mDate')?.value;
    const signIn = document.getElementById('mSignIn')?.value;
    const signOut = document.getElementById('mSignOut')?.value;
    const breakMins = parseInt(document.getElementById('mBreak')?.value||'0');
    const status = document.getElementById('mStatus')?.value;
    const notes = document.getElementById('mNotes')?.value;
    const msg = document.getElementById('attFormMsg');
    if (!uid || !date) { msg.innerHTML = '<div class="error-msg">Please fill required fields.</div>'; return; }
    const signInTs = signIn ? new Date(`${date}T${signIn}`).getTime() : null;
    const signOutTs = signOut ? new Date(`${date}T${signOut}`).getTime() : null;
    if (signOutTs && signInTs && signOutTs <= signInTs) { msg.innerHTML = '<div class="error-msg">Sign out must be after sign in.</div>'; return; }
    const key = getAttendanceKey(uid, date);
    const existingForKey = state.attendance[key];
    if (!m.editId && existingForKey) {
      // Auto-switch to edit mode for that existing record
      const existing = existingForKey;
      state.modal = { type: 'manualAtt', date: date, editId: existing.id, att: existing };
      document.querySelector('.modal-overlay')?.parentElement?.remove();
      renderModal();
      const msgEl = document.getElementById('attFormMsg');
      if (msgEl) msgEl.innerHTML = '<div class="success-msg" style="background:rgba(245,200,66,0.1);border-color:rgba(245,200,66,0.3);color:var(--yellow)">A record already exists for this date — editing it now.</div>';
      return;
    }
    const id = m.editId ? m.att.id : genId();
    state.attendance[key] = { id, userId: uid, date, signIn: signInTs, signOut: signOutTs, breakTime: breakMins*60000, status, notes };
    save(); closeModal(); toast(m.editId ? 'Attendance updated' : 'Attendance added', 'success');
  }

  function deleteAtt(id) {
    if (!confirm('Delete this attendance record?')) return;
    const key = Object.keys(state.attendance).find(k => state.attendance[k].id === id);
    if (key) { delete state.attendance[key]; save(); render(); toast('Record deleted', 'info'); }
  }

  function openAddEmpModal() {
    state.modal = { type: 'addEmp' };
    document.querySelector('.modal-overlay')?.parentElement?.remove();
    renderModal();
  }

  function editEmpModal(id) {
    const u = state.users[id];
    if (!u) return;
    state.modal = { type: 'editEmp', user: u };
    document.querySelector('.modal-overlay')?.parentElement?.remove();
    renderModal();
  }

  function renderAddEmpModal() {
    return `
    <div class="modal">
      <div class="modal-header"><div class="modal-title">Add New Employee</div><div class="modal-close" onclick="App.closeModal()">×</div></div>
      <div id="empFormMsg"></div>
      <div class="form-row">
        <div class="form-group"><label>Full Name</label><input type="text" id="eeName" placeholder="Full name"></div>
        <div class="form-group"><label>Department</label><input type="text" id="eeDept" placeholder="Department"></div>
      </div>
      <div class="form-group"><label>Email</label><input type="email" id="eeEmail" placeholder="email@company.com"></div>
      <div class="form-row">
        <div class="form-group"><label>Role</label>
          <select id="eeRole">
            <option value="employee">Employee</option>
            <option value="hr">HR</option>
            <option value="manager">Manager</option>
            <option value="admin">Admin</option>
          </select>
        </div>
        <div class="form-group"><label>Temp Password</label><input type="text" id="eePass" placeholder="Temporary password"></div>
      </div>
      <div class="modal-footer">
        <button class="btn btn-secondary" onclick="App.closeModal()">Cancel</button>
        <button class="btn btn-primary" onclick="App.saveNewEmp()">Add Employee</button>
      </div>
    </div>`;
  }

  function renderEditEmpModal(m) {
    const u = m.user;
    return `
    <div class="modal">
      <div class="modal-header"><div class="modal-title">Edit Employee</div><div class="modal-close" onclick="App.closeModal()">×</div></div>
      <div id="empFormMsg"></div>
      <div class="form-row">
        <div class="form-group"><label>Full Name</label><input type="text" id="eeName" value="${u.name}"></div>
        <div class="form-group"><label>Department</label><input type="text" id="eeDept" value="${u.department}"></div>
      </div>
      <div class="form-group"><label>Email</label><input type="email" id="eeEmail" value="${u.email}"></div>
      <div class="form-group"><label>Role</label>
        <select id="eeRole">
          ${['employee','hr','manager','admin'].map(r=>`<option value="${r}" ${u.role===r?'selected':''}>${r.charAt(0).toUpperCase()+r.slice(1)}</option>`).join('')}
        </select>
      </div>
      <div class="modal-footer">
        <button class="btn btn-secondary" onclick="App.closeModal()">Cancel</button>
        <button class="btn btn-primary" onclick="App.saveEditEmp('${u.id}')">Save Changes</button>
      </div>
    </div>`;
  }

  function saveNewEmp() {
    const name = document.getElementById('eeName')?.value.trim();
    const dept = document.getElementById('eeDept')?.value.trim();
    const email = document.getElementById('eeEmail')?.value.trim();
    const role = document.getElementById('eeRole')?.value;
    const pass = document.getElementById('eePass')?.value;
    const msg = document.getElementById('empFormMsg');
    if (!name || !dept || !email || !pass) { msg.innerHTML = '<div class="error-msg">All fields required.</div>'; return; }
    if (Object.values(state.users).find(u => u.email === email)) { msg.innerHTML = '<div class="error-msg">Email already exists.</div>'; return; }
    const id = genId();
    state.users[id] = { id, name, email, password: btoa(pass), role, department: dept, employeeId: genEmpId(), createdAt: Date.now() };
    save(); closeModal(); toast('Employee added successfully', 'success');
  }

  function saveEditEmp(id) {
    const name = document.getElementById('eeName')?.value.trim();
    const dept = document.getElementById('eeDept')?.value.trim();
    const email = document.getElementById('eeEmail')?.value.trim();
    const role = document.getElementById('eeRole')?.value;
    const msg = document.getElementById('empFormMsg');
    if (!name || !dept || !email) { msg.innerHTML = '<div class="error-msg">All fields required.</div>'; return; }
    const dup = Object.values(state.users).find(u => u.email === email && u.id !== id);
    if (dup) { msg.innerHTML = '<div class="error-msg">Email already in use.</div>'; return; }
    state.users[id] = { ...state.users[id], name, department: dept, email, role };
    if (state.currentUser.id === id) state.currentUser = state.users[id];
    save(); closeModal(); toast('Employee updated', 'success');
  }

  function deleteEmp(id) {
    if (!confirm('Delete this employee and all their attendance records?')) return;
    delete state.users[id];
    Object.keys(state.attendance).forEach(k => { if (state.attendance[k].userId === id) delete state.attendance[k]; });
    save(); render(); toast('Employee deleted', 'info');
  }

  function exportCSV(scope) {
    let records = Object.values(state.attendance);
    if (scope === 'month') {
      const m = new Date().toISOString().slice(0,7);
      records = records.filter(a => a.date.startsWith(m));
    }
    const rows = [['Employee','Employee ID','Date','Sign In','Sign Out','Work Hours','Break Time','Productive Hours','Status','Notes']];
    records.sort((a,b) => b.date.localeCompare(a.date)).forEach(a => {
      const u = state.users[a.userId];
      const workMs = a.signOut ? (a.signOut - a.signIn - (a.breakTime||0)) : 0;
      rows.push([
        u?.name||'Unknown', u?.employeeId||'', a.date,
        fmtTime(a.signIn), a.signOut ? fmtTime(a.signOut) : '',
        a.signOut ? (workMs/3600000).toFixed(2) : '',
        ((a.breakTime||0)/60000).toFixed(0) + ' min',
        a.signOut ? (workMs/3600000).toFixed(2) : '',
        a.status, a.notes||''
      ]);
    });
    const csv = rows.map(r => r.map(c => `"${String(c).replace(/"/g,'""')}"`).join(',')).join('\n');
    const blob = new Blob([csv], { type: 'text/csv' });
    const a = document.createElement('a'); a.href = URL.createObjectURL(blob);
    a.download = `worktrack_attendance_${scope}_${new Date().toISOString().slice(0,10)}.csv`;
    a.click(); toast('CSV exported', 'success');
  }

  function exportPDF() {
    try {
      const { jsPDF } = window.jspdf;
      const doc = new jsPDF();
      doc.setFont('helvetica', 'bold');
      doc.setFontSize(20); doc.text('WorkTrack', 14, 22);
      doc.setFont('helvetica', 'normal');
      doc.setFontSize(11); doc.text('Attendance Report', 14, 30);
      doc.setFontSize(9); doc.setTextColor(120);
      doc.text(`Generated: ${new Date().toLocaleDateString()}`, 14, 37);
      doc.setTextColor(0);
      const records = Object.values(state.attendance).sort((a,b) => b.date.localeCompare(a.date)).slice(0,50);
      const rows = records.map(a => {
        const u = state.users[a.userId];
        const workMs = a.signOut ? (a.signOut - a.signIn - (a.breakTime||0)) : 0;
        return [u?.name||'?', a.date, fmtTime(a.signIn), a.signOut?fmtTime(a.signOut):'—', a.signOut?(workMs/3600000).toFixed(1)+'h':'—', a.status];
      });
      doc.autoTable({
        startY: 44, head: [['Employee','Date','In','Out','Hours','Status']],
        body: rows, styles: { fontSize: 8, cellPadding: 3 },
        headStyles: { fillColor: [30,34,48], textColor: 240, fontStyle: 'bold' },
        alternateRowStyles: { fillColor: [245,246,250] }
      });
      doc.save(`worktrack_report_${new Date().toISOString().slice(0,10)}.pdf`);
      toast('PDF exported', 'success');
    } catch(e) { toast('PDF export failed. Try CSV instead.', 'error'); }
  }

  return {
    init() { load(); render(); },
    setAuthTab, login, register, logout, setPage,
    handleSignIn, handleBreakStart, handleBreakEnd, handleSignOut,
    closeModal, openManualAttModal, editAttModal, saveManualAtt, deleteAtt,
    openAddEmpModal, editEmpModal, saveNewEmp, saveEditEmp, deleteEmp,
    calNav, calDayClick, setAttSearch, setAttFilter, attChangePage,
    exportCSV, exportPDF, changePassword
  };
})();

window.addEventListener('DOMContentLoaded', () => App.init());
</script>
</body>
</html>
