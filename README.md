<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Anas — Web Portfolio</title>

  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link href="https://fonts.googleapis.com/css2?family=Rajdhani:wght@400;500;600;700&family=IBM+Plex+Mono:wght@300;400;500&display=swap" rel="stylesheet">

  <script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/gsap.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/ScrollTrigger.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>

  <style>
    /* ══════════════════════════════════
       TOKENS
    ══════════════════════════════════ */
    :root {
      --bg:        #0a0800;
      --bg2:       #100d02;
      --surface:   #16110300;
      --border:    rgba(255,140,0,0.12);
      --border-hi: rgba(255,140,0,0.35);
      --white:     #fef3dc;
      --muted:     #7a6540;
      --fox1:      #f6851b; /* MetaMask primary orange */
      --fox2:      #e2761b; /* deeper orange */
      --fox3:      #ffd166; /* amber highlight */
      --fox4:      #ff4500; /* hot ember red */
      --glow:      rgba(246,133,27,0.28);
      --glowh:     rgba(246,133,27,0.5);
      --font-head: 'Rajdhani', sans-serif;
      --font-mono: 'IBM Plex Mono', monospace;
      --r:         10px;
      --rl:        20px;
    }

    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    html { scroll-behavior: smooth; }
    body {
      background: var(--bg);
      color: var(--white);
      font-family: var(--font-mono);
      line-height: 1.6;
      overflow-x: hidden;
      cursor: none;
    }
    ::selection { background: var(--fox1); color: #000; }

    /* ══════════════════════════════════
       CURSOR
    ══════════════════════════════════ */
    #cur-dot {
      width: 7px; height: 7px;
      background: var(--fox1);
      border-radius: 50%;
      position: fixed; top:0; left:0;
      pointer-events: none; z-index:9999;
      transform: translate(-50%,-50%);
      box-shadow: 0 0 10px var(--fox1);
      transition: background .15s;
    }
    #cur-ring {
      width: 32px; height: 32px;
      border: 1.5px solid rgba(246,133,27,.5);
      border-radius: 50%;
      position: fixed; top:0; left:0;
      pointer-events: none; z-index:9998;
      transform: translate(-50%,-50%);
      transition: width .3s, height .3s, border-color .3s;
    }
    body:has(a:hover) #cur-ring,
    body:has(button:hover) #cur-ring {
      width: 52px; height: 52px;
      border-color: var(--fox1);
    }

    /* ══════════════════════════════════
       CANVAS
    ══════════════════════════════════ */
    #three-canvas {
      position: fixed; inset: 0;
      z-index: 0; pointer-events: none;
      opacity: .6;
    }

    /* ══════════════════════════════════
       SCROLL PROGRESS
    ══════════════════════════════════ */
    #progress {
      position: fixed; top:0; left:0; height:2px;
      background: linear-gradient(90deg, var(--fox4), var(--fox1), var(--fox3));
      z-index:9999; width:0;
      box-shadow: 0 0 8px var(--fox1);
      transition: width .1s linear;
    }

    /* ══════════════════════════════════
       HEX GRID BACKGROUND PATTERN
    ══════════════════════════════════ */
    body::before {
      content:'';
      position:fixed; inset:0; z-index:0; pointer-events:none;
      opacity:.03;
      background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='56' height='100'%3E%3Cpath d='M28 66L0 50V17L28 1l28 16v33z' fill='none' stroke='%23f6851b' stroke-width='1'/%3E%3Cpath d='M28 100L0 84V51l28-16 28 16v33z' fill='none' stroke='%23f6851b' stroke-width='1'/%3E%3C/svg%3E");
      background-size: 56px 100px;
    }

    /* ══════════════════════════════════
       NAV
    ══════════════════════════════════ */
    nav {
      position:fixed; top:0; left:0; right:0; z-index:100;
      padding: 16px 48px;
      display:flex; align-items:center; justify-content:space-between;
      background: rgba(10,8,0,.7);
      backdrop-filter: blur(20px);
      border-bottom: 1px solid var(--border);
      transition: padding .3s;
    }
    nav.scrolled { padding: 10px 48px; }

    .nav-logo {
      display:flex; align-items:center; gap:10px;
      font-family: var(--font-head);
      font-size: 1.4rem; font-weight:700;
      letter-spacing:.04em;
      color: var(--fox1);
      text-shadow: 0 0 20px var(--glow);
    }

    /* SVG fox icon inline */
    .fox-icon {
      width:28px; height:28px;
    }

    .nav-links { display:flex; gap:36px; list-style:none; }
    .nav-links a {
      font-family: var(--font-mono);
      font-size:.72rem;
      color: var(--muted);
      text-decoration:none;
      letter-spacing:.12em;
      text-transform:uppercase;
      position:relative;
      transition: color .2s;
    }
    .nav-links a::after {
      content:''; position:absolute; bottom:-4px; left:0;
      width:0; height:1px; background: var(--fox1);
      transition: width .3s;
    }
    .nav-links a:hover { color: var(--fox3); }
    .nav-links a:hover::after { width:100%; }

    .nav-connect {
      display:inline-flex; align-items:center; gap:8px;
      padding:9px 20px; border-radius:6px;
      border:1px solid var(--fox1);
      color:var(--fox1);
      font-family:var(--font-mono); font-size:.72rem;
      letter-spacing:.08em; text-decoration:none;
      background: rgba(246,133,27,.06);
      transition: all .3s;
    }
    .nav-connect::after { display:none !important; }
    .nav-connect:hover {
      background: var(--fox1);
      color:#000;
      box-shadow: 0 0 24px var(--glow);
    }
    .connect-dot {
      width:6px; height:6px; border-radius:50%;
      background: #44ff88;
      box-shadow: 0 0 6px #44ff88;
      animation: blink 2s ease-in-out infinite;
    }
    @keyframes blink {
      0%,100%{opacity:1} 50%{opacity:.3}
    }

    .hamburger { display:none; flex-direction:column; gap:5px; cursor:pointer; }
    .hamburger span { display:block; width:22px; height:2px; background:var(--fox1); border-radius:2px; transition:.3s; }

    /* ══════════════════════════════════
       HERO
    ══════════════════════════════════ */
    #hero {
      min-height:100vh;
      display:flex; align-items:center;
      padding:120px 48px 80px;
      position:relative; z-index:1;
      overflow:hidden;
    }

    .hero-inner { max-width:1120px; margin:0 auto; width:100%; }
    .hero-layout {
      display:grid;
      grid-template-columns:minmax(0, 1.08fr) minmax(320px, .92fr);
      gap:42px;
      align-items:center;
    }
    .hero-copy { position:relative; z-index:2; }

    .hero-tag {
      display:inline-flex; align-items:center; gap:8px;
      font-size:.7rem; letter-spacing:.18em; text-transform:uppercase;
      color: var(--fox3);
      border:1px solid rgba(255,209,102,.2);
      padding:7px 16px; border-radius:4px;
      background: rgba(255,209,102,.04);
      margin-bottom:28px;
      opacity:0;
    }

    .hero-headline {
      font-family: var(--font-head);
      font-size: clamp(3.5rem, 10vw, 8.5rem);
      font-weight:700;
      line-height:.95;
      letter-spacing:-.02em;
      margin-bottom:32px;
    }

    .hero-headline .line { display:block; overflow:hidden; }
    .hero-headline .word { display:inline-block; }

    .orange-fill {
      color: var(--fox1);
      text-shadow: 0 0 60px rgba(246,133,27,.5);
    }

    .outline-text {
      -webkit-text-stroke: 1.5px var(--fox1);
      color: transparent;
    }

    .hero-sub {
      font-size:.9rem;
      color: var(--muted);
      max-width:500px;
      line-height:1.85;
      margin-bottom:44px;
      opacity:0;
    }

    .hero-actions { display:flex; gap:14px; flex-wrap:wrap; opacity:0; }
    .hero-visual {
      position:relative;
      display:flex;
      justify-content:center;
      opacity:0;
      transform:translateY(18px);
    }
    .portrait-shell {
      position:relative;
      width:min(100%, 430px);
      aspect-ratio:0.82;
    }
    .portrait-shell::before {
      content:'';
      position:absolute;
      inset:6% 8% 2%;
      border-radius:36px;
      background:
        radial-gradient(circle at 50% 24%, rgba(255,255,255,.12), transparent 38%),
        linear-gradient(180deg, rgba(255,209,102,.14), rgba(246,133,27,.03) 55%, rgba(10,8,0,0) 100%);
    }
    .portrait-shell::after {
      content:'';
      position:absolute;
      inset:0;
      border-radius:38px;
      border:1px solid rgba(255,209,102,.18);
      background:
        linear-gradient(135deg, rgba(255,209,102,.09), transparent 22%),
        linear-gradient(315deg, rgba(246,133,27,.08), transparent 28%);
      box-shadow: 0 20px 60px rgba(0,0,0,.34), 0 0 40px rgba(246,133,27,.14);
    }
    .portrait-frame {
      position:absolute;
      inset:20px;
      border-radius:30px;
      overflow:hidden;
      background:
        radial-gradient(circle at top, rgba(255,209,102,.14), transparent 28%),
        linear-gradient(180deg, rgba(24,18,6,.96), rgba(11,8,2,.86));
      border:1px solid rgba(255,140,0,.14);
      box-shadow: inset 0 1px 0 rgba(255,255,255,.08);
    }
    .portrait-ring {
      position:absolute;
      width:82%;
      height:82%;
      left:9%;
      top:6%;
      border-radius:32px;
      border:1px solid rgba(255,209,102,.08);
      z-index:1;
      pointer-events:none;
    }
    .portrait-img {
      width:100%;
      height:100%;
      object-fit:cover;
      object-position:center top;
      display:block;
      filter:contrast(1.03) saturate(.94) drop-shadow(0 18px 35px rgba(0,0,0,.28));
      -webkit-mask-image:radial-gradient(82% 86% at 50% 34%, #000 64%, transparent 100%);
      mask-image:radial-gradient(82% 86% at 50% 34%, #000 64%, transparent 100%);
    }
    .portrait-accent {
      position:absolute;
      inset:auto 22px 22px auto;
      padding:8px 12px;
      border-radius:999px;
      border:1px solid rgba(255,209,102,.18);
      background:rgba(10,8,0,.55);
      backdrop-filter:blur(10px);
      color:var(--fox3);
      font-size:.65rem;
      letter-spacing:.12em;
      text-transform:uppercase;
      z-index:2;
    }

    .btn {
      display:inline-flex; align-items:center; gap:8px;
      padding:13px 30px; border-radius:6px;
      font-family:var(--font-mono); font-size:.8rem;
      letter-spacing:.05em; text-decoration:none;
      cursor:pointer; border:none;
      transition: all .3s;
      position:relative; overflow:hidden;
    }
    .btn::before {
      content:''; position:absolute; inset:0;
      background:rgba(255,255,255,.07);
      transform:translateX(-100%);
      transition:transform .4s;
    }
    .btn:hover::before { transform:translateX(0); }

    .btn-fire {
      background: linear-gradient(135deg, var(--fox4), var(--fox1), var(--fox3));
      color:#000; font-weight:500;
      box-shadow: 0 4px 28px rgba(246,133,27,.4);
    }
    .btn-fire:hover {
      box-shadow: 0 8px 48px rgba(246,133,27,.7);
      transform:translateY(-2px);
    }

    .btn-outline {
      background:transparent;
      border:1px solid var(--border-hi);
      color:var(--white);
    }
    .btn-outline:hover {
      border-color: var(--fox1);
      box-shadow: 0 0 20px var(--glow);
      transform:translateY(-2px);
    }

    /* wallet address style badge */
    .hero-address {
      display:inline-flex; align-items:center; gap:10px;
      font-size:.72rem; color:var(--muted);
      border:1px solid var(--border);
      padding:8px 16px; border-radius:99px;
      background: rgba(246,133,27,.04);
      margin-top:28px; opacity:0;
      letter-spacing:.06em;
    }
    .hero-address .addr-dot {
      width:8px; height:8px; border-radius:50%;
      background:var(--fox1);
      box-shadow:0 0 8px var(--fox1);
    }

    .hero-scroll {
      position:absolute; bottom:40px; left:50%;
      transform:translateX(-50%);
      display:flex; flex-direction:column; align-items:center; gap:8px;
      color:var(--muted); font-size:.65rem; letter-spacing:.15em;
      text-transform:uppercase; opacity:0;
      animation: fadeUp 1s 2.2s forwards;
    }
    .scroll-bar {
      width:1px; height:48px;
      background:linear-gradient(to bottom, var(--fox1), transparent);
      animation:pulse 2s ease-in-out infinite;
    }
    @keyframes pulse { 0%,100%{opacity:.3} 50%{opacity:1} }
    @keyframes fadeUp { to{opacity:1; transform:translateX(-50%) translateY(0)} from{opacity:0;transform:translateX(-50%) translateY(10px)} }

    /* ══════════════════════════════════
       SECTION
    ══════════════════════════════════ */
    .section {
      padding:100px 48px;
      position:relative; z-index:1;
      max-width:980px; margin:0 auto;
    }

    .sec-label {
      font-size:.68rem; letter-spacing:.22em;
      text-transform:uppercase; color:var(--fox1);
      margin-bottom:14px;
      display:flex; align-items:center; gap:10px;
    }
    .sec-label::before {
      content:''; display:inline-block;
      width:18px; height:1px; background:var(--fox1);
    }

    .sec-title {
      font-family:var(--font-head);
      font-size:clamp(2.2rem, 5vw, 4rem);
      font-weight:700; letter-spacing:-.01em;
      line-height:1.05; margin-bottom:16px;
    }

    .sec-body {
      color:var(--muted); max-width:520px;
      line-height:1.85; margin-bottom:56px;
      font-size:.88rem;
    }

    /* divider */
    .divider { border:none; border-top:1px solid var(--border); }

    /* ══════════════════════════════════
       ABOUT
    ══════════════════════════════════ */
    #about { border-top:1px solid var(--border); }

    .about-grid {
      display:grid;
      grid-template-columns:1.2fr 0.8fr;
      gap:56px; align-items:start;
    }

    .about-text p {
      color:var(--muted); line-height:1.9;
      margin-bottom:18px; font-size:.88rem;
    }
    .about-text p strong { color:var(--fox3); font-weight:500; }

    /* chain stats */
    .chain-stats {
      background: rgba(246,133,27,.04);
      border:1px solid var(--border);
      border-radius:var(--rl);
      padding:28px;
      display:grid; grid-template-columns:1fr 1fr;
      gap:2px;
      overflow:hidden;
    }

    .chain-stat {
      padding:20px;
      position:relative;
      transition:background .3s;
    }
    .chain-stat:hover { background:rgba(246,133,27,.06); }
    .chain-stat:nth-child(1),.chain-stat:nth-child(2) { border-bottom:1px solid var(--border); }
    .chain-stat:nth-child(1),.chain-stat:nth-child(3) { border-right:1px solid var(--border); }

    .cstat-num {
      font-family:var(--font-head);
      font-size:2.4rem; font-weight:700;
      color:var(--fox1);
      text-shadow:0 0 30px var(--glow);
      line-height:1;
      margin-bottom:6px;
    }
    .cstat-label {
      font-size:.68rem; color:var(--muted);
      letter-spacing:.06em;
    }

    /* ══════════════════════════════════
       SKILLS
    ══════════════════════════════════ */
    #skills { border-top:1px solid var(--border); }

    .skills-list {
      display:grid;
      grid-template-columns:repeat(auto-fill, minmax(280px,1fr));
      gap:12px;
    }

    .skill-row {
      background:rgba(246,133,27,.03);
      border:1px solid var(--border);
      border-radius:var(--r);
      padding:20px 22px;
      opacity:0; transform:translateX(-20px);
      transition:border-color .3s, box-shadow .3s, transform .3s;
    }
    .skill-row:hover {
      border-color:var(--border-hi);
      box-shadow:0 0 24px rgba(246,133,27,.1);
      transform:translateX(0) translateY(-2px) !important;
    }

    .skill-top {
      display:flex; justify-content:space-between; align-items:center;
      margin-bottom:10px;
    }
    .skill-name {
      font-family:var(--font-head); font-size:1rem; font-weight:600;
      letter-spacing:.04em;
    }
    .skill-val {
      font-size:.7rem; color:var(--fox1); font-family:var(--font-mono);
    }
    .skill-bg {
      height:3px; background:rgba(255,255,255,.05);
      border-radius:99px; overflow:hidden;
    }
    .skill-fill {
      height:100%; width:0; border-radius:99px;
      background:linear-gradient(90deg, var(--fox4), var(--fox1), var(--fox3));
      box-shadow:0 0 8px rgba(246,133,27,.5);
      transition:width 1.3s cubic-bezier(.23,1,.32,1);
    }

    /* ══════════════════════════════════
       PROJECTS
    ══════════════════════════════════ */
    #projects { border-top:1px solid var(--border); }

    .projects-grid {
      display:grid;
      grid-template-columns:repeat(auto-fill,minmax(290px,1fr));
      gap:16px;
    }

    .p-card {
      background:rgba(246,133,27,.025);
      border:1px solid var(--border);
      border-radius:var(--rl);
      padding:30px;
      position:relative; overflow:hidden;
      cursor:pointer;
      opacity:0; transform:translateY(28px);
      transition:border-color .4s, box-shadow .4s, transform .4s;
      transform-style:preserve-3d;
    }

    .p-card::after {
      content:'';
      position:absolute; inset:0;
      background:radial-gradient(circle at var(--mx,50%) var(--my,50%), rgba(246,133,27,.1), transparent 65%);
      opacity:0; transition:opacity .4s; pointer-events:none;
    }

    .p-card:hover {
      border-color:rgba(246,133,27,.4);
      box-shadow:0 20px 60px rgba(246,133,27,.15);
    }
    .p-card:hover::after { opacity:1; }

    /* corner accent */
    .p-card::before {
      content:'';
      position:absolute; top:0; right:0;
      width:60px; height:60px;
      background:linear-gradient(225deg, rgba(246,133,27,.15), transparent 70%);
      border-bottom-left-radius:60px;
    }

    .p-icon {
      width:44px; height:44px; border-radius:10px;
      display:flex; align-items:center; justify-content:center;
      font-size:1.3rem; margin-bottom:20px;
      background:linear-gradient(135deg, rgba(246,133,27,.2), rgba(255,69,0,.2));
      border:1px solid rgba(246,133,27,.25);
    }

    .p-title {
      font-family:var(--font-head); font-size:1.15rem;
      font-weight:700; letter-spacing:.02em;
      margin-bottom:10px;
    }
    .p-desc {
      font-size:.8rem; color:var(--muted);
      line-height:1.75; margin-bottom:22px;
    }
    .p-tags { display:flex; flex-wrap:wrap; gap:7px; margin-bottom:22px; }
    .p-tag {
      font-size:.65rem; letter-spacing:.06em;
      padding:4px 11px; border-radius:4px;
      background:rgba(246,133,27,.08);
      border:1px solid rgba(246,133,27,.18);
      color:var(--fox3);
    }
    .p-link {
      display:inline-flex; align-items:center; gap:6px;
      font-size:.75rem; color:var(--fox1);
      text-decoration:none; letter-spacing:.04em;
      transition:gap .2s, color .2s;
    }
    .p-link:hover { gap:10px; color:var(--fox3); }

    /* ══════════════════════════════════
       CONTACT
    ══════════════════════════════════ */
    #contact { border-top:1px solid var(--border); text-align:center; }
    #contact .sec-label { justify-content:center; }
    #contact .sec-body { margin-left:auto; margin-right:auto; }

    .contact-cards {
      display:flex; justify-content:center;
      flex-wrap:wrap; gap:14px; margin-bottom:52px;
    }

    .cc {
      display:inline-flex; align-items:center; gap:12px;
      padding:14px 26px; border-radius:8px;
      background:rgba(246,133,27,.04);
      border:1px solid var(--border);
      color:var(--white); text-decoration:none;
      font-size:.8rem; transition:all .3s;
    }
    .cc:hover {
      border-color:var(--fox1);
      background:rgba(246,133,27,.1);
      box-shadow:0 0 28px var(--glow);
      transform:translateY(-3px);
    }
    .cc-icon {
      width:30px; height:30px; border-radius:6px;
      background:linear-gradient(135deg, var(--fox4), var(--fox1));
      display:flex; align-items:center; justify-content:center;
      font-size:.9rem;
    }

    /* ══════════════════════════════════
       FOOTER
    ══════════════════════════════════ */
    footer {
      border-top:1px solid var(--border);
      padding:26px 48px;
      display:flex; justify-content:space-between; align-items:center;
      position:relative; z-index:1;
      font-size:.72rem; color:var(--muted);
      flex-wrap:wrap; gap:12px;
    }
    footer .hl { color:var(--fox1); }

    /* ══════════════════════════════════
       GLOW ORBS
    ══════════════════════════════════ */
    .orb {
      position:absolute; border-radius:50%;
      filter:blur(90px); pointer-events:none; z-index:0;
    }

    /* ══════════════════════════════════
       NOISE
    ══════════════════════════════════ */
    body::after {
      content:''; position:fixed; inset:0; z-index:500;
      pointer-events:none; opacity:.028;
      background-image:url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)'/%3E%3C/svg%3E");
      background-size:200px;
    }

    /* ══════════════════════════════════
       RESPONSIVE
    ══════════════════════════════════ */
    @media(max-width:768px){
      nav { padding:14px 20px; }
      .nav-links { display:none; }
      .nav-links.open {
        display:flex; flex-direction:column;
        position:fixed; inset:0; top:58px;
        background:rgba(10,8,0,.97);
        backdrop-filter:blur(20px);
        padding:40px 24px; gap:26px; z-index:99;
      }
      .nav-links.open a { font-size:1.1rem; }
      .hamburger { display:flex; }

      #hero { padding:100px 20px 80px; }
      .hero-layout { grid-template-columns:1fr; gap:28px; }
      .hero-copy { order:2; }
      .hero-visual { order:1; }
      .portrait-shell { width:min(100%, 360px); margin:0 auto; }
      .portrait-frame { inset:14px; }
      .section { padding:70px 20px; }
      .about-grid { grid-template-columns:1fr; }
      footer { flex-direction:column; text-align:center; padding:22px 20px; }
    }
  </style>
</head>
<body>

<div id="progress"></div>
<div id="cur-dot"></div>
<div id="cur-ring"></div>
<canvas id="three-canvas"></canvas>

<!-- ══ NAV ══ -->
<nav id="navbar">
  <div class="nav-logo">
    <!-- MetaMask fox SVG simplified -->
    <svg class="fox-icon" viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">
      <polygon points="50,6 90,28 90,72 50,94 10,72 10,28" fill="none" stroke="#f6851b" stroke-width="4"/>
      <polygon points="50,22 72,34 72,58 50,70 28,58 28,34" fill="#f6851b" opacity=".15"/>
      <polygon points="50,34 62,41 62,55 50,62 38,55 38,41" fill="#f6851b" opacity=".4"/>
      <circle cx="50" cy="48" r="8" fill="#f6851b"/>
    </svg>
    anas.dev
  </div>

  <ul class="nav-links" id="nav-links">
    <li><a href="#about">About</a></li>
    <li><a href="#skills">Skills</a></li>
    <li><a href="#projects">Projects</a></li>
    <li><a href="#contact" class="nav-connect">
      <span class="connect-dot"></span>Connect
    </a></li>
  </ul>

  <div class="hamburger" id="hamburger" aria-label="Toggle menu" role="button" tabindex="0">
    <span></span><span></span><span></span>
  </div>
</nav>

<!-- ══ HERO ══ -->
<section id="hero">
  <div class="orb" style="width:600px;height:600px;background:rgba(246,133,27,.12);top:-150px;right:-80px;"></div>
  <div class="orb" style="width:400px;height:400px;background:rgba(255,69,0,.08);bottom:0;left:-100px;"></div>

  <div class="hero-inner">
    <div class="hero-layout">
      <div class="hero-copy">
    <div class="hero-tag">
      <span class="connect-dot"></span>
      Available for work &nbsp;·&nbsp; Web Dev + Python
    </div>

    <h1 class="hero-headline">
      <span class="line"><span class="word">Hello,</span></span>
      <span class="line"><span class="word orange-fill">I'm Anas.</span></span>
      <span class="line"><span class="word outline-text">Building</span></span>
      <span class="line"><span class="word">the web.</span></span>
    </h1>

    <p class="hero-sub">
     AI-focused Computer Applications student with experience in full-stack development, IoT systems, and cloud-based data solutions, passionate about building web applications, scalable software systems, and data-driven solutions using Python, Java, and JavaScript.
    </p>

    <div class="hero-actions">
      <a href="#projects" class="btn btn-fire">
        <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><path d="M5 12h14M12 5l7 7-7 7"/></svg>
        View Projects
      </a>
      <a href="#contact" class="btn btn-outline">Get In Touch</a>
    </div>

    <div class="hero-address">
      <span class="addr-dot"></span>
      anas.dev &nbsp;·&nbsp; Kerala, India
    </div>
      </div>

      <div class="hero-visual">
        <div class="portrait-shell">
          <div class="portrait-frame">
            <div class="portrait-ring"></div>
            <img src="./new anas.jpeg" alt="Portrait of Anas" class="portrait-img">
          </div>
        </div>
      </div>
    </div>
  </div>

  <div class="hero-scroll">
    <div class="scroll-bar"></div>
    Scroll
  </div>
</section>

<!-- ══ ABOUT ══ -->
<div class="section" id="about">
  <div class="sec-label">01 — About</div>
  <h2 class="sec-title">Who's behind<br>the screen.</h2>

  <div class="about-grid">
    <div class="about-text reveal">
      <p>I'm <strong>Anas</strong>, a motivated beginner software developer who loves turning ideas into working products. I believe great code should be both functional and elegant.</p>
      <p>Currently deepening my skills in <strong>Python, JavaScript,</strong> and modern web development patterns. Every project is a chance to learn something new.</p>
      <p>I'm drawn to building tools that actually help people — clean interfaces, thoughtful interactions, and code that future-me won't regret reading.</p>
    </div>

    <div class="chain-stats reveal">
      <div class="chain-stat">
        <div class="cstat-num" data-count="5">0</div>
        <div class="cstat-label">Tech stack depth</div>
      </div>
      <div class="chain-stat">
        <div class="cstat-num" data-count="6">0</div>
        <div class="cstat-label">Projects shipped</div>
      </div>
      <div class="chain-stat">
        <div class="cstat-num" style="color:var(--fox3)">∞</div>
        <div class="cstat-label">Still to learn</div>
      </div>
      <div class="chain-stat">
        <div class="cstat-num" style="color:var(--fox3)">100%</div>
        <div class="cstat-label">Passion level</div>
      </div>
    </div>
  </div>
</div>

<!-- ══ SKILLS ══ -->
<div class="section" id="skills">
  <div class="sec-label">02 — Skills</div>
  <h2 class="sec-title">My toolkit.</h2>
  <p class="sec-body">Languages and tools I reach for to build things that work.</p>

  <div class="skills-list">
    <div class="skill-row" data-w="85">
      <div class="skill-top"><span class="skill-name">HTML</span><span class="skill-val">85%</span></div>
      <div class="skill-bg"><div class="skill-fill" data-width="85"></div></div>
    </div>
    <div class="skill-row" data-w="78">
      <div class="skill-top"><span class="skill-name">CSS</span><span class="skill-val">78%</span></div>
      <div class="skill-bg"><div class="skill-fill" data-width="78"></div></div>
    </div>
    <div class="skill-row" data-w="72">
      <div class="skill-top"><span class="skill-name">Python</span><span class="skill-val">72%</span></div>
      <div class="skill-bg"><div class="skill-fill" data-width="72"></div></div>
    </div>
    <div class="skill-row" data-w="65">
      <div class="skill-top"><span class="skill-name">JavaScript</span><span class="skill-val">65%</span></div>
      <div class="skill-bg"><div class="skill-fill" data-width="65"></div></div>
    </div>
    <div class="skill-row" data-w="60">
      <div class="skill-top"><span class="skill-name">Git & GitHub</span><span class="skill-val">60%</span></div>
      <div class="skill-bg"><div class="skill-fill" data-width="60"></div></div>
    </div>
    <div class="skill-row" data-w="35">
      <div class="skill-top"><span class="skill-name">React (Learning)</span><span class="skill-val">35%</span></div>
      <div class="skill-bg"><div class="skill-fill" data-width="35"></div></div>
    </div>
  </div>
</div>

<!-- ══ PROJECTS ══ -->
<div class="section" id="projects">
  <div class="sec-label">03 — Projects</div>
  <h2 class="sec-title">What I've built.</h2>
  <p class="sec-body">Personal projects crafted while learning, growing, and shipping.</p>

  <div class="projects-grid">
    <div class="p-card">
      <div class="p-icon">BMS</div>
      <div class="p-title">Billing Management System</div>
      <div class="p-desc">A responsive billing app for generating itemized invoices, managing temporary bills, tracking customer dues, and printing professional receipts for small businesses.</div>
      <div class="p-tags">
        <span class="p-tag">HTML</span><span class="p-tag">CSS</span><span class="p-tag">JavaScript</span><span class="p-tag">Bootstrap</span><span class="p-tag">Local Storage</span>
      </div>
      <a href="../javascript/js7.html" class="p-link">
        Open Project
        <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M7 17L17 7M17 7H7M17 7v10"/></svg>
      </a>
    </div>

    <div class="p-card">
      <div class="p-icon">MM</div>
      <div class="p-title">Malabar Magic Restaurant Website</div>
      <div class="p-desc">A modern restaurant website with an engaging hero section, menu showcase, animated UI, and a shopping cart flow for simulating food orders.</div>
      <div class="p-tags">
        <span class="p-tag">HTML</span><span class="p-tag">CSS</span><span class="p-tag">JavaScript</span><span class="p-tag">Google Fonts</span><span class="p-tag">Font Awesome</span>
      </div>
      <a href="../Malabar Kitchen/malabar kitchen org.html" class="p-link">
        Open Project
        <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M7 17L17 7M17 7H7M17 7v10"/></svg>
      </a>
    </div>

    <div class="p-card">
      <div class="p-icon">PIC</div>
      <div class="p-title">Pan Indian Catering Service Website</div>
      <div class="p-desc">A modern catering business website with service sections, menu categories, booking flows, theme toggle, interactive cards, image galleries, and promotional packages for real-world event catering.</div>
      <div class="p-tags">
        <span class="p-tag">HTML</span><span class="p-tag">CSS</span><span class="p-tag">JavaScript</span><span class="p-tag">Font Awesome</span><span class="p-tag">Google Fonts</span>
      </div>
      <a href="../Pan Indian Catering/catering.html" class="p-link">
        Open Project
        <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M7 17L17 7M17 7H7M17 7v10"/></svg>
      </a>
    </div>

    <div class="p-card">
      <div class="p-icon">PF</div>
      <div class="p-title">Personal Developer Portfolio Website</div>
      <div class="p-desc">A portfolio experience with animated sections, interactive project cards, smooth scrolling, and modern front-end presentation for showcasing skills and work.</div>
      <div class="p-tags">
        <span class="p-tag">HTML</span><span class="p-tag">CSS</span><span class="p-tag">JavaScript</span><span class="p-tag">GSAP</span><span class="p-tag">Three.js</span>
      </div>
      <a href="./new meta portfolio.html" class="p-link">
        View Portfolio
        <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M7 17L17 7M17 7H7M17 7v10"/></svg>
      </a>
    </div>

    <div class="p-card">
      <div class="p-icon">IOT</div>
      <div class="p-title">IoT Smart Door Lock System</div>
      <div class="p-desc">A Bluetooth-controlled smart door lock system combining IoT hardware, backend logic, and database support for secure real-time access control.</div>
      <div class="p-tags">
        <span class="p-tag">IoT</span><span class="p-tag">Bluetooth</span><span class="p-tag">Java</span><span class="p-tag">Database</span>
      </div>
      <a href="./ANAS NEW Resume.pdf" class="p-link">
        View Details
        <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M7 17L17 7M17 7H7M17 7v10"/></svg>
      </a>
    </div>

    <div class="p-card">
      <div class="p-icon">PY</div>
      <div class="p-title">AI Expense Tracker Dashboard</div>
      <div class="p-desc">A Python-based finance tracker that analyzes daily spending, categorizes expenses, visualizes trends, and generates smart monthly summaries to help users manage budgets more effectively.</div>
      <div class="p-tags">
        <span class="p-tag">Python</span><span class="p-tag">Pandas</span><span class="p-tag">Matplotlib</span><span class="p-tag">Data Analysis</span>
      </div>
      <a href="./ANAS NEW Resume.pdf" class="p-link">
        View Details
        <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><path d="M7 17L17 7M17 7H7M17 7v10"/></svg>
      </a>
    </div>
  </div>
</div>

<!-- ══ CONTACT ══ -->
<div class="section" id="contact">
  <div class="sec-label">04 — Contact</div>
  <h2 class="sec-title reveal">Let's connect.</h2>
  <p class="sec-body reveal">Open to projects, collabs, or just a good conversation about code.</p>

  <div class="contact-cards reveal">
    <a href="mailto:anasansi525@gmail.com" class="cc">
      <span class="cc-icon">✉️</span>
      anasansi525@gmail.com
    </a>
    <a href="https://github.com/anasansi525-dotcom" target="_blank" rel="noopener" class="cc">
      <span class="cc-icon">🐙</span>
      github.com/anasansi525-dotcom
    </a>
  </div>

  <a href="mailto:anasansi525@gmail.com" class="btn btn-fire reveal" style="font-size:.85rem;padding:15px 38px;">
    Send Message
    <svg width="13" height="13" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><path d="M5 12h14M12 5l7 7-7 7"/></svg>
  </a>
</div>

<!-- ══ FOOTER ══ -->
<footer>
  <div>Built by <span class="hl">Anas</span> · <span id="year"></span></div>
  <div>Fueled by <span class="hl">curiosity</span> &amp; caffeine ☕</div>
</footer>

<!-- ══════════════════════════════════
     SCRIPTS
══════════════════════════════════ -->
<script>
document.getElementById('year').textContent = new Date().getFullYear();

// ── Cursor ─────────────────────────
const dot = document.getElementById('cur-dot');
const ring = document.getElementById('cur-ring');
let mx=0,my=0,rx=0,ry=0;
document.addEventListener('mousemove', e=>{ mx=e.clientX; my=e.clientY; });
(function loop(){
  rx += (mx-rx)*.13; ry += (my-ry)*.13;
  dot.style.left=mx+'px'; dot.style.top=my+'px';
  ring.style.left=rx+'px'; ring.style.top=ry+'px';
  requestAnimationFrame(loop);
})();

// ── Scroll progress ─────────────────
window.addEventListener('scroll',()=>{
  document.getElementById('progress').style.width =
    (window.scrollY/(document.body.scrollHeight-window.innerHeight)*100)+'%';
});

// ── Nav shrink ──────────────────────
window.addEventListener('scroll',()=>{
  document.getElementById('navbar').classList.toggle('scrolled', window.scrollY>60);
});

// ── Hamburger ───────────────────────
const hbg = document.getElementById('hamburger');
const nl  = document.getElementById('nav-links');
hbg.addEventListener('click',()=>nl.classList.toggle('open'));
hbg.addEventListener('keydown',e=>e.key==='Enter'&&nl.classList.toggle('open'));
nl.querySelectorAll('a').forEach(a=>a.addEventListener('click',()=>nl.classList.remove('open')));

// ── Three.js: fire particle field ───
(function(){
  const canvas   = document.getElementById('three-canvas');
  const renderer = new THREE.WebGLRenderer({canvas,alpha:true,antialias:true});
  renderer.setPixelRatio(Math.min(devicePixelRatio,2));
  renderer.setSize(innerWidth,innerHeight);

  const scene  = new THREE.Scene();
  const camera = new THREE.PerspectiveCamera(65, innerWidth/innerHeight, 0.1, 100);
  camera.position.z = 5;

  // Orange/amber particles
  const N = 2200;
  const geo = new THREE.BufferGeometry();
  const pos = new Float32Array(N*3);
  const col = new Float32Array(N*3);
  const colors = [
    [1.0, 0.53, 0.11],  // orange
    [0.89, 0.46, 0.11], // deep orange
    [1.0, 0.82, 0.4],   // amber
    [1.0, 0.27, 0.0],   // red-orange
  ];
  for(let i=0;i<N;i++){
    pos[i*3]   = (Math.random()-.5)*22;
    pos[i*3+1] = (Math.random()-.5)*22;
    pos[i*3+2] = (Math.random()-.5)*10;
    const c = colors[Math.floor(Math.random()*colors.length)];
    col[i*3]=c[0]; col[i*3+1]=c[1]; col[i*3+2]=c[2];
  }
  geo.setAttribute('position',new THREE.BufferAttribute(pos,3));
  geo.setAttribute('color',   new THREE.BufferAttribute(col,3));

  const mat = new THREE.PointsMaterial({
    size:.035, vertexColors:true,
    transparent:true, opacity:.75
  });
  const pts = new THREE.Points(geo,mat);
  scene.add(pts);

  // Hex wireframe rings
  for(let i=0;i<3;i++){
    const rg = new THREE.TorusGeometry(1.5+i*.8, 0.005, 6, 6);
    const rm = new THREE.MeshBasicMaterial({
      color:0xf6851b, transparent:true, opacity:0.04+i*.01
    });
    const torus = new THREE.Mesh(rg,rm);
    torus.rotation.x = Math.PI/2 + i*0.3;
    torus.position.set(3,0,-1);
    scene.add(torus);
  }

  let mx2=0,my2=0;
  document.addEventListener('mousemove',e=>{
    mx2=(e.clientX/innerWidth -.5)*.6;
    my2=(e.clientY/innerHeight-.5)*.6;
  });
  window.addEventListener('resize',()=>{
    renderer.setSize(innerWidth,innerHeight);
    camera.aspect=innerWidth/innerHeight;
    camera.updateProjectionMatrix();
  });
  (function animate(){
    requestAnimationFrame(animate);
    pts.rotation.y+=.0006;
    pts.rotation.x+=.0002;
    camera.position.x+=(mx2-camera.position.x)*.025;
    camera.position.y+=(-my2-camera.position.y)*.025;
    renderer.render(scene,camera);
  })();
})();

// ── GSAP ────────────────────────────
gsap.registerPlugin(ScrollTrigger);

const tl = gsap.timeline({defaults:{ease:'power3.out'}});
tl.to('.hero-tag',    {opacity:1, y:0, duration:.9, delay:.4})
  .from('.word',      {y:'110%', duration:1.1, stagger:.09, ease:'power4.out'}, '-=.5')
  .to('.hero-sub',    {opacity:1, y:0, duration:.8}, '-=.4')
  .to('.hero-visual', {opacity:1, y:0, duration:.9}, '-=.55')
  .to('.hero-actions',{opacity:1, y:0, duration:.7}, '-=.35')
  .to('.hero-address',{opacity:1, y:0, duration:.6}, '-=.2');

// Reveal
gsap.utils.toArray('.reveal').forEach(el=>{
  gsap.from(el,{
    scrollTrigger:{trigger:el,start:'top 86%'},
    opacity:0,y:40,duration:.9,ease:'power3.out'
  });
});

// Section titles
gsap.utils.toArray('.sec-title').forEach(el=>{
  gsap.from(el,{
    scrollTrigger:{trigger:el,start:'top 88%'},
    opacity:0,y:50,duration:1,ease:'power4.out'
  });
});

// Skill rows
gsap.utils.toArray('.skill-row').forEach((el,i)=>{
  gsap.to(el,{
    scrollTrigger:{trigger:el,start:'top 92%'},
    opacity:1,x:0,duration:.6,delay:i*.06,ease:'power3.out'
  });
});

// Skill fills
gsap.utils.toArray('.skill-fill').forEach(bar=>{
  ScrollTrigger.create({
    trigger:bar, start:'top 93%',
    onEnter:()=>{ bar.style.width=bar.dataset.width+'%'; }
  });
});

// Project cards
gsap.utils.toArray('.p-card').forEach((el,i)=>{
  gsap.to(el,{
    scrollTrigger:{trigger:el,start:'top 88%'},
    opacity:1,y:0,duration:.7,delay:i*.1,ease:'power3.out'
  });
});

// Counter
gsap.utils.toArray('[data-count]').forEach(el=>{
  const target=+el.dataset.count;
  ScrollTrigger.create({
    trigger:el, start:'top 85%',
    onEnter:()=>{
      let n=0, step=target/40;
      const t=setInterval(()=>{
        n=Math.min(n+step,target);
        el.textContent=Math.round(n);
        if(n>=target)clearInterval(t);
      },28);
    }
  });
});

// 3D card tilt
document.querySelectorAll('.p-card').forEach(card=>{
  card.addEventListener('mousemove',e=>{
    const r=card.getBoundingClientRect();
    const x=((e.clientX-r.left)/r.width -.5)*18;
    const y=((e.clientY-r.top) /r.height-.5)*18;
    const mx=((e.clientX-r.left)/r.width *100);
    const my=((e.clientY-r.top) /r.height*100);
    card.style.transform=`translateY(-5px) rotateY(${x}deg) rotateX(${-y}deg)`;
    card.style.setProperty('--mx',mx+'%');
    card.style.setProperty('--my',my+'%');
  });
  card.addEventListener('mouseleave',()=>{ card.style.transform=''; });
});

// Parallax orbs
document.addEventListener('mousemove',e=>{
  document.querySelectorAll('.orb').forEach((o,i)=>{
    const d=.015+i*.012;
    o.style.transform=`translate(${(e.clientX-innerWidth/2)*d}px,${(e.clientY-innerHeight/2)*d}px)`;
  });
});
</script>
</body>
</html>
