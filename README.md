<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Horóscopo 2025</title>
  <link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@600&family=Poppins:wght@300;500&display=swap" rel="stylesheet">
  <style>
    /* Fundo estrelado */
    body {
      margin:0;
      font-family:'Poppins', sans-serif;
      background: radial-gradient(circle at top, #1a0033, #000010);
      color:#fff;
      overflow-x:hidden;
    }
    canvas#stars {
      position:fixed;
      top:0; left:0;
      width:100%; height:100%;
      z-index:-1;
    }
    header {
      text-align:center;
      padding:40px 20px;
      background: linear-gradient(90deg, #8000ff, #ff0080);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
    }
    header h1 {
      font-family:'Playfair Display', serif;
      font-size:3em;
      margin:0;
      text-shadow:0 0 20px #ff80ff;
    }
    header p { font-size:1.2em; opacity:0.9; }

    /* Grid signos */
    .grid {
      display:grid;
      grid-template-columns: repeat(auto-fit, minmax(180px,1fr));
      gap:25px;
      padding:30px;
    }
    .card {
      background: linear-gradient(145deg, #220033, #330066);
      border:2px solid rgba(255,0,255,0.3);
      border-radius:15px;
      text-align:center;
      padding:20px;
      transition: transform 0.4s, box-shadow 0.4s;
      cursor:pointer;
      position:relative;
      overflow:hidden;
    }
    .card::before {
      content:"";
      position:absolute;
      top:-50%; left:-50%;
      width:200%; height:200%;
      background: conic-gradient(from 0deg, transparent, rgba(255,0,255,0.3), transparent 30%);
      animation: rotate 6s linear infinite;
    }
    @keyframes rotate {
      to { transform: rotate(360deg); }
    }
    .card:hover {
      transform: scale(1.1);
      box-shadow:0 0 25px rgba(255,0,255,0.7);
    }
    .card .glyph {
      font-size:50px;
      margin-bottom:10px;
      color:#ff80ff;
      text-shadow:0 0 10px #ff00ff;
    }
    .card strong {
      font-size:1.2em;
      color:#fff;
    }
    .card span { font-size:0.9em; display:block; margin-top:5px; opacity:0.8; }

    /* Drawer lateral */
    .drawer {
      position:fixed;
      top:0; right:-420px;
      width:400px; height:100%;
      background:#1a0033;
      color:#fff;
      padding:25px;
      box-shadow:-5px 0 20px rgba(0,0,0,0.6);
      transition:right 0.5s ease;
      overflow-y:auto;
      border-left:3px solid #ff00ff;
      z-index:1000;
    }
    .drawer.open { right:0; }
    .overlay {
      position:fixed;
      top:0; left:0;
      width:100%; height:100%;
      background:rgba(0,0,0,0.6);
      display:none;
      z-index:900;
    }
    .overlay.show { display:block; }

    .drawer h2 { margin:0; font-family:'Playfair Display', serif; color:#ff80ff; }
    .drawer p { opacity:0.8; }
    .tabs { display:flex; gap:8px; margin:15px 0; flex-wrap:wrap; }
    .tab {
      flex:1;
      padding:8px;
      border-radius:8px;
      background:#330066;
      text-align:center;
      cursor:pointer;
      transition:background 0.3s;
    }
    .tab.active { background:#ff00cc; color:#000; font-weight:bold; }
    .content { margin-top:15px; line-height:1.6em; font-size:1em; }
    .btn {
      margin-top:20px;
      padding:10px 20px;
      border:none;
      background:#ff00cc;
      color:#fff;
      font-weight:bold;
      border-radius:8px;
      cursor:pointer;
      transition: background 0.3s;
    }
    .btn:hover { background:#ff33ff; }
  </style>
</head>
<body>
  <canvas id="stars"></canvas>
  <header>
    <h1>Horóscopo 2025</h1>
    <p>Descubra seu destino em Amor, Carreira, Saúde e Finanças ✨</p>
  </header>

  <main>
    <div id="grid" class="grid"></div>
  </main>

  <div class="overlay" id="overlay" onclick="closeDrawer()"></div>
  <aside id="drawer" class="drawer">
    <h2 id="signName"></h2>
    <p id="signDate"></p>
    <div class="tabs">
      <div class="tab active" onclick="showTab('overview',this)">🔮 Geral</div>
      <div class="tab" onclick="showTab('love',this)">❤️ Amor</div>
      <div class="tab" onclick="showTab('career',this)">💼 Carreira</div>
      <div class="tab" onclick="showTab('health',this)">🍀 Saúde</div>
      <div class="tab" onclick="showTab('money',this)">💰 Finanças</div>
    </div>
    <div id="signContent" class="content"></div>
    <button class="btn" onclick="closeDrawer()">Fechar</button>
  </aside>

  <script>
    /* Dados dos 12 signos */
    const SIGNS = {
      aries:{name:"Áries",date:"21 mar – 19 abr",glyph:"♈",
        overview:"Em 2025 você terá coragem para novos começos.",
        love:"Paixões intensas, mas cuidado com impulsividade.",
        career:"Liderança em destaque, aceite desafios.",
        health:"Exercícios vão equilibrar sua energia.",
        money:"Cuidado com compras por impulso."},
      touro:{name:"Touro",date:"20 abr – 20 mai",glyph:"♉",
        overview:"Ano de estabilidade e construção sólida.",
        love:"Relacionamentos duradouros ganham força.",
        career:"Crescimento lento, mas seguro.",
        health:"Equilibre alimentação e prazer.",
        money:"Boa fase para poupar e investir."},
      gemeos:{name:"Gêmeos",date:"21 mai – 20 jun",glyph:"♊",
        overview:"2025 pede foco e disciplina.",
        love:"Novas conexões leves e divertidas.",
        career:"Aproveite comunicação e aprendizado.",
        health:"Cuidado com ansiedade mental.",
        money:"Controle melhor os gastos variáveis."},
      cancer:{name:"Câncer",date:"21 jun – 22 jul",glyph:"♋",
        overview:"Ano de fortalecimento familiar.",
        love:"Afeto profundo e novos laços emocionais.",
        career:"Valorização da sensibilidade no trabalho.",
        health:"Cuide da mente e emoções.",
        money:"Organize finanças com disciplina."},
      leao:{name:"Leão",date:"23 jul – 22 ago",glyph:"♌",
        overview:"2025 brilha com oportunidades de destaque.",
        love:"Romances intensos, brilhe com equilíbrio.",
        career:"Reconhecimento e liderança.",
        health:"Atenção ao coração e energia vital.",
        money:"Momento de ganhos e generosidade."},
      virgem:{name:"Virgem",date:"23 ago – 22 set",glyph:"♍",
        overview:"Organização será chave para conquistas.",
        love:"Relacionamentos estáveis e sinceros.",
        career:"O detalhe faz a diferença no trabalho.",
        health:"Cuide da rotina e alimentação.",
        money:"Controle rígido garante segurança."},
      libra:{name:"Libra",date:"23 set – 22 out",glyph:"♎",
        overview:"Ano de equilíbrio e novas parcerias.",
        love:"Relacionamentos harmoniosos ganham espaço.",
        career:"A diplomacia abre caminhos.",
        health:"Busque equilíbrio físico e mental.",
        money:"Sociedades trazem prosperidade."},
      escorpiao:{name:"Escorpião",date:"23 out – 21 nov",glyph:"♏",
        overview:"2025 traz transformação profunda.",
        love:"Intensidade e novas paixões.",
        career:"Mudanças trazem crescimento.",
        health:"Regeneração será palavra-chave.",
        money:"Controle e estratégia em investimentos."},
      sagitario:{name:"Sagitário",date:"22 nov – 21 dez",glyph:"♐",
        overview:"Expansão e aventuras em 2025.",
        love:"Relacionamentos livres e verdadeiros.",
        career:"Novos horizontes profissionais.",
        health:"Energia alta, pratique esportes.",
        money:"Gastos com viagens, mas bons retornos."},
      capricornio:{name:"Capricórnio",date:"22 dez – 19 jan",glyph:"♑",
        overview:"Ano de disciplina e grandes conquistas.",
        love:"Relacionamentos estáveis crescem.",
        career:"Sucesso por esforço e paciência.",
        health:"Equilibre corpo e mente.",
        money:"Crescimento financeiro sólido."},
      aquario:{name:"Aquário",date:"20 jan – 18 fev",glyph:"♒",
        overview:"Inovação e originalidade em alta.",
        love:"Relacionamentos fora do comum.",
        career:"Criatividade abre oportunidades.",
        health:"Cuide da circulação e respiração.",
        money:"Invista em tecnologia e ideias novas."},
      peixes:{name:"Peixes",date:"19 fev – 20 mar",glyph:"♓",
        overview:"Ano de intuição e espiritualidade.",
        love:"Amor profundo e conexão verdadeira.",
        career:"Criatividade inspira seu trabalho.",
        health:"Atenção ao descanso e equilíbrio.",
        money:"Cuidado com ilusões financeiras."}
    };

    const grid=document.getElementById("grid");
    const drawer=document.getElementById("drawer");
    const overlay=document.getElementById("overlay");
    const signName=document.getElementById("signName");
    const signDate=document.getElementById("signDate");
    const signContent=document.getElementById("signContent");
    let current=null;

    function buildGrid(){
      Object.values(SIGNS).forEach(s=>{
        const c=document.createElement("div");
        c.className="card";
        c.innerHTML=`<div class="glyph">${s.glyph}</div><strong>${s.name}</strong><span>${s.date}</span>`;
        c.onclick=()=>openDrawer(s);
        grid.appendChild(c);
      });
    }
    function openDrawer(s){
      current=s;
      signName.textContent=s.name+" "+s.glyph;
      signDate.textContent=s.date;
      showTab("overview",document.querySelector(".tab"));
      drawer.classList.add("open");
      overlay.classList.add("show");
    }
    function closeDrawer(){
      drawer.classList.remove("open");
      overlay.classList.remove("show");
    }
    function showTab(type,el){
      document.querySelectorAll(".tab").forEach(t=>t.classList.remove("active"));
      el.classList.add("active");
      signContent.textContent=current[type];
    }
    buildGrid();

    /* Estrelas animadas */
    const canvas=document.getElementById("stars"),ctx=canvas.getContext("2d");
    let w,h,stars=[];
    function resize(){ w=canvas.width=window.innerWidth; h=canvas.height=window.innerHeight; }
    window.onresize=resize; resize();
    for(let i=0;i<150;i++) stars.push({x:Math.random()*w,y:Math.random()*h,r:Math.random()*2,o:Math.random()});
    function drawStars(){
      ctx.clearRect(0,0,w,h);
      ctx.fillStyle="white";
      stars.forEach(s=>{
        ctx.globalAlpha=s.o;
        ctx.beginPath(); ctx.arc(s.x,s.y,s.r,0,2*Math.PI); ctx.fill();
        s.o+= (Math.random()*0.05-0.025);
        if(s.o<0) s.o=0; if(s.o>1) s.o=1;
      });
      requestAnimationFrame(drawStars);
    }
    drawStars();
  </script>
</body>
</html>
