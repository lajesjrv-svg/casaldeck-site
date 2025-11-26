# casaldeck-site
Cartas para casal
<!doctype html>
<html lang="pt-BR">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>CasalDeck — Cartas de Conversa</title>
  <meta name="description" content="Baralho interativo para casais - níveis: Leve, Médio, Profundo. Versão web responsiva, pronta para celular." />
  <style>
    :root{
      --bg:#F4F0FF; /* lilac soft */
      --card:#FFFFFF;
      --accent:#7C3AED; /* roxo vibrante */
      --accent-2:#A78BFA; /* lavanda */
      --muted:#6B7280;
      --glass: rgba(255,255,255,0.55);
      --radius:20px;
      --shadow: 0 12px 32px rgba(80,0,120,0.1);
      font-family: 'Poppins', system-ui, sans-serif;
    }
    *{box-sizing:border-box}
    html,body{height:100%;margin:0;background:linear-gradient(180deg,var(--bg),#FBFAFF);color:#111827}
    .app{max-width:920px;margin:28px auto;padding:18px}
    header{display:flex;align-items:center;gap:16px}
    .logo{width:64px;height:64px;border-radius:18px;background:linear-gradient(135deg,var(--accent),#C084FC);display:flex;align-items:center;justify-content:center;color:white;font-weight:700;font-size:22px;box-shadow:var(--shadow)}
    h1{margin:0;font-size:22px}
    p.lead{margin:4px 0 16px;color:var(--muted)}

    .controls{display:flex;gap:10px;flex-wrap:wrap;margin-bottom:16px}
    .segmented{display:flex;background:var(--card);padding:6px;border-radius:14px;box-shadow:var(--shadow)}
    .segmented button{border:0;background:transparent;padding:10px 16px;border-radius:12px;font-weight:600;color:var(--muted);cursor:pointer;font-size:14px}
    .segmented button.active{background:linear-gradient(90deg,var(--accent),var(--accent-2));color:white;box-shadow:var(--shadow)}

    .primary{background:linear-gradient(90deg,var(--accent),var(--accent-2));color:white;border:0;padding:12px 18px;border-radius:14px;font-weight:700;cursor:pointer;box-shadow:var(--shadow)}
    .secondary{background:transparent;border:2px solid var(--accent-2);padding:10px 14px;border-radius:14px;cursor:pointer;color:var(--accent);font-weight:600}

    .cardArea{display:flex;gap:18px;flex-wrap:wrap}
    .cardPreview{flex:1;min-width:260px;background:var(--card);border-radius:var(--radius);padding:24px;box-shadow:var(--shadow);display:flex;flex-direction:column;align-items:center;justify-content:center;transition:0.3s}
    .cardPreview:hover{transform:scale(1.02)}
    .cardText{font-size:20px;color:#1E1B4B;text-align:center;font-weight:500}
    .meta{margin-top:12px;color:var(--muted);font-size:14px}

    .history{min-width:220px;max-width:300px;background:linear-gradient(180deg,var(--glass),rgba(255,255,255,0.8));backdrop-filter: blur(8px);border-radius:16px;padding:14px;box-shadow:var(--shadow)}
    .history h4{margin:0 0 8px 0;font-weight:700;color:#4C1D95}
    .historyList{display:flex;flex-direction:column;gap:8px;max-height:320px;overflow:auto}
    .chip{background:#FFFFFF;padding:10px;border-radius:10px;font-size:14px;box-shadow:0 6px 18px rgba(16,24,40,0.06);color:#4C1D95;font-weight:500}

    footer{margin-top:22px;color:var(--muted);font-size:13px;text-align:center;font-weight:500}

    @media (max-width:640px){
      .cardArea{flex-direction:column}
      .history{order:2}
    }
  </style>
</head>
<body>
  <div class="app">
    <header>
      <div class="logo">CD</div>
      <div>
        <h1>CasalDeck</h1>
        <p class="lead">Cartas de conversa para casais — escolha profundidade, sorteie e dialogue com segurança.</p>
      </div>
    </header>

    <div class="controls">
      <div class="segmented" role="tablist" aria-label="Níveis">
        <button id="level-leve" class="active" data-level="leve">Leve</button>
        <button id="level-medio" data-level="medio">Médio</button>
        <button id="level-profundo" data-level="profundo">Profundo</button>
      </div>

      <button id="drawBtn" class="primary">Sortear Carta</button>
      <button id="resetBtn" class="secondary">Limpar Histórico</button>
      <button id="shareBtn" class="secondary">Copiar Carta</button>
    </div>

    <main class="cardArea">
      <section class="cardPreview" aria-live="polite">
        <div style="font-size:12px;color:var(--muted);font-weight:600;margin-bottom:8px" id="current-level">Nível: Leve</div>
        <div class="cardText" id="cardText">Toque em "Sortear Carta" para iniciar.</div>
        <div class="meta" id="meta">Dica: ouça sem interromper. Valide antes de responder.</div>
      </section>

      <aside class="history">
        <h4>Histórico</h4>
        <div class="historyList" id="historyList">
          <div class="chip">Nenhuma carta sorteada ainda</div>
        </div>
      </aside>
    </main>

    <footer>
      <small>Use o app como ferramenta de diálogo — se as conversas forem muito difíceis, considere procurar um terapeuta de casal.</small>
    </footer>
  </div>

  <script>
    // Cartas por nível (expansível)
    const CARDS = {
      leve:[
        'O que mais te fez sorrir esta semana?',
        'Qual pequeno gesto meu você gosta muito?',
        'Quando você se sente mais conectado(a) comigo no dia a dia?',
        'Qual memória nossa sempre te faz rir?'
      ],
      medio:[
        'Qual parte da nossa rotina mais te estressa e por quê?',
        'Que necessidade emocional sua você sente que não estou percebendo?',
        'O que eu faço que, sem querer, te afasta?',
        'Há algo que você gostaria que eu pedisse com mais frequência?'
      ],
      profundo:[
        'Qual mágoa antiga você ainda carrega e gostaria de curar juntos?',
        'O que você teme que se repita no nosso relacionamento?',
        'Qual vulnerabilidade você tem dificuldade de compartilhar comigo?',
        'O que você precisa para se sentir totalmente seguro(a) comigo?'
      ]
    };

    // State
    let currentLevel = 'leve';
    let history = [];

    const btns = document.querySelectorAll('.segmented button');
    const drawBtn = document.getElementById('drawBtn');
    const resetBtn = document.getElementById('resetBtn');
    const shareBtn = document.getElementById('shareBtn');
    const cardText = document.getElementById('cardText');
    const meta = document.getElementById('meta');
    const historyList = document.getElementById('historyList');
    const currentLevelEl = document.getElementById('current-level');

    function setLevel(l){
      currentLevel = l;
      btns.forEach(b=>b.classList.toggle('active', b.dataset.level===l));
      currentLevelEl.textContent = 'Nível: ' + (l==='leve'?'Leve':l==='medio'?'Médio':'Profundo');
      // Reset small state
      cardText.textContent = 'Toque em "Sortear Carta" para iniciar.';
    }

    btns.forEach(b=>{
      b.addEventListener('click', ()=>setLevel(b.dataset.level));
    });

    function drawCard(){
      const pool = CARDS[currentLevel];
      // avoid immediate repeat: filter last one
      const last = history.length ? history[history.length-1].text : null;
      const candidates = pool.filter(c=>c!==last);
      const pick = candidates[Math.floor(Math.random()*candidates.length)];
      const item = {level:currentLevel, text:pick, at:new Date().toISOString()};
      history.unshift(item);
      renderCard(item);
      renderHistory();
    }

    function renderCard(item){
      cardText.textContent = item.text;
      meta.textContent = 'Nível: ' + (item.level==='leve'?'Leve':item.level==='medio'?'Médio':'Profundo') + ' — Fale 3 minutos cada, sem interrupções.';
    }

    function renderHistory(){
      historyList.innerHTML = '';
      if(history.length===0){
        const el = document.createElement('div'); el.className='chip'; el.textContent='Nenhuma carta sorteada ainda'; historyList.appendChild(el); return;
      }
      history.forEach(h=>{
        const el = document.createElement('div'); el.className='chip';
        el.textContent = '[' + (h.level==='leve'?'L':h.level==='medio'?'M':'P') + '] ' + h.text;
        historyList.appendChild(el);
      });
    }

    function resetHistory(){ history = []; renderHistory(); cardText.textContent='Toque em "Sortear Carta" para iniciar.'; }

    function copyCurrent(){
      const text = cardText.textContent;
      if(!text || text.includes('Toque')) return alert('Não há carta para copiar.');
      navigator.clipboard?.writeText(text).then(()=>{
        const prev = shareBtn.textContent;
        shareBtn.textContent='Copiado!';
        setTimeout(()=> shareBtn.textContent = prev, 1200);
      }).catch(()=>alert('Não foi possível copiar.'));
    }

    drawBtn.addEventListener('click', drawCard);
    resetBtn.addEventListener('click', ()=>{ if(confirm('Limpar histórico?')) resetHistory(); });
    shareBtn.addEventListener('click', copyCurrent);

    // initial render
    renderHistory();

    // Accessibility: keyboard shortcuts
    document.addEventListener('keydown', (e)=>{
      if(e.key===' '){ e.preventDefault(); drawCard(); }
      if(e.key==='1') setLevel('leve');
      if(e.key==='2') setLevel('medio');
      if(e.key==='3') setLevel('profundo');
    });
  </script>
</body>
</html>
