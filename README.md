<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Ticket de Salida · Clase 9</title>
<link href="https://fonts.googleapis.com/css2?family=Syne:wght@400;700;800&family=DM+Sans:ital,wght@0,300;0,400;0,500;1,300&display=swap" rel="stylesheet">
<style>
  :root {
    --bg-dark: #0f0f1a;
    --bg-light: #f4f3ef;
    --accent: #e8a838;
    --violet: #7b68ee;
    --teal: #3fb88a;
    --red: #e05555;
    --white: #ffffff;
    --dark: #0f0f1a;
    --muted: #8888aa;
    --card: #ffffff;
  }
  * { box-sizing: border-box; margin: 0; padding: 0; }
  html, body { width: 100%; height: 100%; font-family: 'DM Sans', sans-serif; background: var(--bg-dark); overflow: hidden; }

  .slides { width: 100%; height: 100vh; position: relative; }

  .slide {
    position: absolute; inset: 0;
    display: flex; flex-direction: column; justify-content: center; align-items: flex-start;
    padding: 6vh 7vw 90px;
    opacity: 0; pointer-events: none;
    transition: opacity 0.45s ease, transform 0.45s ease;
    transform: translateY(20px);
    overflow-y: auto;
  }
  .slide.active { opacity: 1; pointer-events: all; transform: translateY(0); }
  .slide.exit   { opacity: 0; transform: translateY(-20px); }
  .slide-light  { background: var(--bg-light); }
  .slide-dark   { background: var(--bg-dark); }

  .top-bar { position: absolute; top: 0; left: 0; right: 0; height: 4px; }

  .band { position: absolute; left: 0; top: 0; bottom: 0; width: 6px; }

  .counter {
    font-family: 'Syne', sans-serif; font-size: 11px;
    letter-spacing: 0.22em; text-transform: uppercase; margin-bottom: 1rem;
  }
  .question {
    font-family: 'Syne', sans-serif; font-weight: 800; line-height: 1.1; margin-bottom: 1.8rem;
    font-size: clamp(1.6rem, 3.5vw, 3rem);
  }
  .q-dark  { color: var(--dark); }
  .q-white { color: var(--white); }

  /* ── OPCIONES ── */
  .options-grid {
    display: grid; grid-template-columns: repeat(3, 1fr);
    gap: 10px; width: 100%; max-width: 860px;
  }
  .option-card {
    background: var(--card); border: 2px solid #dddde8; border-radius: 10px;
    padding: 13px 16px; display: flex; align-items: center; gap: 12px;
    font-size: 14px; color: var(--dark); cursor: pointer;
    transition: all 0.2s; user-select: none;
  }
  .option-card:hover { border-color: #aaaadd; background: #f8f8ff; }
  .option-card.selected { border-color: var(--violet); background: #f0eeff; }
  .option-card.selected .check-box { background: var(--violet); border-color: var(--violet); }
  .option-card.selected .check-box::after { opacity: 1; }
  .check-box {
    width: 22px; height: 22px; min-width: 22px;
    border: 2px solid #aaaacc; border-radius: 5px;
    background: var(--bg-light); position: relative; transition: all 0.2s;
  }
  .check-box::after {
    content: '✓'; position: absolute; inset: 0;
    display: flex; align-items: center; justify-content: center;
    color: white; font-size: 13px; font-weight: 700; opacity: 0;
    transition: opacity 0.15s;
  }

  /* ── ROLES ── */
  .roles-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 14px; width: 100%; max-width: 860px; }
  .role-card {
    background: var(--card); border: 2px solid #dddde8; border-radius: 10px;
    overflow: hidden; cursor: pointer; transition: all 0.2s;
  }
  .role-card:hover { border-color: #aaaadd; }
  .role-card.selected { border-width: 2px; }
  .role-card.selected.teal-card  { border-color: var(--teal);   background: #edfaf4; }
  .role-card.selected.red-card   { border-color: var(--red);    background: #fef0f0; }
  .role-card.selected.amber-card { border-color: var(--accent); background: #fef8ec; }
  .role-top { height: 6px; }
  .role-body { padding: 18px 16px; text-align: center; }
  .role-check {
    width: 26px; height: 26px; border: 2px solid #aaaacc;
    border-radius: 5px; background: var(--bg-light);
    margin: 0 auto 12px; position: relative; transition: all 0.2s;
  }
  .role-card.selected .role-check::after {
    content: '✓'; position: absolute; inset: 0;
    display: flex; align-items: center; justify-content: center;
    font-size: 14px; font-weight: 700;
  }
  .role-card.selected.teal-card  .role-check { background: var(--teal);   border-color: var(--teal);   }
  .role-card.selected.red-card   .role-check { background: var(--red);    border-color: var(--red);    }
  .role-card.selected.amber-card .role-check { background: var(--accent); border-color: var(--accent); }
  .role-card.selected .role-check::after { color: white; }
  .role-title { font-family: 'Syne', sans-serif; font-size: 15px; font-weight: 700; color: var(--dark); margin-bottom: 5px; }
  .role-sub   { font-size: 12px; color: var(--muted); line-height: 1.4; }

  /* ── TEXTAREA ── */
  .write-block { width: 100%; max-width: 820px; margin-bottom: 1.5rem; }
  .write-label-text { font-size: 12px; color: var(--muted); font-style: italic; margin-bottom: 8px; }
  .write-label-dark { color: #555577; }
  textarea {
    width: 100%; border: 2px solid #dddde8; border-radius: 10px;
    padding: 14px 16px; font-size: 15px; font-family: 'DM Sans', sans-serif;
    color: var(--dark); background: var(--card); resize: none;
    line-height: 1.7; transition: border-color 0.2s; outline: none;
  }
  textarea:focus { border-color: var(--violet); }
  textarea.dark-ta {
    background: #16162a; border-color: #2a2a4a; color: #ccccee;
  }
  textarea.dark-ta:focus { border-color: var(--violet); }

  /* ── SEMÁFORO ── */
  .semaforo-wrap { margin-bottom: 1rem; }
  .sema-title { font-family: 'Syne', sans-serif; font-size: 13px; font-weight: 700; color: var(--dark); margin-bottom: 12px; }
  .semaforo { display: flex; gap: 14px; }
  .sema-btn {
    flex: 1; max-width: 200px; border: 2px solid #dddde8; border-radius: 10px;
    padding: 14px 10px; text-align: center; cursor: pointer;
    background: var(--card); transition: all 0.2s; user-select: none;
  }
  .sema-btn:hover { border-color: #aaaadd; }
  .sema-dot { width: 36px; height: 36px; border-radius: 50%; margin: 0 auto 10px; }
  .sema-label { font-size: 12px; color: var(--dark); line-height: 1.4; }
  .sema-btn.selected-rojo   { border-color: var(--red);    background: #fef0f0; }
  .sema-btn.selected-ambar  { border-color: #f0a500;        background: #fefaec; }
  .sema-btn.selected-verde  { border-color: var(--teal);   background: #edfaf4; }

  /* ── PORTADA ── */
  .portada-tag  { font-family: 'Syne', sans-serif; font-size: 11px; letter-spacing: 0.25em; text-transform: uppercase; color: var(--accent); margin-bottom: 1rem; }
  .portada-title { font-family: 'Syne', sans-serif; font-weight: 800; font-size: clamp(3rem, 7vw, 5.5rem); color: white; line-height: 1.0; margin-bottom: 1rem; }
  .portada-sub  { font-size: clamp(1rem, 2vw, 1.25rem); color: #9999bb; font-weight: 300; margin-bottom: 2rem; line-height: 1.5; }
  .portada-badge { display: inline-block; background: var(--accent); color: var(--dark); font-family: 'Syne', sans-serif; font-weight: 700; font-size: 14px; padding: 12px 26px; border-radius: 6px; }
  .portada-meta  { margin-top: 1rem; font-size: 13px; color: var(--muted); font-style: italic; }
  .deco { position: absolute; border-radius: 50%; pointer-events: none; }

  /* ── CIERRE ── */
  .cierre-line { width: 100%; max-width: 720px; height: 1px; background: rgba(123,104,238,0.3); margin: 1.5rem 0; }
  .cierre-inst { font-size: 16px; color: #9999bb; max-width: 620px; line-height: 1.6; margin-bottom: 1.5rem; }
  .cierre-quote { font-size: 14px; color: #5555aa; font-style: italic; max-width: 560px; line-height: 1.6; }

  /* ── RESULTADO ── */
  .result-panel {
    width: 100%; max-width: 820px;
    background: #16162a; border: 1.5px solid #2a2a4a;
    border-radius: 14px; padding: 22px 26px;
    margin-top: 1rem;
  }
  .result-row { display: flex; gap: 14px; margin-bottom: 12px; align-items: flex-start; }
  .result-row:last-child { margin-bottom: 0; }
  .result-key { font-size: 11px; color: #5555aa; text-transform: uppercase; letter-spacing: 0.1em; min-width: 100px; padding-top: 2px; font-family: 'Syne', sans-serif; }
  .result-val { font-size: 14px; color: #ccccee; line-height: 1.5; }
  .tag { display: inline-block; border-radius: 20px; padding: 3px 12px; font-size: 12px; font-weight: 500; }
  .tag-violet { background: rgba(123,104,238,0.2); color: #a090ff; }
  .tag-teal   { background: rgba(63,184,138,0.2);  color: #4fd4a0; }
  .tag-red    { background: rgba(224,85,85,0.2);   color: #ff8888; }
  .tag-amber  { background: rgba(232,168,56,0.2);  color: #f0c060; }

  .slide-num { position: absolute; top: 28px; right: 36px; font-family: 'Syne', sans-serif; font-size: 12px; letter-spacing: 0.1em; }
  .slide-num-light { color: rgba(15,15,26,0.2); }
  .slide-num-dark  { color: rgba(255,255,255,0.15); }

  .note { font-size: 12px; font-style: italic; color: var(--muted); margin-top: 1rem; }

  /* ── NAV ── */
  nav {
    position: fixed; bottom: 24px; left: 50%; transform: translateX(-50%);
    display: flex; align-items: center; gap: 12px;
    background: rgba(10,10,22,0.9); backdrop-filter: blur(14px);
    border: 1px solid rgba(255,255,255,0.08); border-radius: 50px;
    padding: 10px 20px; z-index: 100;
  }
  nav button {
    background: none; border: none; cursor: pointer; color: #8888aa;
    font-size: 17px; padding: 4px 10px; border-radius: 20px; transition: all 0.2s;
    font-family: 'DM Sans', sans-serif;
  }
  nav button:hover:not(:disabled) { color: white; background: rgba(255,255,255,0.08); }
  nav button:disabled { opacity: 0.25; cursor: default; }
  .nav-dots { display: flex; gap: 8px; align-items: center; }
  .nav-dot { width: 7px; height: 7px; border-radius: 50%; background: #333355; cursor: pointer; border: none; transition: all 0.25s; }
  .nav-dot.active-dot { background: var(--accent); transform: scale(1.35); }
  .nav-dot.done-dot   { background: var(--teal); }

  /* sub-q ancla */
  .sub-q { font-size: 15px; color: #9999bb; font-style: italic; max-width: 680px; line-height: 1.5; margin-bottom: 1.5rem; }
  .sep-line { width: 100%; max-width: 740px; height: 1px; background: rgba(123,104,238,0.3); margin-bottom: 1.5rem; }
</style>
</head>
<body>

<div class="slides" id="slides">

  <!-- ── 0: PORTADA ── -->
  <div class="slide slide-dark active" id="slide-0">
    <div class="top-bar" style="background:var(--accent);"></div>
    <div class="deco" style="width:400px;height:400px;right:-70px;top:-90px;background:rgba(123,104,238,0.07);"></div>
    <div class="deco" style="width:240px;height:240px;left:-70px;bottom:-50px;background:rgba(232,168,56,0.07);"></div>
    <div class="portada-tag">Comprensión Histórica del Presente</div>
    <div class="portada-title">Ticket de<br>Salida</div>
    <div class="portada-sub">Clase 9 · Actores sociales en<br>procesos históricos recientes</div>
    <div class="portada-badge">Escribe tus respuestas directamente aquí</div>
    <div class="portada-meta">4 preguntas · ~8 minutos</div>
    <div class="slide-num slide-num-dark">01 / 06</div>
  </div>

  <!-- ── 1: Q1 ACTOR ── -->
  <div class="slide slide-light" id="slide-1">
    <div class="top-bar" style="background:var(--violet);"></div>
    <div class="band" style="background:var(--violet);"></div>
    <div class="counter" style="color:var(--violet);">01 / 04</div>
    <div class="question q-dark">¿Qué actor social te pareció<br>más relevante en los procesos<br>históricos recientes vistos hoy?</div>
    <div class="options-grid" id="actors-grid">
      <div class="option-card" onclick="selectOption('actor',this,'Movimiento estudiantil')"><div class="check-box"></div>Movimiento estudiantil</div>
      <div class="option-card" onclick="selectOption('actor',this,'Organizaciones sindicales')"><div class="check-box"></div>Organizaciones sindicales</div>
      <div class="option-card" onclick="selectOption('actor',this,'Partidos políticos')"><div class="check-box"></div>Partidos políticos</div>
      <div class="option-card" onclick="selectOption('actor',this,'Movimientos feministas')"><div class="check-box"></div>Movimientos feministas</div>
      <div class="option-card" onclick="selectOption('actor',this,'Comunidades territoriales')"><div class="check-box"></div>Comunidades territoriales</div>
      <div class="option-card" onclick="selectOption('actor',this,'Medios de comunicación')"><div class="check-box"></div>Medios de comunicación</div>
    </div>
    <p class="note">Haz clic en la opción que elijas</p>
    <div class="slide-num slide-num-light">02 / 06</div>
  </div>

  <!-- ── 2: Q2 ROL ── -->
  <div class="slide slide-light" id="slide-2">
    <div class="top-bar" style="background:var(--teal);"></div>
    <div class="band" style="background:var(--teal);"></div>
    <div class="counter" style="color:var(--teal);">02 / 04</div>
    <div class="question q-dark">¿Cuál fue el rol principal<br>de ese actor en el proceso histórico?</div>
    <div class="roles-grid">
      <div class="role-card teal-card" onclick="selectRole(this,'Impulsó el cambio')">
        <div class="role-top" style="background:var(--teal);"></div>
        <div class="role-body">
          <div class="role-check"></div>
          <div class="role-title">Impulsó el cambio</div>
          <div class="role-sub">Movilizó, presionó,<br>lideró demandas</div>
        </div>
      </div>
      <div class="role-card red-card" onclick="selectRole(this,'Resistió el cambio')">
        <div class="role-top" style="background:var(--red);"></div>
        <div class="role-body">
          <div class="role-check"></div>
          <div class="role-title">Resistió el cambio</div>
          <div class="role-sub">Se opuso, defendió<br>el statu quo</div>
        </div>
      </div>
      <div class="role-card amber-card" onclick="selectRole(this,'Medió entre partes')">
        <div class="role-top" style="background:var(--accent);"></div>
        <div class="role-body">
          <div class="role-check"></div>
          <div class="role-title">Medió entre partes</div>
          <div class="role-sub">Negoció, articuló<br>actores distintos</div>
        </div>
      </div>
    </div>
    <p class="note">Haz clic en la tarjeta que mejor describe el rol del actor que elegiste</p>
    <div class="slide-num slide-num-light">03 / 06</div>
  </div>

  <!-- ── 3: Q3 JUSTIFICACIÓN + SEMÁFORO ── -->
  <div class="slide slide-light" id="slide-3">
    <div class="top-bar" style="background:var(--accent);"></div>
    <div class="band" style="background:var(--accent);"></div>
    <div class="counter" style="color:#b07a10;">03 / 04</div>
    <div class="question q-dark" style="margin-bottom:1rem;">En una oración: ¿por qué ese actor<br>fue clave en ese momento histórico?</div>
    <div class="write-block">
      <div class="write-label-text">Escribe tu respuesta aquí:</div>
      <textarea id="razon" rows="3" placeholder="Escribe aquí..."></textarea>
    </div>
    <div class="semaforo-wrap">
      <div class="sema-title">¿Cómo saliste de la clase hoy?</div>
      <div class="semaforo">
        <div class="sema-btn" onclick="selectSema(this,'rojo','No entendí bien')">
          <div class="sema-dot" style="background:#e05555;"></div>
          <div class="sema-label">No entendí bien cómo actúan los actores sociales</div>
        </div>
        <div class="sema-btn" onclick="selectSema(this,'ambar','Entendí con dudas')">
          <div class="sema-dot" style="background:#f0a500;"></div>
          <div class="sema-label">Entendí, pero me quedaron algunas dudas</div>
        </div>
        <div class="sema-btn" onclick="selectSema(this,'verde','Puedo explicarlo')">
          <div class="sema-dot" style="background:#3fb88a;"></div>
          <div class="sema-label">Puedo explicarlo con mis propias palabras</div>
        </div>
      </div>
    </div>
    <div class="slide-num slide-num-light">04 / 06</div>
  </div>

  <!-- ── 4: Q4 ANCLA ── -->
  <div class="slide slide-dark" id="slide-4">
    <div class="top-bar" style="background:var(--violet);"></div>
    <div class="deco" style="width:340px;height:340px;right:-60px;bottom:-60px;background:rgba(123,104,238,0.07);"></div>
    <div class="counter" style="color:var(--violet);">04 / 04 · Pregunta ancla</div>
    <div class="question q-white">¿Qué actor social que no<br>mencionamos hoy crees<br>que faltó en el relato?</div>
    <div class="sep-line"></div>
    <div class="sub-q">¿Por qué crees que ese actor suele quedar fuera del relato histórico?</div>
    <div class="write-block">
      <div class="write-label-text write-label-dark">Escribe tu reflexión aquí →</div>
      <textarea id="reflexion" class="dark-ta" rows="4" placeholder="Escribe aquí..."></textarea>
    </div>
    <div class="slide-num slide-num-dark">05 / 06</div>
  </div>

  <!-- ── 5: RESUMEN ── -->
  <div class="slide slide-dark" id="slide-5">
    <div class="top-bar" style="background:var(--accent);"></div>
    <div class="deco" style="width:360px;height:360px;right:-60px;bottom:-60px;background:rgba(232,168,56,0.06);"></div>
    <div class="counter" style="color:var(--accent);">Resumen · Tu ticket completo</div>
    <div class="question q-white" style="font-size:clamp(1.4rem,2.5vw,2rem);margin-bottom:1.2rem;">¿Quién decide qué actores<br>importan en la historia?</div>
    <div class="cierre-line"></div>
    <div id="result-panel" class="result-panel"></div>
    <div class="cierre-quote" style="margin-top:1.2rem;">"La historia la escriben quienes tienen la palabra — y también quienes se la arrebatan."</div>
    <div class="slide-num slide-num-dark">06 / 06</div>
  </div>

</div>

<!-- ── NAV ── -->
<nav>
  <button id="btn-prev" onclick="cambiar(-1)" disabled>←</button>
  <div class="nav-dots" id="dots"></div>
  <button id="btn-next" onclick="cambiar(1)">→</button>
</nav>

<script>
  const total = 6;
  let current = 0;
  const state = { actor: '', rol: '', razon: '', semaforo: '', reflexion: '' };

  // dots
  const dotsEl = document.getElementById('dots');
  for (let i = 0; i < total; i++) {
    const d = document.createElement('button');
    d.className = 'nav-dot' + (i === 0 ? ' active-dot' : '');
    d.onclick = () => goTo(i);
    dotsEl.appendChild(d);
  }

  function updateDots() {
    document.querySelectorAll('.nav-dot').forEach((d, i) => {
      d.className = 'nav-dot';
      if (i < current) d.classList.add('done-dot');
      else if (i === current) d.classList.add('active-dot');
    });
  }

  function goTo(n) {
    if (n === 5) buildResult();
    const slides = document.querySelectorAll('.slide');
    slides[current].classList.remove('active');
    slides[current].classList.add('exit');
    const prev = current;
    current = n;
    setTimeout(() => slides[prev].classList.remove('exit'), 480);
    slides[current].classList.add('active');
    document.getElementById('btn-prev').disabled = current === 0;
    document.getElementById('btn-next').disabled = current === total - 1;
    updateDots();
  }

  function cambiar(dir) {
    const next = current + dir;
    if (next >= 0 && next < total) goTo(next);
  }

  // Seleccionar actor
  function selectOption(key, el, val) {
    el.closest('.options-grid').querySelectorAll('.option-card').forEach(c => c.classList.remove('selected'));
    el.classList.add('selected');
    state[key] = val;
  }

  // Seleccionar rol
  function selectRole(el, val) {
    document.querySelectorAll('.role-card').forEach(c => c.classList.remove('selected'));
    el.classList.add('selected');
    state.rol = val;
  }

  // Semáforo
  function selectSema(el, color, val) {
    document.querySelectorAll('.sema-btn').forEach(b => b.className = 'sema-btn');
    el.classList.add('selected-' + color);
    state.semaforo = val;
  }

  // Construir resumen
  function buildResult() {
    state.razon     = document.getElementById('razon').value.trim();
    state.reflexion = document.getElementById('reflexion').value.trim();

    const semaColors = {
      'No entendí bien': 'tag-red',
      'Entendí con dudas': 'tag-amber',
      'Puedo explicarlo': 'tag-teal',
    };

    const rolColors = {
      'Impulsó el cambio':  'tag-teal',
      'Resistió el cambio': 'tag-red',
      'Medió entre partes': 'tag-amber',
    };

    const rows = [
      { key: 'Actor clave',    val: state.actor     || '—', cls: 'tag-violet' },
      { key: 'Su rol',         val: state.rol        || '—', cls: rolColors[state.rol] || 'tag-violet' },
      { key: '¿Por qué clave?',val: state.razon      || '—', cls: null },
      { key: 'Semáforo',       val: state.semaforo   || '—', cls: semaColors[state.semaforo] || 'tag-amber' },
      { key: 'Actor que faltó',val: state.reflexion  || '—', cls: null },
    ];

    document.getElementById('result-panel').innerHTML = rows.map(r => `
      <div class="result-row">
        <span class="result-key">${r.key}</span>
        <span class="result-val">
          ${r.cls ? `<span class="tag ${r.cls}">${r.val}</span>` : r.val}
        </span>
      </div>
    `).join('');
  }

  // Teclado
  document.addEventListener('keydown', e => {
    const tag = document.activeElement.tagName;
    if (tag === 'TEXTAREA' || tag === 'INPUT') return;
    if (e.key === 'ArrowRight' || e.key === 'ArrowDown') cambiar(1);
    if (e.key === 'ArrowLeft'  || e.key === 'ArrowUp')   cambiar(-1);
  });

  updateDots();
</script>
</body>
</html>
