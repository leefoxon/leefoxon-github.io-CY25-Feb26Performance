<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>CMO Performance Dashboard</title>

<style>
  :root {
    /* Citation Group Brand Palette */
    --bg: #F5F6F8;
    --surface: #FFFFFF;
    --surface2: #ECEFF1;
    --border: #DDE1E6;
    --accent: #E91E63;
    --accent2: #00BCD4;
    --accent3: #D81B60;
    --accent4: #9C27B0;
    --text: #37474F;
    --muted: #78909C;
    --green: #00897B;
    --red: #D81B60;
    --amber: #FF6B35;
    --pink: #E91E63;
    --purple: #9C27B0;
    --teal: #00BCD4;
    --orange: #FF6B35;
    --lgrey: #ECEFF1;
  }
  * { margin: 0; padding: 0; box-sizing: border-box; }
  body {
    background: var(--bg);
    color: var(--text);
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Helvetica Neue', Arial, sans-serif;
    min-height: 100vh;
    overflow-x: hidden;
  }
  .noise {
    position: fixed;
    inset: 0;
    background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)' opacity='0.04'/%3E%3C/svg%3E");
    pointer-events: none;
    z-index: 0;
  }
  header {
    padding: 20px 40px 18px;
    background: linear-gradient(135deg, #E91E63 0%, #9C27B0 100%);
    border-bottom: none;
    display: flex;
    justify-content: space-between;
    align-items: flex-end;
    position: relative;
    z-index: 1;
  }
  .logo-area h1 {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Arial, sans-serif;
    font-size: 11px;
    font-weight: 700;
    letter-spacing: 4px;
    text-transform: uppercase;
    color: rgba(255,255,255,0.75);
    margin-bottom: 6px;
  }
  .logo-area h2 {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Arial, sans-serif;
    font-size: 26px;
    font-weight: 700;
    color: #FFFFFF;
    line-height: 1;
  }
  .header-meta {
    text-align: right;
    font-size: 11px;
    color: rgba(255,255,255,0.75);
    line-height: 1.8;
  }
  .header-meta .live-dot {
    display: inline-block;
    width: 6px; height: 6px;
    background: var(--green);
    border-radius: 50%;
    animation: pulse 2s infinite;
    margin-right: 6px;
    vertical-align: middle;
  }
  @keyframes pulse { 0%,100%{opacity:1}50%{opacity:.3} }

  .main {
    padding: 28px 40px;
    position: relative;
    z-index: 1;
  }

  /* Navigation tabs */
  .tabs {
    display: flex;
    gap: 4px;
    margin-bottom: 28px;
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 5px;
    width: fit-content;
    flex-wrap: wrap;
  }
  .tab {
    padding: 7px 16px;
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Arial, sans-serif;
    font-size: 12px;
    font-weight: 600;
    letter-spacing: 0.2px;
    cursor: pointer;
    border-radius: 6px;
    transition: all .18s;
    color: var(--muted);
    border: none;
    background: none;
  }
  .tab.active {
    background: var(--accent);
    color: #FFFFFF;
    box-shadow: 0 2px 8px rgba(233,30,99,0.25);
  }
  .tab:hover:not(.active) { color: var(--text); background: rgba(55,71,79,0.06); }

  .section { display: none; }
  .section.active { display: block; }

  /* KPI Cards */
  .kpi-row {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
    gap: 12px;
    margin-bottom: 24px;
  }
  .kpi {
    background: var(--surface);
    border: 1px solid var(--border);
    border-left: 4px solid var(--kpi-color, var(--accent));
    border-radius: 8px;
    padding: 18px 20px;
    position: relative;
    overflow: hidden;
    transition: box-shadow .2s, transform .15s;
    box-shadow: 0 1px 4px rgba(55,71,79,0.07);
  }
  .kpi:hover { box-shadow: 0 4px 16px rgba(55,71,79,0.12); transform: translateY(-1px); }
  .kpi::before {
    content: '';
    position: absolute;
    top: 0; left: 0; right: 0;
    height: 0;
  }
  .kpi-label {
    font-size: 11px;
    font-weight: 600;
    letter-spacing: 0.5px;
    text-transform: uppercase;
    color: var(--muted);
    margin-bottom: 8px;
  }
  .kpi-value {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Arial, sans-serif;
    font-size: 28px;
    font-weight: 800;
    line-height: 1;
    color: var(--text);
    margin-bottom: 6px;
  }
  .kpi-sub {
    font-size: 10px;
    color: var(--muted);
    display: flex;
    align-items: center;
    gap: 6px;
  }
  .badge {
    display: inline-flex;
    align-items: center;
    gap: 3px;
    padding: 2px 7px;
    border-radius: 20px;
    font-size: 10px;
    font-weight: 500;
  }
  .badge.green { background: rgba(0,137,123,.12); color: var(--green); }
  .badge.red { background: rgba(216,27,96,.12); color: var(--red); }
  .badge.amber { background: rgba(251,191,36,.15); color: var(--amber); }

  /* Charts */
  .charts-row {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 16px;
    margin-bottom: 24px;
  }
  .charts-row-3 {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
    gap: 16px;
    margin-bottom: 24px;
  }
  .chart-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 22px;
  }
  .chart-card.wide { grid-column: span 2; }
  .chart-title {
    font-size: 10px;
    letter-spacing: 2px;
    text-transform: uppercase;
    color: var(--muted);
    margin-bottom: 18px;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  .chart-title span { color: var(--accent); }

  /* Bar chart */
  .bar-chart { display: flex; flex-direction: column; gap: 10px; }
  .bar-row { display: flex; align-items: center; gap: 10px; }
  .bar-label { font-size: 11px; color: var(--muted); width: 40px; flex-shrink: 0; text-align: right; }
  .bar-track { flex: 1; height: 20px; background: var(--surface2); border-radius: 3px; overflow: hidden; position: relative; }
  .bar-fill {
    height: 100%;
    border-radius: 3px;
    transition: width 1s cubic-bezier(.4,0,.2,1);
    animation: barIn 1s forwards;
  }
  @keyframes barIn { from { width: 0 !important; } }
  .bar-val { font-size: 11px; color: var(--text); width: 50px; flex-shrink: 0; }
  .bar-vs { font-size: 10px; width: 50px; flex-shrink: 0; }

  /* Funnel */
  .funnel { display: flex; flex-direction: column; gap: 8px; }
  .funnel-step {
    position: relative;
    display: flex;
    align-items: center;
    gap: 12px;
  }
  .funnel-bar-wrap {
    flex: 1;
    position: relative;
  }
  .funnel-bar-track {
    height: 36px;
    background: var(--surface2);
    border-radius: 4px;
    overflow: hidden;
  }
  .funnel-bar-fill {
    height: 100%;
    display: flex;
    align-items: center;
    padding: 0 12px;
    font-size: 11px;
    color: #FFFFFF;
    font-weight: 600;
    animation: barIn 1.2s forwards;
  }
  .funnel-meta {
    font-size: 10px;
    color: var(--muted);
    margin-top: 2px;
    padding-left: 2px;
  }
  .funnel-step-label {
    font-size: 10px;
    letter-spacing: 1px;
    text-transform: uppercase;
    color: var(--muted);
    width: 90px;
    flex-shrink: 0;
  }
  .arrow-down {
    text-align: center;
    color: var(--border);
    font-size: 12px;
    padding-left: 102px;
    line-height: 1;
  }

  /* Comparison table */
  .comparison-table {
    width: 100%;
    border-collapse: collapse;
  }
  .comparison-table th {
    font-size: 10px;
    letter-spacing: 2px;
    text-transform: uppercase;
    color: var(--muted);
    padding: 10px 14px;
    text-align: left;
    border-bottom: 1px solid var(--border);
    font-weight: 400;
  }
  .comparison-table td {
    font-size: 12px;
    padding: 11px 14px;
    border-bottom: 1px solid rgba(42,45,58,.5);
    vertical-align: middle;
  }
  .comparison-table tr:last-child td { border-bottom: none; }
  .comparison-table tr:hover td { background: rgba(232,255,71,.03); }
  .metric-name { color: var(--text); }
  .metric-num { font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Arial, sans-serif; font-weight: 700; }

  /* Section header */
  .section-header {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Arial, sans-serif;
    font-size: 14px;
    font-weight: 700;
    color: var(--text);
    letter-spacing: 2px;
    text-transform: uppercase;
    margin-bottom: 16px;
    padding-bottom: 10px;
    border-bottom: 1px solid var(--border);
    display: flex;
    align-items: center;
    gap: 10px;
  }
  .section-header::before {
    content: '';
    width: 4px;
    height: 16px;
    background: var(--accent);
    border-radius: 2px;
  }

  /* Spark line */
  .sparkline-row {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 16px;
    margin-bottom: 24px;
  }
  .sparkline-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 20px;
  }
  .sparkline-card svg { width: 100%; height: 60px; margin-top: 12px; }

  /* Insight boxes */
  .insights-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
    gap: 12px;
    margin-bottom: 24px;
  }
  .insight {
    background: var(--surface);
    border: 1px solid var(--border);
    border-left: 3px solid var(--insight-color, var(--accent));
    border-radius: 6px;
    padding: 16px 18px;
  }
  .insight-title {
    font-size: 11px;
    letter-spacing: 1.5px;
    text-transform: uppercase;
    color: var(--insight-color, var(--accent));
    margin-bottom: 8px;
    font-weight: 500;
  }
  .insight-text {
    font-size: 12px;
    color: var(--text);
    line-height: 1.6;
  }

  /* Mini donut */
  .donut-row {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 14px;
    margin-bottom: 24px;
  }
  .donut-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 20px;
    display: flex;
    align-items: center;
    gap: 16px;
  }
  .donut-label {
    font-size: 10px;
    letter-spacing: 1.5px;
    text-transform: uppercase;
    color: var(--muted);
    margin-bottom: 4px;
  }
  .donut-value {
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Arial, sans-serif;
    font-size: 22px;
    font-weight: 800;
  }
  .donut-sub { font-size: 10px; color: var(--muted); }

  /* Scrollbar */
  ::-webkit-scrollbar { width: 4px; }
  ::-webkit-scrollbar-track { background: var(--bg); }
  ::-webkit-scrollbar-thumb { background: var(--border); border-radius: 2px; }

  .tag { 
    font-size: 9px; 
    padding: 2px 8px; 
    border-radius: 20px; 
    letter-spacing: 1px;
    text-transform: uppercase;
    border: 1px solid currentColor;
    opacity: .7;
  }

  .two-col { display: grid; grid-template-columns: 1.4fr 1fr; gap: 16px; margin-bottom: 24px; }
  .one-col { margin-bottom: 24px; }

  .trend-inline {
    display: flex;
    align-items: center;
    gap: 6px;
  }

  .brand-pill {
    display: inline-block;
    padding: 3px 10px;
    border-radius: 20px;
    font-size: 10px;
    font-weight: 600;
    letter-spacing: 1px;
    text-transform: uppercase;
  }
</style>
</head>
<body>
<div class="noise"></div>

<header>
  <div class="logo-area">
    <h1>Citation Group · Marketing Intelligence</h1>
    <h2>CMO Performance Dashboard</h2>
  </div>
  <div class="header-meta">
    <div><span class="live-dot"></span>CY25 Full Year + CY26 YTD (Jan–Feb)</div>
    <div>Brands: CHR AU/NZ · FoundU · ISO Certification · NDIS</div>
    <div>Acquisition → Pipeline → Revenue</div>
  </div>
</header>

