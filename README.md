<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <meta name="apple-mobile-web-app-capable" content="yes">
  <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
  <title>Heartbeat Sync | PlayAh</title>
  <style>
    :root {
      --primary:#c2d2f4; --secondary:#e8dbe7; --accent:#ef9449;
      --indigo:#5a78c0; --dark:#2c2c4a; --fire:#ef9449; --water:#7ba8d4;
    }
    *,*::before,*::after{margin:0;padding:0;box-sizing:border-box;-webkit-tap-highlight-color:transparent;-webkit-touch-callout:none;user-select:none}
    html,body{width:100%;height:100%;overflow:hidden}
    body{font-family:-apple-system,BlinkMacSystemFont,'Helvetica Neue',Arial,sans-serif;color:var(--dark)}
    .bg{position:fixed;inset:0;z-index:-2;background:linear-gradient(140deg,#c2d2f4 0%,#e8dbe7 42%,#d5caf0 70%,#c2d2f4 100%);background-size:300% 300%;animation:bgDrift 10s ease infinite}
    @keyframes bgDrift{0%,100%{background-position:0% 50%}50%{background-position:100% 50%}}
    .orb{position:fixed;border-radius:50%;filter:blur(60px);pointer-events:none;z-index:-1;opacity:.45}
    .orb-1{width:220px;height:220px;background:rgba(239,148,73,.28);top:-60px;left:-50px;animation:orbFloat 9s ease-in-out infinite}
    .orb-2{width:180px;height:180px;background:rgba(123,168,212,.32);bottom:-40px;right:-30px;animation:orbFloat 12s ease-in-out infinite reverse}
    .orb-3{width:140px;height:140px;background:rgba(200,180,240,.35);top:40%;left:60%;animation:orbFloat 7s ease-in-out infinite 2s}
    @keyframes orbFloat{0%,100%{transform:translate(0,0) scale(1)}50%{transform:translate(20px,-20px) scale(1.08)}}
    .page{position:fixed;inset:0;display:flex;flex-direction:column;align-items:center;justify-content:center;padding:48px 24px 36px;opacity:0;pointer-events:none;transition:opacity .55s ease,transform .55s cubic-bezier(.22,1,.36,1);transform:translateY(18px);overflow-y:auto;-webkit-overflow-scrolling:touch}
    .page.active{opacity:1;pointer-events:all;transform:translateY(0)}
    .page.leaving{opacity:0;transform:translateY(-14px);pointer-events:none}
    .brand{font-size:10px;font-weight:700;letter-spacing:5px;text-transform:uppercase;color:var(--indigo);opacity:.6;margin-bottom:10px}
    h1{font-size:30px;font-weight:200;line-height:1.22;text-align:center}
    h2{font-size:22px;font-weight:300;line-height:1.3;text-align:center}
    .sub{font-size:14px;line-height:1.75;text-align:center;color:#6868a0;max-width:300px}
    .btn{display:inline-block;background:linear-gradient(135deg,var(--accent) 0%,#f5a862 100%);color:#fff;border:none;border-radius:50px;padding:15px 44px;font-size:15px;font-weight:500;letter-spacing:.5px;cursor:pointer;margin-top:22px;box-shadow:0 6px 22px rgba(239,148,73,.38);transition:transform .15s,box-shadow .15s;-webkit-appearance:none}
    .btn:active{transform:scale(.95);box-shadow:0 3px 10px rgba(239,148,73,.28)}
    .glass{background:rgba(255,255,255,.52);backdrop-filter:blur(24px) saturate(160%);-webkit-backdrop-filter:blur(24px) saturate(160%);border:1px solid rgba(255,255,255,.78);border-radius:24px;box-shadow:0 8px 32px rgba(90,120,192,.1)}
    /* PAGE 0 */
    .icon-pair{display:flex;align-items:center;gap:24px;margin:26px 0 18px}
    .ip-col{display:flex;flex-direction:column;align-items:center;gap:8px}
    .ip-tag{font-size:10px;letter-spacing:3px;text-transform:uppercase;color:#9898b8}
    .fire-emoji{font-size:58px;animation:fireFloat 3s ease-in-out infinite;filter:drop-shadow(0 4px 14px rgba(239,148,73,.4))}
    .water-emoji{font-size:58px;animation:waterFloat 3s ease-in-out infinite 1.5s;filter:drop-shadow(0 4px 14px rgba(123,168,212,.4))}
    @keyframes fireFloat{0%,100%{transform:translateY(0) rotate(-4deg)}50%{transform:translateY(-10px) rotate(4deg)}}
    @keyframes waterFloat{0%,100%{transform:translateY(0) rotate(4deg)}50%{transform:translateY(-10px) rotate(-4deg)}}
    .ip-divider{width:1px;height:56px;background:linear-gradient(to bottom,transparent,rgba(150,150,210,.4),transparent)}
    /* PAGE 1 */
    #page-hands{padding:0}
    .hands-bg{position:fixed;inset:0;display:flex}
    .touch-half{flex:1;display:flex;flex-direction:column;align-items:center;justify-content:center;gap:14px;position:relative;overflow:hidden;transition:background .35s ease}
    .touch-half.fire-side{background:linear-gradient(160deg,rgba(239,148,73,.10),rgba(230,110,40,.04))}
    .touch-half.water-side{background:linear-gradient(200deg,rgba(123,168,212,.15),rgba(194,210,244,.06))}
    .touch-half.fire-side.pressed{background:linear-gradient(160deg,rgba(239,148,73,.32),rgba(230,110,40,.18))}
    .touch-half.water-side.pressed{background:linear-gradient(200deg,rgba(123,168,212,.42),rgba(194,210,244,.28))}
    .zone-big-icon{font-size:82px;filter:drop-shadow(0 6px 20px rgba(0,0,0,.12));transition:transform .3s ease}
    .touch-half.pressed .zone-big-icon{transform:scale(1.15)}
    .zone-lbl{font-size:11px;font-weight:700;letter-spacing:4px;text-transform:uppercase;color:rgba(44,44,74,.48)}
    .zone-hint{font-size:13px;color:rgba(44,44,74,.32)}
    .pulse-ring{position:absolute;width:130px;height:130px;border-radius:50%;border:2px solid transparent;opacity:0;animation:pulseRingAnim 1.6s ease-out infinite}
    .fire-side .pulse-ring{border-color:rgba(239,148,73,.6)}
    .water-side .pulse-ring{border-color:rgba(123,168,212,.7)}
    .touch-half.pressed .pulse-ring{opacity:1}
    @keyframes pulseRingAnim{0%{transform:scale(.7);opacity:.8}100%{transform:scale(2.4);opacity:0}}
    .zone-vline{position:fixed;left:50%;top:0;bottom:0;width:1px;background:linear-gradient(to bottom,transparent,rgba(150,150,210,.22),transparent);pointer-events:none;z-index:6}
    .hands-top{position:fixed;top:0;left:0;right:0;padding:52px 20px 14px;text-align:center;pointer-events:none;z-index:10}
    .hands-top .brand{display:block}
    .hands-top h2{font-size:16px;font-weight:400;color:rgba(44,44,74,.68)}
    #cd-overlay{position:fixed;top:50%;left:50%;transform:translate(-50%,-50%);z-index:20;display:none}
    .cd-svg{width:108px;height:108px}
    .cd-bg{fill:rgba(255,255,255,.88);filter:drop-shadow(0 8px 28px rgba(0,0,0,.12))}
    .cd-track{fill:none;stroke:rgba(194,210,244,.4);stroke-width:5}
    .cd-ring{fill:none;stroke:url(#cdGrad);stroke-width:5;stroke-linecap:round;transform-origin:50% 50%;transform:rotate(-90deg);stroke-dasharray:263.9;stroke-dashoffset:0;transition:stroke-dashoffset 1s linear}
    .cd-num{dominant-baseline:middle;text-anchor:middle;font-size:36px;font-family:-apple-system,sans-serif;font-weight:100;fill:var(--dark)}
    /* PAGE 2 */
    .scan-card{padding:32px 28px;text-align:center;width:100%;max-width:340px}
    .scan-row{display:flex;align-items:center;justify-content:center;gap:18px;margin:16px 0}
    .scan-icon{font-size:38px}
    .dot-trio{display:flex;gap:7px;align-items:center}
    .sdot{width:7px;height:7px;border-radius:50%;background:var(--indigo);opacity:.3;animation:sdotAnim 1.4s ease-in-out infinite}
    .sdot:nth-child(2){animation-delay:.25s}
    .sdot:nth-child(3){animation-delay:.5s}
    @keyframes sdotAnim{0%,80%,100%{transform:scale(.5);opacity:.3}40%{transform:scale(1.1);opacity:1}}
    .ekg-wrap{width:100%;height:64px;overflow:hidden;margin:22px 0 6px}
    .ekg-svg{width:200%;height:100%;animation:ekgScroll 2.2s linear infinite}
    @keyframes ekgScroll{from{transform:translateX(0)}to{transform:translateX(-50%)}}
    /* PAGE 3 */
    .sync-row{display:flex;align-items:center;gap:14px;margin:12px 0}
    .sync-ico{font-size:42px}
    .sync-heart{font-size:28px;animation:heartPulse .85s ease-in-out infinite;filter:drop-shadow(0 0 6px rgba(239,100,100,.5))}
    @keyframes heartPulse{0%,100%{transform:scale(1)}50%{transform:scale(1.38)}}
    .ring-wrap{position:relative;width:200px;height:200px;margin:6px 0}
    .ring-svg{width:200px;height:200px}
    .ring-track{fill:none;stroke:rgba(194,210,244,.35);stroke-width:14}
    .ring-fill{fill:none;stroke:url(#ringGrad);stroke-width:14;stroke-linecap:round;transform-origin:50% 50%;transform:rotate(-90deg);stroke-dasharray:534.07;stroke-dashoffset:534.07;transition:stroke-dashoffset 2.4s cubic-bezier(.22,1,.36,1)}
    .ring-inner{position:absolute;inset:0;display:flex;flex-direction:column;align-items:center;justify-content:center}
    .pct-num{font-size:54px;font-weight:100;color:var(--dark);letter-spacing:-3px;line-height:1}
    .pct-lbl{font-size:10px;letter-spacing:3px;text-transform:uppercase;color:#9898b8;margin-top:3px}
    .result-quote{background:rgba(255,255,255,.6);border:1px solid rgba(255,255,255,.9);border-radius:18px;padding:16px 20px;margin:14px 0;max-width:310px;font-size:14px;font-style:italic;color:var(--dark);text-align:center;line-height:1.75}
    .mist{position:fixed;border-radius:50%;pointer-events:none;background:radial-gradient(circle,rgba(194,210,244,.65),transparent);animation:mistAnim var(--dur,2s) ease-out forwards}
    @keyframes mistAnim{0%{transform:translate(0,0) scale(0);opacity:.85}100%{transform:translate(var(--dx,0),var(--dy,0)) scale(3.5);opacity:0}}
    /* PAGE 4 */
    .anniv-card{padding:24px 22px;width:100%;max-width:350px;margin:18px 0 4px}
    .flabel{display:block;font-size:10px;font-weight:700;letter-spacing:3px;text-transform:uppercase;color:#9898b8;margin-bottom:10px}
    .date-input{width:100%;padding:14px 16px;border:1.5px solid rgba(194,210,244,.7);border-radius:14px;background:rgba(255,255,255,.7);font-size:18px;color:var(--dark);outline:none;-webkit-appearance:none;appearance:none;font-family:inherit;transition:border-color .2s,background .2s}
    .date-input:focus{border-color:var(--indigo);background:rgba(255,255,255,.92)}
    .married-row{display:flex;align-items:center;gap:12px;padding:14px 16px;background:rgba(255,255,255,.46);border-radius:14px;border:1.5px solid rgba(232,219,231,.7);margin-top:12px;cursor:pointer}
    .married-row input[type="checkbox"]{width:22px;height:22px;accent-color:var(--indigo);cursor:pointer;flex-shrink:0}
    .married-row span{font-size:15px;color:var(--dark);cursor:pointer}
    .err-msg{color:#d94f4f;font-size:13px;margin-top:8px;text-align:center;display:none}
    /* PAGE 5 */
    .days-wrap{text-align:center;margin:6px 0 2px}
    .days-num{font-size:72px;font-weight:100;color:var(--dark);letter-spacing:-4px;line-height:1}
    .days-lbl{font-size:11px;letter-spacing:4px;text-transform:uppercase;color:#9898b8;margin-top:6px}
    .promo-card{background:linear-gradient(145deg,rgba(239,148,73,.12),rgba(194,210,244,.18));border:1.5px solid rgba(239,148,73,.35);border-radius:22px;padding:26px 22px;text-align:center;width:100%;max-width:340px;margin:18px 0 8px}
    .promo-big-icon{font-size:52px;margin-bottom:10px}
    .promo-program{font-size:9px;font-weight:800;letter-spacing:4px;text-transform:uppercase;color:var(--indigo);opacity:.55;margin-bottom:5px}
    .promo-tier-lbl{font-size:11px;font-weight:700;letter-spacing:2px;text-transform:uppercase;color:var(--accent);margin-bottom:10px}
    .promo-reward-txt{font-size:26px;font-weight:200;color:var(--dark);line-height:1.3;margin-bottom:12px}
    .promo-detail-txt{font-size:13px;color:#7878a0;line-height:1.65}
    .booth-note{font-size:11px;color:#aaaacc;text-align:center;max-width:280px;line-height:1.65;margin-top:4px}
  </style>
</head>
<body>
  <div class="bg"></div>
  <div class="orb orb-1"></div>
  <div class="orb orb-2"></div>
  <div class="orb orb-3"></div>

  <!-- PAGE 0 — WELCOME -->
  <div class="page active" id="page-welcome">
    <div class="brand">PlayAh</div>
    <h1>The Heartbeat<br>Sync</h1>
    <div class="icon-pair">
      <div class="ip-col"><div class="fire-emoji">🔥</div><span class="ip-tag">Fire</span></div>
      <div class="ip-divider"></div>
      <div class="ip-col"><div class="water-emoji">💧</div><span class="ip-tag">Water</span></div>
    </div>
    <p class="sub">A journey to discover the rhythm between two souls — where fire meets water, something extraordinary flows.</p>
    <button class="btn" onclick="showPage('page-hands')">Begin Together</button>
  </div>

  <!-- PAGE 1 — HANDS -->
  <div class="page" id="page-hands">
    <div class="hands-top">
      <span class="brand">PlayAh</span>
      <h2>Both place your hand &amp; hold for 7 seconds</h2>
    </div>
    <div class="hands-bg" id="hands-layout">
      <div class="touch-half fire-side" id="fire-side">
        <div class="pulse-ring"></div>
        <div class="zone-big-icon">🔥</div>
        <div class="zone-lbl">Fire</div>
        <div class="zone-hint">Hold here</div>
      </div>
      <div class="touch-half water-side" id="water-side">
        <div class="pulse-ring"></div>
        <div class="zone-big-icon">💧</div>
        <div class="zone-lbl">Water</div>
        <div class="zone-hint">Hold here</div>
      </div>
    </div>
    <div class="zone-vline"></div>
    <div id="cd-overlay">
      <svg class="cd-svg" viewBox="0 0 108 108">
        <defs>
          <linearGradient id="cdGrad" x1="0%" y1="0%" x2="100%" y2="100%">
            <stop offset="0%" stop-color="#ef9449"/><stop offset="100%" stop-color="#7ba8d4"/>
          </linearGradient>
        </defs>
        <circle class="cd-bg" cx="54" cy="54" r="50"/>
        <circle class="cd-track" cx="54" cy="54" r="42"/>
        <circle class="cd-ring" id="cd-ring" cx="54" cy="54" r="42"/>
        <text class="cd-num" id="cd-num" x="54" y="54">7</text>
      </svg>
    </div>
  </div>

  <!-- PAGE 2 — SCANNING -->
  <div class="page" id="page-scanning">
    <div class="glass scan-card">
      <div class="brand" style="text-align:center">PlayAh</div>
      <div class="scan-row">
        <span class="scan-icon">🔥</span>
        <div class="dot-trio"><div class="sdot"></div><div class="sdot"></div><div class="sdot"></div></div>
        <span class="scan-icon">💧</span>
      </div>
      <h2 style="font-size:18px;font-weight:400;margin-bottom:4px">Measuring your connection…</h2>
      <p class="sub" style="font-size:13px;color:#9898b8">Analysing heartbeat synchronisation</p>
      <div class="ekg-wrap">
        <svg class="ekg-svg" viewBox="0 0 720 64" preserveAspectRatio="none">
          <defs>
            <linearGradient id="ekgGrad" x1="0%" y1="0%" x2="100%" y2="0%">
              <stop offset="0%" stop-color="#ef9449" stop-opacity=".6"/>
              <stop offset="35%" stop-color="#9a78d4" stop-opacity="1"/>
              <stop offset="65%" stop-color="#7ba8d4" stop-opacity="1"/>
              <stop offset="100%" stop-color="#ef9449" stop-opacity=".6"/>
            </linearGradient>
          </defs>
          <path d="M0,32 L55,32 L62,26 L68,32 L84,32 L90,40 L96,3 L103,62 L110,20 L116,32 L130,26 L140,32 L360,32 L415,32 L422,26 L428,32 L444,32 L450,40 L456,3 L463,62 L470,20 L476,32 L490,26 L500,32 L720,32" fill="none" stroke="url(#ekgGrad)" stroke-width="2.2" stroke-linecap="round" stroke-linejoin="round"/>
        </svg>
      </div>
    </div>
  </div>

  <!-- PAGE 3 — RESULT -->
  <div class="page" id="page-result">
    <div class="brand">PlayAh</div>
    <h2 style="margin-bottom:4px">Your Sync Result</h2>
    <div class="sync-row">
      <span class="sync-ico">🔥</span><span class="sync-heart">❤️</span><span class="sync-ico">💧</span>
    </div>
    <div class="ring-wrap">
      <svg class="ring-svg" viewBox="0 0 200 200">
        <defs>
          <linearGradient id="ringGrad" x1="0%" y1="0%" x2="100%" y2="100%">
            <stop offset="0%" stop-color="#ef9449"/>
            <stop offset="50%" stop-color="#c47acc"/>
            <stop offset="100%" stop-color="#7ba8d4"/>
          </linearGradient>
        </defs>
        <circle class="ring-track" cx="100" cy="100" r="85"/>
        <circle class="ring-fill" id="ring-fill" cx="100" cy="100" r="85"/>
      </svg>
      <div class="ring-inner">
        <div class="pct-num" id="pct-num">0%</div>
        <div class="pct-lbl">in sync</div>
      </div>
    </div>
    <div class="result-quote">"The rhythm is there, let PlayAh help you<br>maintain the flow."</div>
    <button class="btn" onclick="showPage('page-anniversary')">Explore Your Reward →</button>
  </div>

  <!-- PAGE 4 — ANNIVERSARY -->
  <div class="page" id="page-anniversary">
    <div class="brand">PlayAh</div>
    <h2>When did your<br>love story begin?</h2>
    <p class="sub" style="margin:8px 0">Enter your anniversary date and unlock your exclusive reward.</p>
    <div class="glass anniv-card">
      <span class="flabel">Anniversary Date</span>
      <input type="date" class="date-input" id="anniversary-date">
      <label class="married-row" for="married-check">
        <input type="checkbox" id="married-check">
        <span>💍 We are married</span>
      </label>
    </div>
    <button class="btn" onclick="handleCalculate()">Calculate Our Love</button>
    <div class="err-msg" id="date-error">Please select your anniversary date first.</div>
  </div>

  <!-- PAGE 5 — PROMO -->
  <div class="page" id="page-promo">
    <div class="brand">PlayAh</div>
    <div class="days-wrap">
      <div class="days-num" id="days-num">0</div>
      <div class="days-lbl">Days in Love</div>
    </div>
    <div class="promo-card">
      <div class="promo-big-icon" id="promo-icon">🎁</div>
      <div class="promo-program">Thấm Lâu Rewards</div>
      <div class="promo-tier-lbl" id="promo-tier">Your Tier</div>
      <div class="promo-reward-txt" id="promo-reward">Your Reward</div>
      <div class="promo-detail-txt" id="promo-detail"></div>
    </div>
    <p class="booth-note">This exclusive offer is available for Activation Booth participants only.</p>
    <button class="btn" onclick="showPage('page-welcome')" style="margin-top:10px">Start Again ↺</button>
  </div>

  <script>
  function showPage(id){
    const c=document.querySelector('.page.active'),n=document.getElementById(id);
    if(!n||c===n)return;
    c.classList.add('leaving');c.classList.remove('active');
    setTimeout(()=>{c.classList.remove('leaving');n.classList.add('active');onEnter(id);},380);
  }
  function onEnter(id){
    if(id==='page-hands')resetHands();
    if(id==='page-scanning')startScanning();
    if(id==='page-result')showResult();
    if(id==='page-promo')showPromo();
  }
  const CD_TOTAL=7,CD_CIRC=2*Math.PI*42;
  let fireOn=false,waterOn=false,cdTimer=null,cdCount=CD_TOTAL;
  const layout=document.getElementById('hands-layout'),
        fireSide=document.getElementById('fire-side'),
        waterSide=document.getElementById('water-side'),
        cdOverlay=document.getElementById('cd-overlay'),
        cdRing=document.getElementById('cd-ring'),
        cdNumEl=document.getElementById('cd-num');
  function resetHands(){
    fireOn=waterOn=false;stopCountdown();
    fireSide.classList.remove('pressed');waterSide.classList.remove('pressed');
  }
  layout.addEventListener('touchstart',handleTouch,{passive:false});
  layout.addEventListener('touchend',handleTouch,{passive:false});
  layout.addEventListener('touchcancel',handleTouch,{passive:false});
  layout.addEventListener('touchmove',e=>e.preventDefault(),{passive:false});
  function handleTouch(e){
    e.preventDefault();
    const mid=layout.getBoundingClientRect().left+layout.getBoundingClientRect().width/2;
    let nf=false,nw=false;
    for(const t of e.touches){if(t.clientX<mid)nf=true;else nw=true;}
    applyZoneState(nf,nw);
  }
  function applyZoneState(nf,nw){
    fireOn=nf;waterOn=nw;
    fireSide.classList.toggle('pressed',fireOn);
    waterSide.classList.toggle('pressed',waterOn);
    if(fireOn&&waterOn){if(!cdTimer)startCountdown();}else stopCountdown();
  }
  let mF=false,mW=false;
  fireSide.addEventListener('mousedown',()=>{mF=true;applyZoneState(mF,mW);});
  waterSide.addEventListener('mousedown',()=>{mW=true;applyZoneState(mF,mW);});
  document.addEventListener('mouseup',()=>{mF=mW=false;applyZoneState(false,false);});
  function startCountdown(){
    cdCount=CD_TOTAL;renderCountdown();cdOverlay.style.display='block';
    cdTimer=setInterval(()=>{
      cdCount--;renderCountdown();
      if(cdCount<=0){clearInterval(cdTimer);cdTimer=null;cdOverlay.style.display='none';showPage('page-scanning');}
    },1000);
  }
  function stopCountdown(){
    if(cdTimer){clearInterval(cdTimer);cdTimer=null;}
    cdCount=CD_TOTAL;cdOverlay.style.display='none';
  }
  function renderCountdown(){
    cdNumEl.textContent=cdCount;
    cdRing.style.strokeDashoffset=CD_CIRC*(1-(CD_TOTAL-cdCount)/CD_TOTAL);
  }
  function startScanning(){setTimeout(()=>showPage('page-result'),3400);}
  let syncPct=0;
  function showResult(){
    syncPct=Math.floor(Math.random()*26)+73;
    const ring=document.getElementById('ring-fill'),numEl=document.getElementById('pct-num'),circ=2*Math.PI*85;
    ring.style.transition='none';ring.style.strokeDasharray=circ;ring.style.strokeDashoffset=circ;
    numEl.textContent='0%';ring.getBoundingClientRect();
    ring.style.transition='stroke-dashoffset 2.4s cubic-bezier(.22,1,.36,1)';
    spawnMist();
    setTimeout(()=>{
      ring.style.strokeDashoffset=circ*(1-syncPct/100);
      let cur=0;const inc=syncPct/80;
      const t=setInterval(()=>{cur=Math.min(cur+inc,syncPct);numEl.textContent=Math.floor(cur)+'%';if(cur>=syncPct){numEl.textContent=syncPct+'%';clearInterval(t);}},28);
    },350);
  }
  function spawnMist(){
    for(let i=0;i<22;i++){
      setTimeout(()=>{
        const p=document.createElement('div');p.className='mist';
        const sz=Math.random()*70+25,x=Math.random()*innerWidth,y=Math.random()*innerHeight,
              dx=((Math.random()-.5)*220).toFixed(0)+'px',dy=((Math.random()-.5)*220).toFixed(0)+'px',
              dur=(Math.random()*1.6+1.4).toFixed(2)+'s';
        p.style.cssText=`width:${sz}px;height:${sz}px;left:${x}px;top:${y}px;--dx:${dx};--dy:${dy};--dur:${dur};`;
        document.body.appendChild(p);setTimeout(()=>p.remove(),3200);
      },i*90);
    }
  }
  const dateInput=document.getElementById('anniversary-date');
  dateInput.max=new Date().toISOString().split('T')[0];
  let loveDays=0,currentPromo=null;
  const PROMOS=[
    {test:(y,m,married)=>married||y>=5,icon:'👑',tier:'5 Years+ / Married',reward:'Free Box',detail:'"Long Haul" personalised packaging — because some loves deserve to be celebrated forever.'},
    {test:(y,m)=>y>=2,icon:'🎁',tier:'2 Years+',reward:'Buy 1, Get 1',detail:'Double the love, double the scent. Share the journey with someone you cherish.'},
    {test:(y,m)=>m>=6,icon:'💫',tier:'6 Months – 1 Year',reward:'30% Off + Free Sample Pack',detail:'You\'re building something beautiful. Let PlayAh be part of every chapter.'},
    {test:(y,m)=>m>=1,icon:'🌸',tier:'1 – 3 Months',reward:'15% Off + Free Sample Pack',detail:'The beginning of something wonderful. Celebrate every heartbeat together.'},
    {test:()=>true,icon:'✨',tier:'New Love',reward:'Free Sample Pack',detail:'Every love story starts with a spark. This first gift is on us.'},
  ];
  function handleCalculate(){
    const val=dateInput.value,errEl=document.getElementById('date-error');
    if(!val){errEl.style.display='block';return;}errEl.style.display='none';
    const anniv=new Date(val),now=new Date();
    loveDays=Math.max(0,Math.floor((now-anniv)/86400000));
    const isMarried=document.getElementById('married-check').checked,
          months=loveDays/30.44,years=loveDays/365.25;
    currentPromo=PROMOS.find(p=>p.test(years,months,isMarried));
    showPage('page-promo');
  }
  function showPromo(){
    document.getElementById('promo-icon').textContent=currentPromo.icon;
    document.getElementById('promo-tier').textContent=currentPromo.tier;
    document.getElementById('promo-reward').textContent=currentPromo.reward;
    document.getElementById('promo-detail').textContent=currentPromo.detail;
    const el=document.getElementById('days-num'),target=loveDays;
    let cur=0;el.textContent='0';
    const t=setInterval(()=>{cur=Math.min(cur+target/60,target);el.textContent=Math.floor(cur).toLocaleString();if(cur>=target){el.textContent=target.toLocaleString();clearInterval(t);}},20);
  }
  </script>
</body>
</html>
