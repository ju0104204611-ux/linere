<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>만남 예약 시스템</title>
<link href="https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@300;400;500;700;900&family=DM+Serif+Display:ital@0;1&display=swap" rel="stylesheet">
<style>
:root {
  --ink: #1a1a2e;
  --paper: #f5f0e8;
  --cream: #faf7f2;
  --accent: #c8963c;
  --accent-light: #e8c882;
  --sage: #5a7a6a;
  --rose: #c86464;
  --muted: #7a7a8a;
  --line: rgba(26,26,46,0.12);
  --landlord: #2d5a8e;
  --landlord-bg: #eef4fb;
  --tenant: #8e4a2d;
  --tenant-bg: #fbeee8;
  --match: #3a7a4a;
  --match-bg: #e8f5eb;
  --admin: #4a2d8e;
  --admin-bg: #f0eefb;
}
* { box-sizing: border-box; margin: 0; padding: 0; }
body { font-family: 'Noto Sans KR', sans-serif; background: var(--paper); color: var(--ink); min-height: 100vh; }
body::before {
  content: ''; position: fixed; inset: 0; z-index: 0;
  background-image: radial-gradient(ellipse 80% 60% at 20% 10%, rgba(200,150,60,0.06) 0%, transparent 60%), radial-gradient(ellipse 60% 80% at 80% 90%, rgba(45,90,142,0.06) 0%, transparent 60%);
  pointer-events: none;
}