<div class="main">
  <div class="tabs">
    <button class="tab active" onclick="switchTab('overview')">Overview</button>
    <button class="tab" onclick="switchTab('chr')">HR & Safety</button>
    <button class="tab" onclick="switchTab('foundo')">FoundU</button>
    <button class="tab" onclick="switchTab('iso')">ISO / NDIS</button>
    <button class="tab" onclick="switchTab('efficiency')">Efficiency</button>
    <button class="tab" onclick="switchTab('aacv')">CHR AACV</button>
    <button class="tab" onclick="switchTab('foundo-rolling')">FoundU Rolling</button>
    <button class="tab" onclick="switchTab('cac')">ACV:CAC Ratios</button>
    <button class="tab" onclick="switchTab('nz')">NZ Exit Impact</button>
  </div>

  <!-- ============ OVERVIEW ============ -->
  <div id="overview" class="section active">

    <div class="section-header">CY26 YTD Performance Snapshot (Jan–Feb)</div>

    <div class="kpi-row">
      <div class="kpi" style="--kpi-color:#e8ff47">
        <div class="kpi-label">CHR Inbound Spend</div>
        <div class="kpi-value">$252K</div>
        <div class="kpi-sub"><span class="badge amber">↓ 5.5% vs target</span> YTD</div>
      </div>
      <div class="kpi" style="--kpi-color:#47c7ff">
        <div class="kpi-label">CHR Total Opps</div>
        <div class="kpi-value">303</div>
        <div class="kpi-sub">Jan 140 · Feb 163 <span class="badge green">↑ vs target</span></div>
      </div>
      <div class="kpi" style="--kpi-color:#4ade80">
        <div class="kpi-label">CHR Total Deals</div>
        <div class="kpi-value">46</div>
        <div class="kpi-sub">Jan 19 · Feb 23 <span class="badge red">↓ vs target</span></div>
      </div>
      <div class="kpi" style="--kpi-color:#a78bfa">
        <div class="kpi-label">CHR Total ACV</div>
        <div class="kpi-value">$514K</div>
        <div class="kpi-sub">Jan $187K · Feb $327K <span class="badge green">↑ strong Feb</span></div>
      </div>
      <div class="kpi" style="--kpi-color:#ff6b6b">
        <div class="kpi-label">ISO Total Deals</div>
        <div class="kpi-value">68</div>
        <div class="kpi-sub">Jan 22 · Feb 46 <span class="badge green">↑ strong Feb</span></div>
      </div>
      <div class="kpi" style="--kpi-color:#e8ff47">
        <div class="kpi-label">ISO Total ACV</div>
        <div class="kpi-value">$342K</div>
        <div class="kpi-sub">Jan $105K · Feb $238K <span class="badge amber">↓ vs target</span></div>
      </div>
      <div class="kpi" style="--kpi-color:#47c7ff">
        <div class="kpi-label">FoundU Sales</div>
        <div class="kpi-value">28</div>
        <div class="kpi-sub">Jan 15 · Feb 13 <span class="badge green">↑ vs target</span></div>
      </div>
      <div class="kpi" style="--kpi-color:#4ade80">
        <div class="kpi-label">FoundU Revenue</div>
        <div class="kpi-value">$512K</div>
        <div class="kpi-sub">Jan $185K · Feb $327K <span class="badge green">↑ outbound surge</span></div>
      </div>
    </div>

    <div class="section-header">CY25 Full Year Revenue (ACV) vs Budget</div>

    <div class="chart-card one-col">
      <div class="chart-title">ACV Monthly — Actual vs Budget (All Brands) <span>CY25</span></div>
      <div class="bar-chart">
        <!-- Data: Budget vs Actual for each month from ACV Trend sheet -->
        <div style="display:flex;gap:8px;margin-bottom:12px;font-size:10px;color:var(--muted)">
          <span style="display:flex;align-items:center;gap:5px"><span style="width:12px;height:3px;background:var(--accent);display:inline-block;border-radius:2px"></span>Actual</span>
          <span style="display:flex;align-items:center;gap:5px"><span style="width:12px;height:3px;background:var(--border);display:inline-block;border-radius:2px"></span>Budget</span>
        </div>
        <div id="acv-chart"></div>
      </div>
    </div>

    <div class="charts-row">
      <div class="chart-card">
        <div class="chart-title">CY25 Full Year — Leads to Deals Funnel <span>CHR AU</span></div>
        <div class="funnel">
          <div class="funnel-step">
            <div class="funnel-step-label">Total Leads</div>
            <div class="funnel-bar-wrap">
              <div class="funnel-bar-track">
                <div class="funnel-bar-fill" style="width:100%;background:var(--accent);color:#FFFFFF">5,441 actual</div>
              </div>
              <div class="funnel-meta">Target ~6,683 | <span style="color:var(--amber)">82% of target</span></div>
            </div>
          </div>
          <div class="arrow-down">↓ 50% L→O</div>
          <div class="funnel-step">
            <div class="funnel-step-label">Opps</div>
            <div class="funnel-bar-wrap">
              <div class="funnel-bar-track">
                <div class="funnel-bar-fill" style="width:50%;background:var(--accent2)">2,328 actual</div>
              </div>
              <div class="funnel-meta">Target ~2,327 | <span style="color:var(--green)">On target</span></div>
            </div>
          </div>
          <div class="arrow-down">↓ 67% O→Sat</div>
          <div class="funnel-step">
            <div class="funnel-step-label">Sat (Meetings)</div>
            <div class="funnel-bar-wrap">
              <div class="funnel-bar-track">
                <div class="funnel-bar-fill" style="width:34%;background:var(--accent4)">1,563 actual</div>
              </div>
              <div class="funnel-meta">Target ~1,653</div>
            </div>
          </div>
          <div class="arrow-down">↓ 24% Sat→Deal</div>
          <div class="funnel-step">
            <div class="funnel-step-label">Deals</div>
            <div class="funnel-bar-wrap">
              <div class="funnel-bar-track">
                <div class="funnel-bar-fill" style="width:8%;background:var(--green)">376 actual</div>
              </div>
              <div class="funnel-meta">Target ~425 | <span style="color:var(--red)">88% of target</span></div>
            </div>
          </div>
        </div>
      </div>

      <div class="chart-card">
        <div class="chart-title">CY25 ACV vs Target — All Brands <span>Monthly %</span></div>
        <div id="brand-vs-target-chart"></div>
      </div>
    </div>

    <div class="section-header">Key Commercial Insights</div>
    <div class="insights-grid">
      <div class="insight" style="--insight-color:#e8ff47">
        <div class="insight-title">🏆 Bright Spot</div>
        <div class="insight-text">CHR AU inbound overperformed in Feb–Jun 25, with spend consistently exceeding targets by 13–21%. Opp-to-sat rates held at 70%+ throughout CY25.</div>
      </div>
      <div class="insight" style="--insight-color:#f87171">
        <div class="insight-title">⚠ Revenue Shortfall</div>
        <div class="insight-text">CY25 ACV missed budget in 7 of 12 months. H2 was particularly soft — Aug to Dec all underdelivered, with Nov at only 48% of budget. Total ACV $4.1M vs $5.5M budgeted.</div>
      </div>
      <div class="insight" style="--insight-color:#47c7ff">
        <div class="insight-title">🔁 ISO Mixed Signal</div>
        <div class="insight-text">ISO cert deals were healthy all year (10–28/month) but ACV per deal trended down. PPC CPL improved mid-year from $636 to $431 by Feb 26.</div>
      </div>
      <div class="insight" style="--insight-color:#a78bfa">
        <div class="insight-title">📈 FoundU Gaining</div>
        <div class="insight-text">FoundU CY26 YTD shows strong conversion rates: Inbound Lead-to-Meeting 36%, Sat-to-Sale 69%. Revenue $369K in 2 months vs $889K total CY25.</div>
      </div>
      <div class="insight" style="--insight-color:#fbbf24">
        <div class="insight-title">💰 PPC ROAS Watch</div>
        <div class="insight-text">2024 PPC ROAS (TCV) was 2.75x across $1.2M spend. Q1 CY25 ROAS tracking at 2.55x on $345K. Attribution only 54% tracked — real ROAS likely higher.</div>
      </div>
      <div class="insight" style="--insight-color:#4ade80">
        <div class="insight-title">🎯 CY26 Early Signal</div>
        <div class="insight-text">CHR inbound opps 303 YTD (Jan–Feb 26) well above pace. But deal conversion is lagging target with only 42 deals vs 52 target. Pipeline is rich — focus on close rate.</div>
      </div>
    </div>
  </div>

  <!-- ============ CHR ============ -->
  <div id="chr" class="section">
    <div class="section-header">HR & Safety (Citation HR AU/NZ) — Full Funnel Analysis</div>

    <div class="kpi-row">
      <div class="kpi" style="--kpi-color:#e8ff47">
        <div class="kpi-label">CY25 Total Spend</div>
        <div class="kpi-value">$1.66M</div>
        <div class="kpi-sub"><span class="badge green">+5.2% vs budget</span></div>
      </div>
      <div class="kpi" style="--kpi-color:#47c7ff">
        <div class="kpi-label">CY25 Total ACV</div>
        <div class="kpi-value">$3.86M</div>
        <div class="kpi-sub"><span class="badge red">-9.3% vs budget</span> est. excl. NZ</div>
      </div>
      <div class="kpi" style="--kpi-color:#4ade80">
        <div class="kpi-label">CY25 Total Deals</div>
        <div class="kpi-value">376</div>
        <div class="kpi-sub"><span class="badge amber">~88% of target</span></div>
      </div>
      <div class="kpi" style="--kpi-color:#a78bfa">
        <div class="kpi-label">CY25 Inbound CPL</div>
        <div class="kpi-value">~$1,700</div>
        <div class="kpi-sub">PPC primary channel</div>
      </div>
      <div class="kpi" style="--kpi-color:#ff6b6b">
        <div class="kpi-label">CY26 Jan Deals</div>
        <div class="kpi-value">19</div>
        <div class="kpi-sub"><span class="badge green">↑ vs target 13</span></div>
      </div>
      <div class="kpi" style="--kpi-color:#e8ff47">
        <div class="kpi-label">CY26 Feb Deals</div>
        <div class="kpi-value">23</div>
        <div class="kpi-sub"><span class="badge red">↓ vs target 39</span></div>
      </div>
    </div>

    <div class="section-header">CY25 Monthly Performance — Inbound Channel</div>
    <div class="chart-card one-col">
      <div class="chart-title">CHR AU — Inbound Leads, Opps, Deals by Month <span>CY25 Actual</span></div>
      <div id="chr-monthly-chart"></div>
    </div>

    <div class="charts-row">
      <div class="chart-card">
        <div class="chart-title">Inbound Channel Mix — CY25 Deal Attribution <span>by source</span></div>
        <div class="bar-chart">
          <div class="bar-row">
            <div class="bar-label">PPC</div>
            <div class="bar-track"><div class="bar-fill" style="width:60%;background:var(--accent)"></div></div>
            <div class="bar-val">~60%</div>
            <div class="bar-vs" style="color:var(--green)">Primary</div>
          </div>
          <div class="bar-row">
            <div class="bar-label">Organic</div>
            <div class="bar-track"><div class="bar-fill" style="width:15%;background:var(--accent2)"></div></div>
            <div class="bar-val">~15%</div>
            <div class="bar-vs" style="color:var(--muted)"></div>
          </div>
          <div class="bar-row">
            <div class="bar-label">MVF</div>
            <div class="bar-track"><div class="bar-fill" style="width:10%;background:var(--accent4)"></div></div>
            <div class="bar-val">~10%</div>
            <div class="bar-vs" style="color:var(--muted)"></div>
          </div>
          <div class="bar-row">
            <div class="bar-label">Bark</div>
            <div class="bar-track"><div class="bar-fill" style="width:8%;background:var(--amber)"></div></div>
            <div class="bar-val">~8%</div>
            <div class="bar-vs" style="color:var(--amber)">High CPL</div>
          </div>
          <div class="bar-row">
            <div class="bar-label">OB Mix</div>
            <div class="bar-track"><div class="bar-fill" style="width:7%;background:var(--green)"></div></div>
            <div class="bar-val">~7%</div>
            <div class="bar-vs" style="color:var(--muted)"></div>
          </div>
        </div>
      </div>

      <div class="chart-card">
        <div class="chart-title">Outbound Channel Performance — CY25 <span>Deals by source</span></div>
        <div class="bar-chart">
          <div class="bar-row">
            <div class="bar-label" style="width:90px">Offline/Partner</div>
            <div class="bar-track"><div class="bar-fill" style="width:40%;background:var(--accent)"></div></div>
            <div class="bar-val">High</div>
            <div class="bar-vs" style="color:var(--green)">Good CR</div>
          </div>
          <div class="bar-row">
            <div class="bar-label" style="width:90px">Juice Card</div>
            <div class="bar-track"><div class="bar-fill" style="width:32%;background:var(--accent2)"></div></div>
            <div class="bar-val">Med</div>
            <div class="bar-vs" style="color:var(--green)">High ACV</div>
          </div>
          <div class="bar-row">
            <div class="bar-label" style="width:90px">Partner Ch.</div>
            <div class="bar-track"><div class="bar-fill" style="width:28%;background:var(--accent4)"></div></div>
            <div class="bar-val">Med</div>
            <div class="bar-vs" style="color:var(--green)">↑ ACV</div>
          </div>
          <div class="bar-row">
            <div class="bar-label" style="width:90px">Content</div>
            <div class="bar-track"><div class="bar-fill" style="width:12%;background:var(--amber)"></div></div>
            <div class="bar-val">Low</div>
            <div class="bar-vs" style="color:var(--amber)">Weak CR</div>
          </div>
          <div class="bar-row">
            <div class="bar-label" style="width:90px">Closed Lost</div>
            <div class="bar-track"><div class="bar-fill" style="width:8%;background:var(--red)"></div></div>
            <div class="bar-val">Low</div>
            <div class="bar-vs" style="color:var(--red)">3% CR</div>
          </div>
        </div>
      </div>
    </div>

    <div class="section-header">CY25 → CY26 Conversion Rate Benchmarks</div>
    <div class="chart-card one-col">
      <div class="chart-title">Funnel Efficiency — CY25 Full Year vs CY26 Jan (CHR AU Inbound)</div>
      <table class="comparison-table">
        <thead>
          <tr>
            <th>Stage</th>
            <th>CY25 Rate</th>
            <th>CY26 Jan Rate</th>
            <th>CY26 Feb Rate</th>
            <th>Direction</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td class="metric-name">Lead → Opp</td>
            <td><span class="metric-num" style="color:var(--accent2)">~50%</span></td>
            <td><span class="metric-num" style="color:var(--green)">~58%</span></td>
            <td><span class="metric-num" style="color:var(--green)">~60%</span></td>
            <td><span class="badge green">↑ Improving</span></td>
          </tr>
          <tr>
            <td class="metric-name">Opp → Sat (Meeting)</td>
            <td><span class="metric-num" style="color:var(--accent2)">~67%</span></td>
            <td><span class="metric-num" style="color:var(--green)">~73%</span></td>
            <td><span class="metric-num" style="color:var(--amber)">~75%</span></td>
            <td><span class="badge green">↑ Strong</span></td>
          </tr>
          <tr>
            <td class="metric-name">Sat → Deal</td>
            <td><span class="metric-num" style="color:var(--accent2)">24.6%</span></td>
            <td><span class="metric-num" style="color:var(--red)">18.6%</span></td>
            <td><span class="metric-num" style="color:var(--red)">16.1%</span></td>
            <td><span class="badge red">↓ Watch</span></td>
          </tr>
          <tr>
            <td class="metric-name">PPC CPL</td>
            <td><span class="metric-num" style="color:var(--accent2)">$1,703–2,175</span></td>
            <td><span class="metric-num" style="color:var(--green)">$2,117</span></td>
            <td><span class="metric-num" style="color:var(--green)">$1,709</span></td>
            <td><span class="badge amber">Stable</span></td>
          </tr>
          <tr>
            <td class="metric-name">Inbound ACV per Deal</td>
            <td><span class="metric-num" style="color:var(--accent2)">~$8K</span></td>
            <td><span class="metric-num" style="color:var(--accent2)">~$10K</span></td>
            <td><span class="metric-num" style="color:var(--amber)">~$6K</span></td>
            <td><span class="badge amber">Volatile</span></td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>

  <!-- ============ FOUNDO ============ -->
  <div id="foundo" class="section">
    <div class="section-header">FoundU — Payroll Platform Performance</div>

    <div class="kpi-row">
      <div class="kpi" style="--kpi-color:#e8ff47">
        <div class="kpi-label">CY25 Total Spend</div>
        <div class="kpi-value">$889K</div>
        <div class="kpi-sub"><span class="badge red">-3.1% vs $918K budget</span></div>
      </div>
      <div class="kpi" style="--kpi-color:#47c7ff">
        <div class="kpi-label">CY26 Jan Spend</div>
        <div class="kpi-value">$65.5K</div>
        <div class="kpi-sub"><span class="badge green">+0.7% vs target</span></div>
      </div>
      <div class="kpi" style="--kpi-color:#4ade80">
        <div class="kpi-label">CY26 Feb Spend</div>
        <div class="kpi-value">$67.8K</div>
        <div class="kpi-sub"><span class="badge amber">-3.2% vs target</span></div>
      </div>
      <div class="kpi" style="--kpi-color:#a78bfa">
        <div class="kpi-label">CY26 Jan Sales</div>
        <div class="kpi-value">15</div>
        <div class="kpi-sub">Revenue $185K <span class="badge green">↑ vs $132K target</span></div>
      </div>
      <div class="kpi" style="--kpi-color:#ff6b6b">
        <div class="kpi-label">CY26 Feb Sales</div>
        <div class="kpi-value">13</div>
        <div class="kpi-sub">Revenue $184K <span class="badge amber">↓ vs $232K target</span></div>
      </div>
      <div class="kpi" style="--kpi-color:#e8ff47">
        <div class="kpi-label">2024 PPC ROAS</div>
        <div class="kpi-value">2.75x</div>
        <div class="kpi-sub">$1.2M spend → $3.3M TCV</div>
      </div>
    </div>

    <div class="section-header">CY26 Funnel Breakdown — January vs February</div>

    <div class="charts-row">
      <div class="chart-card">
        <div class="chart-title">FoundU CY26 — Inbound Funnel <span>Jan vs Feb</span></div>
        <div class="funnel">
          <div class="funnel-step">
            <div class="funnel-step-label">Leads (Inb.)</div>
            <div class="funnel-bar-wrap">
              <div class="funnel-bar-track">
                <div class="funnel-bar-fill" style="width:100%;background:var(--accent);color:#FFFFFF">Jan: 45 · Feb: 55</div>
              </div>
            </div>
          </div>
          <div class="arrow-down">↓ 36% / 24%</div>
          <div class="funnel-step">
            <div class="funnel-step-label">Mtg Booked</div>
            <div class="funnel-bar-wrap">
              <div class="funnel-bar-track">
                <div class="funnel-bar-fill" style="width:36%;background:var(--accent2)">Jan: 16 · Feb: 13</div>
              </div>
            </div>
          </div>
          <div class="arrow-down">↓ 81% / 69%</div>
          <div class="funnel-step">
            <div class="funnel-step-label">Mtg Sat</div>
            <div class="funnel-bar-wrap">
              <div class="funnel-bar-track">
                <div class="funnel-bar-fill" style="width:29%;background:var(--accent4)">Jan: 13 · Feb: 9</div>
              </div>
            </div>
          </div>
          <div class="arrow-down">↓ 69% / 44%</div>
          <div class="funnel-step">
            <div class="funnel-step-label">Closed Won</div>
            <div class="funnel-bar-wrap">
              <div class="funnel-bar-track">
                <div class="funnel-bar-fill" style="width:20%;background:var(--green)">Jan: 9 · Feb: 4</div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div class="chart-card">
        <div class="chart-title">FoundU CY26 — Outbound Funnel <span>Jan vs Feb</span></div>
        <div class="funnel">
          <div class="funnel-step">
            <div class="funnel-step-label">Leads (OB)</div>
            <div class="funnel-bar-wrap">
              <div class="funnel-bar-track">
                <div class="funnel-bar-fill" style="width:100%;background:var(--accent);color:#FFFFFF">Jan: 14 · Feb: 39</div>
              </div>
            </div>
          </div>
          <div class="arrow-down">↓ 57% / 31%</div>
          <div class="funnel-step">
            <div class="funnel-step-label">Mtg Booked</div>
            <div class="funnel-bar-wrap">
              <div class="funnel-bar-track">
                <div class="funnel-bar-fill" style="width:57%;background:var(--accent2)">Jan: 8 · Feb: 12</div>
              </div>
            </div>
          </div>
          <div class="arrow-down">↓ 75% / 67%</div>
          <div class="funnel-step">
            <div class="funnel-step-label">Mtg Sat</div>
            <div class="funnel-bar-wrap">
              <div class="funnel-bar-track">
                <div class="funnel-bar-fill" style="width:43%;background:var(--accent4)">Jan: 6 · Feb: 8</div>
              </div>
            </div>
          </div>
          <div class="arrow-down">↓ 100% / 113%</div>
          <div class="funnel-step">
            <div class="funnel-step-label">Closed Won</div>
            <div class="funnel-bar-wrap">
              <div class="funnel-bar-track">
                <div class="funnel-bar-fill" style="width:43%;background:var(--green)">Jan: 6 · Feb: 9</div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="section-header">CY26 Channel Performance — PPC vs MVF vs Organic</div>
    <div class="chart-card one-col">
      <div class="chart-title">FoundU — Inbound Channel Split CY26 YTD</div>
      <table class="comparison-table">
        <thead>
          <tr>
            <th>Channel</th>
            <th>Jan Leads</th>
            <th>Jan Sales</th>
            <th>Jan CPL</th>
            <th>Feb Leads</th>
            <th>Feb Sales</th>
            <th>Feb CPL</th>
            <th>Signal</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td class="metric-name">PPC (incl. Capterra)</td>
            <td><span class="metric-num" style="color:var(--text)">22</span></td>
            <td><span class="metric-num" style="color:var(--green)">6</span></td>
            <td style="color:var(--red)">$2,976</td>
            <td><span class="metric-num" style="color:var(--text)">37</span></td>
            <td><span class="metric-num" style="color:var(--amber)">3</span></td>
            <td style="color:var(--green)">$1,535</td>
            <td><span class="badge amber">CPL improving</span></td>
          </tr>
          <tr>
            <td class="metric-name">MVF</td>
            <td><span class="metric-num" style="color:var(--text)">18</span></td>
            <td><span class="metric-num" style="color:var(--green)">1</span></td>
            <td style="color:var(--green)">$500</td>
            <td><span class="metric-num" style="color:var(--text)">23</span></td>
            <td><span class="metric-num" style="color:var(--red)">0</span></td>
            <td style="color:var(--green)">$478</td>
            <td><span class="badge red">Low close rate</span></td>
          </tr>
          <tr>
            <td class="metric-name">Organic</td>
            <td><span class="metric-num" style="color:var(--text)">5</span></td>
            <td><span class="metric-num" style="color:var(--green)">1</span></td>
            <td style="color:var(--green)">Free</td>
            <td><span class="metric-num" style="color:var(--red)">0</span></td>
            <td><span class="metric-num" style="color:var(--green)">1</span></td>
            <td style="color:var(--green)">Free</td>
            <td><span class="badge amber">Volatile volume</span></td>
          </tr>
          <tr>
            <td class="metric-name">Outbound Total</td>
            <td><span class="metric-num" style="color:var(--text)">14</span></td>
            <td><span class="metric-num" style="color:var(--green)">6</span></td>
            <td>—</td>
            <td><span class="metric-num" style="color:var(--green)">39</span></td>
            <td><span class="metric-num" style="color:var(--green)">9</span></td>
            <td>—</td>
            <td><span class="badge green">Strong CR 40%+</span></td>
          </tr>
        </tbody>
      </table>
    </div>

    <div class="insights-grid">
      <div class="insight" style="--insight-color:#e8ff47">
        <div class="insight-title">Outbound Engine</div>
        <div class="insight-text">Outbound is delivering disproportionately high close rates (100%+ sat-to-sale in Jan). Feb outbound generated 39 leads with 9 sales — 23% end-to-end conversion. This is a key growth lever.</div>
      </div>
      <div class="insight" style="--insight-color:#f87171">
        <div class="insight-title">MVF Efficiency Gap</div>
        <div class="insight-text">MVF is generating volume at low CPL ($478–500) but converting poorly to sales. 18 MVF leads in Jan produced only 1 sale. Consider lead quality review or better sales handoff process.</div>
      </div>
      <div class="insight" style="--insight-color:#47c7ff">
        <div class="insight-title">AACV Trend</div>
        <div class="insight-text">Average ACV per FoundU deal CY26: Jan $12,314, Feb $14,149. Both ahead of the CY25 average CPL-derived target of $11,039. Deal quality is improving.</div>
      </div>
    </div>
  </div>

  <!-- ============ ISO ============ -->
  <div id="iso" class="section">
    <div class="section-header">ISO Certification & NDIS — Performance Overview</div>

    <div class="kpi-row">
      <div class="kpi" style="--kpi-color:#e8ff47">
        <div class="kpi-label">ISO CY25 Spend</div>
        <div class="kpi-value">$628K</div>
        <div class="kpi-sub"><span class="badge green">+2.9% vs $611K budget</span></div>
      </div>
      <div class="kpi" style="--kpi-color:#47c7ff">
        <div class="kpi-label">ISO CY25 Total Deals</div>
        <div class="kpi-value">175</div>
        <div class="kpi-sub">Online + Offline combined</div>
      </div>
      <div class="kpi" style="--kpi-color:#4ade80">
        <div class="kpi-label">ISO CY26 Jan Deals</div>
        <div class="kpi-value">22</div>
        <div class="kpi-sub"><span class="badge red">vs target 27 — 81%</span></div>
      </div>
      <div class="kpi" style="--kpi-color:#a78bfa">
        <div class="kpi-label">ISO CY26 Feb Deals</div>
        <div class="kpi-value">39</div>
        <div class="kpi-sub"><span class="badge amber">vs target 39 — on target</span></div>
      </div>
      <div class="kpi" style="--kpi-color:#ff6b6b">
        <div class="kpi-label">ISO CY26 Jan ACV</div>
        <div class="kpi-value">$105K</div>
        <div class="kpi-sub"><span class="badge red">vs $141K target — 74%</span></div>
      </div>
      <div class="kpi" style="--kpi-color:#e8ff47">
        <div class="kpi-label">ISO CY26 Feb ACV</div>
        <div class="kpi-value">$190K</div>
        <div class="kpi-sub"><span class="badge amber">vs $205K target — 92%</span></div>
      </div>
    </div>

    <div class="charts-row">
      <div class="chart-card">
        <div class="chart-title">ISO CY26 — Inbound (PPC) Funnel <span>Jan vs Feb</span></div>
        <div class="funnel">
          <div class="funnel-step">
            <div class="funnel-step-label">PPC Leads</div>
            <div class="funnel-bar-wrap">
              <div class="funnel-bar-track">
                <div class="funnel-bar-fill" style="width:100%;background:var(--accent);color:#FFFFFF">Jan: 86 · Feb: 92 (tgt 91–109)</div>
              </div>
            </div>
          </div>
          <div class="arrow-down">↓ 57% / 67%</div>
          <div class="funnel-step">
            <div class="funnel-step-label">Opps</div>
            <div class="funnel-bar-wrap">
              <div class="funnel-bar-track">
                <div class="funnel-bar-fill" style="width:57%;background:var(--accent2)">Jan: 49 · Feb: 62</div>
              </div>
            </div>
          </div>
          <div class="arrow-down">↓ 73% / 76%</div>
          <div class="funnel-step">
            <div class="funnel-step-label">Meetings Sat</div>
            <div class="funnel-bar-wrap">
              <div class="funnel-bar-track">
                <div class="funnel-bar-fill" style="width:42%;background:var(--accent4)">Jan: 36 · Feb: 47</div>
              </div>
            </div>
          </div>
          <div class="arrow-down">↓ 14% / 15%</div>
          <div class="funnel-step">
            <div class="funnel-step-label">Deals</div>
            <div class="funnel-bar-wrap">
              <div class="funnel-bar-track">
                <div class="funnel-bar-fill" style="width:12%;background:var(--green)">Jan: 5 · Feb: 7</div>
              </div>
              <div class="funnel-meta"><span style="color:var(--red)">Sat→Deal 14% — below target 20%</span></div>
            </div>
          </div>
        </div>
      </div>

      <div class="chart-card">
        <div class="chart-title">ISO CY26 — Outbound (BDM/Partner) Funnel <span>Jan vs Feb</span></div>
        <div class="funnel">
          <div class="funnel-step">
            <div class="funnel-step-label">OB Opps</div>
            <div class="funnel-bar-wrap">
              <div class="funnel-bar-track">
                <div class="funnel-bar-fill" style="width:100%;background:var(--accent);color:#FFFFFF">Jan: 55 · Feb: 50</div>
              </div>
            </div>
          </div>
          <div class="arrow-down">↓ 95% / 90%</div>
          <div class="funnel-step">
            <div class="funnel-step-label">Meetings Sat</div>
            <div class="funnel-bar-wrap">
              <div class="funnel-bar-track">
                <div class="funnel-bar-fill" style="width:95%;background:var(--accent2)">Jan: 52 · Feb: 45</div>
              </div>
            </div>
          </div>
          <div class="arrow-down">↓ 29% / 56%</div>
          <div class="funnel-step">
            <div class="funnel-step-label">Deals</div>
            <div class="funnel-bar-wrap">
              <div class="funnel-bar-track">
                <div class="funnel-bar-fill" style="width:27%;background:var(--green)">Jan: 15 · Feb: 25</div>
              </div>
              <div class="funnel-meta"><span style="color:var(--green)">Outbound closes 3x inbound rate</span></div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="section-header">ISO Channel Deep Dive — CY26 YTD</div>
    <div class="chart-card one-col">
      <div class="chart-title">ISO — All Channels Performance CY26 Jan + Feb</div>
      <table class="comparison-table">
        <thead>
          <tr>
            <th>Channel</th>
            <th>Jan Leads</th>
            <th>Jan Deals</th>
            <th>Jan ACV</th>
            <th>Feb Leads</th>
            <th>Feb Deals</th>
            <th>Feb ACV</th>
            <th>CPL</th>
            <th>Signal</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td class="metric-name">PPC (Inbound)</td>
            <td>86</td>
            <td><span style="color:var(--amber)">5</span></td>
            <td style="color:var(--amber)">$27K</td>
            <td>92</td>
            <td><span style="color:var(--green)">7</span></td>
            <td style="color:var(--amber)">$30K</td>
            <td style="color:var(--amber)">$608/$518</td>
            <td><span class="badge amber">Low close</span></td>
          </tr>
          <tr>
            <td class="metric-name">Organic (Inbound)</td>
            <td>17</td>
            <td><span style="color:var(--green)">2</span></td>
            <td style="color:var(--red)">$10K</td>
            <td>17</td>
            <td><span style="color:var(--green)">7</span></td>
            <td style="color:var(--green)">$41K</td>
            <td>Free</td>
            <td><span class="badge green">High value</span></td>
          </tr>
          <tr>
            <td class="metric-name">Content Mktg (Inb.)</td>
            <td>25</td>
            <td><span style="color:var(--red)">0</span></td>
            <td style="color:var(--red)">$0</td>
            <td>—</td>
            <td><span style="color:var(--green)">6</span></td>
            <td style="color:var(--green)">$15K</td>
            <td>—</td>
            <td><span class="badge amber">Lag effect</span></td>
          </tr>
          <tr>
            <td class="metric-name">Outbound (BDM)</td>
            <td>—</td>
            <td><span style="color:var(--green)">15</span></td>
            <td style="color:var(--green)">$68K</td>
            <td>—</td>
            <td><span style="color:var(--green)">25</span></td>
            <td style="color:var(--green)">$120K</td>
            <td>—</td>
            <td><span class="badge green">Dominant</span></td>
          </tr>
          <tr>
            <td class="metric-name">Juice Cards/Cross</td>
            <td>—</td>
            <td>—</td>
            <td>—</td>
            <td>—</td>
            <td><span style="color:var(--green)">4</span></td>
            <td style="color:var(--green)">$22K</td>
            <td>—</td>
            <td><span class="badge green">Emerging</span></td>
          </tr>
          <tr>
            <td class="metric-name">Refer & Earn</td>
            <td>—</td>
            <td>—</td>
            <td>—</td>
            <td>—</td>
            <td><span style="color:var(--green)">4</span></td>
            <td style="color:var(--green)">$24K</td>
            <td>—</td>
            <td><span class="badge green">Strong R&E</span></td>
          </tr>
          <tr>
            <td class="metric-name">Partner Channel</td>
            <td>—</td>
            <td>—</td>
            <td>—</td>
            <td>—</td>
            <td><span style="color:var(--green)">6</span></td>
            <td style="color:var(--green)">$30K</td>
            <td>—</td>
            <td><span class="badge green">Good ACV</span></td>
          </tr>
        </tbody>
      </table>
    </div>

    <div class="insights-grid">
      <div class="insight" style="--insight-color:#e8ff47">
        <div class="insight-title">Outbound Dominance</div>
        <div class="insight-text">ISO offline/partner channel generates 57% of deals — the tracker labels this "Offline" and it likely reflects partner-referred and offline-sourced leads rather than BDM cold outreach. Feb outbound deals (25) doubled Jan (15) — key acceleration driver heading into Q1 2026.</div>
      </div>
      <div class="insight" style="--insight-color:#f87171">
        <div class="insight-title">PPC Sat→Deal Gap</div>
        <div class="insight-text">ISO PPC funnel has strong lead-to-opp rates (~67%) but Sat-to-Deal is only 14% vs 20% target. 36 meetings in Jan produced only 5 deals — a major conversion leak at the bottom of the funnel.</div>
      </div>
      <div class="insight" style="--insight-color:#4ade80">
        <div class="insight-title">Refer & Earn Upside</div>
        <div class="insight-text">Feb saw 4 R&E deals worth $24K ACV with essentially zero acquisition cost. The program is nascent — scaling investment here could yield high-ROI growth with minimal incremental spend.</div>
      </div>
    </div>
  </div>

  <!-- ============ EFFICIENCY ============ -->
  <div id="efficiency" class="section">
    <div class="section-header">Marketing Efficiency & PPC Performance</div>

    <div class="kpi-row">
      <div class="kpi" style="--kpi-color:#e8ff47">
        <div class="kpi-label">2024 PPC ROAS (TCV)</div>
        <div class="kpi-value">2.75x</div>
        <div class="kpi-sub">$1.21M spend → $3.32M TCV</div>
      </div>
      <div class="kpi" style="--kpi-color:#47c7ff">
        <div class="kpi-label">Q1 CY25 PPC ROAS</div>
        <div class="kpi-value">2.55x</div>
        <div class="kpi-sub">$345K spend → $880K TCV</div>
      </div>
      <div class="kpi" style="--kpi-color:#4ade80">
        <div class="kpi-label">CHR CPL (CY25 avg)</div>
        <div class="kpi-value">$1,800</div>
        <div class="kpi-sub">Range $1,430–$2,175</div>
      </div>
      <div class="kpi" style="--kpi-color:#a78bfa">
        <div class="kpi-label">ISO CPL (CY25 avg)</div>
        <div class="kpi-value">$630</div>
        <div class="kpi-sub">Range $431–$694</div>
      </div>
      <div class="kpi" style="--kpi-color:#ff6b6b">
        <div class="kpi-label">Attribution Coverage</div>
        <div class="kpi-value">54%</div>
        <div class="kpi-sub">~15–20% untracked in CHR/ISO</div>
      </div>
      <div class="kpi" style="--kpi-color:#e8ff47">
        <div class="kpi-label">CY25 Avg aACV CHR</div>
        <div class="kpi-value">~$8K</div>
        <div class="kpi-sub">CY26 trending ~$10–14K</div>
      </div>
    </div>

    <div class="section-header">Brand CPL Comparison — CY25 vs CY26 YTD</div>
    <div class="charts-row">
      <div class="chart-card">
        <div class="chart-title">Cost Per Lead — Monthly Trend CHR AU <span>CY25</span></div>
        <div class="bar-chart">
          <div class="bar-row"><div class="bar-label">Jan</div><div class="bar-track"><div class="bar-fill" style="width:78%;background:var(--red)"></div></div><div class="bar-val">$1,703</div><div class="bar-vs" style="color:var(--red)">↑ high</div></div>
          <div class="bar-row"><div class="bar-label">Feb</div><div class="bar-track"><div class="bar-fill" style="width:84%;background:var(--amber)"></div></div><div class="bar-val">$1,831</div><div class="bar-vs" style="color:var(--amber)"></div></div>
          <div class="bar-row"><div class="bar-label">Mar</div><div class="bar-track"><div class="bar-fill" style="width:77%;background:var(--green)"></div></div><div class="bar-val">$1,679</div><div class="bar-vs" style="color:var(--green)">↓ OK</div></div>
          <div class="bar-row"><div class="bar-label">Apr</div><div class="bar-track"><div class="bar-fill" style="width:86%;background:var(--amber)"></div></div><div class="bar-val">$1,871</div><div class="bar-vs"></div></div>
          <div class="bar-row"><div class="bar-label">May</div><div class="bar-track"><div class="bar-fill" style="width:64%;background:var(--green)"></div></div><div class="bar-val">$1,401</div><div class="bar-vs" style="color:var(--green)">Best</div></div>
          <div class="bar-row"><div class="bar-label">Jun</div><div class="bar-track"><div class="bar-fill" style="width:81%;background:var(--amber)"></div></div><div class="bar-val">$1,758</div><div class="bar-vs"></div></div>
          <div class="bar-row"><div class="bar-label">Jul</div><div class="bar-track"><div class="bar-fill" style="width:90%;background:var(--red)"></div></div><div class="bar-val">$1,968</div><div class="bar-vs" style="color:var(--red)">High</div></div>
          <div class="bar-row"><div class="bar-label">Aug</div><div class="bar-track"><div class="bar-fill" style="width:88%;background:var(--red)"></div></div><div class="bar-val">$1,913</div><div class="bar-vs"></div></div>
          <div class="bar-row"><div class="bar-label">Sep</div><div class="bar-track"><div class="bar-fill" style="width:95%;background:var(--red)"></div></div><div class="bar-val">$2,066</div><div class="bar-vs" style="color:var(--red)">Worst</div></div>
          <div class="bar-row"><div class="bar-label">Oct</div><div class="bar-track"><div class="bar-fill" style="width:100%;background:var(--red)"></div></div><div class="bar-val">$2,175</div><div class="bar-vs" style="color:var(--red)">Peak</div></div>
          <div class="bar-row"><div class="bar-label">Nov</div><div class="bar-track"><div class="bar-fill" style="width:87%;background:var(--amber)"></div></div><div class="bar-val">$1,900</div><div class="bar-vs"></div></div>
          <div class="bar-row"><div class="bar-label">Dec</div><div class="bar-track"><div class="bar-fill" style="width:66%;background:var(--green)"></div></div><div class="bar-val">$1,431</div><div class="bar-vs" style="color:var(--green)">↓ Good</div></div>
        </div>
      </div>
      <div class="chart-card">
        <div class="chart-title">Cost Per Lead — ISO vs FoundU <span>CY25 monthly</span></div>
        <div class="bar-chart">
          <div style="margin-bottom:10px;font-size:10px;color:var(--muted)">ISO PPC CPL</div>
          <div class="bar-row"><div class="bar-label">Jan</div><div class="bar-track"><div class="bar-fill" style="width:100%;background:var(--accent)"></div></div><div class="bar-val">$636</div><div class="bar-vs" style="color:var(--red)">High</div></div>
          <div class="bar-row"><div class="bar-label">Feb</div><div class="bar-track"><div class="bar-fill" style="width:68%;background:var(--green)"></div></div><div class="bar-val">$431</div><div class="bar-vs" style="color:var(--green)">Best</div></div>
          <div class="bar-row"><div class="bar-label">Jun</div><div class="bar-track"><div class="bar-fill" style="width:75%;background:var(--accent2)"></div></div><div class="bar-val">$477</div><div class="bar-vs"></div></div>
          <div class="bar-row"><div class="bar-label">Sep</div><div class="bar-track"><div class="bar-fill" style="width:100%;background:var(--red)"></div></div><div class="bar-val">$662</div><div class="bar-vs" style="color:var(--red)">High</div></div>
          <div class="bar-row"><div class="bar-label">Dec</div><div class="bar-track"><div class="bar-fill" style="width:100%;background:var(--amber)"></div></div><div class="bar-val">$638</div><div class="bar-vs"></div></div>
          <div style="margin:14px 0 10px;font-size:10px;color:var(--muted)">FoundU CPL</div>
          <div class="bar-row"><div class="bar-label">Jan 26</div><div class="bar-track"><div class="bar-fill" style="width:100%;background:var(--red)"></div></div><div class="bar-val">$2,976</div><div class="bar-vs" style="color:var(--red)">High</div></div>
          <div class="bar-row"><div class="bar-label">Feb 26</div><div class="bar-track"><div class="bar-fill" style="width:52%;background:var(--green)"></div></div><div class="bar-val">$1,535</div><div class="bar-vs" style="color:var(--green)">↓ 48%</div></div>
        </div>
      </div>
    </div>

    <div class="section-header">Strategic Recommendations</div>
    <div class="insights-grid">
      <div class="insight" style="--insight-color:#e8ff47">
        <div class="insight-title">1 — Standardise Attribution Tracking</div>
        <div class="insight-text">CHR/ISO: ~15–20% of leads untracked. FoundU payroll recognised via ELM in HubSpot, not standard lead attribution. Standardise UTM tagging and CRM source fields across all brands.</div>
      </div>
      <div class="insight" style="--insight-color:#47c7ff">
        <div class="insight-title">2 — Sat→Deal Is The Leak</div>
        <div class="insight-text">ISO PPC sat-to-deal is 14% vs 20% target; CHR declining from 24% to 18% in CY26. Pipeline is healthy — the problem is conversion at close. Sales coaching and process audit needed.</div>
      </div>
      <div class="insight" style="--insight-color:#4ade80">
        <div class="insight-title">3 — Double Down on Outbound</div>
        <div class="insight-text">Across all brands, outbound delivers 2–3x higher close rates than inbound at equivalent or lower cost. Both FoundU and ISO outbound are gaining momentum — invest in BDM headcount and sequences.</div>
      </div>
      <div class="insight" style="--insight-color:#a78bfa">
        <div class="insight-title">4 — CHR CPL Seasonal Pattern</div>
        <div class="insight-text">CHR PPC CPL peaks in Aug–Oct ($1,900–2,175) and troughs in May ($1,401) and Dec ($1,431). Reallocate budget to off-peak months for efficiency gains of 30%+.</div>
      </div>
      <div class="insight" style="--insight-color:#fbbf24">
        <div class="insight-title">5 — H2 Revenue Recovery</div>
        <div class="insight-text">CY25 H2 ACV underperformed budget significantly. Nov was 48% of budget ($252K vs $526K). This appears structural, not just seasonal. Review SDR/BDM resource allocation in Q3–Q4.</div>
      </div>
      <div class="insight" style="--insight-color:#f87171">
        <div class="insight-title">6 — NZ/enableHR Track</div>
        <div class="insight-text">enableHR AU and NZ show no tracked spend or leads in either CY25 or CY26 — these are completely blank. If active, data collection is broken. If inactive, formally sunset the tracking template.</div>
      </div>
    </div>
  </div>

