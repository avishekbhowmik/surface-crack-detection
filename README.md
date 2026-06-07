[index.html](https://github.com/user-attachments/files/28682987/index.html)
# surface-crack-detection<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>CrackSense — Surface Crack Detection AI</title>
<link href="https://fonts.googleapis.com/css2?family=Space+Mono:wght@400;700&family=DM+Sans:wght@300;400;500;600&family=Bebas+Neue&display=swap" rel="stylesheet">
<style>
  :root {
    --bg:       #0a0c10;
    --surface:  #111318;
    --card:     #161b24;
    --border:   #1e2530;
    --accent:   #e8ff47;
    --accent2:  #ff4747;
    --accent3:  #47c2ff;
    --text:     #e8eaf0;
    --muted:    #6b7280;
    --safe:     #22c55e;
    --danger:   #ef4444;
    --warn:     #f59e0b;
    --mono:     'Space Mono', monospace;
    --sans:     'DM Sans', sans-serif;
    --display:  'Bebas Neue', sans-serif;
  }

  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  html { scroll-behavior: smooth; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: var(--sans);
    min-height: 100vh;
    overflow-x: hidden;
  }

  /* ── Noise overlay ───────────────────────────────── */
  body::before {
    content: '';
    position: fixed; inset: 0;
    background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 256 256' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='noise'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23noise)' opacity='0.04'/%3E%3C/svg%3E");
    pointer-events: none; z-index: 1000;
    opacity: 0.4;
  }

  /* ── NAV ─────────────────────────────────────────── */
  nav {
    position: fixed; top: 0; left: 0; right: 0; z-index: 100;
    display: flex; align-items: center; justify-content: space-between;
    padding: 0 2.5rem;
    height: 64px;
    background: rgba(10,12,16,0.85);
    backdrop-filter: blur(20px);
    border-bottom: 1px solid var(--border);
  }

  .nav-logo {
    font-family: var(--display);
    font-size: 1.6rem;
    letter-spacing: 0.05em;
    color: var(--text);
    display: flex; align-items: center; gap: 0.5rem;
  }

  .nav-logo span {
    color: var(--accent);
    font-size: 1.6rem;
  }

  .nav-links {
    display: flex; gap: 0.25rem;
    list-style: none;
  }

  .nav-links a {
    color: var(--muted);
    text-decoration: none;
    font-size: 0.82rem;
    font-weight: 500;
    letter-spacing: 0.06em;
    text-transform: uppercase;
    padding: 0.4rem 0.9rem;
    border-radius: 4px;
    transition: all 0.2s;
  }

  .nav-links a:hover { color: var(--text); background: var(--border); }
  .nav-links a.active { color: var(--accent); }

  .nav-badge {
    font-family: var(--mono);
    font-size: 0.68rem;
    background: var(--accent);
    color: #000;
    padding: 0.2rem 0.6rem;
    border-radius: 2px;
    font-weight: 700;
    letter-spacing: 0.05em;
  }

  /* ── HERO ────────────────────────────────────────── */
  #home {
    min-height: 100vh;
    display: flex; flex-direction: column; justify-content: center;
    padding: 6rem 2.5rem 4rem;
    position: relative;
    overflow: hidden;
  }

  .hero-grid {
    position: absolute; inset: 0;
    background-image:
      linear-gradient(rgba(232,255,71,0.03) 1px, transparent 1px),
      linear-gradient(90deg, rgba(232,255,71,0.03) 1px, transparent 1px);
    background-size: 60px 60px;
  }

  .hero-glow {
    position: absolute;
    width: 600px; height: 600px;
    background: radial-gradient(circle, rgba(232,255,71,0.06) 0%, transparent 70%);
    top: -100px; right: -100px;
    pointer-events: none;
  }

  .hero-inner { max-width: 900px; position: relative; z-index: 2; }

  .hero-tag {
    display: inline-flex; align-items: center; gap: 0.5rem;
    font-family: var(--mono);
    font-size: 0.72rem;
    color: var(--accent);
    background: rgba(232,255,71,0.08);
    border: 1px solid rgba(232,255,71,0.2);
    padding: 0.35rem 0.8rem;
    border-radius: 2px;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    margin-bottom: 1.5rem;
  }

  .hero-tag::before {
    content: '';
    width: 6px; height: 6px;
    background: var(--accent);
    border-radius: 50%;
    animation: pulse 2s infinite;
  }

  @keyframes pulse {
    0%, 100% { opacity: 1; transform: scale(1); }
    50% { opacity: 0.4; transform: scale(0.8); }
  }

  h1 {
    font-family: var(--display);
    font-size: clamp(3.5rem, 8vw, 7rem);
    line-height: 0.92;
    letter-spacing: 0.02em;
    margin-bottom: 1.5rem;
  }

  h1 em {
    font-style: normal;
    color: var(--accent);
    display: block;
  }

  .hero-desc {
    font-size: 1.05rem;
    color: var(--muted);
    line-height: 1.7;
    max-width: 560px;
    margin-bottom: 2.5rem;
  }

  .hero-actions {
    display: flex; gap: 1rem; flex-wrap: wrap;
  }

  .btn-primary {
    background: var(--accent);
    color: #000;
    font-family: var(--mono);
    font-size: 0.78rem;
    font-weight: 700;
    letter-spacing: 0.08em;
    text-transform: uppercase;
    padding: 0.85rem 2rem;
    border: none; border-radius: 3px;
    cursor: pointer;
    text-decoration: none;
    display: inline-flex; align-items: center; gap: 0.5rem;
    transition: all 0.2s;
  }

  .btn-primary:hover {
    background: #fff;
    transform: translateY(-2px);
    box-shadow: 0 8px 30px rgba(232,255,71,0.3);
  }

  .btn-secondary {
    background: transparent;
    color: var(--text);
    font-family: var(--mono);
    font-size: 0.78rem;
    letter-spacing: 0.08em;
    text-transform: uppercase;
    padding: 0.85rem 2rem;
    border: 1px solid var(--border);
    border-radius: 3px;
    cursor: pointer;
    text-decoration: none;
    display: inline-flex; align-items: center; gap: 0.5rem;
    transition: all 0.2s;
  }

  .btn-secondary:hover {
    border-color: var(--muted);
    background: var(--surface);
  }

  .hero-stats {
    display: flex; gap: 3rem; margin-top: 4rem;
    padding-top: 2rem;
    border-top: 1px solid var(--border);
  }

  .stat-item { display: flex; flex-direction: column; gap: 0.25rem; }

  .stat-value {
    font-family: var(--display);
    font-size: 2.2rem;
    color: var(--accent);
    line-height: 1;
  }

  .stat-label {
    font-family: var(--mono);
    font-size: 0.65rem;
    color: var(--muted);
    text-transform: uppercase;
    letter-spacing: 0.1em;
  }

  /* ── SECTION BASE ────────────────────────────────── */
  section {
    padding: 6rem 2.5rem;
    max-width: 1200px;
    margin: 0 auto;
  }

  .section-label {
    font-family: var(--mono);
    font-size: 0.68rem;
    color: var(--accent);
    text-transform: uppercase;
    letter-spacing: 0.15em;
    margin-bottom: 0.75rem;
  }

  .section-title {
    font-family: var(--display);
    font-size: clamp(2rem, 4vw, 3.5rem);
    letter-spacing: 0.03em;
    margin-bottom: 1rem;
    line-height: 1;
  }

  .section-desc {
    color: var(--muted);
    font-size: 0.95rem;
    line-height: 1.7;
    max-width: 560px;
    margin-bottom: 3rem;
  }

  /* ── DETECT SECTION ──────────────────────────────── */
  #detect { padding-top: 2rem; }

  .detect-wrapper {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 1.5rem;
    align-items: start;
  }

  .upload-panel {
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 8px;
    overflow: hidden;
  }

  .panel-header {
    padding: 1rem 1.5rem;
    border-bottom: 1px solid var(--border);
    display: flex; align-items: center; justify-content: space-between;
  }

  .panel-title {
    font-family: var(--mono);
    font-size: 0.72rem;
    text-transform: uppercase;
    letter-spacing: 0.1em;
    color: var(--muted);
  }

  .panel-dot {
    width: 8px; height: 8px;
    background: var(--accent);
    border-radius: 50%;
  }

  .drop-zone {
    padding: 2.5rem 1.5rem;
    display: flex; flex-direction: column;
    align-items: center; justify-content: center;
    gap: 1rem;
    cursor: pointer;
    transition: all 0.2s;
    min-height: 220px;
    position: relative;
  }

  .drop-zone.has-image { padding: 0; min-height: 280px; }

  .drop-zone:hover .drop-icon { transform: translateY(-4px); }

  .drop-icon {
    font-size: 2.5rem;
    transition: transform 0.3s;
    opacity: 0.5;
  }

  .drop-text {
    font-size: 0.85rem;
    color: var(--muted);
    text-align: center;
    line-height: 1.6;
  }

  .drop-text strong { color: var(--text); }

  .drop-zone input[type=file] {
    position: absolute; inset: 0;
    opacity: 0; cursor: pointer;
    width: 100%; height: 100%;
  }

  #preview-img {
    width: 100%; height: 100%;
    object-fit: cover;
    display: none;
    border-radius: 0;
  }

  .drop-zone.has-image #preview-img { display: block; }
  .drop-zone.has-image .drop-icon,
  .drop-zone.has-image .drop-text { display: none; }

  .upload-actions {
    padding: 1rem 1.5rem;
    border-top: 1px solid var(--border);
    display: flex; gap: 0.75rem;
  }

  .btn-analyze {
    flex: 1;
    background: var(--accent);
    color: #000;
    font-family: var(--mono);
    font-size: 0.75rem;
    font-weight: 700;
    letter-spacing: 0.08em;
    text-transform: uppercase;
    padding: 0.75rem;
    border: none; border-radius: 4px;
    cursor: pointer;
    transition: all 0.2s;
    display: flex; align-items: center; justify-content: center; gap: 0.5rem;
  }

  .btn-analyze:hover { background: #fff; }
  .btn-analyze:disabled { opacity: 0.4; cursor: not-allowed; }

  .btn-clear {
    background: transparent;
    color: var(--muted);
    font-family: var(--mono);
    font-size: 0.72rem;
    letter-spacing: 0.06em;
    text-transform: uppercase;
    padding: 0.75rem 1rem;
    border: 1px solid var(--border);
    border-radius: 4px;
    cursor: pointer;
    transition: all 0.2s;
  }

  .btn-clear:hover { color: var(--text); border-color: var(--muted); }

  /* ── RESULTS PANEL ───────────────────────────────── */
  .results-panel {
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 8px;
    overflow: hidden;
    display: flex; flex-direction: column;
  }

  .results-empty {
    display: flex; flex-direction: column;
    align-items: center; justify-content: center;
    gap: 0.75rem;
    min-height: 380px;
    color: var(--muted);
    font-size: 0.85rem;
  }

  .results-empty-icon { font-size: 2.5rem; opacity: 0.3; }

  .result-content { display: none; flex-direction: column; }
  .result-content.visible { display: flex; }

  /* Verdict bar */
  .verdict-bar {
    padding: 1.5rem;
    display: flex; align-items: center; gap: 1.25rem;
    border-bottom: 1px solid var(--border);
  }

  .verdict-icon {
    width: 52px; height: 52px;
    border-radius: 6px;
    display: flex; align-items: center; justify-content: center;
    font-size: 1.4rem;
    flex-shrink: 0;
  }

  .verdict-icon.crack { background: rgba(239,68,68,0.12); }
  .verdict-icon.safe  { background: rgba(34,197,94,0.12); }

  .verdict-label {
    font-family: var(--mono);
    font-size: 0.65rem;
    text-transform: uppercase;
    letter-spacing: 0.12em;
    color: var(--muted);
    margin-bottom: 0.25rem;
  }

  .verdict-text {
    font-family: var(--display);
    font-size: 1.6rem;
    letter-spacing: 0.04em;
    line-height: 1;
  }

  .verdict-text.crack { color: var(--danger); }
  .verdict-text.safe  { color: var(--safe); }

  /* Probability gauge */
  .prob-section {
    padding: 1.25rem 1.5rem;
    border-bottom: 1px solid var(--border);
  }

  .prob-header {
    display: flex; justify-content: space-between; align-items: baseline;
    margin-bottom: 0.6rem;
  }

  .prob-label {
    font-family: var(--mono);
    font-size: 0.65rem;
    text-transform: uppercase;
    letter-spacing: 0.1em;
    color: var(--muted);
  }

  .prob-value {
    font-family: var(--display);
    font-size: 1.8rem;
    letter-spacing: 0.02em;
    line-height: 1;
  }

  .gauge-track {
    height: 6px;
    background: var(--border);
    border-radius: 3px;
    overflow: hidden;
  }

  .gauge-fill {
    height: 100%;
    border-radius: 3px;
    transition: width 1s cubic-bezier(0.25, 0.46, 0.45, 0.94);
    width: 0%;
  }

  /* Metrics grid */
  .metrics-grid {
    display: grid; grid-template-columns: 1fr 1fr;
    gap: 1px;
    background: var(--border);
    border-top: 1px solid var(--border);
    border-bottom: 1px solid var(--border);
  }

  .metric-cell {
    background: var(--card);
    padding: 1rem 1.25rem;
  }

  .metric-name {
    font-family: var(--mono);
    font-size: 0.6rem;
    text-transform: uppercase;
    letter-spacing: 0.1em;
    color: var(--muted);
    margin-bottom: 0.3rem;
  }

  .metric-val {
    font-family: var(--mono);
    font-size: 1rem;
    font-weight: 700;
    color: var(--text);
  }

  /* Grad-CAM */
  .gradcam-section {
    padding: 1.25rem 1.5rem;
  }

  .gradcam-label {
    font-family: var(--mono);
    font-size: 0.65rem;
    text-transform: uppercase;
    letter-spacing: 0.1em;
    color: var(--muted);
    margin-bottom: 0.75rem;
    display: flex; align-items: center; gap: 0.5rem;
  }

  .gradcam-label span {
    background: rgba(71,194,255,0.1);
    color: var(--accent3);
    border: 1px solid rgba(71,194,255,0.2);
    padding: 0.15rem 0.5rem;
    border-radius: 2px;
    font-size: 0.58rem;
  }

  .gradcam-container {
    position: relative;
    border-radius: 4px;
    overflow: hidden;
    background: var(--bg);
    height: 160px;
    display: flex; align-items: center; justify-content: center;
  }

  .gradcam-container img {
    width: 100%; height: 100%; object-fit: cover;
  }

  .gradcam-placeholder {
    color: var(--muted);
    font-size: 0.78rem;
    font-family: var(--mono);
    text-align: center;
  }

  /* ── COMPARE SECTION ─────────────────────────────── */
  #compare { border-top: 1px solid var(--border); }

  .compare-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 1.5rem;
    margin-bottom: 1.5rem;
  }

  .compare-slot {
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 8px;
    overflow: hidden;
  }

  .compare-drop {
    min-height: 200px;
    display: flex; flex-direction: column;
    align-items: center; justify-content: center;
    gap: 0.75rem;
    cursor: pointer;
    position: relative;
    transition: all 0.2s;
    padding: 2rem;
  }

  .compare-drop:hover { background: var(--surface); }

  .compare-drop input[type=file] {
    position: absolute; inset: 0;
    opacity: 0; cursor: pointer;
    width: 100%; height: 100%;
  }

  .compare-drop-img {
    width: 100%; height: 200px;
    object-fit: cover;
    display: none;
  }

  .compare-drop.has-img .compare-drop-img { display: block; }
  .compare-drop.has-img .compare-drop-icon,
  .compare-drop.has-img .compare-drop-text { display: none; }

  .compare-drop-icon { font-size: 2rem; opacity: 0.3; }

  .compare-drop-text {
    font-size: 0.8rem;
    color: var(--muted);
    text-align: center;
  }

  .compare-result {
    padding: 1rem 1.25rem;
    border-top: 1px solid var(--border);
    display: none;
  }

  .compare-result.visible { display: block; }

  .compare-verdict {
    display: flex; align-items: center; gap: 0.75rem;
    margin-bottom: 0.6rem;
  }

  .compare-dot {
    width: 10px; height: 10px;
    border-radius: 50%;
    flex-shrink: 0;
  }

  .compare-verdict-text {
    font-family: var(--display);
    font-size: 1.1rem;
    letter-spacing: 0.04em;
  }

  .compare-bar-wrap { margin-top: 0.5rem; }

  .compare-bar-label {
    display: flex; justify-content: space-between;
    font-family: var(--mono);
    font-size: 0.6rem;
    color: var(--muted);
    text-transform: uppercase;
    letter-spacing: 0.08em;
    margin-bottom: 0.35rem;
  }

  .compare-bar-track {
    height: 4px;
    background: var(--border);
    border-radius: 2px;
    overflow: hidden;
  }

  .compare-bar-fill {
    height: 100%;
    border-radius: 2px;
    transition: width 1s ease;
    width: 0%;
  }

  .compare-btn-row {
    display: flex; gap: 0.75rem; justify-content: center;
  }

  /* ── PROJECT SECTION ─────────────────────────────── */
  #project { border-top: 1px solid var(--border); }

  .project-tabs {
    display: flex; gap: 0;
    border: 1px solid var(--border);
    border-radius: 6px;
    overflow: hidden;
    margin-bottom: 2rem;
    width: fit-content;
  }

  .tab-btn {
    background: transparent;
    color: var(--muted);
    font-family: var(--mono);
    font-size: 0.68rem;
    text-transform: uppercase;
    letter-spacing: 0.1em;
    padding: 0.6rem 1.25rem;
    border: none;
    border-right: 1px solid var(--border);
    cursor: pointer;
    transition: all 0.2s;
  }

  .tab-btn:last-child { border-right: none; }
  .tab-btn:hover { color: var(--text); background: var(--surface); }
  .tab-btn.active { background: var(--accent); color: #000; font-weight: 700; }

  .tab-content { display: none; }
  .tab-content.active { display: block; }

  /* Overview cards */
  .overview-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 1px;
    background: var(--border);
    border: 1px solid var(--border);
    border-radius: 8px;
    overflow: hidden;
    margin-bottom: 2rem;
  }

  .overview-card {
    background: var(--card);
    padding: 1.75rem;
  }

  .ov-icon { font-size: 1.5rem; margin-bottom: 0.75rem; }

  .ov-title {
    font-weight: 600;
    font-size: 0.9rem;
    margin-bottom: 0.4rem;
    color: var(--text);
  }

  .ov-desc {
    font-size: 0.8rem;
    color: var(--muted);
    line-height: 1.6;
  }

  /* Dataset table */
  .data-table {
    width: 100%;
    border-collapse: collapse;
    font-size: 0.85rem;
  }

  .data-table th {
    background: var(--surface);
    color: var(--muted);
    font-family: var(--mono);
    font-size: 0.62rem;
    text-transform: uppercase;
    letter-spacing: 0.1em;
    padding: 0.75rem 1rem;
    text-align: left;
    border-bottom: 1px solid var(--border);
    font-weight: 400;
  }

  .data-table td {
    padding: 0.75rem 1rem;
    border-bottom: 1px solid var(--border);
    color: var(--text);
    font-size: 0.83rem;
  }

  .data-table tr:last-child td { border-bottom: none; }
  .data-table tr:hover td { background: var(--surface); }

  .data-table .key-cell {
    font-family: var(--mono);
    font-size: 0.72rem;
    color: var(--muted);
    font-weight: 400;
  }

  /* Model cards */
  .model-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 1.5rem;
  }

  .model-card {
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 1.5rem;
    position: relative;
    overflow: hidden;
  }

  .model-card::before {
    content: '';
    position: absolute; top: 0; left: 0; right: 0;
    height: 2px;
  }

  .model-card.custom::before { background: var(--accent); }
  .model-card.transfer::before { background: var(--accent3); }

  .model-tag {
    display: inline-block;
    font-family: var(--mono);
    font-size: 0.6rem;
    text-transform: uppercase;
    letter-spacing: 0.1em;
    padding: 0.2rem 0.6rem;
    border-radius: 2px;
    margin-bottom: 1rem;
  }

  .model-card.custom .model-tag { background: rgba(232,255,71,0.1); color: var(--accent); }
  .model-card.transfer .model-tag { background: rgba(71,194,255,0.1); color: var(--accent3); }

  .model-name {
    font-family: var(--display);
    font-size: 1.4rem;
    letter-spacing: 0.04em;
    margin-bottom: 0.75rem;
  }

  .model-desc {
    font-size: 0.8rem;
    color: var(--muted);
    line-height: 1.6;
    margin-bottom: 1.25rem;
  }

  .model-specs { display: flex; flex-direction: column; gap: 0.4rem; }

  .spec-row {
    display: flex; justify-content: space-between;
    align-items: center;
    padding: 0.4rem 0;
    border-bottom: 1px solid var(--border);
    font-size: 0.78rem;
  }

  .spec-row:last-child { border-bottom: none; }
  .spec-key { color: var(--muted); font-family: var(--mono); font-size: 0.65rem; text-transform: uppercase; letter-spacing: 0.08em; }
  .spec-val { color: var(--text); font-weight: 500; }

  /* Results tab */
  .results-comparison {
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 8px;
    overflow: hidden;
  }

  .metric-row {
    display: grid;
    grid-template-columns: 2fr 1fr 1fr;
    padding: 0.9rem 1.25rem;
    border-bottom: 1px solid var(--border);
    align-items: center;
    gap: 1rem;
  }

  .metric-row:last-child { border-bottom: none; }

  .metric-row-header {
    background: var(--surface);
    font-family: var(--mono);
    font-size: 0.62rem;
    text-transform: uppercase;
    letter-spacing: 0.1em;
    color: var(--muted);
  }

  .metric-name-cell { font-size: 0.83rem; color: var(--text); }
  .metric-val-cell {
    font-family: var(--mono);
    font-size: 0.82rem;
    text-align: center;
    font-weight: 700;
  }

  .badge-good { color: var(--safe); }
  .badge-ok   { color: var(--warn); }

  /* Research Questions */
  .rq-list { display: flex; flex-direction: column; gap: 0.75rem; }

  .rq-item {
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 6px;
    padding: 1rem 1.25rem;
    display: flex; gap: 1rem; align-items: flex-start;
  }

  .rq-num {
    font-family: var(--mono);
    font-size: 0.65rem;
    color: var(--accent);
    background: rgba(232,255,71,0.08);
    border: 1px solid rgba(232,255,71,0.15);
    padding: 0.2rem 0.5rem;
    border-radius: 2px;
    flex-shrink: 0;
    margin-top: 0.1rem;
  }

  .rq-text { font-size: 0.85rem; color: var(--muted); line-height: 1.6; }

  /* ── LOADING SPINNER ─────────────────────────────── */
  .spinner {
    width: 18px; height: 18px;
    border: 2px solid rgba(0,0,0,0.2);
    border-top-color: #000;
    border-radius: 50%;
    animation: spin 0.7s linear infinite;
  }

  @keyframes spin { to { transform: rotate(360deg); } }

  .spinner-light {
    border-color: rgba(255,255,255,0.2);
    border-top-color: var(--accent);
  }

  /* ── FOOTER ──────────────────────────────────────── */
  footer {
    border-top: 1px solid var(--border);
    padding: 2rem 2.5rem;
    display: flex; align-items: center; justify-content: space-between;
    font-family: var(--mono);
    font-size: 0.65rem;
    color: var(--muted);
    text-transform: uppercase;
    letter-spacing: 0.1em;
  }

  .footer-logo { color: var(--accent); font-size: 0.8rem; }

  /* ── TOAST ───────────────────────────────────────── */
  .toast {
    position: fixed; bottom: 2rem; right: 2rem;
    background: var(--card);
    border: 1px solid var(--border);
    border-left: 3px solid var(--accent);
    padding: 0.75rem 1.25rem;
    border-radius: 4px;
    font-family: var(--mono);
    font-size: 0.72rem;
    color: var(--text);
    z-index: 9999;
    transform: translateY(100px);
    opacity: 0;
    transition: all 0.3s;
    letter-spacing: 0.05em;
  }

  .toast.show { transform: translateY(0); opacity: 1; }

  /* ── RESPONSIVE ──────────────────────────────────── */
  @media (max-width: 768px) {
    .detect-wrapper, .compare-grid, .model-grid { grid-template-columns: 1fr; }
    .overview-grid { grid-template-columns: 1fr; }
    .hero-stats { gap: 1.5rem; flex-wrap: wrap; }
    nav { padding: 0 1rem; }
    .nav-links { display: none; }
    section { padding: 4rem 1.25rem; }
  }
