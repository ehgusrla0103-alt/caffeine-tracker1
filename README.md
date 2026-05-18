<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>카페인 트래커</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@300;400;500;700&family=DM+Mono:wght@400;500&display=swap" rel="stylesheet">
<style>
  /* 기본 색상 팔레트 및 폰트 설정은 원본 유지 */
  :root {
    --bg: #0F0F0F;
    --surface: #181818;
    --surface2: #222;
    --border: rgba(255,255,255,0.07);
    --border2: rgba(255,255,255,0.14);
    --text: #F0EDE6;
    --text2: #9A9590;
    --text3: #5C5A57;
    --safe: #7DBF5C;
    --safe-bg: rgba(125,191,92,0.10);
    --safe-border: rgba(125,191,92,0.30);
    --warn: #E8A84A;
    --warn-bg: rgba(232,168,74,0.10);
    --warn-border: rgba(232,168,74,0.30);
    --danger: #E8574A;
    --danger-bg: rgba(232,87,74,0.10);
    --danger-border: rgba(232,87,74,0.30);
    --accent: #C8A96E;
  }

  * { box-sizing: border-box; margin: 0; padding: 0; }
  html { font-size: 15px; }
  /* 1) 파란 글씨 제거를 위한 기본 링크 스타일 재정의 */
  a { color: inherit; text-decoration: none; }
  a:visited { color: inherit; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'Noto Sans KR', sans-serif;
    min-height: 100vh;
    padding-bottom: 3rem;
  }

  /* ── HEADER ── */
  .header {
    border-bottom: 1px solid var(--border);
    padding: 1.5rem 2rem;
    display: flex;
    align-items: baseline;
    gap: 1rem;
  }
  .header-title {
    font-family: 'DM Mono', monospace;
    font-size: 1.05rem;
    font-weight: 500;
    letter-spacing: 0.04em;
    color: var(--accent);
  }
  .header-sub {
    font-size: 0.75rem;
    color: var(--text3);
    letter-spacing: 0.02em;
  }

  /* ── LAYOUT 공통 ── */
  .container { max-width: 860px; margin: 0 auto; padding: 0 1.5rem; }
  .main-grid {
    display: grid;
    grid-template-columns: 1fr 340px;
    gap: 1.5rem;
    margin-top: 1.5rem;
    align-items: start;
  }
  @media (max-width: 900px) {
    .main-grid { grid-template-columns: 1fr; }
  }

  /* ── SECTION LABELS ── */
  .sec-label {
    font-family: 'DM Mono', monospace;
    font-size: 0.65rem;
    letter-spacing: 0.12em;
    text-transform: uppercase;
    color: var(--text3);
    margin-bottom: 0.75rem;
  }

  /* ── STEP BOX ── */
  .step-box {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 1.25rem;
    margin-bottom: 1.25rem;
  }

  /* ── BRAND GRID ── */
  .brand-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(110px, 1fr));
    gap: 8px;
  }
  .brand-btn {
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 10px 8px;
    cursor: pointer;
    transition: border-color 0.15s, background 0.15s;
    text-align: center;
  }
  .brand-btn:hover { border-color: var(--border2); background: #2A2A2A; }
  .brand-btn.active {
    border-color: var(--accent);
    background: rgba(200,169,110,0.10);
  }
  .brand-icon { font-size: 1.4rem; margin-bottom: 4px; }
  .brand-name-label {
    font-size: 0.72rem;
    color: var(--text2);
    font-weight: 500;
    line-height: 1.3;
  }
  .brand-btn.active .brand-name-label { color: var(--accent); }

  /* ── MENU LIST ── */
  .menu-list {
    display: flex;
    flex-direction: column;
    gap: 6px;
  }
  .menu-item {
    display: flex;
    align-items: center;
    gap: 10px;
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 10px 12px;
    cursor: pointer;
    transition: border-color 0.15s;
  }
  .menu-item:hover { border-color: var(--border2); }
  .menu-name { flex: 1; font-size: 0.87rem; }
  .menu-mg {
    font-family: 'DM Mono', monospace;
    font-size: 0.75rem;
    color: var(--text3);
  }
  .menu-add-btn {
    background: none;
    border: 1px solid var(--border2);
    border-radius: 5px;
    color: var(--text2);
    font-size: 1rem;
    width: 24px; height: 24px;
    cursor: pointer;
    display: flex; align-items: center; justify-content: center;
    flex-shrink: 0;
    transition: border-color 0.15s, color 0.15s;
  }
  .menu-add-btn:hover { border-color: var(--accent); color: var(--accent); }

  /* custom input */
  .custom-row {
    display: flex; gap: 8px; margin-top: 8px;
  }
  .custom-row input {
    flex: 1; background: var(--surface2); border: 1px solid var(--border);
    border-radius: 8px; color: var(--text); font-family: inherit;
    font-size: 0.82rem; padding: 8px 12px; outline: none;
    transition: border-color 0.15s;
  }
  .custom-row input:focus { border-color: var(--border2); }
  .custom-row input[type=number] { max-width: 80px; }
  .custom-row button {
    background: var(--surface2); border: 1px solid var(--border);
    border-radius: 8px; color: var(--text2); font-family: inherit;
    font-size: 0.82rem; padding: 8px 14px; cursor: pointer;
    white-space: nowrap; transition: border-color 0.15s, color 0.15s;
  }
  .custom-row button:hover { border-color: var(--accent); color: var(--accent); }

  /* ── LOG ── */
  .log-empty {
    font-size: 0.8rem; color: var(--text3);
    text-align: center; padding: 1.5rem 0;
  }
  .log-item {
    display: flex; align-items: center; gap: 8px;
    padding: 8px 0;
    border-bottom: 1px solid var(--border);
    font-size: 0.84rem;
  }
  .log-item:last-child { border-bottom: none; }
  .log-brand-tag {
    font-size: 0.68rem; background: var(--surface2);
    border: 1px solid var(--border); border-radius: 4px;
    padding: 2px 6px; color: var(--text3); white-space: nowrap;
  }
  .log-name { flex: 1; color: var(--text2); }
  .log-mg {
    font-family: 'DM Mono', monospace;
    font-size: 0.8rem; color: var(--text2); min-width: 55px; text-align: right;
  }
  .log-qty { display: flex; align-items: center; gap: 5px; }
  .log-qty-btn {
    width: 20px; height: 20px; border: 1px solid var(--border2);
    border-radius: 4px; background: none; color: var(--text2);
    font-size: 0.9rem; cursor: pointer; display: flex; align-items: center; justify-content: center;
    transition: border-color 0.12s;
  }
  .log-qty-btn:hover { border-color: var(--accent); color: var(--accent); }
  .log-qty-num {
    font-family: 'DM Mono', monospace;
    font-size: 0.75rem; min-width: 14px; text-align: center;
  }
  .log-del {
    background: none; border: none; color: var(--text3);
    font-size: 1rem; cursor: pointer; padding: 0 2px;
    transition: color 0.12s;
  }
  .log-del:hover { color: var(--danger); }

  /* ── RESULT CARD ── */
  .result-card {
    border-radius: 12px;
    padding: 1.25rem;
    border: 1px solid var(--border);
    transition: border-color 0.4s, background 0.4s;
    margin-bottom: 1.25rem;
  }
  .result-card.safe { background: var(--safe-bg); border-color: var(--safe-border); }
  .result-card.warn { background: var(--warn-bg); border-color: var(--warn-border); }
  .result-card.danger { background: var(--danger-bg); border-color: var(--danger-border); }

  .result-top { display: flex; align-items: baseline; gap: 6px; margin-bottom: 4px; }
  .result-num {
    font-family: 'DM Mono', monospace;
    font-size: 2.4rem; font-weight: 500; line-height: 1;
    transition: color 0.3s;
  }
  .result-card.safe .result-num { color: var(--safe); }
  .result-card.warn .result-num { color: var(--warn); }
  .result-card.danger .result-num { color: var(--danger); }
  .result-unit { font-size: 0.8rem; color: var(--text2); }

  .meter-wrap {
    height: 6px; border-radius: 6px;
    background: rgba(255,255,255,0.06);
    overflow: hidden; margin: 10px 0 6px;
  }
  .meter-fill {
    height: 100%; border-radius: 6px;
    transition: width 0.5s cubic-bezier(.4,0,.2,1), background 0.4s;
  }
  .result-status {
    font-size: 0.82rem; font-weight: 500;
    transition: color 0.3s;
  }
  .result-card.safe .result-status { color: var(--safe); }
  .result-card.warn .result-status { color: var(--warn); }
  .result-card.danger .result-status { color: var(--danger); }

  /* ── TIPS ── */
  .tips-box {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 1.25rem;
    margin-bottom: 1.25rem;
  }
  .tip-row {
    display: flex; gap: 10px; padding: 8px 0;
    border-bottom: 1px solid var(--border);
    font-size: 0.82rem; line-height: 1.55; color: var(--text2);
  }
  .tip-row:last-child { border-bottom: none; padding-bottom: 0; }
  .tip-row:first-child { padding-top: 0; }
  .tip-icon { flex-shrink: 0; width: 18px; font-size: 0.8rem; margin-top: 1px; }

  /* ── SIDE ── */
  .side-sticky { position: sticky; top: 1.5rem; }

  /* ── INFO NOTE ── */
  .info-note {
    font-size: 0.72rem; color: var(--text3);
    line-height: 1.6; border-top: 1px solid var(--border);
    padding-top: 0.75rem; margin-top: 0.5rem;
  }

  /* ── DIVIDER ── */
  .divider { height: 1px; background: var(--border); margin: 1rem 0; }

  /* ── RESET BTN ── */
  .reset-btn {
    width: 100%; background: none;
    border: 1px solid var(--border); border-radius: 8px;
    color: var(--text3); font-family: inherit;
    font-size: 0.78rem; padding: 9px; cursor: pointer;
    transition: border-color 0.15s, color 0.15s;
    letter-spacing: 0.04em;
  }
  .reset-btn:hover { border-color: var(--border2); color: var(--text2); }

  /* ── PLACEHOLDER for no brand ── */
  #menuSection { min-height: 40px; }
  .placeholder-msg {
    font-size: 0.8rem; color: var(--text3);
    padding: 0.75rem 0;
  }

  /* 2) 비율 고정을 위한 컨테이너 너비 제한 추가(필요시 제거 가능) */
  /* 이미 .container의 max-width를 860px로 설정해 두었고, .main-grid로 양쪽 비율을 관리합니다. */

</style>
</head>
<body>

<div class="header">
  <span class="header-title">☕ Caffeine Tracker</span>
  <span class="header-sub">식품의약품안전처 기준 · 성인 1일 권고량 400mg</span>
</div>

<div class="container">
  <div class="main-grid">

    <!-- ── LEFT COLUMN ── -->
    <div class="left-col">

      <!-- STEP 1: 브랜드 -->
      <div class="step-box">
        <div class="sec-label">01 — 브랜드 선택</div>
        <div class="brand-grid" id="brandGrid"></div>
      </div>

      <!-- STEP 2: 메뉴 -->
      <div class="step-box">
        <div class="sec-label">02 — 메뉴 선택</div>
        <div id="menuSection">
          <div class="placeholder-msg">위에서 브랜드를 먼저 선택해주세요.</div>
        </div>
      </div>

      <!-- STEP 3: 오늘 목록 -->
      <div class="step-box">
        <div class="sec-label">03 — 오늘 마신 음료</div>
        <div id="logList"><div class="log-empty">아직 추가된 음료가 없어요.</div></div>
        <div class="divider" style="margin-top:0.75rem;margin-bottom:0.75rem;"></div>
        <button class="reset-btn" onclick="resetAll()">초기화</button>
      </div>

    </div><!-- /left -->

    <!-- ── RIGHT COLUMN ── -->
    <div class="side-sticky">

      <!-- RESULT -->
      <div class="result-card" id="resultCard">
        <div class="result-top">
          <span class="result-num" id="resultNum">0</span>
          <span class="result-unit">mg</span>
        </div>
        <div class="meter-wrap">
          <div class="meter-fill" id="meterFill" style="width:0%;background:var(--safe)"></div>
        </div>
        <div class="result-status" id="resultStatus">음료를 추가하면 결과가 나타나요.</div>
        <div class="info-note" id="infoNote">
          일일 권고량: 성인 400mg · 임산부 300mg<br>
          어린이·청소년 체중 ×2.5mg<br>
          <span style="color:var(--text3)">출처: 식품의약품안전처</span>
        </div>
      </div>

      <!-- TIPS -->
      <div class="tips-box" id="tipsBox" style="display:none;">
        <div class="sec-label" style="margin-bottom:0.5rem;">대체제 &amp; 습관 추천</div>
        <div id="tipsList"></div>
      </div>

    </div><!-- /right -->

  </div>
</div>

<script>
/* ================================================================
   DATA
================================================================ */
const BRANDS = [
  {
    id: 'cvs', icon: '🏪', name: '편의점',
    menus: [
      { name: '몬스터 에너지', mg: 160 },
      { name: '코카콜라 (355ml)', mg: 34 },
      { name: '핫식스', mg: 60 },
      { name: '박카스', mg: 30 },
      { name: '조지아 크래프트 커피', mg: 262 },
      { name: '스타벅스 병커피', mg: 103 },
      { name: '녹차 캔', mg: 20 },
      { name: '스누피 커피', mg: 237 },
      { name: '빙그레 아카페라', mg: 75 },
      { name: '캔커피 (일반)', mg: 74 },
      { name: '초콜릿 100g', mg: 25 },
    ]
  },
  {
    id: 'compose', icon: '🟡', name: '컴포즈',
    menus: [
      { name: '아메리카노', mg: 156 },
      { name: '카페라떼', mg: 156 },
      { name: '아샷추', mg: 95 },
      { name: '에스프레소', mg: 78 },
      { name: '카라멜 마끼야또', mg: 130 },
      { name: '콜드브루', mg: 185 },
    ],
    hasCustom: true
  },
  {
    id: 'ediya', icon: '🔵', name: '이디야',
    menus: [
      { name: '아메리카노 (레귤러)', mg: 145 },
      { name: '카페라떼', mg: 145 },
      { name: '아샷추', mg: 100 },
      { name: '에스프레소', mg: 75 },
      { name: '카라멜 마끼야또', mg: 120 },
      { name: '콜드브루', mg: 175 },
    ],
    hasCustom: true
  },
  {
    id: 'twosome', icon: '🍓', name: '투썸',
    menus: [
      { name: '아메리카노 (레귤러)', mg: 177 },
      { name: '카페라떼', mg: 177 },
      { name: '아샷추', mg: 110 },
      { name: '에스프레소', mg: 90 },
      { name: '카라멜 마끼야또', mg: 150 },
      { name: '콜드브루', mg: 220 },
    ],
    hasCustom: true
  },
  {
    id: 'mega', icon: '🟠', name: '메가커피',
    menus: [
      { name: '아메리카노', mg: 193 },
      { name: '카페라떼', mg: 193 },
      { name: '아샷추', mg: 120 },
      { name: '에스프레소', mg: 96 },
      { name: '카라멜 마끼야또', mg: 160 },
      { name: '콜드브루', mg: 210 },
    ],
    hasCustom: true
  },
  {
    id: 'ten', icon: '🔟', name: '텐퍼센트',
    menus: [
      { name: '아메리카노', mg: 140 },
      { name: '카페라떼', mg: 140 },
      { name: '아샷추', mg: 95 },
      { name: '에스프레소', mg: 70 },
      { name: '카라멜 마끼야또', mg: 120 },
      { name: '콜드브루', mg: 180 },
    ],
    hasCustom: true
  },
  {
    id: 'sbux', icon: '🟢', name: '스타벅스',
    menus: [
      { name: '아메리카노 (톨)', mg: 150 },
      { name: '카페라떼 (톨)', mg: 150 },
      { name: '아샷추 (톨)', mg: 105 },
      { name: '에스프레소 (싱글)', mg: 75 },
      { name: '카라멜 마끼야또 (톨)', mg: 150 },
      { name: '콜드브루 (톨)', mg: 155 },
    ],
    hasCustom: true
  },
];

const TIPS = {
  safe: [
    { icon: '✓', text: '안전한 섭취량이에요! 오늘 하루도 잘 하고 계세요.' },
    { icon: '✓', text: '카페인 없는 루이보스티·허브티로 따뜻한 한 잔을 즐겨보세요.' },
    { icon: '✓', text: '보리차·결명자차는 카페인 없이 구수한 맛으로 수분 보충에 좋아요.' },
  ],
  warn: [
    { icon: '!', text: '권장량이 가까워지고 있어요. 이후엔 디카페인 음료를 선택해보세요.' },
    { icon: '!', text: '치커리커피·보리커피는 커피 향을 그대로 느끼면서 카페인을 줄일 수 있어요.' },
    { icon: '!', text: '오후 2시 이후 카페인은 수면의 질을 낮출 수 있으니 페퍼민트 차로 전환해보세요.' },
    { icon: '!', text: '로즈마리 차는 집중력 유지에 도움이 되면서도 카페인이 없어요.' },
    { icon: '!', text: '카페인 과다 섭취 시 허탈감·내성·불면 등 부작용이 생길 수 있어요.' },
  ],
  danger: [
    { icon: '✕', text: '일일 권고량 400mg을 초과했어요! 두근거림·떨림·불안이 생길 수 있어요.' },
    { icon: '✕', text: '지금 당장은 물을 충분히 마셔 카페인 배출을 도와주세요 (1~2컵 이상).' },
    { icon: '✕', text: '카페인 허탈감(강한 졸음)이 찾아올 수 있어요. 짧은 낮잠(20분)으로 대처해보세요.' },
    { icon: '✕', text: '마테차는 카페인이 소량 있지만 카페인 내성이 낮아 부드러운 대안이 돼요.' },
    { icon: '✕', text: '홍차는 카페인이 있지만 커피의 절반 수준이에요. 내일부터 단계적으로 줄여보세요.' },
    { icon: '✕', text: '치커리커피·보리커피를 루틴에 넣으면 카페인 의존도를 낮추는 데 도움이 돼요.' },
  ],
};

/* ================================================================
   STATE
================================================================ */
let activeBrandId = null;
let log = []; // { key, brandName, menuName, mg, qty }

/* ================================================================
   RENDER BRANDS
================================================================ */
function renderBrands() {
  const grid = document.getElementById('brandGrid');
  grid.innerHTML = BRANDS.map(b => `
    <div class="brand-btn ${activeBrandId === b.id ? 'active' : ''}"
         onclick="selectBrand('${b.id}')">
      <div class="brand-icon">${b.icon}</div>
      <div class="brand-name-label">${b.name}</div>
    </div>
  `).join('');
}

/* ================================================================
   RENDER MENUS
================================================================ */
function selectBrand(id) {
  activeBrandId = id;
  renderBrands();
  renderMenus();
}

function renderMenus() {
  const sec = document.getElementById('menuSection');
  const brand = BRANDS.find(b => b.id === activeBrandId);
  if (!brand) {
    sec.innerHTML = '<div class="placeholder-msg">위에서 브랜드를 먼저 선택해주세요.</div>';
    return;
  }
  let html = '<div class="menu-list">';
  brand.menus.forEach((m, i) => {
    html += `
      <div class="menu-item" onclick="addItem('${brand.id}','${brand.name}','${m.name}',${m.mg})">
        <span class="menu-name">${m.name}</span>
        <span class="menu-mg">${m.mg}mg</span>
        <button class="menu-add-btn" title="추가">+</button>
      </div>`;
  });
  html += '</div>';
  if (brand.hasCustom) {
    html += `
      <div class="custom-row" style="margin-top:10px;">
        <input type="text" id="custName" placeholder="기타 메뉴명" />
        <input type="number" id="custMg" placeholder="mg" min="0" max="2000" />
        <button onclick="addCustom('${brand.id}','${brand.name}')">추가</button>
      </div>`;
  }
  sec.innerHTML = html;
}

/* ================================================================
   ADD ITEM
================================================================ */
function addItem(brandId, brandName, menuName, mg) {
  const key = brandId + '|' + menuName;
  const ex = log.find(l => l.key === key);
  if (ex) { ex.qty++; }
  else { log.push({ key, brandName, menuName, mg, qty: 1 }); }
  renderLog();
}

function addCustom(brandId, brandName) {
  const name = document.getElementById('custName').value.trim();
  const mg = parseInt(document.getElementById('custMg').value);
  if (!name || isNaN(mg) || mg <= 0) return;
  addItem(brandId, brandName, name, mg);
  document.getElementById('custName').value = '';
  document.getElementById('custMg').value = '';
}

/* ================================================================
   LOG
================================================================ */
function changeQty(key, delta) {
  const i = log.findIndex(l => l.key === key);
  if (i < 0) return;
  log[i].qty += delta;
  if (log[i].qty <= 0) log.splice(i, 1);
  renderLog();
}

function renderLog() {
  const el = document.getElementById('logList');
  if (!log.length) {
    el.innerHTML = '<div class="log-empty">아직 추가된 음료가 없어요.</div>';
    updateResult(0);
    return;
  }
  el.innerHTML = log.map(l => `
    <div class="log-item">
      <span class="log-brand-tag">${l.brandName}</span>
      <span class="log-name">${l.menuName}</span>
      <span class="log-mg">${l.mg * l.qty}mg</span>
      <div class="log-qty">
        <button class="log-qty-btn" onclick="changeQty('${l.key}',-1)">−</button>
        <span class="log-qty-num">${l.qty}</span>
        <button class="log-qty-btn" onclick="changeQty('${l.key}',1)">+</button>
      </div>
      <button class="log-del" onclick="removeItem('${l.key}')">×</button>
    </div>
  `).join('');
  const total = log.reduce((s, l) => s + l.mg * l.qty, 0);
  updateResult(total);
}

function removeItem(key) {
  log = log.filter(l => l.key !== key);
  renderLog();
}

function resetAll() {
  log = [];
  renderLog();
}

/* ================================================================
   RESULT
================================================================ */
function updateResult(total) {
  const card = document.getElementById('resultCard');
  const num = document.getElementById('resultNum');
  const fill = document.getElementById('meterFill');
  const status = document.getElementById('resultStatus');
  const tipsBox = document.getElementById('tipsBox');
  const tipsList = document.getElementById('tipsList');

  num.textContent = total;

  const pct = Math.min((total / 400) * 100, 100);
  fill.style.width = pct + '%';

  let level, fillColor, statusText, tipSet;

  if (total === 0) {
    level = 'safe'; fillColor = 'var(--safe)';
    statusText = '음료를 추가하면 결과가 나타나요.';
    tipSet = null;
  } else if (total <= 200) {
    level = 'safe'; fillColor = 'var(--safe)';
    statusText = '훌륭해요! 안전한 카페인 섭취 범위예요.';
    tipSet = TIPS.safe;
  } else if (total <= 399) {
    level = 'warn'; fillColor = 'var(--warn)';
    statusText = `⚠ 주의 — 권고량의 ${Math.round(pct)}%에 도달했어요.`;
    tipSet = TIPS.warn;
  } else {
    level = 'danger'; fillColor = 'var(--danger)';
    statusText = `🚨 초과 — 권고량(400mg) 대비 ${total - 400}mg 초과!`;
    tipSet = TIPS.danger;
  }

  fill.style.background = fillColor;
  card.className = 'result-card ' + level;
  status.textContent = statusText;

  if (!tipSet) {
    tipsBox.style.display = 'none';
  } else {
    tipsBox.style.display = 'block';
    tipsList.innerHTML = tipSet.map(t =>
      `<div class="tip-row"><span class="tip-icon">${t.icon}</span><span>${t.text}</span></div>`
    ).join('');
  }
}

/* ================================================================
   INIT
================================================================ */
renderBrands();
renderLog();
</script>
</body>
</html>