</div>

<!-- CHR AACV OVERLAY TAB -->
<div id="aacv" class="section">
  <div class="section-header">
    <div class="section-title">CHR — Average ACV Analysis</div>
    <div class="section-subtitle">CY25 AACV by channel and monthly trend · CY26 YTD vs prior year</div>
  </div>

  <!-- Channel AACV Cards -->
  <div class="kpi-grid" style="grid-template-columns:repeat(3,1fr);margin-bottom:32px">
    <div class="kpi-card">
      <div class="kpi-label">Inbound Blended AACV</div>
      <div class="kpi-value" style="color:var(--accent)">$11,522</div>
      <div class="kpi-sub">CY25 avg · range $7.5K–$17.8K</div>
    </div>
    <div class="kpi-card">
      <div class="kpi-label">PPC Channel AACV</div>
      <div class="kpi-value" style="color:var(--accent2)">$10,477</div>
      <div class="kpi-sub">CY25 avg · range $5.9K–$18.3K</div>
    </div>
    <div class="kpi-card">
      <div class="kpi-label">MVF Channel AACV</div>
      <div class="kpi-value" style="color:var(--accent4)">$14,256</div>
      <div class="kpi-sub">CY25 avg · highest channel avg · range $5K–$37K</div>
    </div>
    <div class="kpi-card">
      <div class="kpi-label">Organic/Referral AACV</div>
      <div class="kpi-value" style="color:var(--green)">$11,238</div>
      <div class="kpi-sub">CY25 avg · range $5.0K–$19.5K</div>
    </div>
    <div class="kpi-card">
      <div class="kpi-label">Outbound Blended AACV</div>
      <div class="kpi-value" style="color:var(--accent)">$11,119</div>
      <div class="kpi-sub">CY25 avg · range $7.4K–$15.1K</div>
    </div>
    <div class="kpi-card">
      <div class="kpi-label">Offline/Partner Channel AACV</div>
      <div class="kpi-value" style="color:var(--amber)">$8,054</div>
      <div class="kpi-sub">CY25 avg — lowest channel · high vol, lower deal size</div>
    </div>
  </div>

  <!-- CY26 vs CY25 AACV comparison -->
  <div style="display:grid;grid-template-columns:1fr 1fr;gap:24px;margin-bottom:32px">
    <div class="kpi-card" style="padding:24px">
      <div class="kpi-label" style="margin-bottom:16px">CY25 Blended Monthly AACV — Inbound vs Outbound</div>
      <div id="aacv-monthly-chart"></div>
      <div style="display:flex;gap:16px;margin-top:12px;font-size:10px;color:var(--muted)">
        <span style="display:flex;align-items:center;gap:5px"><span style="width:12px;height:3px;background:var(--accent);display:inline-block;border-radius:2px"></span>Inbound AACV</span>
        <span style="display:flex;align-items:center;gap:5px"><span style="width:12px;height:3px;background:var(--accent2);display:inline-block;border-radius:2px"></span>Outbound AACV</span>
      </div>
    </div>
    <div class="kpi-card" style="padding:24px">
      <div class="kpi-label" style="margin-bottom:16px">CY26 Jan–Feb AACV vs CY25 Same Period</div>
      <div id="aacv-yoy-chart"></div>
      <div style="font-size:11px;color:var(--muted);margin-top:16px;line-height:1.6">
        Jan CY26 AACV <span style="color:var(--green)">+25%</span> vs Jan CY25 ($9,837 vs $7,859)<br>
        Feb CY26 AACV <span style="color:var(--red)">-22%</span> vs Feb CY25 ($11,743 vs $15,103)<br>
        Feb variance driven by deal mix — CY25 Feb had unusually large contract cluster
      </div>
    </div>
  </div>

  <!-- Channel AACV Comparison Table -->
  <div class="kpi-card" style="padding:24px;margin-bottom:24px">
    <div class="kpi-label" style="margin-bottom:16px">CY25 Channel AACV — Full Year Summary</div>
    <div style="overflow-x:auto">
      <table style="width:100%;border-collapse:collapse;font-size:11px">
        <thead>
          <tr style="border-bottom:1px solid var(--border)">
            <th style="text-align:left;padding:8px;color:var(--muted);font-weight:400">Channel</th>
            <th style="text-align:right;padding:8px;color:var(--muted);font-weight:400">Avg AACV</th>
            <th style="text-align:right;padding:8px;color:var(--muted);font-weight:400">Min</th>
            <th style="text-align:right;padding:8px;color:var(--muted);font-weight:400">Max</th>
            <th style="text-align:right;padding:8px;color:var(--muted);font-weight:400">vs Brand Avg</th>
            <th style="text-align:left;padding:8px;color:var(--muted);font-weight:400">Signal</th>
          </tr>
        </thead>
        <tbody>
          <tr style="border-bottom:1px solid var(--border)">
            <td style="padding:8px;color:var(--text)">MVF Enquiries</td>
            <td style="padding:8px;text-align:right;color:var(--accent4)">$14,256</td>
            <td style="padding:8px;text-align:right;color:var(--muted)">$5,000</td>
            <td style="padding:8px;text-align:right;color:var(--muted)">$37,250</td>
            <td style="padding:8px;text-align:right;color:var(--green)">+28%</td>
            <td style="padding:8px;color:var(--muted)">Highest avg — enterprise-skewed inquiries</td>
          </tr>
          <tr style="border-bottom:1px solid var(--border)">
            <td style="padding:8px;color:var(--text)">Inbound Blended</td>
            <td style="padding:8px;text-align:right;color:var(--accent)">$11,522</td>
            <td style="padding:8px;text-align:right;color:var(--muted)">$7,545</td>
            <td style="padding:8px;text-align:right;color:var(--muted)">$17,770</td>
            <td style="padding:8px;text-align:right;color:var(--green)">+4%</td>
            <td style="padding:8px;color:var(--muted)">Solid baseline — stable with seasonal dip Nov–Jan</td>
          </tr>
          <tr style="border-bottom:1px solid var(--border)">
            <td style="padding:8px;color:var(--text)">Organic/Referral</td>
            <td style="padding:8px;text-align:right;color:var(--green)">$11,238</td>
            <td style="padding:8px;text-align:right;color:var(--muted)">$5,048</td>
            <td style="padding:8px;text-align:right;color:var(--muted)">$19,479</td>
            <td style="padding:8px;text-align:right;color:var(--green)">+1%</td>
            <td style="padding:8px;color:var(--muted)">Strong Jul spike — likely enterprise renewals or referrals</td>
          </tr>
          <tr style="border-bottom:1px solid var(--border)">
            <td style="padding:8px;color:var(--text)">Outbound Blended</td>
            <td style="padding:8px;text-align:right;color:var(--accent2)">$11,119</td>
            <td style="padding:8px;text-align:right;color:var(--muted)">$7,396</td>
            <td style="padding:8px;text-align:right;color:var(--muted)">$15,103</td>
            <td style="padding:8px;text-align:right;color:var(--muted)">+0%</td>
            <td style="padding:8px;color:var(--muted)">Consistent — least volatile channel by AACV</td>
          </tr>
          <tr style="border-bottom:1px solid var(--border)">
            <td style="padding:8px;color:var(--text)">PPC</td>
            <td style="padding:8px;text-align:right;color:var(--accent2)">$10,477</td>
            <td style="padding:8px;text-align:right;color:var(--muted)">$5,970</td>
            <td style="padding:8px;text-align:right;color:var(--muted)">$18,285</td>
            <td style="padding:8px;text-align:right;color:var(--red)">-6%</td>
            <td style="padding:8px;color:var(--muted)">Slightly below avg — volume channel, smaller deals</td>
          </tr>
          <tr>
            <td style="padding:8px;color:var(--text)">BDM Generated</td>
            <td style="padding:8px;text-align:right;color:var(--amber)">$8,054</td>
            <td style="padding:8px;text-align:right;color:var(--muted)">$1,650</td>
            <td style="padding:8px;text-align:right;color:var(--muted)">$26,670</td>
            <td style="padding:8px;text-align:right;color:var(--red)">-28%</td>
            <td style="padding:8px;color:var(--muted)">Lowest avg — BDMs converting SMB end of market</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>

  <!-- H1/H2 AACV Seasonal Pattern -->
  <div class="kpi-card" style="padding:24px">
    <div class="kpi-label" style="margin-bottom:4px">Seasonal AACV Pattern — Key Insight</div>
    <div class="kpi-sub" style="margin-bottom:16px">H2 AACV $10,665 vs H1 $11,574 — 7.8% decline in second half. Deal quality degrades alongside the volume drop.</div>
    <div id="aacv-seasonal-chart"></div>
    <div class="insight" style="--insight-color:var(--amber);margin-top:16px">
      <div class="insight-title">AACV × Volume = Double Compression in H2</div>
      <div class="insight-text">CHR loses revenue in H2 via two mechanisms simultaneously: fewer deals close (volume -35% H2 vs H1) AND each deal is worth less (-7.8%). Aug–Sep show elevated AACV ($11.4K–$13.4K) but Nov–Dec collapse to $7.4K–$10.3K. This suggests Dec closes are predominantly SMB end-of-year pressured deals. Consider a structured mid-market focus in Nov–Dec to protect AACV floor.</div>
    </div>
  </div>