</style>
</head>
<body>

<!-- NAV -->
<nav>
  <div class="nav-logo">Crack<span>Sense</span></div>
  <ul class="nav-links">
    <li><a href="#home" class="active">Home</a></li>
    <li><a href="#detect">Detect</a></li>
    <li><a href="#compare">Compare</a></li>
    <li><a href="#project">Project</a></li>
  </ul>
  <div class="nav-badge">Demo v1.0</div>
</nav>

<!-- HERO -->
<section id="home" style="max-width:100%; padding-left:2.5rem; padding-right:2.5rem;">
  <div class="hero-grid"></div>
  <div class="hero-glow"></div>
  <div class="hero-inner">
    <div class="hero-tag">AI-Powered Structural Inspection</div>
    <h1>Detect<em>Surface Cracks</em>Instantly</h1>
    <p class="hero-desc">
      Upload any concrete surface image. Our CNN model analyses it in milliseconds,
      returns a crack probability score, and shows exactly where the model looked
      using Grad-CAM explainability.
    </p>
    <div class="hero-actions">
      <a href="#detect" class="btn-primary">▶ Analyse Image</a>
      <a href="#project" class="btn-secondary">View Project Details</a>
    </div>
    <div class="hero-stats">
      <div class="stat-item">
        <div class="stat-value">40K</div>
        <div class="stat-label">Training Images</div>
      </div>
      <div class="stat-item">
        <div class="stat-value">&lt;90%</div>
        <div class="stat-label">Val. Accuracy</div>
      </div>
      <div class="stat-item">
        <div class="stat-value">2</div>
        <div class="stat-label">CNN Models</div>
      </div>
      <div class="stat-item">
        <div class="stat-value">96px</div>
        <div class="stat-label">Input Resolution</div>
      </div>
    </div>
  </div>