/* ── HEADER ── */
header {
  position: relative; z-index: 10; background: var(--ink);
  padding: 20px 40px; display: flex; align-items: center; gap: 16px;
  box-shadow: 0 4px 24px rgba(26,26,46,0.3);
}
header.admin-header { background: linear-gradient(135deg, #1a1a2e 60%, #2d1a5e); }
.header-icon { width: 44px; height: 44px; background: var(--accent); border-radius: 12px; display: flex; align-items: center; justify-content: center; font-size: 20px; flex-shrink: 0; }
.header-icon.admin-icon { background: var(--admin); }
header h1 { font-family: 'DM Serif Display', serif; font-size: 22px; color: var(--paper); }
header p { font-size: 12px; color: rgba(245,240,232,0.55); margin-top: 2px; }
.header-right { margin-left: auto; display: flex; gap: 8px; align-items: center; }
.admin-link-btn {
  padding: 7px 16px; border-radius: 20px; font-size: 12px; font-weight: 700;
  background: rgba(255,255,255,0.12); color: rgba(245,240,232,0.8);
  border: 1px solid rgba(255,255,255,0.18); cursor: pointer;
  font-family: 'Noto Sans KR', sans-serif; transition: all 0.2s; text-decoration: none;
  display: inline-flex; align-items: center; gap: 5px;
}
.admin-link-btn:hover { background: rgba(255,255,255,0.2); color: white; }

/* ── PROGRESS ── */
.progress-bar {
  position: relative; z-index: 10; background: rgba(26,26,46,0.04);
  border-bottom: 1px solid var(--line); padding: 0 40px;
  display: flex; overflow-x: auto;
}
.step-tab { padding: 13px 0; margin-right: 28px; font-size: 12.5px; font-weight: 500; color: var(--muted); border-bottom: 2px solid transparent; white-space: nowrap; transition: all 0.3s; flex-shrink: 0; }
.step-tab.active { color: var(--ink); border-color: var(--accent); font-weight: 700; }
.step-tab.done { color: var(--sage); border-color: var(--sage); }
.step-num { display: inline-flex; align-items: center; justify-content: center; width: 18px; height: 18px; background: var(--muted); color: white; border-radius: 50%; font-size: 10px; font-weight: 700; margin-right: 6px; transition: all 0.3s; }
.step-tab.active .step-num { background: var(--accent); }
.step-tab.done .step-num { background: var(--sage); }

/* ── MAIN ── */
main { position: relative; z-index: 5; max-width: 980px; margin: 0 auto; padding: 32px 24px 80px; }
.view { display: none; }
.view.active { display: block; }

/* ── CARD ── */
.card { background: var(--cream); border: 1px solid var(--line); border-radius: 20px; padding: 28px 32px; margin-bottom: 20px; box-shadow: 0 2px 12px rgba(26,26,46,0.06); animation: fadeUp 0.35s ease both; }
@keyframes fadeUp { from { opacity:0; transform:translateY(14px); } to { opacity:1; transform:translateY(0); } }
.card-header { display: flex; align-items: flex-start; gap: 12px; margin-bottom: 24px; padding-bottom: 16px; border-bottom: 1px solid var(--line); }
.role-badge { padding: 4px 12px; border-radius: 20px; font-size: 11px; font-weight: 700; letter-spacing: 0.5px; flex-shrink: 0; margin-top: 2px; }
.badge-landlord { background: var(--landlord-bg); color: var(--landlord); }
.badge-tenant   { background: var(--tenant-bg);   color: var(--tenant); }
.badge-admin    { background: var(--admin-bg);     color: var(--admin); }
.card-title { font-size: 17px; font-weight: 700; }
.card-desc  { font-size: 12.5px; color: var(--muted); margin-top: 3px; line-height: 1.5; }

/* ── DATE SECTION ── */
.date-section { margin-bottom: 8px; }
.date-header { display: flex; align-items: center; gap: 10px; padding: 11px 14px; background: white; border: 1.5px solid var(--line); border-radius: 12px; cursor: pointer; transition: all 0.2s; user-select: none; }
.date-header:hover { border-color: var(--accent); background: rgba(200,150,60,0.03); }
.date-header.open  { border-color: var(--landlord); border-bottom-color: transparent; border-radius: 12px 12px 0 0; background: var(--landlord-bg); }
.date-header.tenant-open { border-color: var(--tenant); border-bottom-color: transparent; border-radius: 12px 12px 0 0; background: var(--tenant-bg); }
.dow-circle { width: 36px; height: 36px; flex-shrink: 0; border-radius: 10px; display: flex; align-items: center; justify-content: center; font-size: 12px; font-weight: 700; background: var(--paper); color: var(--muted); transition: all 0.2s; }
.dow-circle.sat { background: #eef4fb; color: var(--landlord); }
.dow-circle.sun { background: #fbeee8; color: var(--rose); }
.dow-circle.sel-l { background: var(--landlord); color: white; }
.dow-circle.sel-t { background: var(--tenant); color: white; }
.date-text { flex: 1; }
.date-text .d-main { font-size: 14px; font-weight: 600; }
.date-text .d-sub  { font-size: 11px; color: var(--muted); }
.sel-badge { font-size: 11px; font-weight: 700; padding: 3px 10px; border-radius: 20px; flex-shrink: 0; }
.sel-badge-l { background: var(--landlord-bg); color: var(--landlord); }
.sel-badge-t { background: var(--tenant-bg); color: var(--tenant); }
.chevron { font-size: 11px; color: var(--muted); transition: transform 0.25s; }

/* ── TIME PANEL ── */
.time-panel { display: none; border: 1.5px solid var(--landlord); border-top: none; border-radius: 0 0 12px 12px; background: white; padding: 14px 14px 16px; }
.time-panel.visible { display: block; }
.time-panel.t-border { border-color: var(--tenant); }
.time-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(84px, 1fr)); gap: 7px; }
.time-slot { padding: 9px 6px; border: 1.5px solid var(--line); border-radius: 9px; text-align: center; cursor: pointer; transition: all 0.18s; font-size: 12.5px; font-weight: 500; background: var(--paper); user-select: none; line-height: 1.3; color: var(--ink); }
.time-slot .time-main { font-size: 13px; font-weight: 600; }
.time-slot .time-sub  { font-size: 10px; color: var(--muted); }
.time-slot:hover:not(.disabled) { border-color: var(--accent); background: rgba(200,150,60,0.07); transform: translateY(-1px); }
.time-slot.l-sel { background: var(--landlord-bg); border-color: var(--landlord); color: var(--landlord); }
.time-slot.t-sel { background: var(--tenant-bg); border-color: var(--tenant); color: var(--tenant); }
.time-slot.matched { background: var(--match-bg); border-color: var(--match); color: var(--match); animation: glow 2s ease infinite; }
@keyframes glow { 0%,100% { box-shadow: 0 0 0 2px rgba(58,122,74,0.18); } 50% { box-shadow: 0 0 0 5px rgba(58,122,74,0.07); } }
.time-slot.disabled { opacity: 0.22; cursor: not-allowed; pointer-events: none; }

/* ── SUMMARY BAR ── */
.summary-bar { display: flex; align-items: center; justify-content: space-between; margin-bottom: 16px; }
.slot-count { font-size: 12.5px; font-weight: 600; background: var(--accent-light); color: var(--ink); padding: 4px 14px; border-radius: 20px; }
.hint { font-size: 12px; color: var(--muted); }

/* ── BUTTONS ── */
.btn { display: inline-flex; align-items: center; justify-content: center; gap: 7px; padding: 12px 26px; border-radius: 11px; font-size: 13.5px; font-weight: 700; font-family: 'Noto Sans KR', sans-serif; cursor: pointer; border: none; transition: all 0.2s; }
.btn-primary  { background: var(--ink);      color: var(--paper); box-shadow: 0 4px 14px rgba(26,26,46,0.22); }
.btn-accent   { background: var(--accent);   color: white; box-shadow: 0 4px 14px rgba(200,150,60,0.3); }
.btn-landlord { background: var(--landlord); color: white; box-shadow: 0 4px 14px rgba(45,90,142,0.28); }
.btn-tenant   { background: var(--tenant);   color: white; box-shadow: 0 4px 14px rgba(142,74,45,0.28); }
.btn-ghost    { background: transparent; color: var(--muted); border: 1.5px solid var(--line); }
.btn-admin    { background: var(--admin);    color: white; box-shadow: 0 4px 14px rgba(74,45,142,0.28); }
.btn-danger   { background: var(--rose);     color: white; }
.btn-sm       { padding: 7px 16px; font-size: 12px; border-radius: 8px; }
.btn:hover:not(:disabled) { filter: brightness(1.08); transform: translateY(-1px); }
.btn:disabled { opacity: 0.38; cursor: not-allowed; transform: none !important; }
.btn-row { display: flex; gap: 10px; margin-top: 24px; flex-wrap: wrap; }

/* ── URL BOX ── */
.url-box { background: var(--ink); border-radius: 14px; padding: 18px 22px; display: flex; align-items: center; gap: 14px; margin: 16px 0; }
.url-text { flex: 1; font-size: 12px; color: var(--accent-light); font-family: monospace; word-break: break-all; line-height: 1.6; }
.copy-btn { flex-shrink: 0; padding: 8px 18px; background: var(--accent); color: white; border: none; border-radius: 8px; font-size: 12px; font-weight: 700; cursor: pointer; font-family: 'Noto Sans KR', sans-serif; transition: all 0.2s; }
.copy-btn:hover { background: #b8823c; }
.copy-btn.copied { background: var(--sage); }

/* ── INFO STRIP ── */
.info-strip { background: linear-gradient(135deg, var(--landlord-bg), rgba(251,238,232,0.6)); border: 1px solid var(--line); border-radius: 12px; padding: 14px 18px; margin-bottom: 18px; font-size: 13px; line-height: 2; }

/* ── RESULT ── */
.result-hero { text-align: center; padding: 28px 20px 20px; border: 2px solid var(--match); background: var(--match-bg); border-radius: 16px; margin-bottom: 20px; }
.result-hero .emoji { font-size: 42px; margin-bottom: 10px; }
.result-hero h3 { font-size: 20px; font-weight: 700; color: var(--match); }
.result-hero p  { font-size: 13px; color: var(--muted); margin-top: 6px; }
.match-list { list-style: none; display: flex; flex-direction: column; gap: 8px; }
.match-item { background: white; border: 1.5px solid var(--match); border-radius: 11px; padding: 13px 18px; display: flex; align-items: center; gap: 10px; font-size: 14px; font-weight: 600; color: var(--match); }
.no-match { text-align: center; padding: 36px 20px; }

/* ── NAME INPUT FORM ── */
.name-form { background: white; border: 1.5px solid var(--line); border-radius: 14px; padding: 20px; margin-top: 20px; }
.name-form h4 { font-size: 14px; font-weight: 700; margin-bottom: 14px; color: var(--ink); }
.form-row { display: flex; gap: 10px; margin-bottom: 10px; flex-wrap: wrap; }
.form-group { flex: 1; min-width: 140px; }
.form-group label { display: block; font-size: 11.5px; font-weight: 600; color: var(--muted); margin-bottom: 5px; }
.form-group input {
  width: 100%; padding: 9px 12px; border: 1.5px solid var(--line); border-radius: 9px;
  font-size: 13.5px; font-family: 'Noto Sans KR', sans-serif; color: var(--ink);
  background: var(--paper); transition: border-color 0.2s; outline: none;
}
.form-group input:focus { border-color: var(--accent); background: white; }
.match-select { width: 100%; padding: 9px 12px; border: 1.5px solid var(--line); border-radius: 9px; font-size: 13.5px; font-family: 'Noto Sans KR', sans-serif; color: var(--ink); background: var(--paper); outline: none; transition: border-color 0.2s; }
.match-select:focus { border-color: var(--accent); }

/* ── ADMIN DASHBOARD ── */
.admin-stats { display: grid; grid-template-columns: repeat(auto-fill, minmax(160px, 1fr)); gap: 14px; margin-bottom: 28px; }
.stat-card { background: white; border: 1px solid var(--line); border-radius: 14px; padding: 18px 16px; text-align: center; }
.stat-card .stat-num { font-family: 'DM Serif Display', serif; font-size: 36px; color: var(--ink); line-height: 1; }
.stat-card .stat-label { font-size: 12px; color: var(--muted); margin-top: 6px; font-weight: 500; }
.stat-card.c-match  .stat-num { color: var(--match); }
.stat-card.c-pend   .stat-num { color: var(--accent); }
.stat-card.c-total  .stat-num { color: var(--landlord); }

/* Admin table */
.admin-table-wrap { overflow-x: auto; border-radius: 14px; border: 1px solid var(--line); }
.admin-table { width: 100%; border-collapse: collapse; background: white; font-size: 13px; }
.admin-table thead { background: var(--ink); color: var(--paper); }
.admin-table th { padding: 12px 14px; text-align: left; font-size: 11.5px; font-weight: 600; letter-spacing: 0.3px; white-space: nowrap; }
.admin-table tbody tr { border-bottom: 1px solid var(--line); transition: background 0.15s; }
.admin-table tbody tr:hover { background: rgba(200,150,60,0.04); }
.admin-table td { padding: 12px 14px; vertical-align: middle; }
.admin-table td .time-chip { display: inline-block; background: var(--match-bg); color: var(--match); font-size: 11.5px; font-weight: 700; padding: 3px 9px; border-radius: 20px; white-space: nowrap; }
.admin-table td .pending-chip { background: rgba(200,150,60,0.12); color: var(--accent); font-size: 11.5px; font-weight: 700; padding: 3px 9px; border-radius: 20px; }
.admin-table td .del-btn { padding: 4px 10px; border-radius: 6px; font-size: 11px; background: rgba(200,100,100,0.1); color: var(--rose); border: 1px solid rgba(200,100,100,0.25); cursor: pointer; font-family: 'Noto Sans KR', sans-serif; transition: all 0.2s; }
.admin-table td .del-btn:hover { background: var(--rose); color: white; }

.admin-toolbar { display: flex; align-items: center; justify-content: space-between; margin-bottom: 14px; flex-wrap: wrap; gap: 10px; }
.filter-tabs { display: flex; gap: 6px; }
.filter-tab { padding: 6px 14px; border-radius: 20px; font-size: 12px; font-weight: 600; cursor: pointer; border: 1.5px solid var(--line); background: white; color: var(--muted); font-family: 'Noto Sans KR', sans-serif; transition: all 0.2s; }
.filter-tab.active { background: var(--admin); color: white; border-color: var(--admin); }

.empty-admin { text-align: center; padding: 48px 20px; }
.empty-admin .emoji { font-size: 40px; margin-bottom: 12px; }
.empty-admin p { color: var(--muted); font-size: 14px; line-height: 1.7; }

.loading-spin { text-align: center; padding: 40px; color: var(--muted); font-size: 14px; }

/* calendar view */
.cal-grid { display: grid; grid-template-columns: repeat(7, 1fr); gap: 6px; margin-top: 16px; }
.cal-head { text-align: center; font-size: 11px; font-weight: 700; color: var(--muted); padding: 6px 0; }
.cal-head.sat { color: var(--landlord); }
.cal-head.sun { color: var(--rose); }
.cal-cell { background: white; border: 1.5px solid var(--line); border-radius: 10px; padding: 8px 6px; min-height: 72px; cursor: default; }
.cal-cell.has-event { border-color: var(--match); background: var(--match-bg); cursor: pointer; }
.cal-cell.has-event:hover { transform: translateY(-1px); box-shadow: 0 3px 10px rgba(58,122,74,0.15); }
.cal-cell.out-range { opacity: 0.3; }
.cal-date-num { font-size: 13px; font-weight: 700; color: var(--ink); }
.cal-dot { width: 6px; height: 6px; background: var(--match); border-radius: 50%; margin: 4px auto 0; }
.cal-count { font-size: 10px; color: var(--match); font-weight: 700; text-align: center; margin-top: 2px; }

/* tabs */
.view-tabs { display: flex; gap: 6px; margin-bottom: 20px; }
.view-tab { padding: 8px 18px; border-radius: 20px; font-size: 12.5px; font-weight: 600; cursor: pointer; border: 1.5px solid var(--line); background: white; color: var(--muted); font-family: 'Noto Sans KR', sans-serif; transition: all 0.2s; }
.view-tab.active { background: var(--admin); color: white; border-color: var(--admin); }

/* ── MOBILE ── */
@media (max-width: 600px) {
  header { padding: 14px 16px; }
  main { padding: 16px 12px 60px; }
  .card { padding: 16px 14px; }
  .progress-bar { padding: 0 14px; }
  .step-tab { font-size: 11px; margin-right: 14px; }
  .time-grid { grid-template-columns: repeat(3, 1fr); }
  .url-box { flex-direction: column; }
  .cal-grid { gap: 3px; }
  .admin-stats { grid-template-columns: repeat(3, 1fr); }
}

/* 시스템 공지 (Firebase 연결 전) */
#loading-overlay { position:fixed; top:0; left:0; right:0; bottom:0; background:rgba(255,255,255,0.9); z-index:9999; display:flex; flex-direction:column; align-items:center; justify-content:center; }
.spinner { width:40px; height:40px; border:4px solid var(--line); border-top-color:var(--accent); border-radius:50%; animation:spin 1s linear infinite; margin-bottom:16px; }
@keyframes spin { to { transform:rotate(360deg); } }

/* ── AI FEATURES ── */
.ai-btn { background: linear-gradient(135deg, #6b46c1, #805ad5); color: white; border: none; padding: 8px 16px; border-radius: 8px; font-size: 12px; font-weight: bold; cursor: pointer; display: inline-flex; align-items: center; gap: 6px; box-shadow: 0 4px 10px rgba(107,70,193,0.3); transition: all 0.2s; font-family: 'Noto Sans KR', sans-serif; }
.ai-btn:hover { transform: translateY(-1px); filter: brightness(1.1); }
.ai-btn:disabled { opacity: 0.7; cursor: not-allowed; transform: none; }
.ai-input { width: 100%; padding: 10px 12px; border: 1.5px solid var(--line); border-radius: 8px; font-size: 13px; margin-bottom: 10px; font-family: 'Noto Sans KR', sans-serif; box-sizing: border-box; }
.ai-input:focus { border-color: #805ad5; outline: none; }
.ai-loading { display: inline-block; width: 14px; height: 14px; border: 2px solid white; border-top-color: transparent; border-radius: 50%; animation: spin 1s linear infinite; }
</style>
</head>
<body>

<div id="loading-overlay">
  <div class="spinner"></div>
  <p style="font-weight:700; color:var(--ink);">클라우드 데이터베이스 연결 중...</p>
</div>

<header id="main-header">
  <div class="header-icon" id="header-icon">🏠</div>
  <div>
    <h1 id="header-title">만남 예약 시스템</h1>
    <p id="header-sub">임대인과 임차인의 일정을 간편하게 맞춰드립니다</p>
  </div>
  <div class="header-right">
    <button class="admin-link-btn" onclick="goAdmin()">⚙️ 관리자</button>
  </div>
</header>

<div class="progress-bar" id="progress-bar">
  <div class="step-tab active" id="tab1"><span class="step-num">1</span>임대인 제안</div>
  <div class="step-tab" id="tab2"><span class="step-num">2</span>링크 공유 (담당자)</div>
  <div class="step-tab" id="tab3"><span class="step-num">3</span>임차인 확인</div>
  <div class="step-tab" id="tab4"><span class="step-num">4</span>결과 확정</div>
</div>

<main>
  <!-- STEP 1 -->
  <div class="view active" id="view1">
    <div class="card">
      <div class="card-header">
        <span class="role-badge badge-landlord">임대인</span>
        <div>
          <div class="card-title">방문 가능한 날짜와 시간대를 선택해주세요</div>
          <div class="card-desc">날짜 행을 클릭해 펼친 뒤, 가능한 시간을 모두 선택하세요 (<span id="date-range-text"></span>)</div>
        </div>
      </div>
      <div class="summary-bar">
        <span class="slot-count" id="l-count">0개 시간대 선택됨</span>
        <span class="hint">날짜 클릭 → 시간 선택</span>
      </div>
      <div id="landlord-list"></div>
      <div class="btn-row">
        <button class="btn btn-landlord" id="l-next-btn" disabled onclick="goStep2()" style="width:100%">공유용 링크 생성하기 →</button>
      </div>
    </div>
  </div>

  <!-- STEP 2 -->
  <div class="view" id="view2">
    <div class="card">
      <div class="card-header">
        <span class="role-badge badge-admin" style="background:#e8c882; color:#8e6a2d;">담당자</span>
        <div>
          <div class="card-title">임대인이 보낸 링크를 임차인에게 전달해주세요</div>
          <div class="card-desc">아래 링크를 복사하여 임차인에게 안내 문자 또는 카카오톡으로 전송합니다.</div>
        </div>
      </div>
      <div class="url-box">
        <div class="url-text" id="share-url"></div>
        <button class="copy-btn" id="copy-btn" onclick="copyUrl('share-url','copy-btn')">복사</button>
      </div>
      <div style="background:rgba(200,150,60,0.08);border:1px solid rgba(200,150,60,0.28);border-radius:12px;padding:16px;">
        <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:12px;flex-wrap:wrap;gap:8px;">
            <p style="font-size:12.5px;font-weight:700;color:var(--accent);margin:0;">💬 임차인 안내 문자 예시</p>
            <button class="ai-btn" id="ai-sms-btn" onclick="generateAISMS()">✨ AI 맞춤 문자 생성</button>
        </div>
        <input type="text" id="ai-sms-prompt" class="ai-input" placeholder="AI에게 요청: 예) 아주 정중하게 작성해줘, 주차장 공사 안내 포함해줘">
        <div style="position:relative;">
            <textarea id="sms-text" style="width:100%;height:160px;font-size:13px;color:var(--ink);line-height:1.6;padding:12px;border:1px solid var(--line);border-radius:8px;resize:vertical;font-family:'Noto Sans KR', sans-serif;"></textarea>
        </div>
      </div>
      <div class="btn-row">
        <button class="btn btn-ghost" onclick="goStep1()">← 일정 다시 선택</button>
        <button class="btn btn-primary" onclick="simulateTenantClick()">임차인 화면 미리보기 (테스트) →</button>
      </div>
    </div>
  </div>

  <!-- STEP 3 -->
  <div class="view" id="view3">
    <div class="card">
      <div class="card-header">
        <span class="role-badge badge-tenant">임차인</span>
        <div>
          <div class="card-title">임대인 방문 가능 시간을 선택해주세요</div>
          <div class="card-desc">임대인이 제안한 날짜·시간 중 방문을 원하시는 시간대를 선택해주세요</div>
        </div>
      </div>
      <div class="info-strip" id="t-info-strip"></div>
      <div id="tenant-list"></div>
      <div class="btn-row">
        <button class="btn btn-tenant" onclick="goStep4()" style="width:100%">선택 완료 및 결과 확인하기 →</button>
      </div>
    </div>
  </div>

  <!-- STEP 4 -->
  <div class="view" id="view4">
    <div class="card" id="result-card"></div>
  </div>

  <!-- ADMIN -->
  <div class="view" id="view-admin">
    <div class="card">
      <div class="card-header">
        <span class="role-badge badge-admin">관리자</span>
        <div>
          <div class="card-title">전체 예약 현황 대시보드</div>
          <div class="card-desc">클라우드에 저장된 모든 확정 예약 일정을 관리합니다</div>
        </div>
        <div style="margin-left:auto;display:flex;gap:8px;flex-wrap:wrap">
          <button class="btn btn-sm btn-ghost" onclick="loadAdminData()">🔄 새로고침</button>
          <button class="btn btn-sm btn-ghost" onclick="goStep1()">← 예약 생성 화면</button>
        </div>
      </div>
      <div id="admin-stats-row" class="admin-stats"></div>
      <div class="view-tabs">
        <button class="view-tab active" onclick="switchAdminTab('list',this)">📋 목록</button>
        <button class="view-tab" onclick="switchAdminTab('calendar',this)">📅 달력</button>
      </div>
      <div id="admin-list-view">
        <div class="admin-toolbar">
          <div class="filter-tabs" id="filter-tabs">
            <button class="filter-tab active" onclick="filterAdmin('all',this)">전체</button>
            <button class="filter-tab" onclick="filterAdmin('confirmed',this)">확정</button>
          </div>
          <button class="btn btn-sm btn-danger" onclick="clearAllConfirm()">전체 데이터 삭제</button>
        </div>
        <div id="admin-table-wrap"></div>
      </div>
      <div id="admin-calendar-view" style="display:none">
        <div id="admin-calendar"></div>
      </div>
    </div>
  </div>
</main>

<script type="module">
// Firebase SDK Import
import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
import { getFirestore, collection, addDoc, getDocs, deleteDoc, doc } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

// Firebase 설정 및 로컬 폴백 상태
let db, auth, bookingsCollection;
let currentUser = null;
let useLocalDB = false; // 로컬 환경 실행 확인용

async function initFirebase() {
    try {
        const configStr = typeof __firebase_config !== 'undefined' ? __firebase_config : null;
        
        // 로컬(메모장)에서 실행하여 config가 없는 경우 로컬 스토리지 모드로 전환
        if (!configStr || configStr === '{}') {
            console.warn("클라우드 DB 설정이 없습니다. 로컬 테스트 모드로 실행합니다.");
            useLocalDB = true;
            return null;
        }

        const firebaseConfig = JSON.parse(configStr);
        const app = initializeApp(firebaseConfig);
        auth = getAuth(app);
        db = getFirestore(app);
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        
        // 필수 규칙: 경로 준수
        bookingsCollection = collection(db, 'artifacts', appId, 'public', 'data', 'bookings');

        if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
            await signInWithCustomToken(auth, __initial_auth_token);
        } else {
            await signInAnonymously(auth);
        }

        return new Promise((resolve) => {
            onAuthStateChanged(auth, (user) => {
                currentUser = user;
                resolve(user);
            });
        });
    } catch (error) {
        console.error("Firebase 초기화 실패:", error);
        useLocalDB = true;
        return null;
    }
}

// ═══════════════════════════ CONSTANTS & DATES ═══════════════════════════
const HOURS = [];
for (let h = 9; h <= 20; h++) {
  const period = h < 12 ? '오전' : '오후';
  const h12 = h <= 12 ? h : h - 12;
  HOURS.push({ label: `${period} ${h12}시`, period, h12, h24: h });
}
const DAYS_KO = ['일','월','화','수','목','금','토'];

// 6월 22일부터 7월 6일(2026년)까지로 날짜 고정
function getAllDates() {
  const out = [];
  // JavaScript Date 객체에서 월(Month)은 0부터 시작하므로 5는 6월, 6은 7월입니다.
  const d = new Date(2026, 5, 22); 
  const end = new Date(2026, 6, 6); 
  
  while (d <= end) { 
      out.push(new Date(d)); 
      d.setDate(d.getDate()+1); 
  }
  return out;
}
const ALL_DATES = getAllDates();

function dk(d) { return `${d.getFullYear()}-${String(d.getMonth()+1).padStart(2,'0')}-${String(d.getDate()).padStart(2,'0')}`; }
function fmtDate(key) {
  const parts = key.split('-'); const dt = new Date(key);
  return `${parseInt(parts[1])}월 ${parseInt(parts[2])}일 (${DAYS_KO[dt.getDay()]})`;
}
function fmtHour(hi) { return HOURS[hi].label; }
function fmtSlot(key, hi) { return `${fmtDate(key)} ${fmtHour(hi)}`; }

// 동적 안내 문구 설정 (항상 6월 22일 ~ 7월 6일로 렌더링됨)
const startDateStr = `${ALL_DATES[0].getMonth()+1}월 ${ALL_DATES[0].getDate()}일`;
const endDateStr = `${ALL_DATES[ALL_DATES.length-1].getMonth()+1}월 ${ALL_DATES[ALL_DATES.length-1].getDate()}일`;
document.getElementById('date-range-text').textContent = `${startDateStr} ~ ${endDateStr}`;

// ═══════════════════════════ STATE ═══════════════════════════
let landlordSlots = {};
let tenantSlots   = {};
let allBookings   = [];
let currentFilter = 'all';

// ═══════════════════════════ GEMINI API ═══════════════════════════
const apiKey = ""; 

async function callGeminiAPI(prompt) {
    const url = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${apiKey}`;
    const payload = {
        contents: [{ parts: [{ text: prompt }] }],
        systemInstruction: { parts: [{ text: "당신은 한국의 친절하고 전문적인 부동산 임대관리 어시스턴트입니다." }] }
    };

    const delays = [1000, 2000, 4000, 8000, 16000];
    for (let i = 0; i < 5; i++) {
        try {
            const response = await fetch(url, {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify(payload)
            });
            if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
            const data = await response.json();
            return data.candidates?.[0]?.content?.parts?.[0]?.text || "AI 응답을 생성하지 못했습니다.";
        } catch (error) {
            if (i === 4) {
                console.error("Gemini API Error after retries:", error);
                return "죄송합니다. 현재 AI 서비스를 이용할 수 없습니다.";
            }
            await new Promise(r => setTimeout(r, delays[i]));
        }
    }
}

window.generateAISMS = async () => {
    const btn = document.getElementById('ai-sms-btn');
    const promptInput = document.getElementById('ai-sms-prompt').value;
    const textArea = document.getElementById('sms-text');
    
    btn.disabled = true;
    btn.innerHTML = '<span class="ai-loading"></span> 생성 중...';
    
    const currentDraft = textArea.value;
    const prompt = `다음은 부동산 방문 일정을 잡기 위해 임차인에게 보낼 문자 메시지 초안입니다.\n\n초안:\n${currentDraft}\n\n위 초안의 '예약 링크 URL'과 '제안 일정 요약' 정보는 절대 누락하거나 훼손하지 말고 꼭 포함하세요. 대신 사용자의 추가 요청사항에 맞게 문장의 어투나 내용을 자연스럽게 수정해서 문자 메시지를 완성해주세요. 출력은 부가 설명 없이 문자 메시지 본문만 작성하세요.\n\n사용자 추가 요청사항: ${promptInput || '더 부드럽고 친절한 어투로 다듬어줘.'}`;
    
    const result = await callGeminiAPI(prompt);
    textArea.value = result;
    
    btn.disabled = false;
    btn.innerHTML = '✨ AI 맞춤 문자 재생성';
};

window.generateAIChecklist = async (landlord, tenant, time, memo) => {
    const btn = document.getElementById('ai-check-btn');
    const resultDiv = document.getElementById('ai-checklist-result');
    
    btn.disabled = true;
    btn.innerHTML = '<span class="ai-loading"></span> 생성 중...';
    resultDiv.innerHTML = '<div style="text-align:center; padding:20px; color:var(--muted);">AI가 꼼꼼하게 체크리스트를 작성하고 있습니다...</div>';
    
    const prompt = `당신은 꼼꼼한 부동산 중개 전문가입니다. 다음 현장 방문 예약 정보를 바탕으로 임대인과 임차인이 확인할 체크리스트를 작성해주세요. 깔끔한 HTML 태그(ul, li, strong, br 등)를 사용하여 보기 좋게 작성하세요. 마크다운 기호(*) 대신 직접 HTML 태그를 사용해주세요.\n\n[예약 정보]\n- 임대인: ${landlord}\n- 임차인: ${tenant}\n- 방문 시간: ${time}\n- 특이사항(메모): ${memo || '없음'}\n\n[요청사항]\n1. 임대인이 미리 준비할 사항\n2. 임차인이 확인할 사항(수압, 채광 등 일반적인 것 포함)\n3. 특이사항(메모)에 대한 구체적인 조언`;
    
    const result = await callGeminiAPI(prompt);
    resultDiv.innerHTML = result.replace(/```html|```/g, ''); 
    
    btn.disabled = false;
    btn.innerHTML = '✨ 다시 생성하기';
};

// ═══════════════════════════ FIRESTORE STORAGE ═══════════════════════════
async function saveBooking(booking) {
    if (useLocalDB) {
        const arr = JSON.parse(localStorage.getItem('local_bookings') || '[]');
        booking.id = Date.now().toString(36);
        arr.push(booking);
        localStorage.setItem('local_bookings', JSON.stringify(arr));
        return true;
    }
    if (!currentUser) return false;
    try {
        await addDoc(bookingsCollection, booking);
        return true;
    } catch (e) {
        console.error("Firestore Save Error:", e);
        return false;
    }
}

async function loadBookings() {
    if (useLocalDB) {
        return JSON.parse(localStorage.getItem('local_bookings') || '[]');
    }
    if (!currentUser) return [];
    try {
        const querySnapshot = await getDocs(bookingsCollection);
        const arr = [];
        querySnapshot.forEach((doc) => {
            arr.push({ id: doc.id, ...doc.data() });
        });
        return arr;
    } catch (e) {
        console.error("Firestore Load Error:", e);
        return [];
    }
}

async function deleteBookingById(id) {
    if (useLocalDB) {
        const arr = JSON.parse(localStorage.getItem('local_bookings') || '[]');
        const filtered = arr.filter(b => b.id !== id);
        localStorage.setItem('local_bookings', JSON.stringify(filtered));
        return;
    }
    if (!currentUser) return;
    try {
        await deleteDoc(doc(bookingsCollection, id));
    } catch (e) {
        console.error("Firestore Delete Error:", e);
    }
}

async function clearAllCloudData() {
    if (useLocalDB) {
        localStorage.removeItem('local_bookings');
        return;
    }
    if (!currentUser) return;
    try {
        const querySnapshot = await getDocs(bookingsCollection);
        const deletePromises = [];
        querySnapshot.forEach((docSnap) => {
            deletePromises.push(deleteDoc(doc(bookingsCollection, docSnap.id)));
        });
        await Promise.all(deletePromises);
    } catch (e) {
        console.error("Firestore Clear All Error:", e);
    }
}

// ═══════════════════════════ DATE SECTION (UI) ═══════════════════════════
function buildDateSection(key, dateObj, role) {
  const dow = dateObj.getDay();
  const slotMap = role === 'landlord' ? landlordSlots : tenantSlots;
  if (!slotMap[key]) slotMap[key] = new Set();
  const selSet = slotMap[key];
  const section = document.createElement('div');
  section.className = 'date-section';
  const hdr = document.createElement('div');
  hdr.className = 'date-header';
  const circle = document.createElement('div');
  circle.className = 'dow-circle' + (dow===0?' sun':dow===6?' sat':'');
  circle.textContent = DAYS_KO[dow];
  const dtxt = document.createElement('div');
  dtxt.className = 'date-text';
  dtxt.innerHTML = `<div class="d-main">${dateObj.getMonth()+1}월 ${dateObj.getDate()}일</div><div class="d-sub">${dateObj.getFullYear()}</div>`;
  hdr.appendChild(circle);
  hdr.appendChild(dtxt);
  const badgePlaceholder = document.createElement('span');
  hdr.appendChild(badgePlaceholder);
  const chev = document.createElement('span');
  chev.className = 'chevron'; chev.textContent = '▼';
  hdr.appendChild(chev);
  const panel = document.createElement('div');
  panel.className = 'time-panel' + (role==='tenant'?' t-border':'');
  const grid = document.createElement('div');
  grid.className = 'time-grid';
  HOURS.forEach((h, hi) => {
    const slot = document.createElement('div');
    slot.className = 'time-slot';
    slot.innerHTML = `<div class="time-main">${h.period} ${h.h12}시</div><div class="time-sub">${h.h24}:00</div>`;
    if (role === 'landlord') {
      if (selSet.has(hi)) slot.classList.add('l-sel');
      slot.addEventListener('click', () => {
        if (selSet.has(hi)) { selSet.delete(hi); slot.classList.remove('l-sel'); }
        else { selSet.add(hi); slot.classList.add('l-sel'); }
        updateHdrBadge(hdr, circle, selSet, role, dow);
        updateLCount();
      });
    } else {
      const lSet = landlordSlots[key] || new Set();
      if (!lSet.has(hi)) slot.classList.add('disabled');
      else {
        if (selSet.has(hi)) slot.classList.add('t-sel');
        slot.addEventListener('click', () => {
          if (selSet.has(hi)) { selSet.delete(hi); slot.classList.remove('t-sel'); }
          else { selSet.add(hi); slot.classList.add('t-sel'); }
          updateHdrBadge(hdr, circle, selSet, role, dow);
        });
      }
    }
    grid.appendChild(slot);
  });
  panel.appendChild(grid);
  hdr.addEventListener('click', () => {
    const isOpen = panel.classList.contains('visible');
    if (isOpen) { panel.classList.remove('visible'); hdr.classList.remove('open','tenant-open'); chev.style.transform = ''; }
    else { panel.classList.add('visible'); hdr.classList.add(role==='landlord'?'open':'tenant-open'); chev.style.transform = 'rotate(180deg)'; }
  });
  updateHdrBadge(hdr, circle, selSet, role, dow);
  if (selSet.size > 0) { panel.classList.add('visible'); hdr.classList.add(role==='landlord'?'open':'tenant-open'); chev.style.transform='rotate(180deg)'; }
  section.appendChild(hdr);
  section.appendChild(panel);
  return section;
}

function updateHdrBadge(hdr, circle, selSet, role, dow) {
  circle.className = 'dow-circle'+(dow===0?' sun':dow===6?' sat':'');
  if (selSet.size > 0) circle.classList.add(role==='landlord'?'sel-l':'sel-t');
  let badge = hdr.querySelector('.sel-badge');
  if (!badge) { badge = document.createElement('span'); badge.className='sel-badge'; hdr.insertBefore(badge, hdr.querySelector('.chevron')); }
  if (selSet.size > 0) { badge.className='sel-badge '+(role==='landlord'?'sel-badge-l':'sel-badge-t'); badge.textContent=`${selSet.size}개 선택`; badge.style.display=''; }
  else badge.style.display='none';
}

function buildLandlordList() {
  const el = document.getElementById('landlord-list'); el.innerHTML='';
  ALL_DATES.forEach(d => { const key=dk(d); if(!landlordSlots[key]) landlordSlots[key]=new Set(); el.appendChild(buildDateSection(key,d,'landlord')); });
  updateLCount();
}

function updateLCount() {
  let total=0; Object.values(landlordSlots).forEach(s=>total+=s.size);
  document.getElementById('l-count').textContent=`${total}개 시간대 선택됨`;
  document.getElementById('l-next-btn').disabled=total===0;
}

function buildTenantList() {
  tenantSlots={};
  const el=document.getElementById('tenant-list'); el.innerHTML='';
  const strip=document.getElementById('t-info-strip');
  let lines=[];
  ALL_DATES.forEach(d => { const key=dk(d); const s=landlordSlots[key]; if(s&&s.size>0) { const times=[...s].sort((a,b)=>a-b).map(hi=>fmtHour(hi)).join('  /  '); lines.push(`<strong>${fmtDate(key)}</strong><br><span style="color:var(--landlord);font-size:12.5px">${times}</span>`); } });
  strip.innerHTML=`<strong>📅 임대인 제안 일정</strong><br><br>${lines.join('<br><br>')}`;
  ALL_DATES.forEach(d => { const key=dk(d); const s=landlordSlots[key]; if(!s||s.size===0) return; tenantSlots[key]=new Set(); el.appendChild(buildDateSection(key,d,'tenant')); });
}

// ═══════════════════════════ STEP NAV ═══════════════════════════
function setStep(n) {
  ['view1','view2','view3','view4','view-admin'].forEach((id,i) => document.getElementById(id).classList.remove('active'));
  const viewId = n==='admin'?'view-admin':`view${n}`;
  document.getElementById(viewId).classList.add('active');
  const isAdmin = n==='admin';
  document.getElementById('progress-bar').style.display = isAdmin?'none':'';
  document.getElementById('main-header').className = isAdmin?'admin-header':'';
  document.getElementById('header-icon').className = isAdmin?'header-icon admin-icon':'header-icon';
  document.getElementById('header-icon').textContent = isAdmin?'⚙️':'🏠';
  document.getElementById('header-title').textContent = isAdmin?'관리자 대시보드':'만남 예약 시스템';
  document.getElementById('header-sub').textContent = isAdmin?'실시간 예약 현황을 확인하고 관리합니다':'임대인과 임차인의 일정을 간편하게 맞춰드립니다';
  if (!isAdmin) {
    [1,2,3,4].forEach(i => { const t=document.getElementById(`tab${i}`); t.classList.remove('active','done'); if(i===n)t.classList.add('active'); else if(i<n)t.classList.add('done'); });
  }
  window.scrollTo({top:0,behavior:'smooth'});
}

window.goStep1 = () => { setStep(1); buildLandlordList(); };
window.goAdmin = () => { setStep('admin'); loadAdminData(); };

window.goStep2 = () => {
  setStep(2);
  const parts=[];
  ALL_DATES.forEach(d => { const key=dk(d); const s=landlordSlots[key]; if(s&&s.size>0) parts.push(key+':'+[...s].sort((a,b)=>a-b).join(',')); });
  const payload=encodeURIComponent(parts.join(';'));
  
  const base=location.href.split('?')[0];
  const url=`${base}?step=3&slots=${payload}`;
  
  document.getElementById('share-url').textContent=url;
  
  const lines=[];
  ALL_DATES.forEach(d => { const key=dk(d); const s=landlordSlots[key]; if(s&&s.size>0) { const times=[...s].sort((a,b)=>a-b).map(hi=>fmtHour(hi)).join(' / '); lines.push(`  📅 ${fmtDate(key)}\n     ${times}`); } });
  document.getElementById('sms-text').value=`안녕하세요 😊\n아래 링크로 접속하셔서 임대인 방문 가능 시간 중\n원하시는 시간을 선택해 주시면 일정을 확정하겠습니다.\n\n[ 제안 일정 요약 ]\n${lines.join('\n\n')}\n\n▶ 임차인 전용 예약 링크 (클릭)\n${url}`;
};

window.simulateTenantClick = () => {
    setStep(3); 
    buildTenantList();
};

window.goStep3 = () => { setStep(3); buildTenantList(); };
window.goStep4 = () => { setStep(4); showResult(); };

// ═══════════════════════════ RESULT ═══════════════════════════
function showResult() {
  const matches=[];
  ALL_DATES.forEach(d => { const key=dk(d); const ls=landlordSlots[key]||new Set(); const ts=tenantSlots[key]||new Set(); [...ls].sort((a,b)=>a-b).forEach(hi=>{ if(ts.has(hi)) matches.push({key,hi}); }); });
  const lSum=[]; ALL_DATES.forEach(d=>{ const key=dk(d); const s=landlordSlots[key]; if(s&&s.size>0) lSum.push(`${fmtDate(key)}: ${[...s].sort((a,b)=>a-b).map(hi=>fmtHour(hi)).join(', ')}`); });
  const tSum=[]; ALL_DATES.forEach(d=>{ const key=dk(d); const s=tenantSlots[key]; if(s&&s.size>0) tSum.push(`${fmtDate(key)}: ${[...s].sort((a,b)=>a-b).map(hi=>fmtHour(hi)).join(', ')}`); });
  const card=document.getElementById('result-card');
  let html='';
  if (matches.length>0) {
    html+=`<div class="result-hero"><div class="emoji">🎉</div><h3>방문 일정을 선택하셨습니다!</h3><p>아래에서 최종 확정할 시간을 선택하고 예약 정보를 입력해주세요.</p></div>
    <ul class="match-list">${matches.map(({key,hi},idx)=>`<li class="match-item"><span>✅</span><span>${fmtDate(key)}</span><strong style="margin-left:auto;color:var(--match)">${fmtHour(hi)}</strong></li>`).join('')}</ul>`;
    html+=`<div class="name-form">
      <h4>📝 예약 정보 입력 (클라우드 저장)</h4>
      <div class="form-row">
        <div class="form-group"><label>임대인 성명</label><input type="text" id="inp-landlord" placeholder="예: 홍길동"></div>
        <div class="form-group"><label>임차인 성명</label><input type="text" id="inp-tenant" placeholder="예: 김철수"></div>
        <div class="form-group"><label>연락처 (선택)</label><input type="text" id="inp-phone" placeholder="010-0000-0000"></div>
      </div>
      <div class="form-row">
        <div class="form-group" style="flex:2">
          <label>최종 확정 시간 선택</label>
          <select class="match-select" id="inp-time">
            ${matches.map(({key,hi})=>`<option value="${key}|${hi}">${fmtSlot(key,hi)}</option>`).join('')}
          </select>
        </div>
        <div class="form-group"><label>메모 (선택)</label><input type="text" id="inp-memo" placeholder="현장 방문 등"></div>
      </div>
      <div class="btn-row">
        <button class="btn btn-accent" onclick="confirmAndSave()" style="width:100%">✓ 예약 확정하고 관리자에게 전송</button>
      </div>
    </div>`;
  } else {
    html+=`<div class="no-match" style="text-align:center;padding:36px"><div style="font-size:38px;margin-bottom:12px">😓</div><p style="color:var(--muted);font-size:14px;line-height:1.8"><strong style="color:var(--ink);font-size:16px">선택하신 시간이 없습니다</strong><br><br>임대인이 제안한 시간 중 가능한 시간이 없다면<br>담당자에게 연락하여 다시 일정을 조율해 주세요.</p></div>`;
  }
  html+=`<div style="margin-top:20px;padding-top:18px;border-top:1px solid var(--line)"><p style="font-size:12px;font-weight:700;color:var(--muted);margin-bottom:8px">📋 선택 요약</p><div class="info-strip"><strong style="color:var(--landlord)">임대인 제안</strong><br><span style="color:var(--muted);font-size:12.5px">${lSum.join(' | ')||'없음'}</span><br><br><strong style="color:var(--tenant)">임차인 선택</strong><br><span style="color:var(--muted);font-size:12.5px">${tSum.join(' | ')||'없음'}</span></div></div>
  <div class="btn-row"><button class="btn btn-ghost" onclick="goStep1()">← 처음부터 다시하기</button></div>`;
  card.innerHTML=html;
}

window.confirmAndSave = async () => {
  const landlord=document.getElementById('inp-landlord')?.value?.trim()||'';
  const tenant=document.getElementById('inp-tenant')?.value?.trim()||'';
  const phone=document.getElementById('inp-phone')?.value?.trim()||'';
  const timeVal=document.getElementById('inp-time')?.value||'';
  const memo=document.getElementById('inp-memo')?.value?.trim()||'';
  
  if (!landlord||!tenant) { alert('임대인과 임차인 성명을 모두 입력해주세요.'); return; }
  if (!timeVal) { alert('확정 시간을 선택해주세요.'); return; }
  
  const [key,hiStr]=timeVal.split('|');
  const hi=parseInt(hiStr);
  
  const booking = {
    landlord, tenant, phone, memo,
    dateKey: key, 
    hourIndex: hi,
    confirmedAt: new Date().toISOString(),
    status: 'confirmed'
  };
  
  const btn = document.querySelector('#result-card .btn-accent');
  if (btn) { btn.disabled=true; btn.textContent='클라우드에 저장 중...'; }
  
  const ok = await saveBooking(booking);
  const card=document.getElementById('result-card');
  
  if (ok) {
    card.innerHTML=`<div style="text-align:center;padding:48px 24px">
      <div style="font-size:52px;margin-bottom:16px">🎊</div>
      <h3 style="font-size:22px;font-weight:700;color:var(--match);margin-bottom:10px">예약이 완벽하게 확정되었습니다!</h3>
      <p style="font-size:14px;color:var(--muted);line-height:1.8">
        <strong style="color:var(--ink)">${landlord}</strong> 님 (임대인) ↔ <strong style="color:var(--ink)">${tenant}</strong> 님 (임차인)<br>
        <strong style="color:var(--match);font-size:16px">${fmtSlot(key,hi)}</strong><br>
        관리자 대시보드에 성공적으로 등록되었습니다.
      </p>
      <div style="margin-top: 24px; padding: 20px; background: #f8f5ff; border: 1px solid #e9d8fd; border-radius: 12px; text-align: left;">
        <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:12px; flex-wrap:wrap; gap:8px;">
            <h4 style="color:#553c9a; margin:0; font-size:14.5px; font-weight:700;">📋 AI 현장 방문 가이드 & 체크리스트</h4>
            <button class="ai-btn" id="ai-check-btn" onclick="generateAIChecklist('${landlord}', '${tenant}', '${fmtSlot(key,hi)}', '${memo}')">✨ AI 체크리스트 생성</button>
        </div>
        <div id="ai-checklist-result" style="font-size:13px; color:var(--ink); line-height:1.6; white-space:pre-wrap; background:white; padding:16px; border-radius:8px;">버튼을 눌러 꼼꼼한 현장 확인 리스트와 조언을 받아보세요.</div>
      </div>
      <div class="btn-row" style="justify-content:center;margin-top:24px">
        <button class="btn btn-ghost" onclick="goStep1()">← 새 예약 만들기</button>
        <button class="btn btn-admin" onclick="goAdmin()">⚙️ 관리자 화면 확인</button>
      </div>
    </div>`;
  } else {
    alert('저장 중 오류가 발생했습니다. 다시 시도해주세요.');
    if(btn){btn.disabled=false;btn.textContent='✓ 예약 확정하고 관리자에게 전송';}
  }
};

// ═══════════════════════════ ADMIN ═══════════════════════════
window.loadAdminData = async () => {
  document.getElementById('admin-table-wrap').innerHTML='<div class="loading-spin">⏳ 클라우드에서 데이터 불러오는 중...</div>';
  allBookings = await loadBookings();
  renderAdminStats();
  renderAdminTable(currentFilter);
  renderAdminCalendar();
};

function renderAdminStats() {
  const total = allBookings.length;
  const confirmed = allBookings.filter(b=>b.status==='confirmed').length;
  
  document.getElementById('admin-stats-row').innerHTML=`
    <div class="stat-card c-total"><div class="stat-num">${total}</div><div class="stat-label">전체 예약 수</div></div>
    <div class="stat-card c-match"><div class="stat-num">${confirmed}</div><div class="stat-label">확정 완료</div></div>
    <div class="stat-card"><div class="stat-num" style="color:var(--ink);font-size:28px">${getThisWeekCount()}</div><div class="stat-label">최근 7일 확정</div></div>
  `;
}

function getThisWeekCount() {
  const now = new Date();
  const weekStart = new Date(now); weekStart.setDate(now.getDate()-7); // 최근 7일 기준으로 변경
  return allBookings.filter(b => { const d=new Date(b.confirmedAt); return d>=weekStart&&d<=now; }).length;
}

function renderAdminTable(filter) {
  currentFilter = filter;
  let data = allBookings;
  if (filter==='confirmed') data=data.filter(b=>b.status==='confirmed');
  
  data = [...data].sort((a,b)=>new Date(a.dateKey)-new Date(b.dateKey)||a.hourIndex-b.hourIndex);
  
  const wrap = document.getElementById('admin-table-wrap');
  if (data.length===0) {
    wrap.innerHTML=`<div class="empty-admin"><div class="emoji">📭</div><p>데이터베이스에 예약 내역이 없습니다.<br>예약이 확정되면 실시간으로 표시됩니다.</p></div>`;
    return;
  }
  
  let html=`<div class="admin-table-wrap"><table class="admin-table"><thead><tr>
    <th>#</th><th>날짜</th><th>시간</th><th>임대인</th><th>임차인</th><th>연락처</th><th>메모</th><th>상태</th><th>확정시간</th><th>관리</th>
  </tr></thead><tbody>`;
  
  data.forEach((b,i) => {
    const d = new Date(b.confirmedAt);
    const dateStr = `${d.getFullYear()}.${String(d.getMonth()+1).padStart(2,'0')}.${String(d.getDate()).padStart(2,'0')} ${String(d.getHours()).padStart(2,'0')}:${String(d.getMinutes()).padStart(2,'0')}`;
    const statusHtml = b.status==='confirmed'
      ? `<span class="time-chip">✅ 확정</span>`
      : `<span class="pending-chip">⏳ 대기</span>`;
      
    html+=`<tr>
      <td style="color:var(--muted);font-size:12px">${i+1}</td>
      <td style="font-weight:600">${fmtDate(b.dateKey)}</td>
      <td><span class="time-chip">${fmtHour(b.hourIndex)}</span></td>
      <td>${b.landlord||'-'}</td>
      <td>${b.tenant||'-'}</td>
      <td style="color:var(--muted);font-size:12px">${b.phone||'-'}</td>
      <td style="color:var(--muted);font-size:12px">${b.memo||'-'}</td>
      <td>${statusHtml}</td>
      <td style="color:var(--muted);font-size:11px">${dateStr}</td>
      <td><button class="del-btn" onclick="deleteConfirm('${b.id}')">삭제</button></td>
    </tr>`;
  });
  html+=`</tbody></table></div>`;
  wrap.innerHTML=html;
}

function renderAdminCalendar() {
  const cal = document.getElementById('admin-calendar');
  const byDate = {};
  allBookings.forEach(b => { if(!byDate[b.dateKey]) byDate[b.dateKey]=[]; byDate[b.dateKey].push(b); });

  // 6월과 7월 (2026년)로 달력 고정
  let html = `<div style="display:flex;gap:28px;margin-bottom:16px;flex-wrap:wrap">`;
  html += buildMonthHtml(2026, 5, byDate, `2026년 6월`); // 5 = 6월
  html += buildMonthHtml(2026, 6, byDate, `2026년 7월`); // 6 = 7월
  html += `</div>`;
  cal.innerHTML = html;
}

function buildMonthHtml(year, month, byDate, label) {
  const firstDay = new Date(year, month, 1).getDay();
  const daysInMonth = new Date(year, month+1, 0).getDate();
  
  const firstDateKey = dk(ALL_DATES[0]);
  const lastDateKey = dk(ALL_DATES[ALL_DATES.length-1]);
  
  let html = `<div style="flex:1;min-width:280px">
    <p style="font-size:13px;font-weight:700;margin-bottom:10px;color:var(--admin)">${label}</p>
    <div class="cal-grid">
      ${['일','월','화','수','목','금','토'].map((d,i)=>`<div class="cal-head${i===0?' sun':i===6?' sat':''}">${d}</div>`).join('')}
  `;
  for (let i=0;i<firstDay;i++) html+=`<div class="cal-cell out-range"></div>`;
  
  for (let d=1;d<=daysInMonth;d++) {
    const key=`${year}-${String(month+1).padStart(2,'0')}-${String(d).padStart(2,'0')}`;
    const events=byDate[key]||[];
    const inRange = key >= firstDateKey && key <= lastDateKey;
    
    if (events.length>0) {
      html+=`<div class="cal-cell has-event" title="클릭하여 상세 보기" onclick="showDateDetail('${key}')">
        <div class="cal-date-num">${d}</div>
        <div class="cal-dot"></div>
        <div class="cal-count">${events.length}건</div>
      </div>`;
    } else {
      html+=`<div class="cal-cell${!inRange?' out-range':''}"><div class="cal-date-num">${d}</div></div>`;
    }
  }
  html+=`</div></div>`;
  return html;
}

window.showDateDetail = (key) => {
  const events = allBookings.filter(b=>b.dateKey===key);
  if (!events.length) return;
  const lines = events.map(b=>`• ${b.landlord} ↔ ${b.tenant}  |  ${fmtHour(b.hourIndex)}${b.phone?' ('+b.phone+')':''}`).join('\n\n');
  alert(`📅 ${fmtDate(key)} 예약 현황\n\n${lines}`);
};

window.switchAdminTab = (tab, el) => {
  document.querySelectorAll('.view-tab').forEach(t=>t.classList.remove('active'));
  el.classList.add('active');
  document.getElementById('admin-list-view').style.display = tab==='list'?'':'none';
  document.getElementById('admin-calendar-view').style.display = tab==='calendar'?'':'none';
};

window.filterAdmin = (type, el) => {
  document.querySelectorAll('.filter-tab').forEach(t=>t.classList.remove('active'));
  el.classList.add('active');
  renderAdminTable(type);
};

window.deleteConfirm = async (id) => {
  if (!confirm('이 예약 데이터를 클라우드에서 완전히 삭제하시겠습니까?')) return;
  await deleteBookingById(id);
  await loadAdminData();
};

window.clearAllConfirm = async () => {
  if (!confirm('경고: 모든 예약 데이터를 데이터베이스에서 삭제합니다. 이 작업은 되돌릴 수 없습니다. 진행할까요?')) return;
  await clearAllCloudData();
  await loadAdminData();
};

// ═══════════════════════════ COPY ═══════════════════════════
window.copyUrl = (srcId, btnId) => {
  const url=document.getElementById(srcId).textContent;
  const btn=document.getElementById(btnId);
  const done=()=>{ btn.textContent='복사 완료 ✓'; btn.classList.add('copied'); setTimeout(()=>{ btn.textContent='복사'; btn.classList.remove('copied'); },2200); };
  if(navigator.clipboard) navigator.clipboard.writeText(url).then(done).catch(()=>{fbCopy(url);done();});
  else{fbCopy(url);done();}
};

function fbCopy(t){const ta=document.createElement('textarea');ta.value=t;document.body.appendChild(ta);ta.select();document.execCommand('copy');document.body.removeChild(ta);}

// ═══════════════════════════ INIT ═══════════════════════════
async function init() {
  // 1. Firebase 연결 대기
  await initFirebase();
  document.getElementById('loading-overlay').style.display = 'none';

  // 2. URL 파라미터 분석하여 적절한 화면 보여주기
  const p=new URLSearchParams(location.search);
  
  if (p.get('step')==='admin'||p.get('admin')) { 
      goAdmin(); 
      return; 
  }
  
  if (p.get('step')==='3'&&p.get('slots')) {
    landlordSlots={};
    decodeURIComponent(p.get('slots')).split(';').forEach(part=>{
      if(!part.includes(':')) return;
      const [key,hisStr]=part.split(':');
      if(key&&hisStr) landlordSlots[key]=new Set(hisStr.split(',').map(Number).filter(n=>n>=0&&n<=11));
    });
    setStep(3); 
    buildTenantList(); 
    return;
  }
  
  // 기본: Step 1 (임대인)
  setStep(1); 
  buildLandlordList();
}

// 앱 시작
init();
</script>
</body>
</html>