</div>

<!-- FOUNDO ROLLING 3-MONTH TAB -->
<div id="foundo-rolling" class="section">
  <div class="section-header">
    <div class="section-title">FoundU — Rolling 3-Month Analysis</div>
    <div class="section-subtitle">90-day sales cycle adjusted view · 2023–2026 momentum tracking</div>
  </div>

  <!-- Key framing note -->
  <div class="insight" style="--insight-color:var(--accent2);margin-bottom:28px">
    <div class="insight-title">Why Rolling 3-Month Matters for FoundU</div>
    <div class="insight-text">With a ~90-day sales cycle, any single month's closed revenue reflects activity that started 3 months prior. Monthly snapshots create false reads — a strong Jan close was seeded in October. Rolling 3-month windows smooth this lag, align revenue to the actual marketing and SDR activity that generated it, and give a more accurate read on pipeline momentum and CAC efficiency.</div>
  </div>

  <!-- Rolling KPI cards -->
  <div class="kpi-grid" style="grid-template-columns:repeat(4,1fr);margin-bottom:32px">
    <div class="kpi-card">
      <div class="kpi-label">2024 Peak Rolling Window</div>
      <div class="kpi-value" style="color:var(--accent)">56</div>
      <div class="kpi-sub">Sales in May–Jul & Jun–Aug windows — cycle peak</div>
    </div>
    <div class="kpi-card">
      <div class="kpi-label">2024 Avg Rolling (3mo)</div>
      <div class="kpi-value" style="color:var(--accent2)">48.9</div>
      <div class="kpi-sub">Per rolling window · Mar–Dec</div>
    </div>
    <div class="kpi-card">
      <div class="kpi-label">2025 H1 Avg Rolling (3mo)</div>
      <div class="kpi-value" style="color:var(--green)">44.3</div>
      <div class="kpi-sub">Per rolling window · Mar–Jun · slightly below 2024 pace</div>
    </div>
    <div class="kpi-card">
      <div class="kpi-label">2026 YTD Revenue Run Rate</div>
      <div class="kpi-value" style="color:var(--accent)">$2.2M</div>
      <div class="kpi-sub">Implied annual from Jan–Feb ($369K × 6) — 2.5× CY25</div>
    </div>
  </div>

  <!-- Rolling 3-month sales chart -->
  <div style="display:grid;grid-template-columns:1fr 1fr;gap:24px;margin-bottom:32px">
    <div class="kpi-card" style="padding:24px">
      <div class="kpi-label" style="margin-bottom:16px">Rolling 3-Month Sales — 2024 (Full Year)</div>
      <div id="foundo-rolling-2024"></div>
      <div style="font-size:11px;color:var(--muted);margin-top:12px">Peak window: May–Jul and Jun–Aug at 56 sales. Strong mid-year momentum then dip in Sep–Nov (45–46 range).</div>
    </div>
    <div class="kpi-card" style="padding:24px">
      <div class="kpi-label" style="margin-bottom:16px">Rolling 3-Month Sales — 2025 H1 (Jan–Jun available)</div>
      <div id="foundo-rolling-2025"></div>
      <div style="font-size:11px;color:var(--muted);margin-top:12px">2025 H1 rolling peak at Apr–Jun: 56 (matching 2024 peak). Trajectory strong — Jan–Mar trough at 34 reflects Oct–Dec 2024 pipeline softness.</div>
    </div>
  </div>

  <!-- Revenue momentum with 90-day attribution framing -->
  <div style="display:grid;grid-template-columns:1fr 1fr;gap:24px;margin-bottom:32px">
    <div class="kpi-card" style="padding:24px">
      <div class="kpi-label" style="margin-bottom:16px">Monthly Sales Volume — 2023–2026</div>
      <div id="foundo-monthly-history"></div>
    </div>
    <div class="kpi-card" style="padding:24px">
      <div class="kpi-label" style="margin-bottom:4px">CY26 Monthly Revenue & Deal Size</div>
      <div class="kpi-sub" style="margin-bottom:16px">Jan–Feb 2026 actuals</div>
      <div style="display:grid;grid-template-columns:1fr 1fr;gap:12px">
        <div style="background:var(--surface2);padding:16px;border-radius:8px">
          <div style="font-size:10px;color:var(--muted);margin-bottom:8px">JANUARY</div>
          <div style="font-size:22px;font-family:'Syne',sans-serif;color:var(--accent);font-weight:700">$184.7K</div>
          <div style="font-size:11px;color:var(--muted);margin-top:4px">15 sales closed</div>
          <div style="font-size:11px;color:var(--muted)">AACV $12,314</div>
          <div style="font-size:11px;color:var(--muted)">Inbound 9 · Outbound 6</div>
        </div>
        <div style="background:var(--surface2);padding:16px;border-radius:8px">
          <div style="font-size:10px;color:var(--muted);margin-bottom:8px">FEBRUARY</div>
          <div style="font-size:22px;font-family:'Syne',sans-serif;color:var(--green);font-weight:700">$183.9K</div>
          <div style="font-size:11px;color:var(--muted);margin-top:4px">13 sales closed</div>
          <div style="font-size:11px;color:var(--muted)">AACV $14,149</div>
          <div style="font-size:11px;color:var(--muted)">Inbound 4 · Outbound 9</div>
        </div>
      </div>
      <div style="margin-top:16px;padding:12px;background:var(--surface2);border-radius:8px;font-size:11px;color:var(--muted);line-height:1.7">
        <span style="color:var(--accent)">AACV trending up:</span> Feb AACV $14,149 vs Jan $12,314 (+15%) — outbound mix shift in Feb (9 of 13 sales) driving larger deal sizes, consistent with outbound superiority pattern.<br><br>
        <span style="color:var(--accent)">90-day attribution:</span> Jan–Feb CY26 closes = deals created Oct–Nov CY25. The pipeline seeded now (Feb–Mar) will close Apr–May.
      </div>
    </div>
  </div>

  <!-- Year-on-year comparison -->
  <div class="kpi-card" style="padding:24px;margin-bottom:24px">
    <div class="kpi-label" style="margin-bottom:16px">Year-on-Year Sales Volume Comparison — Same Months</div>
    <div id="foundo-yoy-chart"></div>
    <div style="display:flex;gap:16px;margin-top:12px;font-size:10px;color:var(--muted)">
      <span style="display:flex;align-items:center;gap:5px"><span style="width:12px;height:3px;background:#6b7080;display:inline-block;border-radius:2px"></span>2023</span>
      <span style="display:flex;align-items:center;gap:5px"><span style="width:12px;height:3px;background:var(--accent2);display:inline-block;border-radius:2px"></span>2024</span>
      <span style="display:flex;align-items:center;gap:5px"><span style="width:12px;height:3px;background:var(--accent);display:inline-block;border-radius:2px"></span>2025</span>
      <span style="display:flex;align-items:center;gap:5px"><span style="width:12px;height:3px;background:var(--green);display:inline-block;border-radius:2px"></span>2026</span>
    </div>
  </div>

  <!-- Rolling window interpretation -->
  <div class="insight" style="--insight-color:var(--green)">
    <div class="insight-title">Rolling Analysis Read: What the 90-Day Window Tells You Now</div>
    <div class="insight-text">The Feb CY26 data point closes the Nov–Jan rolling window at 28 sales (15+13). Comparing to the same window in 2024 (Nov–Jan not available) and 2025 (Jan–Mar = 34), the pace looks slightly below 2025 H1. However, the revenue story is different — AACV is rising ($12.3K → $14.1K), meaning fewer but larger deals. The critical window to watch: the May–Jun close data will reveal whether Oct–Nov CY25 pipeline activity (which drove outbound dominance in Feb) translates to a strong Q2. If May–Jun closes ≥26 sales at $13K+ AACV, FoundU is on track for $2M+ annualised.</div>
  </div>