</section>

<!-- DETECT -->
<section id="detect" style="max-width:1200px;">
  <div class="section-label">// Single Image Analysis</div>
  <h2 class="section-title">Crack Detection</h2>
  <p class="section-desc">Upload a concrete surface image to get the crack prediction, probability score, and Grad-CAM attention heatmap.</p>

  <div class="detect-wrapper">

    <!-- Upload Panel -->
    <div class="upload-panel">
      <div class="panel-header">
        <span class="panel-title">Input Image</span>
        <div class="panel-dot"></div>
      </div>
      <div class="drop-zone" id="dropZone">
        <input type="file" id="fileInput" accept="image/*" onchange="handleFile(this)">
        <div class="drop-icon">🖼️</div>
        <div class="drop-text">
          <strong>Click to upload</strong> or drag & drop<br>
          JPG, PNG supported — any concrete surface
        </div>
        <img id="preview-img" src="" alt="preview">
      </div>
      <div class="upload-actions">
        <button class="btn-analyze" id="analyzeBtn" onclick="runAnalysis()" disabled>
          <div class="spinner" id="analyzeSpinner" style="display:none;"></div>
          <span id="analyzeBtnText">Upload an image first</span>
        </button>
        <button class="btn-clear" onclick="clearUpload()">Clear</button>
      </div>
    </div>

    <!-- Results Panel -->
    <div class="results-panel">
      <div class="panel-header">
        <span class="panel-title">Analysis Result</span>
        <div class="panel-dot" style="background:var(--muted);"></div>
      </div>

      <div class="results-empty" id="resultsEmpty">
        <div class="results-empty-icon">⬡</div>
        <div>Awaiting image upload</div>
      </div>

      <div class="result-content" id="resultContent">
        <div class="verdict-bar">
          <div class="verdict-icon" id="verdictIcon">❓</div>
          <div>
            <div class="verdict-label">Classification</div>
            <div class="verdict-text" id="verdictText">—</div>
          </div>
        </div>

        <div class="prob-section">
          <div class="prob-header">
            <span class="prob-label">Crack Probability</span>
            <span class="prob-value" id="probValue">—</span>
          </div>
          <div class="gauge-track">
            <div class="gauge-fill" id="gaugeFill"></div>
          </div>
        </div>

        <div class="metrics-grid">
          <div class="metric-cell">
            <div class="metric-name">Confidence</div>
            <div class="metric-val" id="confVal">—</div>
          </div>
          <div class="metric-cell">
            <div class="metric-name">Model Used</div>
            <div class="metric-val" id="modelUsed">—</div>
          </div>
          <div class="metric-cell">
            <div class="metric-name">Resolution</div>
            <div class="metric-val" id="resVal">—</div>
          </div>
          <div class="metric-cell">
            <div class="metric-name">Inference Time</div>
            <div class="metric-val" id="inferenceTime">—</div>
          </div>
        </div>

        <div class="gradcam-section">
          <div class="gradcam-label">
            Grad-CAM Heatmap
            <span>Explainability</span>
          </div>
          <div class="gradcam-container" id="gradcamContainer">
            <canvas id="gradcamCanvas" style="width:100%;height:100%;"></canvas>
          </div>
        </div>
      </div>
    </div>
  </div>
