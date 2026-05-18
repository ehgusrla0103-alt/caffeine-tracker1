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
    --accent: #C8A96E;
  }

  * { box-sizing: border-box; margin: 0; padding: 0; }
  html, body { height: 100%; }
  /* 1) DOCTYPE 아래 흰색 타이틀 영역 숨김 처리: 화면에 보이지 않도록 cfg */
  /* 실제 DOCTYPE 선언은 제거하지 않되, 화면에 보이지 않게 만듭니다. */

  /* 2) 파란 글씨 제거를 위한 기본 링크 스타일 재정의 */
  a { color: inherit; text-decoration: none; }
  a:visited { color: inherit; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'Noto Sans KR', sans-serif;
    min-height: 100vh;
    padding: 0 0 3rem;
  }

  /* 2-1) 헤더(필요시 숨김) */
  .header { display: none; } /* 1/2번째 화면에서 보였던 흰 타이틀 제거를 위해 숨김 */

  /* ── 레이아웃의 기본 카드 스타일 ── */
  .container { max-width: 860px; margin: 0 auto; padding: 1rem 1.5rem; }

  .section {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 1.25rem;
    margin-bottom: 1.25rem;
  }

  .section-title {
    font-family: 'DM Mono', monospace;
    font-size: 0.9rem;
    color: var(--text3);
    margin-bottom: 0.75rem;
  }

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
    text-align: center;
  }
  .brand-btn.active { border-color: var(--accent); background: rgba(200,169,110,0.10); }

  .menu-list { display: flex; flex-direction: column; gap: 6px; }
  .menu-item {
    display: flex; align-items: center; gap: 10px;
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 10px 12px;
  }
  .menu-name { flex: 1; }
  .menu-mg { font-family: 'DM Mono', monospace; }

  .log-empty { text-align: center; color: var(--text3); padding: 1rem 0; }

  .log-item { display: flex; align-items: center; gap: 8px; padding: 8px 0; border-bottom: 1px solid var(--border); font-size: 0.84rem; }
  .log-brand-tag { font-size: 0.68rem; background: var(--surface2); border: 1px solid var(--border); border-radius: 4px; padding: 2px 6px; color: var(--text3); }

  .log-mg { margin-left: auto; font-family: 'DM Mono', monospace; }

  .meter-wrap { height: 6px; border-radius: 6px; background: rgba(255,255,255,0.06); overflow: hidden; margin: 6px 0 0; }
  .meter-fill { height: 100%; width: 0%; border-radius: 6px; background: var(--safe); transition: width 0.5s; }

  .result-card { border-radius: 12px; padding: 1.25rem; border: 1px solid var(--border); margin-top: 0.5rem; }

  .tips-box { display: none; }

  /* 3) 세로 스크롤형 레이아웃에 맞춘 기본 여백 제어 */
  @media (min-width: 900px) {
    .layout-row { display: grid; grid-template-columns: 1fr 1fr; gap: 1rem; align-items: start; }
  }

  /* 상단 불필요 요소를 숨기려면 다음처럼 추가 가능 */
  /* 예: 특정 요소의 클래스가 있다면 display: none; 적용 */
</style>
</head>
<body>

<!-- 1) 상단 흰색 타이틀 영역 제거/숨김 처리: 실제 화면에 보이지 않도록 header를 숨깁니다 -->
<div class="header" aria-hidden="true" style="display:none;">☕ Caffeine Tracker</div>

<div class="container">

  <!-- 2) 세로 방향 섹션들로 구성: 브랜드, 메뉴, 오늘 마신 음료 순으로 쌓임 -->
  <section class="section" id="brand-section">
    <div class="section-title">01 — 브랜드 선택</div>
    <div class="brand-grid" id="brandGrid"></div>
  </section>

  <section class="section" id="menu-section">
    <div class="section-title">02 — 메뉴 선택</div>
    <div id="menuSection">
      <div class="placeholder-msg" style="color:var(--text3);">위에서 브랜드를 먼저 선택해주세요.</div>
    </div>
  </section>

  <section class="section" id="log-section">
    <div class="section-title">03 — 오늘 마신 음료</div>
    <div id="logList"><div class="log-empty">아직 추가된 음료가 없어요.</div></div>
    <div class="divider" style="height:1px; background: var(--border); margin: 12px 0;"></div>
    <button class="reset-btn" onclick="resetAll()" style="width:100%; border:1px solid var(--border); padding:12px; border-radius:8px;">초기화</button>
  </section>

  <!-- 결과 카드 -->
  <section class="section" id="result-section">
    <div class="section-title" style="margin-bottom:6px;">결과</div>
    <div class="result-card" id="resultCard" style="background: #1e1e1e; border-color:#333;">
      <div class="result-top" style="display:flex; align-items:baseline; gap:6px;">
        <span class="result-num" id="resultNum" style="font-family:'DM Mono', monospace; font-size:2.4rem;">0</span>
        <span class="result-unit" style="color:var(--text2); font-size:0.8rem;">mg</span>
      </div>
      <div class="meter-wrap">
        <div class="meter-fill" id="meterFill" style="width:0%;"></div>
      </div>
      <div class="result-status" id="resultStatus" style="margin-top:6px;">음료를 추가하면 결과가 나타나요.</div>
      <div class="info-note" id="infoNote" style="margin-top:6px;">
        일일 권고량: 성인 400mg · 임산부 300mg<br>
        어린이·청소년 체중 ×2.5mg<br>
        <span style="color:var(--text3)">출처: 식품의약품안전처</span>
      </div>
    </div>
  </section>