</div>
  <!-- Rolling window interpretation -->
  <div class="insight" style="--insight-color:var(--green)">
    <div class="insight-title">Rolling Analysis Read: What the 90-Day Window Tells You Now</div>
    <div class="insight-text">The Feb CY26 data point closes the Nov–Jan rolling window at 28 sales (15+13). Comparing to the same window in 2024 (Nov–Jan not available) and 2025 (Jan–Mar = 34), the pace looks slightly below 2025 H1. However, the revenue story is different — AACV is rising ($12.3K → $14.1K), meaning fewer but larger deals. The critical window to watch: the May–Jun close data will reveal whether Oct–Nov CY25 pipeline activity (which drove outbound dominance in Feb) translates to a strong Q2. If May–Jun closes ≥26 sales at $13K+ AACV, FoundU is on track for $2M+ annualised.</div>
  </div>
</div>

<!-- ACV:CAC RATIOS TAB -->
<div id="cac" class="section">
  <div class="section-header">
    <div class="section-title">ACV:CAC Ratios — All Brands</div>
    <div class="section-subtitle">Revenue generated per dollar of customer acquisition cost · CY25 full year + CY26 YTD</div>
  </div>

  <!-- Brand Comparison Cards -->
  <div style="display:grid;grid-template-columns:repeat(3,1fr);gap:16px;margin-bottom:28px">
    <div class="kpi-card" style="padding:20px">
      <div class="kpi-label" style="margin-bottom:6px">HR & SAFETY AU — CY25</div>
      <div style="font-size:36px;font-family:'Syne',sans-serif;color:var(--accent);font-weight:700">2.7x</div>
      <div class="kpi-sub" style="margin-top:4px">ACV:CAC ratio · $11,174 AACV / $4,195 CAC</div>
      <div style="margin-top:12px;padding-top:12px;border-top:1px solid var(--border)">
        <div style="display:flex;justify-content:space-between;font-size:10px;color:var(--muted);margin-bottom:4px"><span>Total Spend</span><span style="color:var(--text)">$1,661,214</span></div>
        <div style="display:flex;justify-content:space-between;font-size:10px;color:var(--muted);margin-bottom:4px"><span>Total ACV</span><span style="color:var(--text)">$4,425,027</span></div>
        <div style="display:flex;justify-content:space-between;font-size:10px;color:var(--muted);margin-bottom:4px"><span>Deals Closed</span><span style="color:var(--text)">396</span></div>
        <div style="display:flex;justify-content:space-between;font-size:10px;color:var(--muted)"><span>Revenue per $1 spent</span><span style="color:var(--accent)">$2.66</span></div>
      </div>
    </div>
    <div class="kpi-card" style="padding:20px">
      <div class="kpi-label" style="margin-bottom:6px">FOUNDO — CY25 (Apr–Dec)</div>
      <div style="font-size:36px;font-family:'Syne',sans-serif;color:var(--green);font-weight:700">3.5x</div>
      <div class="kpi-sub" style="margin-top:4px">ACV:CAC ratio · $13,967 AACV / $4,033 CAC</div>
      <div style="margin-top:12px;padding-top:12px;border-top:1px solid var(--border)">
        <div style="display:flex;justify-content:space-between;font-size:10px;color:var(--muted);margin-bottom:4px"><span>Total Spend</span><span style="color:var(--text)">$661,335</span></div>
        <div style="display:flex;justify-content:space-between;font-size:10px;color:var(--muted);margin-bottom:4px"><span>Total ACV</span><span style="color:var(--text)">$2,290,557</span></div>
        <div style="display:flex;justify-content:space-between;font-size:10px;color:var(--muted);margin-bottom:4px"><span>Deals Closed</span><span style="color:var(--text)">164</span></div>
        <div style="display:flex;justify-content:space-between;font-size:10px;color:var(--muted)"><span>Revenue per $1 spent</span><span style="color:var(--green)">$3.46</span></div>
      </div>
    </div>
    <div class="kpi-card" style="padding:20px">
      <div class="kpi-label" style="margin-bottom:6px">ISO CERTIFICATION — CY25</div>
      <div style="font-size:36px;font-family:'Syne',sans-serif;color:var(--amber);font-weight:700">~1.6x</div>
      <div class="kpi-sub" style="margin-top:4px">ACV:CAC ratio · ~$5,500 AACV / $3,445 CAC</div>
      <div style="margin-top:12px;padding-top:12px;border-top:1px solid var(--border)">
        <div style="display:flex;justify-content:space-between;font-size:10px;color:var(--muted);margin-bottom:4px"><span>Total Spend</span><span style="color:var(--text)">$602,835</span></div>
        <div style="display:flex;justify-content:space-between;font-size:10px;color:var(--muted);margin-bottom:4px"><span>Deals Closed</span><span style="color:var(--text)">175</span></div>
        <div style="display:flex;justify-content:space-between;font-size:10px;color:var(--muted);margin-bottom:4px"><span>CAC</span><span style="color:var(--text)">$3,445</span></div>
        <div style="display:flex;justify-content:space-between;font-size:10px;color:var(--muted)"><span>Note</span><span style="color:var(--amber)">Outbound not in denominator</span></div>
      </div>
    </div>
  </div>

  <!-- YoY Ratio Comparison -->
  <div style="display:grid;grid-template-columns:1fr 1fr;gap:24px;margin-bottom:28px">
    <div class="kpi-card" style="padding:22px">
      <div class="kpi-label" style="margin-bottom:14px">ACV:CAC Ratio — CY25 vs CY26 YTD</div>
      <div id="cac-yoy-chart"></div>
      <div style="font-size:10px;color:var(--muted);margin-top:12px;line-height:1.7">
        CHR ratio deteriorating: <span style="color:var(--red)">2.7x → 1.8x YoY</span> — CAC up 43% ($4,195 → $6,003) as NZ budget absorbed into AU with lower H1 efficiency.<br>
        FoundU holding: <span style="color:var(--green)">3.5x → 3.0x</span> — marginal compression, still best ratio in portfolio.<br>
        ISO surging: <span style="color:var(--accent)">~1.6x → 3.0x YTD</span> — Feb outbound surge driving dramatic efficiency improvement.
      </div>
    </div>
    <div class="kpi-card" style="padding:22px">
      <div class="kpi-label" style="margin-bottom:14px">FoundU PPC — Brand vs Non-Brand ACV:CAC</div>
      <div id="foundo-brand-chart"></div>
      <div class="insight" style="--insight-color:var(--amber);margin-top:14px">
        <div class="insight-title">Non-Brand PPC: ACV:CAC of 0.4x — Requires Context</div>
        <div class="insight-text">Non-Brand PPC generated 21 deals at $29,367 CAC vs Brand PPC at $4,766 CAC. ACV:CAC is a single-year view — LTV:CAC is the industry standard for B2B SaaS/payroll and may tell a different story if average contract duration is 3+ years. Sep 2025 structural changes (negative keywords, enterprise campaigns, portfolio bidding) are already improving Lead→Opp conversion. Brand PPC is intentionally scaled back — brand awareness investment is needed before this channel can be maximised.</div>
      </div>
    </div>
  </div>

  <!-- CAC Monthly Trend CHR -->
  <div class="kpi-card" style="padding:22px;margin-bottom:22px">
    <div class="kpi-label" style="margin-bottom:14px">CHR AU — Monthly CAC & ACV:CAC Ratio Trend (CY25)</div>
    <div id="chr-cac-monthly"></div>
    <div style="display:flex;gap:16px;margin-top:10px;font-size:10px;color:var(--muted)">
      <span style="display:flex;align-items:center;gap:5px"><span style="width:12px;height:3px;background:var(--accent);display:inline-block;border-radius:2px"></span>CAC ($)</span>
      <span style="display:flex;align-items:center;gap:5px"><span style="width:12px;height:3px;background:var(--green);display:inline-block;border-radius:2px;opacity:.7"></span>ACV:CAC ratio</span>
    </div>
  </div>

  <!-- FoundU marketer insights -->
  <div class="insight" style="--insight-color:var(--accent2)">
    <div class="insight-title">FoundU Digital Marketer Context — Key Strategic Signals</div>
    <div class="insight-text" style="line-height:1.9">
      <strong style="color:var(--accent2)">MVF underperforming:</strong> Lead→Opp at 14% vs 30% target. 28% of enquiries classified "Outsourced Only" — outside FoundU's sales model. One sale in $28.5K spend Oct–Jan. Structural fix: dedicated BDC ownership + weekly lead review. Currently stopped Outsourced-only targeting (5 Feb).<br><br>
      <strong style="color:var(--accent2)">PPC structural improvements Sep 2025:</strong> Enterprise Payroll + Enterprise WFM campaigns added 17 Sep. "Small business" excluded as negative keyword. Portfolio bid strategy implemented Jan 2026. Lead→Opp conversion trending up over last 3 months — direct result of quality targeting shift.<br><br>
      <strong style="color:var(--accent2)">Strategic pivot underway:</strong> Marketer recommends H2 2026 shift from demand-capture (inbound only) to growth architecture — adding ABM, executive roundtables, vertical webinars, partner expansion. This is an acquisition risk diversification strategy, not a CAC increase strategy. Projected impact: higher ACV mix, stronger enterprise positioning, reduced reliance on paid search.
    </div>
  </div>