</section>

<!-- COMPARE -->
<section id="compare" style="max-width:1200px;">
  <div class="section-label">// Side-by-Side</div>
  <h2 class="section-title">Compare Images</h2>
  <p class="section-desc">Upload two images simultaneously to compare crack detection results side by side.</p>

  <div class="compare-grid">
    <!-- Slot A -->
    <div class="compare-slot">
      <div class="panel-header">
        <span class="panel-title">Image A</span>
        <div class="panel-dot" style="background:var(--accent);"></div>
      </div>
      <div class="compare-drop" id="compareDropA">
        <input type="file" accept="image/*" onchange="handleCompare(this, 'A')">
        <div class="compare-drop-icon">📷</div>
        <div class="compare-drop-text">Click to upload Image A</div>
        <img class="compare-drop-img" id="compareImgA" src="" alt="">
      </div>
      <div class="compare-result" id="compareResultA">
        <div class="compare-verdict">
          <div class="compare-dot" id="compareDotA"></div>
          <div class="compare-verdict-text" id="compareVerdictA">—</div>
        </div>
        <div class="compare-bar-wrap">
          <div class="compare-bar-label">
            <span>Crack Probability</span>
            <span id="compareProbA">—</span>
          </div>
          <div class="compare-bar-track">
            <div class="compare-bar-fill" id="compareBarA"></div>
          </div>
        </div>
      </div>
    </div>

    <!-- Slot B -->
    <div class="compare-slot">
      <div class="panel-header">
        <span class="panel-title">Image B</span>
        <div class="panel-dot" style="background:var(--accent3);"></div>
      </div>
      <div class="compare-drop" id="compareDropB">
        <input type="file" accept="image/*" onchange="handleCompare(this, 'B')">
        <div class="compare-drop-icon">📷</div>
        <div class="compare-drop-text">Click to upload Image B</div>
        <img class="compare-drop-img" id="compareImgB" src="" alt="">
      </div>
      <div class="compare-result" id="compareResultB">
        <div class="compare-verdict">
          <div class="compare-dot" id="compareDotB"></div>
          <div class="compare-verdict-text" id="compareVerdictB">—</div>
        </div>
        <div class="compare-bar-wrap">
          <div class="compare-bar-label">
            <span>Crack Probability</span>
            <span id="compareProbB">—</span>
          </div>
          <div class="compare-bar-track">
            <div class="compare-bar-fill" id="compareBarB"></div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <div class="compare-btn-row">
    <button class="btn-analyze" style="max-width:220px;" onclick="runCompare()" id="compareBtn" disabled>
      <div class="spinner" id="compareSpinner" style="display:none;"></div>
      <span id="compareBtnText">Upload both images</span>
    </button>
    <button class="btn-clear" onclick="clearCompare()">Clear Both</button>
  </div>