</div>

<script>
/* ================================================================
   DATA
================================================================ */
const BRANDS = [
  { id: 'cvs', icon: '🏪', name: '편의점', menus: [
    { name: '몬스터 에너지', mg: 160 }, { name: '코카콜라 (355ml)', mg: 34 }, { name: '핫식스', mg: 60 },
    { name: '박카스', mg: 30 }, { name: '조지아 크래프트 커피', mg: 262 }, { name: '스타벅스 병커피', mg: 103 },
    { name: '녹차 캔', mg: 20 }, { name: '스누피 커피', mg: 237 }, { name: '빙그레 아카페라', mg: 75 },
    { name: '캔커피 (일반)', mg: 74 }, { name: '초콜릿 100g', mg: 25 }
  ]},
  { id: 'compose', icon: '🟡', name: '컴포즈', menus: [
    { name: '아메리카노', mg: 156 }, { name: '카페라떼', mg: 156 }, { name: '아샷추', mg: 95 },
    { name: '에스프레소', mg: 78 }, { name: '카라멜 마끼야또', mg: 130 }, { name: '콜드브루', mg: 185 }
  ], hasCustom:true},
  { id: 'ediya', icon: '🔵', name: '이디야', menus: [
    { name: '아메리카노 (레귤러)', mg: 145 }, { name: '카페라떼', mg: 145 }, { name: '아샷추', mg: 100 },
    { name: '에스프레소', mg: 75 }, { name: '카라멜 마끼야또', mg: 120 }, { name: '콜드브루', mg: 175 }
  ], hasCustom:true},
  { id: 'twosome', icon: '🍓', name: '투썸', menus: [
    { name: '아메리카노 (레귤러)', mg: 177 }, { name: '카페라떼', mg: 177 }, { name: '아샷추', mg: 110 },
    { name: '에스프레소', mg: 90 }, { name: '카라멜 마끼야또', mg: 150 }, { name: '콜드브루', mg: 220 }
  ], hasCustom:true},
  { id: 'mega', icon: '🟠', name: '메가커피', menus: [
    { name: '아메리카노', mg: 193 }, { name: '카페라떼', mg: 193 }, { name: '아샷추', mg: 120 },
    { name: '에스프레소', mg: 96 }, { name: '카라멜 마끼야또', mg: 160 }, { name: '콜드브루', mg: 210 }
  ], hasCustom:true},
  { id: 'ten', icon: '🔟', name: '텐퍼센트', menus: [
    { name: '아메리카노', mg: 140 }, { name: '카페라떼', mg: 140 }, { name: '아샷추', mg: 95 },
    { name: '에스프레소', mg: 70 }, { name: '카라멜 마끼야또', mg: 120 }, { name: '콜드브루', mg: 180 }
  ], hasCustom:true},
  { id: 'sbux', icon: '🟢', name: '스타벅스', menus: [
    { name: '아메리카노 (톨)', mg: 150 }, { name: '카페라떼 (톨)', mg: 150 }, { name: '아샷추 (톨)', mg: 105 },
    { name: '에스프레소 (싱글)', mg: 75 }, { name: '카라멜 마끼야또 (톨)', mg: 150 }, { name: '콜드브루 (톨)', mg: 155 }
  ], hasCustom:true}
];

const TIPS = {
  safe: [{icon:'✓', text:'안전한 섭취량이에요! 오늘 하루도 잘 하고 계세요.'}],
  warn: [{icon:'!', text:'권장량이 가까워지고 있어요. 이후엔 디카페인 음료를 선택해보세요.'}],
  danger: [{icon:'✕', text:'일일 권고량 400mg을 초과했어요!'}
  ]
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
    <button class="brand-btn ${activeBrandId === b.id ? 'active' : ''}"
            onclick="selectBrand('${b.id}')">
      <div class="brand-icon">${b.icon}</div>
      <div class="brand-name-label">${b.name}</div>
    </button>
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
  brand.menus.forEach((m) => {
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
  const infoNote = document.getElementById('infoNote');

  num.textContent = total;

  const pct = Math.min((total / 400) * 100, 100);
  fill.style.width = pct + '%';
  if (pct < 50) fill.style.background = 'var(--safe)';
  else if (pct < 100) fill.style.background = 'var(--warn)';
  else fill.style.background = 'var(--danger)';

  let level, tipSet;

  if (total === 0) {
    level = 'safe';
    status.textContent = '음료를 추가하면 결과가 나타나요.';
    tipSet = null;
  } else if (total <= 200) {
    level = 'safe';
    status.textContent = '훌륭해요! 안전한 카페인 섭취 범위예요.';
    tipSet = TIPS.safe;
  } else if (total <= 399) {
    level = 'warn';
    status.textContent = `⚠ 주의 — 권고량의 ${Math.round(pct)}%에 도달했어요.`;
    tipSet = TIPS.warn;
  } else {
    level = 'danger';
    status.textContent = `🚨 초과 — 권고량(400mg) 대비 ${total - 400}mg 초과!`;
    tipSet = TIPS.danger;
  }

  card.className = 'section ' + level;
  if (tipSet) {
    infoNote.innerHTML = '';
  } else {
    infoNote.innerHTML = '';
  }
  // 간단한 예시용으로 tip 목록은 1줄만 표시하도록 확장 가능
}

/* ================================================================
   INIT
================================================================ */
renderBrands();
renderLog();
</script>
</body>
</html>