</div>

<!-- NZ EXIT IMPACT TAB -->
<div id="nz" class="section">
  <div class="section-header">
    <div class="section-title">NZ Exit — Impact & YoY Framework</div>
    <div class="section-subtitle">CHR NZ pulled May 2025 · Spend reallocated to AU · YoY comparison methodology</div>
  </div>

  <!-- Context banner -->
  <div class="insight" style="--insight-color:var(--accent4);margin-bottom:24px">
    <div class="insight-title">Context: What Happened and Why It Matters for YoY</div>
    <div class="insight-text">Citation pulled out of NZ CHR in May 2025 and reallocated ~$22,800/month of spend back into AU CHR campaigns. This creates a YoY comparison problem: CY25 CHR data includes NZ performance (Jan–May active, Jun–Dec pipeline runout), while CY26 is AU-only. Direct YoY comparisons overstate AU growth and understate CY25 as a comparator. The recommended framing is to use CY25 AU-only as the primary benchmark.</div>
  </div>

  <!-- NZ Performance Summary -->
  <div style="display:grid;grid-template-columns:repeat(4,1fr);gap:14px;margin-bottom:24px">
    <div class="kpi-card">
      <div class="kpi-label">NZ TRACKED SPEND</div>
      <div class="kpi-value" style="color:var(--accent4)">$46K</div>
      <div class="kpi-sub">Jan–Feb 2025 only (pulled May)</div>
    </div>
    <div class="kpi-card">
      <div class="kpi-label">NZ DEALS CY25</div>
      <div class="kpi-value" style="color:var(--muted)">~32</div>
      <div class="kpi-sub">Mostly pipeline runout Jun–Dec</div>
    </div>
    <div class="kpi-card">
      <div class="kpi-label">NZ TOTAL ACV CY25</div>
      <div class="kpi-value" style="color:var(--muted)">$175K</div>
      <div class="kpi-sub">AACV $5,472 — 51% below AU avg</div>
    </div>
    <div class="kpi-card">
      <div class="kpi-label">SPEND REALLOCATED</div>
      <div class="kpi-value" style="color:var(--green)">~$22.8K/mo</div>
      <div class="kpi-sub">Into AU CHR from May 2025 onwards</div>
    </div>
  </div>

  <!-- YoY Comparison Framework -->
  <div style="display:grid;grid-template-columns:1fr 1fr;gap:24px;margin-bottom:24px">
    <div class="kpi-card" style="padding:22px">
      <div class="kpi-label" style="margin-bottom:14px">Recommended YoY Framework</div>
      <div style="display:flex;flex-direction:column;gap:10px">
        <div style="background:var(--surface2);padding:14px;border-radius:8px;border-left:3px solid var(--green)">
          <div style="font-size:11px;color:var(--green);font-weight:bold;margin-bottom:6px">✓ PRIMARY COMPARISON</div>
          <div style="font-size:11px;color:var(--text)">CY25 AU-only CHR vs CY26 CHR</div>
          <div style="font-size:10px;color:var(--muted);margin-top:4px">Apples-to-apples. Removes NZ pipeline distortion. CY25 AU deals ~364, ACV ~$4.25M</div>
        </div>
        <div style="background:var(--surface2);padding:14px;border-radius:8px;border-left:3px solid var(--amber)">
          <div style="font-size:11px;color:var(--amber);font-weight:bold;margin-bottom:6px">⚠ USE WITH CAVEAT</div>
          <div style="font-size:11px;color:var(--text)">Total CY25 CHR (AU+NZ) vs CY26</div>
          <div style="font-size:10px;color:var(--muted);margin-top:4px">Appears as AU decline but is structural market exit. Note clearly in any board reporting.</div>
        </div>
        <div style="background:var(--surface2);padding:14px;border-radius:8px;border-left:3px solid var(--red)">
          <div style="font-size:11px;color:var(--red);font-weight:bold;margin-bottom:6px">✗ AVOID</div>
          <div style="font-size:11px;color:var(--text)">Raw CY25 total vs CY26 without NZ footnote</div>
          <div style="font-size:10px;color:var(--muted);margin-top:4px">Creates misleading growth narrative — overstates or understates depending on what's being measured.</div>
        </div>
      </div>
    </div>
    <div class="kpi-card" style="padding:22px">
      <div class="kpi-label" style="margin-bottom:14px">NZ Exit: Effect on CHR AU Blended Metrics</div>
      <div style="display:flex;flex-direction:column;gap:10px">
        <div style="background:var(--surface2);padding:12px;border-radius:8px">
          <div style="font-size:10px;color:var(--muted);margin-bottom:4px">AACV IMPACT</div>
          <div style="font-size:12px;color:var(--text)">NZ AACV $5,472 vs AU $11,174 — NZ deals were pulling the blended CHR average <span style="color:var(--green)">DOWN</span>. Exit improves AU reported AACV by removing lower-value deals from the pool.</div>
        </div>
        <div style="background:var(--surface2);padding:12px;border-radius:8px">
          <div style="font-size:10px;color:var(--muted);margin-bottom:4px">CAC IMPACT</div>
          <div style="font-size:12px;color:var(--text)">~$22.8K/month reallocated to AU. CHR AU H2 spend avg $140K/mo vs H1 $136K/mo — small uplift. Higher AU CAC in CY26 partly reflects NZ budget absorbed into a more competitive AU market.</div>
        </div>
        <div style="background:var(--surface2);padding:12px;border-radius:8px">
          <div style="font-size:10px;color:var(--muted);margin-bottom:4px">CONVERSION IMPACT</div>
          <div style="font-size:12px;color:var(--text)">NZ sat→deal was 42.6% vs AU's lower rate — NZ had better close rate (smaller, more relationship-based market). Exit removes a conversion drag on overall CHR opps volume but removes a close rate tailwind.</div>
        </div>
      </div>
    </div>
  </div>

  <!-- NZ ACV monthly chart -->
  <div class="kpi-card" style="padding:22px">
    <div class="kpi-label" style="margin-bottom:14px">NZ Monthly ACV + Deal Activity CY25 — Pipeline Runout Profile</div>
    <div id="nz-monthly-chart"></div>
    <div style="font-size:11px;color:var(--muted);margin-top:12px;line-height:1.7">
      Jun–Jul CY25 shows residual pipeline closing (Jul peak at $64K ACV — 6 deals likely from Q1 activity). By Sep–Dec activity normalised to small residual. The long runout tail through Dec confirms NZ had active pipeline at time of exit decision — deals completed without ongoing spend, resulting in an artificially high ACV:CAC in the final months.
    </div>
  </div>