</section>

<!-- PROJECT INFO -->
<section id="project" style="max-width:1200px;">
  <div class="section-label">// Research Project</div>
  <h2 class="section-title">Project Details</h2>
  <p class="section-desc">Full technical details of the CNN-based surface crack detection project, including dataset, model architectures, and results.</p>

  <div class="project-tabs">
    <button class="tab-btn active" onclick="switchTab('overview')">Overview</button>
    <button class="tab-btn" onclick="switchTab('dataset')">Dataset</button>
    <button class="tab-btn" onclick="switchTab('models')">Models</button>
    <button class="tab-btn" onclick="switchTab('results')">Results</button>
    <button class="tab-btn" onclick="switchTab('rq')">Research Questions</button>
  </div>

  <!-- Overview Tab -->
  <div class="tab-content active" id="tab-overview">
    <div class="overview-grid">
      <div class="overview-card">
        <div class="ov-icon">🎯</div>
        <div class="ov-title">Problem</div>
        <div class="ov-desc">Binary classification of concrete surface images into crack (Positive) and no-crack (Negative) categories using deep learning.</div>
      </div>
      <div class="overview-card">
        <div class="ov-icon">🧠</div>
        <div class="ov-title">Approach</div>
        <div class="ov-desc">Two CNN models compared: a custom-built baseline trained from scratch, and MobileNetV2 using transfer learning from ImageNet weights.</div>
      </div>
      <div class="overview-card">
        <div class="ov-icon">🔍</div>
        <div class="ov-title">Explainability</div>
        <div class="ov-desc">Grad-CAM and SHAP are applied to verify predictions are based on actual crack regions, not irrelevant background areas.</div>
      </div>
      <div class="overview-card">
        <div class="ov-icon">⚡</div>
        <div class="ov-title">Pipeline</div>
        <div class="ov-desc">tf.data pipeline with parallel decoding and AUTOTUNE prefetch. Images resized from 227×227 to 96×96 for faster GPU processing.</div>
      </div>
      <div class="overview-card">
        <div class="ov-icon">🎛️</div>
        <div class="ov-title">Regularisation</div>
        <div class="ov-desc">L2 regularisation (λ=0.01) and Dropout (0.5–0.7) applied deliberately to keep validation accuracy below 90% and avoid overfitting.</div>
      </div>
      <div class="overview-card">
        <div class="ov-icon">🌐</div>
        <div class="ov-title">Deployment</div>
        <div class="ov-desc">Phase 3 web application hosted on Hugging Face Spaces. Flask backend serves the saved .keras model for real-time inference.</div>
      </div>
    </div>
  </div>

  <!-- Dataset Tab -->
  <div class="tab-content" id="tab-dataset">
    <table class="data-table">
      <thead>
        <tr><th>Attribute</th><th>Details</th></tr>
      </thead>
      <tbody>
        <tr><td class="key-cell">Total Images</td><td>40,000</td></tr>
        <tr><td class="key-cell">Number of Classes</td><td>2 — Binary Classification</td></tr>
        <tr><td class="key-cell">Class Names</td><td>Positive (crack present) and Negative (no crack)</td></tr>
        <tr><td class="key-cell">Images per Class</td><td>20,000 Positive + 20,000 Negative</td></tr>
        <tr><td class="key-cell">Class Distribution</td><td>Perfectly balanced — 50% / 50%</td></tr>
        <tr><td class="key-cell">Native Resolution</td><td>227 × 227 pixels</td></tr>
        <tr><td class="key-cell">Resized To</td><td>96 × 96 pixels (training & inference)</td></tr>
        <tr><td class="key-cell">Image Type</td><td>RGB colour JPEG photographs</td></tr>
        <tr><td class="key-cell">Train / Val Split</td><td>80% training (32,000) / 20% validation (8,000)</td></tr>
        <tr><td class="key-cell">Source</td><td>Real-world concrete surface photographs</td></tr>
        <tr><td class="key-cell">Dataset Link</td><td><a href="https://www.kaggle.com/datasets/arunrk7/surface-crack-detection" target="_blank" style="color:var(--accent3);">kaggle.com/datasets/arunrk7/surface-crack-detection</a></td></tr>
      </tbody>
    </table>
  </div>

  <!-- Models Tab -->
  <div class="tab-content" id="tab-models">
    <div class="model-grid">
      <div class="model-card custom">
        <div class="model-tag">Baseline</div>
        <div class="model-name">Custom CNN</div>
        <div class="model-desc">Three-block convolutional feature extractor designed from scratch. Uses GlobalAveragePooling2D instead of Flatten to reduce parameters and overfitting.</div>
        <div class="model-specs">
          <div class="spec-row"><span class="spec-key">Architecture</span><span class="spec-val">3× [Conv2D + ReLU + MaxPool]</span></div>
          <div class="spec-row"><span class="spec-key">Filters</span><span class="spec-val">32 → 64 → 128</span></div>
          <div class="spec-row"><span class="spec-key">Pooling</span><span class="spec-val">GlobalAveragePooling2D</span></div>
          <div class="spec-row"><span class="spec-key">Dropout</span><span class="spec-val">0.5</span></div>
          <div class="spec-row"><span class="spec-key">Regularisation</span><span class="spec-val">L2 λ = 0.01</span></div>
          <div class="spec-row"><span class="spec-key">Trainable Params</span><span class="spec-val">~93,377</span></div>
          <div class="spec-row"><span class="spec-key">Output</span><span class="spec-val">Dense(1) + Sigmoid</span></div>
        </div>
      </div>
      <div class="model-card transfer">
        <div class="model-tag">Transfer Learning</div>
        <div class="model-name">MobileNetV2</div>
        <div class="model-desc">Pre-trained on ImageNet in feature extraction mode. Base layers frozen — only the custom classification head is trained.</div>
        <div class="model-specs">
          <div class="spec-row"><span class="spec-key">Pre-trained On</span><span class="spec-val">ImageNet (1.2M images)</span></div>
          <div class="spec-row"><span class="spec-key">Base Layers</span><span class="spec-val">Frozen (trainable = False)</span></div>
          <div class="spec-row"><span class="spec-key">Head</span><span class="spec-val">GAP → Dense(128) → Dropout(0.7)</span></div>
          <div class="spec-row"><span class="spec-key">Dropout</span><span class="spec-val">0.7</span></div>
          <div class="spec-row"><span class="spec-key">Trainable Params</span><span class="spec-val">~155,649 (head only)</span></div>
          <div class="spec-row"><span class="spec-key">Frozen Params</span><span class="spec-val">~2,257,984 (base)</span></div>
          <div class="spec-row"><span class="spec-key">Output</span><span class="spec-val">Dense(1) + Sigmoid</span></div>
        </div>
      </div>
    </div>
  </div>

  <!-- Results Tab -->
  <div class="tab-content" id="tab-results">
    <div class="results-comparison">
      <div class="metric-row metric-row-header">
        <div>Metric</div>
        <div style="text-align:center;">Custom CNN</div>
        <div style="text-align:center;">MobileNetV2</div>
      </div>
      <div class="metric-row">
        <div class="metric-name-cell">Validation Accuracy</div>
        <div class="metric-val-cell badge-ok">82–87%</div>
        <div class="metric-val-cell badge-good">85–89%</div>
      </div>
      <div class="metric-row">
        <div class="metric-name-cell">AUC-ROC</div>
        <div class="metric-val-cell badge-ok">0.88–0.93</div>
        <div class="metric-val-cell badge-good">0.91–0.96</div>
      </div>
      <div class="metric-row">
        <div class="metric-name-cell">Trainable Parameters</div>
        <div class="metric-val-cell">~93K</div>
        <div class="metric-val-cell">~155K</div>
      </div>
      <div class="metric-row">
        <div class="metric-name-cell">Training Speed</div>
        <div class="metric-val-cell badge-good">Fastest</div>
        <div class="metric-val-cell badge-ok">Moderate</div>
      </div>
      <div class="metric-row">
        <div class="metric-name-cell">Grad-CAM Quality</div>
        <div class="metric-val-cell badge-ok">Medium</div>
        <div class="metric-val-cell badge-good">High</div>
      </div>
      <div class="metric-row">
        <div class="metric-name-cell">Accuracy Cap (&lt;90%)</div>
        <div class="metric-val-cell badge-good">✓ Met</div>
        <div class="metric-val-cell badge-good">✓ Met</div>
      </div>
    </div>
  </div>

  <!-- Research Questions Tab -->
  <div class="tab-content" id="tab-rq">
    <div class="rq-list">
      <div class="rq-item"><div class="rq-num">RQ1</div><div class="rq-text">How accurately can a custom CNN classify concrete surface cracks while keeping validation accuracy below 90%?</div></div>
      <div class="rq-item"><div class="rq-num">RQ2</div><div class="rq-text">Does transfer learning using MobileNetV2 improve classification performance compared to the custom CNN trained from scratch?</div></div>
      <div class="rq-item"><div class="rq-num">RQ3</div><div class="rq-text">Which model provides the best trade-off between classification accuracy and computational cost?</div></div>
      <div class="rq-item"><div class="rq-num">RQ4</div><div class="rq-text">Which image regions does each model focus on when making correct predictions, as revealed by Grad-CAM heatmaps?</div></div>
      <div class="rq-item"><div class="rq-num">RQ5</div><div class="rq-text">Do Grad-CAM attention patterns differ between the custom CNN and MobileNetV2, and are they semantically meaningful?</div></div>
      <div class="rq-item"><div class="rq-num">RQ6</div><div class="rq-text">What positive and negative visual evidence do SHAP explanations reveal about individual model decisions?</div></div>
      <div class="rq-item"><div class="rq-num">RQ7</div><div class="rq-text">Can explainability methods help identify and understand why specific images are misclassified?</div></div>
    </div>
  </div>
