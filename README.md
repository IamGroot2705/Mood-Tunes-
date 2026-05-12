<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>MoodTunes — Emotion-Driven Music Recommender</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Syne:wght@400;700;800&family=JetBrains+Mono:wght@400;500&display=swap" rel="stylesheet">
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/@tabler/icons-webfont@latest/tabler-icons.min.css">
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  :root {
    --accent: #22d3ee;
    --accent2: #a78bfa;
    --bg: #080b12;
    --surface: #0e1420;
    --surface2: #141c2e;
    --border: rgba(255,255,255,0.07);
    --text: #f0f4ff;
    --muted: rgba(240,244,255,0.5);
  }

  html { scroll-behavior: smooth; }

  body {
    font-family: 'Syne', sans-serif;
    background: var(--bg);
    color: var(--text);
    min-height: 100vh;
    overflow-x: hidden;
  }

  .mono { font-family: 'JetBrains Mono', monospace; }

  .orb {
    position: fixed; border-radius: 50%;
    filter: blur(90px); pointer-events: none; z-index: 0;
  }
  .orb1 { width: 560px; height: 560px; background: rgba(34,211,238,0.07); top: -120px; left: -120px; }
  .orb2 { width: 440px; height: 440px; background: rgba(167,139,250,0.07); bottom: 80px; right: -120px; }

  .wrap { max-width: 880px; margin: 0 auto; padding: 0 28px; position: relative; z-index: 1; }

  /* NAV */
  nav {
    display: flex; align-items: center; justify-content: space-between;
    padding: 24px 0; border-bottom: 1px solid var(--border);
  }
  .logo { font-size: 21px; font-weight: 800; letter-spacing: -0.5px; }
  .logo span { color: var(--accent); }
  .nav-right { display: flex; gap: 10px; align-items: center; }
  .badge {
    font-family: 'JetBrains Mono', monospace; font-size: 11px;
    padding: 5px 11px; border: 1px solid rgba(34,211,238,0.3);
    border-radius: 20px; color: var(--accent); background: rgba(34,211,238,0.06);
  }
  .nav-link {
    font-family: 'JetBrains Mono', monospace; font-size: 11px;
    color: var(--muted); text-decoration: none; padding: 5px 10px;
    border-radius: 6px; transition: color 0.2s;
  }
  .nav-link:hover { color: var(--text); }

  /* HERO */
  .hero { padding: 88px 0 64px; text-align: center; }
  .hero-pill {
    display: inline-flex; align-items: center; gap: 8px;
    font-family: 'JetBrains Mono', monospace; font-size: 11px;
    color: var(--accent); background: rgba(34,211,238,0.08);
    border: 1px solid rgba(34,211,238,0.2); border-radius: 20px;
    padding: 6px 14px; margin-bottom: 32px;
  }
  .pill-dot { width: 6px; height: 6px; background: var(--accent); border-radius: 50%; animation: blink 1.5s infinite; flex-shrink: 0; }
  @keyframes blink { 0%,100%{opacity:1} 50%{opacity:0.3} }

  h1 { font-size: clamp(42px, 6vw, 74px); font-weight: 800; line-height: 1.05; letter-spacing: -2.5px; margin-bottom: 20px; }
  h1 em { font-style: normal; color: var(--accent); }
  .hero-sub {
    font-size: 16px; color: var(--muted); max-width: 460px; margin: 0 auto 42px;
    line-height: 1.7; font-family: 'JetBrains Mono', monospace;
  }

  .cta-row { display: flex; gap: 12px; justify-content: center; flex-wrap: wrap; }
  .btn-primary {
    display: inline-flex; align-items: center; gap: 8px;
    background: var(--accent); color: #080b12;
    font-family: 'Syne', sans-serif; font-weight: 700; font-size: 14px;
    padding: 13px 24px; border-radius: 8px; text-decoration: none;
    transition: opacity 0.2s, transform 0.15s;
  }
  .btn-primary:hover { opacity: 0.85; transform: translateY(-1px); }
  .btn-secondary {
    display: inline-flex; align-items: center; gap: 8px;
    background: transparent; color: var(--text);
    font-family: 'Syne', sans-serif; font-weight: 700; font-size: 14px;
    padding: 13px 24px; border-radius: 8px; text-decoration: none;
    border: 1px solid var(--border); transition: border-color 0.2s, transform 0.15s;
  }
  .btn-secondary:hover { border-color: rgba(255,255,255,0.2); transform: translateY(-1px); }

  /* DEMO CARD */
  .demo-card {
    margin: 60px 0;
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 16px;
    overflow: hidden;
  }
  .demo-header {
    padding: 14px 20px; border-bottom: 1px solid var(--border);
    display: flex; align-items: center; gap: 8px;
  }
  .dot { width: 10px; height: 10px; border-radius: 50%; }
  .dot-r { background: #ff5f56; }
  .dot-y { background: #ffbd2e; }
  .dot-g { background: #27c93f; }
  .demo-url { font-family: 'JetBrains Mono', monospace; font-size: 11px; color: var(--muted); margin-left: 6px; }

  .demo-inner { display: grid; grid-template-columns: 1fr 1fr; }
  @media(max-width: 600px) { .demo-inner { grid-template-columns: 1fr; } }

  .cam-panel {
    padding: 32px; border-right: 1px solid var(--border);
    display: flex; flex-direction: column; align-items: center; gap: 16px;
  }
  @media(max-width: 600px) { .cam-panel { border-right: none; border-bottom: 1px solid var(--border); } }

  .panel-label { font-family: 'JetBrains Mono', monospace; font-size: 11px; color: var(--muted); }

  .cam-frame {
    width: 164px; height: 122px; border-radius: 10px;
    background: #0a0e18; border: 1px solid var(--border);
    position: relative; overflow: hidden;
    display: flex; align-items: center; justify-content: center;
  }
  .scan-line {
    position: absolute; left: 0; right: 0; height: 2px;
    background: linear-gradient(90deg, transparent, var(--accent), transparent);
    animation: scan 2s linear infinite;
  }
  @keyframes scan { 0%{top:0%} 100%{top:100%} }
  .cam-emoji { font-size: 34px; opacity: 0.2; }
  .cam-status { font-family: 'JetBrains Mono', monospace; font-size: 11px; color: var(--accent); }

  .capture-btn {
    background: var(--accent); color: #080b12; border: none;
    border-radius: 8px; padding: 10px 20px;
    font-family: 'Syne', sans-serif; font-weight: 700; font-size: 13px;
    cursor: pointer; display: flex; align-items: center; gap: 6px;
  }
  .capture-btn i { font-size: 16px; }

  .result-panel { padding: 32px; display: flex; flex-direction: column; gap: 16px; }

  .emotion-pill {
    display: inline-flex; align-items: center; gap: 8px;
    background: rgba(34,211,238,0.1); border: 1px solid rgba(34,211,238,0.3);
    border-radius: 20px; padding: 6px 14px; width: fit-content;
  }
  .emotion-label { font-family: 'JetBrains Mono', monospace; font-size: 12px; color: var(--accent); }

  .confidence-ring { display: flex; align-items: center; gap: 10px; }
  .ring-track { fill: none; stroke: rgba(255,255,255,0.08); stroke-width: 4; }
  .ring-fill {
    fill: none; stroke: var(--accent); stroke-width: 4; stroke-linecap: round;
    stroke-dasharray: 88; stroke-dashoffset: 8;
    transform: rotate(-90deg); transform-origin: 50% 50%;
    animation: fillRing 1.8s ease-out forwards;
  }
  @keyframes fillRing { from{stroke-dashoffset:88} to{stroke-dashoffset:8} }
  .ring-label { font-family: 'JetBrains Mono', monospace; font-size: 11px; color: var(--muted); }

  .track-card {
    background: var(--surface2); border: 1px solid var(--border);
    border-radius: 10px; padding: 12px 14px;
    display: flex; align-items: center; gap: 12px;
  }
  .album-art {
    width: 44px; height: 44px; border-radius: 6px; flex-shrink: 0;
    background: linear-gradient(135deg, #1DB954, #0a6b33);
    display: flex; align-items: center; justify-content: center; font-size: 22px;
  }
  .track-info { flex: 1; min-width: 0; }
  .track-name { font-weight: 700; font-size: 13px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
  .track-artist { font-family: 'JetBrains Mono', monospace; font-size: 11px; color: var(--muted); }

  .wave { display: flex; align-items: flex-end; gap: 2px; height: 24px; flex-shrink: 0; }
  .bar { width: 3px; background: var(--accent); border-radius: 2px; animation: wave 0.8s ease-in-out infinite alternate; }
  .bar:nth-child(1){height:8px;animation-delay:0s}
  .bar:nth-child(2){height:16px;animation-delay:0.1s}
  .bar:nth-child(3){height:22px;animation-delay:0.2s}
  .bar:nth-child(4){height:14px;animation-delay:0.15s}
  .bar:nth-child(5){height:20px;animation-delay:0.05s}
  .bar:nth-child(6){height:10px;animation-delay:0.25s}
  .bar:nth-child(7){height:18px;animation-delay:0.12s}
  .bar:nth-child(8){height:6px;animation-delay:0.3s}
  .bar:nth-child(9){height:14px;animation-delay:0.08s}
  .bar:nth-child(10){height:20px;animation-delay:0.18s}
  @keyframes wave { from{transform:scaleY(0.4)} to{transform:scaleY(1)} }

  .privacy-note { font-family: 'JetBrains Mono', monospace; font-size: 10px; color: var(--muted); display: flex; align-items: center; gap: 6px; }
  .privacy-note i { color: var(--accent); font-size: 13px; }

  /* DIVIDER */
  hr.div { border: none; border-top: 1px solid var(--border); margin: 0; }

  /* SECTIONS */
  .section { padding: 52px 0; }
  .section-tag {
    font-family: 'JetBrains Mono', monospace; font-size: 11px;
    color: var(--accent); letter-spacing: 0.12em; text-transform: uppercase; margin-bottom: 10px;
  }
  .section-title { font-size: 30px; font-weight: 800; letter-spacing: -0.5px; margin-bottom: 8px; }
  .section-sub { font-size: 13px; color: var(--muted); font-family: 'JetBrains Mono', monospace; margin-bottom: 32px; line-height: 1.6; }

  /* FEATURES */
  .features { display: grid; grid-template-columns: repeat(auto-fit, minmax(220px, 1fr)); gap: 14px; }
  .feat {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: 12px; padding: 20px 20px 22px;
  }
  .feat-icon { font-size: 24px; margin-bottom: 12px; }
  .feat-title { font-size: 14px; font-weight: 700; margin-bottom: 6px; }
  .feat-desc { font-size: 12px; color: var(--muted); font-family: 'JetBrains Mono', monospace; line-height: 1.65; }

  /* EMOTION MAP */
  .emotion-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(220px, 1fr)); gap: 10px; }
  .emo-row {
    display: flex; align-items: center; gap: 12px;
    background: var(--surface); border: 1px solid var(--border);
    border-radius: 10px; padding: 12px 14px;
  }
  .emo-emoji { font-size: 22px; flex-shrink: 0; }
  .emo-info { min-width: 0; }
  .emo-name { font-size: 13px; font-weight: 700; margin-bottom: 3px; }
  .emo-genres { font-family: 'JetBrains Mono', monospace; font-size: 10px; color: var(--muted); white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }

  /* ARCH */
  .arch-box {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: 12px; padding: 26px 28px;
    font-family: 'JetBrains Mono', monospace; font-size: 12px;
    color: var(--muted); line-height: 2.1; overflow-x: auto;
  }
  .arch-box .k { color: var(--accent); }
  .arch-box .v { color: #a78bfa; }
  .arch-box .c { color: rgba(240,244,255,0.22); }

  /* STACK */
  .stack-list { display: flex; flex-direction: column; gap: 8px; }
  .stack-item {
    display: flex; align-items: center; gap: 14px;
    background: var(--surface); border: 1px solid var(--border);
    border-radius: 10px; padding: 13px 16px;
  }
  .stack-tag {
    font-family: 'JetBrains Mono', monospace; font-size: 10px;
    padding: 3px 8px; border-radius: 4px; flex-shrink: 0;
    background: rgba(34,211,238,0.08); color: var(--accent);
    border: 1px solid rgba(34,211,238,0.2); min-width: 34px; text-align: center;
  }
  .stack-name { font-size: 13px; font-weight: 700; flex: 1; }
  .stack-desc { font-family: 'JetBrains Mono', monospace; font-size: 11px; color: var(--muted); text-align: right; }
  @media(max-width: 500px) { .stack-desc { display: none; } }

  /* QUICKSTART */
  .quickstart { display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 14px; }
  .step {
    background: var(--surface); border: 1px solid var(--border);
    border-radius: 12px; padding: 20px;
  }
  .step-num {
    font-family: 'JetBrains Mono', monospace; font-size: 11px;
    color: var(--accent); margin-bottom: 10px;
  }
  .step-title { font-size: 14px; font-weight: 700; margin-bottom: 6px; }
  .step-desc { font-family: 'JetBrains Mono', monospace; font-size: 11px; color: var(--muted); line-height: 1.7; }
  .code-block {
    background: #0a0e18; border: 1px solid var(--border);
    border-radius: 8px; padding: 10px 12px; margin-top: 10px;
    font-family: 'JetBrains Mono', monospace; font-size: 11px; color: var(--accent);
    overflow-x: auto; white-space: nowrap;
  }

  /* CTA */
  .cta-section {
    padding: 64px 0; text-align: center;
    border-top: 1px solid var(--border);
  }
  .cta-section h2 { font-size: 38px; font-weight: 800; letter-spacing: -1px; margin-bottom: 12px; }
  .cta-section p { font-family: 'JetBrains Mono', monospace; font-size: 13px; color: var(--muted); margin-bottom: 30px; }

  /* FOOTER */
  footer {
    border-top: 1px solid var(--border); padding: 22px 0;
    display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap; gap: 12px;
  }
  .footer-left { font-family: 'JetBrains Mono', monospace; font-size: 11px; color: var(--muted); }
  .footer-links { display: flex; gap: 18px; flex-wrap: wrap; }
  .footer-links a { font-family: 'JetBrains Mono', monospace; font-size: 11px; color: var(--muted); text-decoration: none; transition: color 0.2s; }
  .footer-links a:hover { color: var(--accent); }
</style>
</head>
<body>

<div class="orb orb1"></div>
<div class="orb orb2"></div>

<div class="wrap">

  <!-- NAV -->
  <nav>
    <div class="logo">Mood<span>Tunes</span></div>
    <div class="nav-right">
      <a href="https://moodtunes-alpha.vercel.app/" class="nav-link">Demo</a>
      <a href="https://github.com" class="nav-link">GitHub</a>
      <div class="badge">v1.0 · MIT</div>
    </div>
  </nav>

  <!-- HERO -->
  <section class="hero">
    <div class="hero-pill">
      <div class="pill-dot"></div>
      Live emotion detection · Spotify integration
    </div>
    <h1>Music that reads<br><em>your face.</em></h1>
    <p class="hero-sub">Point your camera. Get the perfect track for your mood. No input. No genre picking. Just you.</p>
    <div class="cta-row">
      <a href="https://moodtunes-alpha.vercel.app/" class="btn-primary">
        <i class="ti ti-player-play"></i> Try the demo
      </a>
      <a href="https://github.com" class="btn-secondary">
        <i class="ti ti-brand-github"></i> View on GitHub
      </a>
    </div>
  </section>

  <!-- BROWSER MOCKUP -->
  <div class="demo-card">
    <div class="demo-header">
      <div class="dot dot-r"></div>
      <div class="dot dot-y"></div>
      <div class="dot dot-g"></div>
      <span class="demo-url">moodtunes-alpha.vercel.app</span>
    </div>
    <div class="demo-inner">
      <div class="cam-panel">
        <div class="panel-label">CAMERA FEED</div>
        <div class="cam-frame">
          <div class="scan-line"></div>
          <span class="cam-emoji">🎭</span>
        </div>
        <div class="cam-status">● ACTIVE · 30fps</div>
        <button class="capture-btn">
          <i class="ti ti-camera"></i> Capture &amp; Analyse
        </button>
      </div>

      <div class="result-panel">
        <div class="panel-label">RESULT</div>
        <div class="emotion-pill">
          <span style="font-size:17px">😊</span>
          <span class="emotion-label">HAPPY detected</span>
        </div>
        <div class="confidence-ring">
          <svg width="38" height="38" viewBox="0 0 36 36" aria-label="91% confidence">
            <circle class="ring-track" cx="18" cy="18" r="14"/>
            <circle class="ring-fill" cx="18" cy="18" r="14"/>
            <text x="18" y="22" text-anchor="middle" fill="rgba(240,244,255,0.9)" font-family="JetBrains Mono" font-size="7" font-weight="500">91%</text>
          </svg>
          <span class="ring-label">91.3% confidence · RetinaFace</span>
        </div>
        <div class="track-card">
          <div class="album-art">🎵</div>
          <div class="track-info">
            <div class="track-name">Levitating</div>
            <div class="track-artist">Dua Lipa · Future Nostalgia</div>
          </div>
          <div class="wave">
            <div class="bar"></div><div class="bar"></div><div class="bar"></div>
            <div class="bar"></div><div class="bar"></div><div class="bar"></div>
            <div class="bar"></div><div class="bar"></div><div class="bar"></div>
            <div class="bar"></div>
          </div>
        </div>
        <div class="privacy-note">
          <i class="ti ti-shield-check"></i>
          Frame analysed in-memory · not stored
        </div>
      </div>
    </div>
  </div>

  <!-- FEATURES -->
  <section class="section">
    <div class="section-tag">Features</div>
    <div class="section-title">Everything in one shot.</div>
    <div class="section-sub">Webcam → emotion → Spotify track. No manual input, no genre picking, no accounts.</div>
    <div class="features">
      <div class="feat">
        <div class="feat-icon">🎭</div>
        <div class="feat-title">7-emotion recognition</div>
        <div class="feat-desc">Happy, sad, angry, surprise, fear, disgust, neutral — via VGG-Face backbone with RetinaFace detector</div>
      </div>
      <div class="feat">
        <div class="feat-icon">🎵</div>
        <div class="feat-title">Live Spotify search</div>
        <div class="feat-desc">Real-time genre-based track fetch via Spotify Web API, with 30-sec audio preview fallback</div>
      </div>
      <div class="feat">
        <div class="feat-icon">📊</div>
        <div class="feat-title">Confidence ring</div>
        <div class="feat-desc">Animated SVG ring shows model certainty percentage so you know exactly how sure the AI is</div>
      </div>
      <div class="feat">
        <div class="feat-icon">🌊</div>
        <div class="feat-title">Waveform visualiser</div>
        <div class="feat-desc">10-bar animated equaliser synced to every recommended track for a live feel</div>
      </div>
      <div class="feat">
        <div class="feat-icon">🎨</div>
        <div class="feat-title">Mood-reactive UI</div>
        <div class="feat-desc">Glow colour, borders, ambient orb and accents all shift dynamically per detected emotion</div>
      </div>
      <div class="feat">
        <div class="feat-icon">🔒</div>
        <div class="feat-title">Privacy-first</div>
        <div class="feat-desc">Frames decoded in RAM, analysed, and discarded immediately. No storage, no accounts, no cookies.</div>
      </div>
    </div>
  </section>

  <hr class="div">

  <!-- EMOTION MAP -->
  <section class="section">
    <div class="section-tag">Mapping</div>
    <div class="section-title">Emotion → Genre.</div>
    <div class="section-sub">Each of 7 detected emotions maps to a curated genre pool. Tracks are fetched live from Spotify.</div>
    <div class="emotion-grid">
      <div class="emo-row"><div class="emo-emoji">😊</div><div class="emo-info"><div class="emo-name">Happy</div><div class="emo-genres">pop · dance · feel-good · summer</div></div></div>
      <div class="emo-row"><div class="emo-emoji">😢</div><div class="emo-info"><div class="emo-name">Sad</div><div class="emo-genres">sad · acoustic · piano · rainy-day</div></div></div>
      <div class="emo-row"><div class="emo-emoji">😠</div><div class="emo-info"><div class="emo-name">Angry</div><div class="emo-genres">metal · hard-rock · punk · rage</div></div></div>
      <div class="emo-row"><div class="emo-emoji">😲</div><div class="emo-info"><div class="emo-name">Surprise</div><div class="emo-genres">electronic · edm · synth-pop · electro</div></div></div>
      <div class="emo-row"><div class="emo-emoji">😨</div><div class="emo-info"><div class="emo-name">Fear</div><div class="emo-genres">ambient · dark-ambient · cinematic</div></div></div>
      <div class="emo-row"><div class="emo-emoji">🤢</div><div class="emo-info"><div class="emo-name">Disgust</div><div class="emo-genres">industrial · noise · experimental</div></div></div>
      <div class="emo-row"><div class="emo-emoji">😐</div><div class="emo-info"><div class="emo-name">Neutral</div><div class="emo-genres">indie · lo-fi · chill · study</div></div></div>
    </div>
  </section>

  <hr class="div">

  <!-- ARCHITECTURE -->
  <section class="section">
    <div class="section-tag">Architecture</div>
    <div class="section-title">How it works.</div>
    <div class="section-sub">Browser captures a frame → FastAPI backend analyses it → Spotify responds with a track.</div>
    <div class="arch-box">
      <span class="k">Browser</span> <span class="c">// React + Vite</span><br>
      &nbsp;&nbsp;getUserMedia() → canvas.drawImage() → <span class="v">base64 JPEG</span><br>
      &nbsp;&nbsp;<span class="k">POST /analyze</span> ─────────────────────────────────►<br>
      &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span class="k">FastAPI</span> <span class="c">// Python</span><br>
      &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;cv2.imdecode() → CLAHE pre-process (LAB)<br>
      &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;DeepFace.analyze() [<span class="v">RetinaFace</span>]<br>
      &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dominant_emotion → genre pool<br>
      &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Spotipy.search() → random track<br>
      &nbsp;&nbsp;◄─── <span class="k">JSON</span> { emotion, confidence, track_name, artist, album_art, spotify_id }<br>
      &nbsp;&nbsp;Framer Motion reveal → <span class="v">Emotion pill</span> + <span class="v">Spotify iFrame</span>
    </div>
  </section>

  <hr class="div">

  <!-- TECH STACK -->
  <section class="section">
    <div class="section-tag">Tech Stack</div>
    <div class="section-title">Built on solid foundations.</div>
    <div class="section-sub">React frontend · FastAPI backend · DeepFace ML · Spotify Web API.</div>
    <div class="stack-list">
      <div class="stack-item"><span class="stack-tag">FE</span><span class="stack-name">React 18 + Vite 5</span><span class="stack-desc">Component state machine · HMR · /analyze proxy</span></div>
      <div class="stack-item"><span class="stack-tag">FE</span><span class="stack-name">Tailwind CSS 3 + Framer Motion 11</span><span class="stack-desc">Utility styling · layout animations · SVG spring</span></div>
      <div class="stack-item"><span class="stack-tag">BE</span><span class="stack-name">FastAPI + Uvicorn</span><span class="stack-desc">Async REST API · ASGI server · hot-reload</span></div>
      <div class="stack-item"><span class="stack-tag">ML</span><span class="stack-name">DeepFace + OpenCV (cv2)</span><span class="stack-desc">VGG-Face backbone · CLAHE pre-processing</span></div>
      <div class="stack-item"><span class="stack-tag">API</span><span class="stack-name">Spotipy · Spotify Web API</span><span class="stack-desc">Genre search · track metadata · iFrame embed</span></div>
      <div class="stack-item"><span class="stack-tag">ENV</span><span class="stack-name">python-dotenv</span><span class="stack-desc">Credential management via .env</span></div>
    </div>
  </section>

  <hr class="div">

  <!-- QUICKSTART -->
  <section class="section">
    <div class="section-tag">Getting Started</div>
    <div class="section-title">Up in three steps.</div>
    <div class="section-sub">Python ≥ 3.10, Node.js ≥ 18, and free Spotify developer credentials.</div>
    <div class="quickstart">
      <div class="step">
        <div class="step-num">01 — Credentials</div>
        <div class="step-title">Get Spotify keys</div>
        <div class="step-desc">Visit developer.spotify.com/dashboard, create an app, set redirect URI to localhost:8000, copy Client ID + Secret.</div>
      </div>
      <div class="step">
        <div class="step-num">02 — Configure</div>
        <div class="step-title">Set up .env</div>
        <div class="step-desc">Copy the example env file and paste your credentials.</div>
        <div class="code-block">cd backend && cp .env.example .env</div>
      </div>
      <div class="step">
        <div class="step-num">03 — Run</div>
        <div class="step-title">One command</div>
        <div class="step-desc">Mac/Linux: run start.sh. Windows: run start.bat. Or start backend and frontend manually.</div>
        <div class="code-block">./start.sh</div>
      </div>
    </div>
  </section>

  <!-- CTA -->
  <div class="cta-section">
    <h2>Try it. It takes <em style="color:var(--accent);font-style:normal">3 seconds.</em></h2>
    <p>No signup. No genre picking. Just your face and a camera.</p>
    <div class="cta-row">
      <a href="https://moodtunes-alpha.vercel.app/" class="btn-primary">
        <i class="ti ti-player-play"></i> Open live demo
      </a>
      <a href="https://github.com" class="btn-secondary">
        <i class="ti ti-brand-github"></i> Star on GitHub
      </a>
    </div>
  </div>

  <footer>
    <div class="footer-left">© 2025 MoodTunes · Built with DeepFace · Spotify Web API · React · FastAPI</div>
    <div class="footer-links">
      <a href="https://moodtunes-alpha.vercel.app/">Live Demo</a>
      <a href="https://github.com">GitHub</a>
      <a href="https://github.com/issues">Report a Bug</a>
      <a href="https://github.com/issues">Request Feature</a>
    </div>
  </footer>

</div>
</body>
</html>