</div>
<script>
function switchTab(id) {
  document.querySelectorAll('.tab').forEach((t,i) => t.classList.remove('active'));
  document.querySelectorAll('.section').forEach(s => s.classList.remove('active'));
  event.target.classList.add('active');
  document.getElementById(id).classList.add('active');
}

// ACV Chart
const acvData = [
  { m:'Jan', bud:201345, act:157902 },
  { m:'Feb', bud:391964, act:412166 },
  { m:'Mar', bud:466163, act:542176 },
  { m:'Apr', bud:453442, act:350940 },
  { m:'May', bud:514300, act:465114 },
  { m:'Jun', bud:568242, act:446398 },
  { m:'Jul', bud:371391, act:397780 },
  { m:'Aug', bud:531751, act:373409 },
  { m:'Sep', bud:545084, act:503135 },
  { m:'Oct', bud:548154, act:333817 },
  { m:'Nov', bud:526582, act:255961 },
  { m:'Dec', bud:361987, act:202867 },
];
const maxACV = Math.max(...acvData.map(d=>Math.max(d.bud,d.act)));
const acvContainer = document.getElementById('acv-chart');
acvData.forEach(d => {
  const actPct = (d.act/maxACV*100).toFixed(1);
  const budPct = (d.bud/maxACV*100).toFixed(1);
  const color = d.act >= d.bud ? '#00897B' : '#D81B60';
  const pct = ((d.act/d.bud-1)*100).toFixed(0);
  const label = d.act >= d.bud ? `+${pct}%` : `${pct}%`;
  acvContainer.innerHTML += `
    <div class="bar-row" style="margin-bottom:4px">
      <div class="bar-label">${d.m}</div>
      <div class="bar-track" style="position:relative">
        <div style="position:absolute;top:0;left:0;height:100%;width:${budPct}%;background:var(--border);border-radius:3px"></div>
        <div class="bar-fill" style="width:${actPct}%;background:${color};position:relative;z-index:1;opacity:.85"></div>
      </div>
      <div class="bar-val" style="color:var(--muted)">$${(d.act/1000).toFixed(0)}K</div>
      <div class="bar-vs" style="color:${color}">${label}</div>
    </div>`;
});

// CHR Monthly Chart
const chrData = [
  { m:'Jan', leads:188, opps:101, deals:21 },
  { m:'Feb', leads:319, opps:202, deals:27 },
  { m:'Mar', leads:369, opps:256, deals:39 },
  { m:'Apr', leads:376, opps:220, deals:39 },
  { m:'May', leads:578, opps:223, deals:35 },
  { m:'Jun', leads:949, opps:221, deals:43 },
  { m:'Jul', leads:363, opps:188, deals:32 },
  { m:'Aug', leads:659, opps:236, deals:32 },
  { m:'Sep', leads:561, opps:205, deals:37 },
  { m:'Oct', leads:450, opps:213, deals:37 },
  { m:'Nov', leads:308, opps:152, deals:34 },
  { m:'Dec', leads:121, opps:111, deals:20 },
];
const maxLeads = Math.max(...chrData.map(d=>d.leads));
const chrContainer = document.getElementById('chr-monthly-chart');
chrContainer.innerHTML = '<div style="display:flex;gap:8px;margin-bottom:12px;font-size:10px;color:var(--muted)"><span style="display:flex;align-items:center;gap:5px"><span style="width:12px;height:3px;background:var(--accent);display:inline-block;border-radius:2px"></span>Leads</span><span style="display:flex;align-items:center;gap:5px"><span style="width:12px;height:3px;background:var(--accent2);display:inline-block;border-radius:2px"></span>Opps</span><span style="display:flex;align-items:center;gap:5px"><span style="width:12px;height:3px;background:var(--green);display:inline-block;border-radius:2px"></span>Deals</span></div>';
chrData.forEach(d => {
  const lp = (d.leads/maxLeads*100).toFixed(1);
  const op = (d.opps/maxLeads*100).toFixed(1);
  const dp = (d.deals/maxLeads*100).toFixed(1);
  chrContainer.innerHTML += `
    <div style="margin-bottom:6px">
      <div style="font-size:10px;color:var(--muted);margin-bottom:3px">${d.m}</div>
      <div style="display:flex;flex-direction:column;gap:2px">
        <div style="height:14px;background:var(--surface2);border-radius:2px;overflow:hidden"><div style="height:100%;width:${lp}%;background:var(--accent);opacity:.8;animation:barIn 1s forwards"></div></div>
        <div style="height:14px;background:var(--surface2);border-radius:2px;overflow:hidden"><div style="height:100%;width:${op}%;background:var(--accent2);opacity:.8;animation:barIn 1.2s forwards"></div></div>
        <div style="height:14px;background:var(--surface2);border-radius:2px;overflow:hidden;position:relative">
          <div style="height:100%;width:${dp}%;background:var(--green);opacity:.9;animation:barIn 1.4s forwards"></div>
          <span style="position:absolute;right:4px;top:0;font-size:9px;color:var(--muted);line-height:14px">${d.deals} deals</span>
        </div>
      </div>
    </div>`;
});

// === ACV:CAC Charts ===
// YoY Ratio comparison
const cacYoYData = [
  { brand:"CHR AU", cy25:2.7, cy26:1.8, cy26c:"var(--red)" },
  { brand:"FoundU", cy25:3.5, cy26:3.0, cy26c:"var(--green)" },
  { brand:"ISO", cy25:1.6, cy26:3.0, cy26c:"var(--green)" },
];
const cacYoYEl = document.getElementById('cac-yoy-chart');
cacYoYData.forEach(d => {
  const maxR = 4;
  const p25 = (d.cy25/maxR*100).toFixed(0);
  const p26 = (d.cy26/maxR*100).toFixed(0);
  const trend = d.cy26 >= d.cy25 ? `+${((d.cy26/d.cy25-1)*100).toFixed(0)}%` : `${((d.cy26/d.cy25-1)*100).toFixed(0)}%`;
  const tc = d.cy26 >= d.cy25 ? '#00897B' : '#D81B60';
  cacYoYEl.innerHTML += `
    <div style="margin-bottom:12px">
      <div style="font-size:10px;color:var(--muted);margin-bottom:4px">${d.brand}</div>
      <div style="display:flex;flex-direction:column;gap:3px">
        <div style="display:flex;align-items:center;gap:8px">
          <div style="width:45px;font-size:9px;color:var(--muted)">CY25</div>
          <div style="flex:1;height:14px;background:var(--surface2);border-radius:2px;overflow:hidden">
            <div style="height:100%;width:${p25}%;background:var(--muted);opacity:.6"></div>
          </div>
          <div style="width:35px;text-align:right;font-size:11px;color:var(--muted)">${d.cy25}x</div>
        </div>
        <div style="display:flex;align-items:center;gap:8px">
          <div style="width:45px;font-size:9px;color:var(--muted)">CY26</div>
          <div style="flex:1;height:14px;background:var(--surface2);border-radius:2px;overflow:hidden">
            <div style="height:100%;width:${p26}%;background:${d.cy26c};opacity:.8"></div>
          </div>
          <div style="width:35px;text-align:right;font-size:11px;color:${d.cy26c}">${d.cy26}x</div>
          <div style="width:40px;font-size:9px;color:${tc};font-weight:bold">${trend}</div>
        </div>
      </div>
    </div>`;
});

// FoundU Brand vs Non-Brand
const brandCacData = [
  { label:"Brand PPC",     cac:4766,  aacv:12612, ratio:2.6, c:"var(--green)" },
  { label:"Non-Brand PPC", cac:29367, aacv:10482, ratio:0.4, c:"var(--red)" },
];
const brandEl = document.getElementById('foundo-brand-chart');
brandCacData.forEach(d => {
  const maxCAC = 30000;
  const p = (d.cac/maxCAC*100).toFixed(0);
  brandEl.innerHTML += `
    <div style="margin-bottom:14px;background:var(--surface2);padding:12px;border-radius:8px">
      <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:8px">
        <div style="font-size:11px;color:var(--text);font-weight:bold">${d.label}</div>
        <div style="font-size:16px;color:${d.c};font-weight:bold;font-family:'Syne',sans-serif">${d.ratio}x ACV:CAC</div>
      </div>
      <div style="display:flex;gap:16px;font-size:10px;color:var(--muted)">
        <div>CAC: <span style="color:${d.c}">${d.cac.toLocaleString()}</span></div>
        <div>AACV: <span style="color:var(--text)">$${d.aacv.toLocaleString()}</span></div>
      </div>
      <div style="margin-top:8px;height:8px;background:var(--border);border-radius:4px;overflow:hidden">
        <div style="height:100%;width:${p}%;background:${d.c};opacity:.7"></div>
      </div>
    </div>`;
});

// CHR AU Monthly CAC
const chrCACMonthly = [
  { m:'Jan', spend:72920,  deals:21 },
  { m:'Feb', spend:159437, deals:27 },
  { m:'Mar', spend:167794, deals:39 },
  { m:'Apr', spend:131564, deals:39 },
  { m:'May', spend:142584, deals:35 },
  { m:'Jun', spend:144425, deals:43 },
  { m:'Jul', spend:139711, deals:32 },
  { m:'Aug', spend:176979, deals:32 },
  { m:'Sep', spend:160815, deals:37 },
  { m:'Oct', spend:138268, deals:37 },
  { m:'Nov', spend:139572, deals:34 },
  { m:'Dec', spend:87145,  deals:20 },
];
const acvByMonth = [165040,407784,541102,351940,461994,446398,397230,364867,496702,333817,251461,206692];
const maxCAC = Math.max(...chrCACMonthly.map(d=>d.spend/d.deals));
const chrCACEl = document.getElementById('chr-cac-monthly');
chrCACMonthly.forEach((d, i) => {
  const cac = d.spend / d.deals;
  const ratio = (acvByMonth[i]/d.deals) / cac;
  const cacPct = (cac/maxCAC*100).toFixed(0);
  const cacColor = cac > 5000 ? '#D81B60' : cac > 4000 ? 'var(--amber)' : '#00897B';
  const ratioColor = ratio > 3 ? '#00897B' : ratio > 2 ? 'var(--accent2)' : '#D81B60';
  chrCACEl.innerHTML += `
    <div style="margin-bottom:5px">
      <div style="font-size:9px;color:var(--muted);margin-bottom:2px">${d.m}</div>
      <div style="display:flex;align-items:center;gap:8px">
        <div style="flex:1;height:12px;background:var(--surface2);border-radius:2px;overflow:hidden;position:relative">
          <div style="height:100%;width:${cacPct}%;background:${cacColor};opacity:.8"></div>
          <span style="position:absolute;right:4px;top:0;font-size:8px;color:var(--muted);line-height:12px">$${cac.toFixed(0)}</span>
        </div>
        <div style="width:42px;text-align:right;font-size:9px;color:${ratioColor};font-weight:bold">${ratio.toFixed(1)}x</div>
      </div>
    </div>`;
});

// NZ Monthly Chart
const nzData = [
  { m:'Jan', acv:4724, deals:1 }, { m:'Feb', acv:0, deals:0 },
  { m:'Mar', acv:0, deals:0 }, { m:'Apr', acv:0, deals:0 },
  { m:'May', acv:0, deals:0 }, { m:'Jun', acv:19374, deals:5 },
  { m:'Jul', acv:63997, deals:6 }, { m:'Aug', acv:22619, deals:8 },
  { m:'Sep', acv:20447, deals:2 }, { m:'Oct', acv:9067, deals:2 },
  { m:'Nov', acv:14849, deals:4 }, { m:'Dec', acv:20041, deals:4 },
];
const maxNZACV = 64000;
const nzEl = document.getElementById('nz-monthly-chart');
nzData.forEach(d => {
  const p = (d.acv/maxNZACV*100).toFixed(0);
  const isActive = ['Jan','Feb','Mar','Apr','May'].includes(d.m);
  const color = d.acv > 0 ? (isActive ? 'var(--accent4)' : 'var(--muted)') : 'transparent';
  nzEl.innerHTML += `
    <div style="margin-bottom:5px">
      <div style="display:flex;align-items:center;gap:6px">
        <div style="width:28px;font-size:9px;color:${isActive ? 'var(--accent4)' : 'var(--muted)'}">${d.m}</div>
        <div style="width:28px;font-size:8px;color:var(--muted);text-align:center">${isActive ? '🟣' : '⬜'}</div>
        <div style="flex:1;height:14px;background:var(--surface2);border-radius:2px;overflow:hidden">
          ${d.acv > 0 ? `<div style="height:100%;width:${p}%;background:${color};opacity:.8"></div>` : ''}
        </div>
        <div style="width:70px;text-align:right;font-size:9px;color:${d.acv > 0 ? 'var(--text)' : 'var(--muted)'}">
          ${d.acv > 0 ? '$'+d.acv.toLocaleString() : 'no spend'} ${d.deals > 0 ? '('+d.deals+'d)' : ''}
        </div>
      </div>
    </div>`;
});
nzEl.innerHTML += '<div style="font-size:9px;color:var(--muted);margin-top:8px">🟣 = NZ active spend period &nbsp; ⬜ = pipeline runout / organic closes only</div>';