</section>

<!-- FOOTER -->
<footer>
  <div class="footer-logo">CrackSense</div>
  <div>Pattern Recognition Course — Phase 2 &amp; 3 Project</div>
  <div>Demo v1.0 — Model integration in Phase 3</div>
</footer>

<div class="toast" id="toast"></div>

<script>
  // ── State ──────────────────────────────────────────
  let uploadedFile = null;
  let compareFileA = null;
  let compareFileB = null;

  // ── Nav active link on scroll ──────────────────────
  const sections = ['home','detect','compare','project'];
  window.addEventListener('scroll', () => {
    const y = window.scrollY + 100;
    sections.forEach(id => {
      const el = document.getElementById(id);
      if (!el) return;
      const a = document.querySelector(`.nav-links a[href="#${id}"]`);
      if (!a) return;
      if (y >= el.offsetTop && y < el.offsetTop + el.offsetHeight) {
        document.querySelectorAll('.nav-links a').forEach(x => x.classList.remove('active'));
        a.classList.add('active');
      }
    });
  });

  // ── File upload handler ────────────────────────────
  function handleFile(input) {
    const file = input.files[0];
    if (!file) return;
    uploadedFile = file;

    const reader = new FileReader();
    reader.onload = e => {
      const img = document.getElementById('preview-img');
      img.src = e.target.result;
      document.getElementById('dropZone').classList.add('has-image');
    };
    reader.readAsDataURL(file);

    const btn = document.getElementById('analyzeBtn');
    btn.disabled = false;
    document.getElementById('analyzeBtnText').textContent = 'Analyse Image';
    showToast('Image loaded — ready to analyse');
  }

  function clearUpload() {
    uploadedFile = null;
    document.getElementById('preview-img').src = '';
    document.getElementById('dropZone').classList.remove('has-image');
    document.getElementById('fileInput').value = '';
    document.getElementById('analyzeBtn').disabled = true;
    document.getElementById('analyzeBtnText').textContent = 'Upload an image first';
    document.getElementById('resultsEmpty').style.display = 'flex';
    document.getElementById('resultContent').classList.remove('visible');
  }

  // ── Simulate analysis (demo mode) ─────────────────
  function runAnalysis() {
    if (!uploadedFile) return;

    const btn = document.getElementById('analyzeBtn');
    btn.disabled = true;
    document.getElementById('analyzeSpinner').style.display = 'block';
    document.getElementById('analyzeBtnText').textContent = 'Analysing...';

    const start = performance.now();

    setTimeout(() => {
      const elapsed = ((performance.now() - start) / 1000).toFixed(2);

      // Simulate result — random for demo
      const prob = Math.random();
      const isCrack = prob > 0.5;

      document.getElementById('analyzeSpinner').style.display = 'none';
      document.getElementById('analyzeBtnText').textContent = 'Re-analyse';
      btn.disabled = false;

      // Show results
      document.getElementById('resultsEmpty').style.display = 'none';
      document.getElementById('resultContent').classList.add('visible');

      // Verdict
      const verdictIcon = document.getElementById('verdictIcon');
      const verdictText = document.getElementById('verdictText');
      if (isCrack) {
        verdictIcon.textContent = '⚠️';
        verdictIcon.className = 'verdict-icon crack';
        verdictText.textContent = 'Crack Detected';
        verdictText.className = 'verdict-text crack';
      } else {
        verdictIcon.textContent = '✅';
        verdictIcon.className = 'verdict-icon safe';
        verdictText.textContent = 'No Crack Found';
        verdictText.className = 'verdict-text safe';
      }

      // Probability
      const pct = (prob * 100).toFixed(1);
      document.getElementById('probValue').textContent = pct + '%';
      document.getElementById('probValue').style.color = isCrack ? 'var(--danger)' : 'var(--safe)';
      const fill = document.getElementById('gaugeFill');
      fill.style.background = isCrack ? 'var(--danger)' : 'var(--safe)';
      setTimeout(() => { fill.style.width = pct + '%'; }, 100);

      // Metrics
      const conf = Math.abs(prob - 0.5) * 2;
      document.getElementById('confVal').textContent = (conf * 100).toFixed(1) + '%';
      document.getElementById('modelUsed').textContent = 'MobileNetV2';
      document.getElementById('resVal').textContent = '96 × 96';
      document.getElementById('inferenceTime').textContent = elapsed + 's';

      // Draw simulated Grad-CAM on canvas
      drawGradCam(uploadedFile, isCrack);

      showToast(isCrack ? '⚠️ Crack detected' : '✅ Surface looks intact');
    }, 1200 + Math.random() * 600);
  }

  // ── Simulated Grad-CAM using canvas ───────────────
  function drawGradCam(file, isCrack) {
    const canvas = document.getElementById('gradcamCanvas');
    const container = document.getElementById('gradcamContainer');
    const ctx = canvas.getContext('2d');
    canvas.width  = container.clientWidth  || 320;
    canvas.height = container.clientHeight || 160;

    const img = new Image();
    img.onload = () => {
      ctx.drawImage(img, 0, 0, canvas.width, canvas.height);

      // Overlay a simulated heatmap
      const W = canvas.width, H = canvas.height;
      const cx = isCrack ? W * 0.4 : W * 0.5;
      const cy = isCrack ? H * 0.5 : H * 0.3;

      const grad = ctx.createRadialGradient(cx, cy, 0, cx, cy, W * 0.45);
      grad.addColorStop(0,    isCrack ? 'rgba(255,30,30,0.65)' : 'rgba(255,200,30,0.4)');
      grad.addColorStop(0.4,  isCrack ? 'rgba(255,120,0,0.35)' : 'rgba(30,255,120,0.2)');
      grad.addColorStop(1,    'rgba(0,0,255,0.0)');

      ctx.fillStyle = grad;
      ctx.fillRect(0, 0, W, H);

      // Label
      ctx.fillStyle = 'rgba(0,0,0,0.55)';
      ctx.fillRect(0, H - 22, W, 22);
      ctx.fillStyle = '#e8ff47';
      ctx.font = '10px Space Mono, monospace';
      ctx.fillText('GRAD-CAM  ·  SIMULATED DEMO  ·  Real heatmap requires model integration', 8, H - 7);
    };
    img.src = URL.createObjectURL(file);
  }

  // ── Compare handlers ───────────────────────────────
  function handleCompare(input, slot) {
    const file = input.files[0];
    if (!file) return;

    if (slot === 'A') compareFileA = file;
    else compareFileB = file;

    const reader = new FileReader();
    reader.onload = e => {
      const img = document.getElementById(`compareImg${slot}`);
      img.src = e.target.result;
      document.getElementById(`compareDrop${slot}`).classList.add('has-img');
    };
    reader.readAsDataURL(file);

    updateCompareBtn();
  }

  function updateCompareBtn() {
    const btn = document.getElementById('compareBtn');
    if (compareFileA && compareFileB) {
      btn.disabled = false;
      document.getElementById('compareBtnText').textContent = 'Compare Both';
    }
  }

  function runCompare() {
    if (!compareFileA || !compareFileB) return;

    const btn = document.getElementById('compareBtn');
    btn.disabled = true;
    document.getElementById('compareSpinner').style.display = 'block';
    document.getElementById('compareBtnText').textContent = 'Analysing...';

    setTimeout(() => {
      document.getElementById('compareSpinner').style.display = 'none';
      document.getElementById('compareBtnText').textContent = 'Compare Again';
      btn.disabled = false;

      ['A','B'].forEach(slot => {
        const prob = Math.random();
        const isCrack = prob > 0.5;
        const pct = (prob * 100).toFixed(1);

        const dot = document.getElementById(`compareDot${slot}`);
        const verdict = document.getElementById(`compareVerdict${slot}`);
        const probEl = document.getElementById(`compareProb${slot}`);
        const bar = document.getElementById(`compareBar${slot}`);
        const result = document.getElementById(`compareResult${slot}`);

        dot.style.background = isCrack ? 'var(--danger)' : 'var(--safe)';
        verdict.textContent = isCrack ? 'Crack Detected' : 'No Crack Found';
        verdict.style.color = isCrack ? 'var(--danger)' : 'var(--safe)';
        probEl.textContent = pct + '%';
        bar.style.background = isCrack ? 'var(--danger)' : 'var(--safe)';
        setTimeout(() => { bar.style.width = pct + '%'; }, 100);
        result.classList.add('visible');
      });

      showToast('Comparison complete');
    }, 1400);
  }

  function clearCompare() {
    compareFileA = null; compareFileB = null;
    ['A','B'].forEach(slot => {
      document.getElementById(`compareImg${slot}`).src = '';
      document.getElementById(`compareDrop${slot}`).classList.remove('has-img');
      document.getElementById(`compareResult${slot}`).classList.remove('visible');
      const bar = document.getElementById(`compareBar${slot}`);
      bar.style.width = '0%';
    });
    const btn = document.getElementById('compareBtn');
    btn.disabled = true;
    document.getElementById('compareBtnText').textContent = 'Upload both images';
  }

  // ── Tabs ───────────────────────────────────────────
  function switchTab(id) {
    document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
    document.querySelectorAll('.tab-content').forEach(c => c.classList.remove('active'));
    document.getElementById(`tab-${id}`).classList.add('active');
    event.target.classList.add('active');
  }

  // ── Toast ──────────────────────────────────────────
  function showToast(msg) {
    const t = document.getElementById('toast');
    t.textContent = msg;
    t.classList.add('show');
    setTimeout(() => t.classList.remove('show'), 2800);
  }

  // ── Drag & drop ────────────────────────────────────
  const dz = document.getElementById('dropZone');
  dz.addEventListener('dragover', e => { e.preventDefault(); dz.style.background = 'rgba(232,255,71,0.04)'; });
  dz.addEventListener('dragleave', () => { dz.style.background = ''; });
  dz.addEventListener('drop', e => {
    e.preventDefault();
    dz.style.background = '';
    const file = e.dataTransfer.files[0];
    if (file && file.type.startsWith('image/')) {
      const dt = new DataTransfer();
      dt.items.add(file);
      document.getElementById('fileInput').files = dt.files;
      handleFile(document.getElementById('fileInput'));
    }
  });
</script>
</body>
</html>
