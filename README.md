<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Galactic Clicker — Miner espacial</title>
  <style>
    :root{--bg:#0b1020;--card:#0f1724;--accent:#7dd3fc;--muted:#9aa4b2;--glass: rgba(255,255,255,0.03)}
    html,body{height:100%;margin:0;font-family:Inter,system-ui,Segoe UI,Roboto,'Helvetica Neue',Arial}
    body{background: radial-gradient(ellipse at 10% 10%, #07102a 0%, var(--bg) 40%), linear-gradient(180deg,#071229 0%, #061022 100%); color:#e6eef8; display:flex;align-items:center;justify-content:center;padding:20px}

    .app{width:1100px;max-width:100%;display:grid;grid-template-columns:420px 1fr;gap:20px}
    .panel{background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));border-radius:14px;padding:18px;box-shadow:0 6px 30px rgba(2,6,23,0.6);border:1px solid rgba(255,255,255,0.03)}

    header{display:flex;align-items:center;gap:12px;margin-bottom:12px}
    .logo{width:56px;height:56px;border-radius:10px;background:linear-gradient(135deg,#0ea5e9,#7c3aed);display:flex;align-items:center;justify-content:center;font-weight:700;font-size:20px}
    h1{font-size:18px;margin:0}
    p.lead{margin:0;color:var(--muted);font-size:13px}

    .click-area{background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));border-radius:10px;padding:14px;display:flex;flex-direction:column;align-items:center;gap:12px}
    #big-button{width:220px;height:220px;border-radius:50%;background:conic-gradient(from 200deg at 50% 50%, #1e293b, #0ea5e9);display:flex;align-items:center;justify-content:center;cursor:pointer;box-shadow:0 10px 40px rgba(14,165,233,0.12);border:4px solid rgba(255,255,255,0.04)}
    #big-button:active{transform:translateY(2px) scale(0.995)}
    #big-button .core{font-size:28px;font-weight:800}
    .resource{font-size:22px;font-weight:700}
    .sub{color:var(--muted);font-size:12px}

    .store{display:flex;flex-direction:column;gap:10px;margin-top:6px}
    .store-item{display:flex;align-items:center;gap:12px;padding:10px;border-radius:10px;background:var(--glass);border:1px solid rgba(255,255,255,0.02)}
    .item-left{width:48px;height:48px;border-radius:8px;background:linear-gradient(180deg,#0b1220,#091220);display:flex;align-items:center;justify-content:center;font-weight:700}
    .item-main{flex:1}
    .item-title{font-weight:700}
    .item-desc{font-size:12px;color:var(--muted)}
    .item-actions{text-align:right}
    .btn{background:linear-gradient(180deg,#0ea5e9,#3b82f6);border:none;padding:8px 12px;border-radius:8px;color:#031025;font-weight:700;cursor:pointer}
    .btn.secondary{background:transparent;border:1px solid rgba(255,255,255,0.04);color:var(--accent)}

    .right{display:flex;flex-direction:column;gap:14px}
    .stats{display:flex;gap:12px}
    .stat{flex:1;padding:12px;border-radius:10px;background:var(--glass);text-align:center}
    .stat .label{font-size:12px;color:var(--muted)}
    .stat .value{font-weight:800;font-size:18px}

    .upgrades{display:grid;grid-template-columns:repeat(auto-fill,minmax(220px,1fr));gap:10px}
    .upgrade{padding:12px;border-radius:10px;background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));border:1px solid rgba(255,255,255,0.02)}

    footer{font-size:12px;color:var(--muted);display:flex;align-items:center;justify-content:space-between;padding-top:6px}

    .small{font-size:12px;color:var(--muted)}
    .particle{position:absolute;pointer-events:none;z-index:999}

    @media(max-width:900px){.app{grid-template-columns:1fr;}}
  </style>
</head>
<body>
  <div class="app">
    <div class="panel">
      <header>
        <div class="logo">GM</div>
        <div>
          <h1>Galactic Miner</h1>
          <p class="lead">Click the core to mine Stardust — compra minas, drones y fábricas para minar automáticamente.</p>
        </div>
      </header>

      <div class="click-area">
        <div id="big-button" title="Click!">
          <div class="core">⭐​<div style="font-size:12px">Tap</div></div>
        </div>
        <div class="resource"><span id="stardust">0</span> <span class="sub">Stardust</span></div>
        <div class="stats" style="width:100%">
          <div class="stat"><div class="label">Per Click</div><div id="per-click" class="value">1</div></div>
          <div class="stat"><div class="label">Per Second</div><div id="per-second" class="value">0</div></div>
        </div>

        <div class="store" id="store">
          <!-- items inserted by JS -->
        </div>

        <footer>
          <div class="small">Progreso guardado localmente</div>
          <div>
            <button class="btn secondary" id="export">Export</button>
            <button class="btn secondary" id="import">Import</button>
            <button class="btn" id="reset">Reset</button>
          </div>
        </footer>
      </div>
    </div>

    <div class="panel right">
      <div style="display:flex;justify-content:space-between;align-items:center">
        <div style="font-weight:800">Tienda y mejoras</div>
        <div class="small">Nivel de prestigio: <span id="prestige-level">0</span></div>
      </div>

      <div class="upgrades" id="upgrades">
        <!-- upgrades -->
      </div>

      <div style="margin-top:6px;padding:10px;background:var(--glass);border-radius:10px">
        <div style="font-weight:700">Logros</div>
        <div id="achievements" class="small">Ninguno aún.</div>
      </div>

      <div style="margin-top:auto;padding-top:8px">
        <div style="font-weight:700">Controles rápidos</div>
        <div class="small">Haz click en el núcleo para minar. Compra objetos para generar Stardust por segundo (SPS). Usa Reset para obtener Boost de prestigio.</div>
      </div>
    </div>
  </div>

  <canvas id="particles" class="particle" width="800" height="600"></canvas>

  <script>
    // --- Game data ---
    const STORAGE_KEY = 'galactic_clicker_v1'
    const app = {
      stardust: 0,
      perClick: 2,
      perSecond: 1,
      items: [
        {id:'drone', name:'Miner Drone', desc:'Pequeño dron que recoge Stardust automáticamente.', baseCost:15, sps:0.1, qty:0},
        {id:'rig', name:'Mining Rig', desc:'Rack de minería automatizado.', baseCost:120, sps:1, qty:0},
        {id:'factory', name:'Orbital Factory', desc:'Fábrica que refina a gran escala.', baseCost:1500, sps:12, qty:0},
        {id:'portal', name:'Warp Portal', desc:'Genera riqueza desde otra dimensión.', baseCost:20000, sps:160, qty:0}
      ],
      upgrades: [
        {id:'click+1', name:'Mejora de click', desc:'+1 per click', cost:100, bought:false, effect:()=>{app.perClick+=2}},
        {id:'drone+double', name:'Drones mejorados', desc:'Drones x2', cost:500, bought:false, effect:()=>{getItem('drone').sps*=2}},
        {id:'rig+eff', name:'Rigs eficientes', desc:'Rigs x2', cost:3000, bought:false, effect:()=>{getItem('rig').sps*=2}},
      ],
      prestige: {level:0, multiplier:1}
    }

    // --- Utilities ---
    function format(n){ if(n<1000) return n.toFixed(n%1?1:0); if(n<1e6) return (n/1e3).toFixed(2)+'K'; if(n<1e9) return (n/1e6).toFixed(2)+'M'; return n.toExponential(2) }
    function getItem(id){return app.items.find(i=>i.id===id)}

    // --- Rendering ---
    const $ = id => document.getElementById(id)
    function render(){
      $('stardust').textContent = format(app.stardust)
      $('per-click').textContent = format(app.perClick)
      $('per-second').textContent = format(app.perSecond)
      $('prestige-level').textContent = app.prestige.level

      // store list
      const store = $('store'); store.innerHTML=''
      app.items.forEach(it=>{
        const cost = getCost(it)
        const div = document.createElement('div'); div.className='store-item'
        div.innerHTML = `
          <div class="item-left">🔧</div>
          <div class="item-main">
            <div class="item-title">${it.name}</div>
            <div class="item-desc">${it.desc} • sps: ${it.sps}</div>
          </div>
          <div class="item-actions">
            <div class="small">Precio: ${format(cost)}</div>
            <div style="margin-top:6px"><button class="btn" data-buy="${it.id}">Comprar</button></div>
          </div>
        `
        store.appendChild(div)
      })

      // upgrades
      const ups = $('upgrades'); ups.innerHTML=''
      app.upgrades.forEach(u=>{
        const el = document.createElement('div'); el.className='upgrade'
        el.innerHTML = `<div style="display:flex;justify-content:space-between;align-items:center"><div><div style='font-weight:700'>${u.name}</div><div class='small'>${u.desc}</div></div><div>${u.bought?'<div class="small">Comprado</div>':'<button class="btn" data-up="'+u.id+'">${format(u.cost)}</button>'}</div></div>`
        ups.appendChild(el)
      })

      // attach listeners
      document.querySelectorAll('[data-buy]').forEach(b=>b.onclick = ()=>buyItem(b.dataset.buy))
      document.querySelectorAll('[data-up]').forEach(b=>b.onclick = ()=>buyUpgrade(b.dataset.up))
    }

    function getCost(item){ // exponential cost
      return Math.floor(item.baseCost * Math.pow(1.15, item.qty))
    }

    // --- game actions ---
    function clickCore(){
      app.stardust += app.perClick * app.prestige.multiplier
      spawnParticle()
      save()
      render()
    }

    function buyItem(id){
      const it = getItem(id); const cost = getCost(it)
      if(app.stardust >= cost){
        app.stardust -= cost; it.qty++; recalcSPS(); spawnBuyEffect(); save(); render();
      } else flashLow()
    }

    function buyUpgrade(id){
      const up = app.upgrades.find(u=>u.id===id)
      if(!up || up.bought) return
      if(app.stardust>=up.cost){app.stardust-=up.cost; up.bought=true; up.effect(); recalcSPS(); save(); render();}
      else flashLow()
    }

    function recalcSPS(){
      let sps = 0
      app.items.forEach(it => sps += it.sps * it.qty)
      app.perSecond = sps * app.prestige.multiplier
    }

    // passive income loop
    setInterval(()=>{
      const gain = app.perSecond/10
      app.stardust += gain
      save()
      render()
    },100)

    // prestige/reset
    function resetGame(){
      const keep = confirm('¿Resetear para ganar 1x multiplicador de prestigio por cada 10000 Stardust total?')
      if(!keep) return
      // compute boost
      const total = app.stardust
      const addLevel = Math.floor(total/10000)
      if(addLevel<=0){alert('Necesitas al menos 10000 Stardust para obtener prestigio.'); return}
      // apply prestige
      app.prestige.level += addLevel
      app.prestige.multiplier = 1 + app.prestige.level*0.05
      // reset economy but keep prestige
      app.stardust = 0; app.perClick = 1; app.perSecond = 0
      app.items.forEach(it=>{it.qty=0; it.sps = it.sps})
      app.upgrades.forEach(u=>{u.bought=false})
      save(); render();
    }

    // achievements (simple)
    function checkAchievements(){
      const ach = []
      if(app.items.find(i=>i.id==='drone').qty>=10) ach.push('10 Drones!')
      if(app.items.find(i=>i.id==='factory').qty>=1) ach.push('Primera fábrica')
      if(app.stardust>=100000) ach.push('100k Stardust')
      $('achievements').textContent = ach.length?ach.join(' • '):'Ninguno aún.'
    }

    // --- persistence ---
    function save(){
      try{localStorage.setItem(STORAGE_KEY, JSON.stringify(app))}catch(e){console.warn('No se pudo guardar')}
    }
    function load(){
      try{
        const raw = localStorage.getItem(STORAGE_KEY)
        if(!raw) return
        const o = JSON.parse(raw)
        // naive merge
        Object.assign(app, {stardust:o.stardust||0, perClick:o.perClick||1, perSecond:o.perSecond||0, prestige:o.prestige||app.prestige})
        if(o.items) { o.items.forEach(si=>{const it=getItem(si.id); if(it) it.qty = si.qty; }) }
        if(o.upgrades) { o.upgrades.forEach(uo=>{const uu = app.upgrades.find(x=>x.id===uo.id); if(uu) uu.bought = uo.bought}) }
      }catch(e){console.warn('load fail', e)}
    }

    // import/export
    document.getElementById('export').onclick = ()=>{ const data = JSON.stringify(app); prompt('Copia tu save:', data)}
    document.getElementById('import').onclick = ()=>{ const data = prompt('Pega aquí tu save JSON:'); try{ const o = JSON.parse(data); localStorage.setItem(STORAGE_KEY, JSON.stringify(o)); load(); render(); alert('Importado') }catch(e){alert('JSON inválido')} }
    document.getElementById('reset').onclick = ()=>resetGame()

    // UI interactions
    document.getElementById('big-button').addEventListener('click', ()=>{ clickCore(); checkAchievements() })

    // visual particles
    const canvas = document.getElementById('particles'); const ctx = canvas.getContext('2d');
    function resizeCanvas(){canvas.width = window.innerWidth; canvas.height = window.innerHeight}
    window.addEventListener('resize', resizeCanvas); resizeCanvas()
    const particles = []
    function spawnParticle(){
      for(let i=0;i<8;i++){particles.push({x:window.innerWidth/2 + (Math.random()-0.5)*200, y:window.innerHeight/2 + (Math.random()-0.5)*200, vx:(Math.random()-0.5)*4, vy:-Math.random()*4-1, life:60})}
    }
    function spawnBuyEffect(){ for(let i=0;i<18;i++){particles.push({x:Math.random()*window.innerWidth, y:Math.random()*window.innerHeight, vx:(Math.random()-0.5)*6, vy:(Math.random()-0.5)*6, life:40})} }
    function flashLow(){ const el = document.getElementById('big-button'); el.animate([{boxShadow:'0 12px 40px rgba(255,0,0,0.2)'},{boxShadow:'0 10px 40px rgba(14,165,233,0.12)'}],{duration:400}) }

    function loop(){ ctx.clearRect(0,0,canvas.width,canvas.height); for(let i=particles.length-1;i>=0;i--){const p=particles[i]; p.x+=p.vx; p.y+=p.vy; p.vy+=0.08; p.life--; const alpha = p.life/60; ctx.globalAlpha = Math.max(0,alpha); ctx.fillStyle = 'white'; ctx.fillRect(p.x, p.y, 3, 3); if(p.life<=0) particles.splice(i,1) } requestAnimationFrame(loop) }
    loop()

    // initial setup
    load(); recalcSPS(); render(); checkAchievements();

    // autosave every 5s
    setInterval(()=>{save();},5000)
  </script>
</body>
</html>