// Monthly Inbound vs Outbound AACV
const aacvMonthlyData = [
  { m:'Jan', inb:8051, out:7859 },
  { m:'Feb', inb:17770, out:15103 },
  { m:'Mar', inb:11805, out:13874 },
  { m:'Apr', inb:10214, out:9024 },
  { m:'May', inb:13745, out:13200 },
  { m:'Jun', inb:11090, out:10381 },
  { m:'Jul', inb:10365, out:12413 },
  { m:'Aug', inb:12084, out:11402 },
  { m:'Sep', inb:16073, out:13424 },
  { m:'Oct', inb:11134, out:9022 },
  { m:'Nov', inb:7545, out:7396 },
  { m:'Dec', inb:8393, out:10335 },
];
const maxAACV = Math.max(...aacvMonthlyData.map(d=>Math.max(d.inb,d.out)));
const aacvMonthlyEl = document.getElementById('aacv-monthly-chart');
aacvMonthlyData.forEach(d => {
  const ip = (d.inb/maxAACV*100).toFixed(1);
  const op = (d.out/maxAACV*100).toFixed(1);
  aacvMonthlyEl.innerHTML += `
    <div style="margin-bottom:5px">
      <div style="font-size:10px;color:var(--muted);margin-bottom:2px">${d.m}</div>
      <div style="display:flex;flex-direction:column;gap:2px">
        <div style="height:12px;background:var(--surface2);border-radius:2px;overflow:hidden;position:relative">
          <div style="height:100%;width:${ip}%;background:var(--accent);opacity:.8"></div>
          <span style="position:absolute;right:4px;top:0;font-size:9px;color:var(--bg);line-height:12px">$${(d.inb/1000).toFixed(1)}K</span>
        </div>
        <div style="height:12px;background:var(--surface2);border-radius:2px;overflow:hidden;position:relative">
          <div style="height:100%;width:${op}%;background:var(--accent2);opacity:.8"></div>
          <span style="position:absolute;right:4px;top:0;font-size:9px;color:var(--bg);line-height:12px">$${(d.out/1000).toFixed(1)}K</span>
        </div>
      </div>
    </div>`;
});

// CY26 vs CY25 YoY AACV chart
const aacvYoYData = [
  { period:'Jan CY25', val:7859, color:'var(--muted)', label:'CY25' },
  { period:'Jan CY26', val:9837, color:'#00897B', label:'CY26 +25%' },
  { period:'Feb CY25', val:15103, color:'var(--muted)', label:'CY25' },
  { period:'Feb CY26', val:11743, color:'#D81B60', label:'CY26 -22%' },
];
const maxYoY = Math.max(...aacvYoYData.map(d=>d.val));
const aacvYoYEl = document.getElementById('aacv-yoy-chart');
aacvYoYData.forEach(d => {
  const pct = (d.val/maxYoY*100).toFixed(1);
  aacvYoYEl.innerHTML += `
    <div class="bar-row" style="margin-bottom:6px">
      <div class="bar-label" style="width:68px;font-size:10px">${d.period}</div>
      <div class="bar-track">
        <div class="bar-fill" style="width:${pct}%;background:${d.color};opacity:.9"></div>
      </div>
      <div class="bar-val" style="color:${d.color}">$${(d.val/1000).toFixed(1)}K</div>
    </div>`;
});

// AACV seasonal + deal volume overlay
const aacvSeasonalData = [
  { m:'Jan', aacv:7859, deals:21 },
  { m:'Feb', aacv:15103, deals:27 },
  { m:'Mar', aacv:13874, deals:39 },
  { m:'Apr', aacv:9024, deals:39 },
  { m:'May', aacv:13200, deals:35 },
  { m:'Jun', aacv:10381, deals:43 },
  { m:'Jul', aacv:12413, deals:32 },
  { m:'Aug', aacv:11402, deals:32 },
  { m:'Sep', aacv:13424, deals:37 },
  { m:'Oct', aacv:9022, deals:37 },
  { m:'Nov', aacv:7396, deals:34 },
  { m:'Dec', aacv:10335, deals:20 },
];
const maxAACVS = Math.max(...aacvSeasonalData.map(d=>d.aacv));
const maxDeals = Math.max(...aacvSeasonalData.map(d=>d.deals));
const aacvSeasonEl = document.getElementById('aacv-seasonal-chart');
aacvSeasonalData.forEach(d => {
  const ap = (d.aacv/maxAACVS*100).toFixed(1);
  const dp = (d.deals/maxDeals*100).toFixed(1);
  const acolor = d.aacv >= 11000 ? '#00897B' : d.aacv >= 9000 ? 'var(--amber)' : '#D81B60';
  const dcolor = d.deals >= 37 ? 'var(--accent2)' : d.deals >= 30 ? 'var(--muted)' : '#D81B60';
  aacvSeasonEl.innerHTML += `
    <div style="margin-bottom:5px">
      <div style="font-size:10px;color:var(--muted);margin-bottom:2px">${d.m}</div>
      <div style="display:flex;flex-direction:column;gap:2px">
        <div style="height:12px;background:var(--surface2);border-radius:2px;overflow:hidden;position:relative">
          <div style="height:100%;width:${ap}%;background:${acolor};opacity:.8"></div>
          <span style="position:absolute;right:4px;top:0;font-size:9px;line-height:12px;color:var(--bg)">$${(d.aacv/1000).toFixed(1)}K AACV</span>
        </div>
        <div style="height:8px;background:var(--surface2);border-radius:2px;overflow:hidden">
          <div style="height:100%;width:${dp}%;background:${dcolor};opacity:.5"></div>
        </div>
      </div>
    </div>`;
});
aacvSeasonEl.innerHTML += '<div style="display:flex;gap:16px;margin-top:8px;font-size:10px;color:var(--muted)"><span style="display:flex;align-items:center;gap:5px"><span style="width:12px;height:3px;background:var(--green);display:inline-block;border-radius:2px"></span>AACV (tall bar)</span><span style="display:flex;align-items:center;gap:5px"><span style="width:12px;height:3px;background:var(--accent2);display:inline-block;border-radius:2px;opacity:.5"></span>Deal Volume (short bar)</span></div>';

// === FoundU Rolling Charts ===
// 2024 rolling 3-month
const rolling2024 = [
  { w:'Mar', val:35 }, { w:'Apr', val:43 }, { w:'May', val:52 },
  { w:'Jun', val:56 }, { w:'Jul', val:56 }, { w:'Aug', val:50 },
  { w:'Sep', val:45 }, { w:'Oct', val:46 }, { w:'Nov', val:46 }, { w:'Dec', val:54 }
];
const maxR24 = Math.max(...rolling2024.map(d=>d.val));
const r24El = document.getElementById('foundo-rolling-2024');
rolling2024.forEach(d => {
  const p = (d.val/maxR24*100).toFixed(1);
  const c = d.val >= 50 ? '#00897B' : d.val >= 43 ? 'var(--amber)' : '#D81B60';
  r24El.innerHTML += `
    <div class="bar-row" style="margin-bottom:5px">
      <div class="bar-label" style="font-size:10px">${d.w}</div>
      <div class="bar-track">
        <div class="bar-fill" style="width:${p}%;background:${c};opacity:.85"></div>
      </div>
      <div class="bar-val" style="color:${c}">${d.val}</div>
    </div>`;
});

// 2025 rolling 3-month (H1 only)
const rolling2025 = [
  { w:'Mar', val:34 }, { w:'Apr', val:42 }, { w:'May', val:45 }, { w:'Jun', val:56 }
];
const maxR25 = 56;
const r25El = document.getElementById('foundo-rolling-2025');
rolling2025.forEach(d => {
  const p = (d.val/maxR25*100).toFixed(1);
  const c = d.val >= 50 ? '#00897B' : d.val >= 40 ? 'var(--amber)' : '#D81B60';
  r25El.innerHTML += `
    <div class="bar-row" style="margin-bottom:5px">
      <div class="bar-label" style="font-size:10px">${d.w}</div>
      <div class="bar-track">
        <div class="bar-fill" style="width:${p}%;background:${c};opacity:.85"></div>
      </div>
      <div class="bar-val" style="color:${c}">${d.val}</div>
    </div>`;
});

// Monthly history comparison
const fouHistoryData = [
  { m:'Jan', y23:10, y24:9, y25:8, y26:15 },
  { m:'Feb', y23:14, y24:13, y25:13, y26:13 },
  { m:'Mar', y23:18, y24:13, y25:13, y26:null },
  { m:'Apr', y23:14, y24:17, y25:16, y26:null },
  { m:'May', y23:31, y24:22, y25:16, y26:null },
  { m:'Jun', y23:20, y24:17, y25:24, y26:null },
  { m:'Jul', y23:10, y24:17, y25:null, y26:null },
  { m:'Aug', y23:19, y24:16, y25:null, y26:null },
  { m:'Sep', y23:20, y24:12, y25:null, y26:null },
  { m:'Oct', y23:9, y24:18, y25:null, y26:null },
  { m:'Nov', y23:31, y24:16, y25:null, y26:null },
  { m:'Dec', y23:9, y24:20, y25:null, y26:null },
];
const maxFou = 31;
const fouHistEl = document.getElementById('foundo-monthly-history');
fouHistoryData.forEach(d => {
  const getBar = (val, color) => {
    if(val === null) return `<div style="height:10px;background:transparent"></div>`;
    const p = (val/maxFou*100).toFixed(1);
    return `<div style="height:10px;background:var(--surface2);border-radius:2px;overflow:hidden;position:relative"><div style="height:100%;width:${p}%;background:${color};opacity:.8"></div><span style="position:absolute;right:3px;top:0;font-size:8px;color:var(--muted);line-height:10px">${val}</span></div>`;
  };
  fouHistEl.innerHTML += `
    <div style="margin-bottom:4px">
      <div style="font-size:10px;color:var(--muted);margin-bottom:2px">${d.m}</div>
      <div style="display:flex;flex-direction:column;gap:2px">
        ${getBar(d.y23,'#6b7080')}
        ${getBar(d.y24,'var(--accent2)')}
        ${getBar(d.y25,'#E91E63')}
        ${getBar(d.y26,'#00897B')}
      </div>
    </div>`;
});

// YoY comparison for Jan-Jun
const fouYoYData = [
  { m:'Jan', y23:10, y24:9, y25:8, y26:15 },
  { m:'Feb', y23:14, y24:13, y25:13, y26:13 },
  { m:'Mar', y23:18, y24:13, y25:13, y26:null },
  { m:'Apr', y23:14, y24:17, y25:16, y26:null },
  { m:'May', y23:31, y24:22, y25:16, y26:null },
  { m:'Jun', y23:20, y24:17, y25:24, y26:null },
];
const maxYoYFou = 31;
const fouYoYEl = document.getElementById('foundo-yoy-chart');
fouYoYData.forEach(d => {
  const bars = [
    {val:d.y23, c:'#6b7080'}, {val:d.y24, c:'var(--accent2)'},
    {val:d.y25, c:'#E91E63'}, {val:d.y26, c:'#00897B'}
  ];
  fouYoYEl.innerHTML += `
    <div style="margin-bottom:8px">
      <div style="font-size:10px;color:var(--muted);margin-bottom:3px">${d.m}</div>
      <div style="display:flex;gap:3px;height:24px;align-items:flex-end">
        ${bars.map(b => b.val ? `<div style="flex:1;height:${(b.val/maxYoYFou*100).toFixed(0)}%;background:${b.c};border-radius:2px 2px 0 0;opacity:.85;display:flex;align-items:flex-start;justify-content:center;font-size:8px;color:var(--bg);padding-top:2px">${b.val}</div>` : `<div style="flex:1;background:var(--surface2);border-radius:2px;opacity:.3"></div>`).join('')}
      </div>
    </div>`;
});

const brandData = [
  { m:'Jan', chr:0.82, iso:1.25, acv_pct:0.78 },
  { m:'Feb', chr:1.04, iso:1.08, acv_pct:1.05 },
  { m:'Mar', chr:1.16, iso:0.71, acv_pct:1.16 },
  { m:'Apr', chr:0.78, iso:0.87, acv_pct:0.78 },
  { m:'May', chr:0.90, iso:0.95, acv_pct:0.91 },
  { m:'Jun', chr:0.79, iso:0.94, acv_pct:0.79 },
  { m:'Jul', chr:1.07, iso:0.83, acv_pct:1.07 },
  { m:'Aug', chr:0.69, iso:0.86, acv_pct:0.69 },
  { m:'Sep', chr:0.91, iso:0.83, acv_pct:0.92 },
  { m:'Oct', chr:0.61, iso:0.78, acv_pct:0.61 },
  { m:'Nov', chr:0.48, iso:0.81, acv_pct:0.49 },
  { m:'Dec', chr:0.57, iso:0.93, acv_pct:0.56 },
];
const bvtContainer = document.getElementById('brand-vs-target-chart');
bvtContainer.innerHTML = '<div class="bar-chart">';
brandData.forEach(d => {
  const chrColor = d.chr >= 1 ? '#00897B' : d.chr >= 0.85 ? 'var(--amber)' : '#D81B60';
  const isoColor = d.iso >= 1 ? '#00897B' : d.iso >= 0.85 ? 'var(--amber)' : '#D81B60';
  bvtContainer.innerHTML += `
    <div class="bar-row">
      <div class="bar-label">${d.m}</div>
      <div class="bar-track" style="display:flex;gap:2px">
        <div style="flex:1;height:100%;background:var(--surface2);border-radius:2px 0 0 2px;overflow:hidden">
          <div style="height:100%;width:${Math.min(d.chr*100,100)}%;background:${chrColor};opacity:.8;animation:barIn 1s forwards"></div>
        </div>
        <div style="flex:1;height:100%;background:var(--surface2);border-radius:0 2px 2px 0;overflow:hidden">
          <div style="height:100%;width:${Math.min(d.iso*100,100)}%;background:${isoColor};opacity:.6;animation:barIn 1.2s forwards"></div>
        </div>
      </div>
      <div class="bar-val" style="color:${chrColor}">${(d.chr*100).toFixed(0)}%</div>
      <div class="bar-vs" style="color:${isoColor}">${(d.iso*100).toFixed(0)}%</div>
    </div>`;
});
bvtContainer.innerHTML += '</div><div style="display:flex;gap:12px;margin-top:10px;font-size:10px;color:var(--muted)"><span style="display:flex;align-items:center;gap:5px"><span style="width:12px;height:3px;background:var(--accent);display:inline-block;border-radius:2px;opacity:.8"></span>CHR ACV vs budget</span><span style="display:flex;align-items:center;gap:5px"><span style="width:12px;height:3px;background:var(--accent2);display:inline-block;border-radius:2px;opacity:.6"></span>ISO ACV vs budget</span></div>';
</script>
</body>
</html>
