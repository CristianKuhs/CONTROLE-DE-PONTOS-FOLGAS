<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Placar da Equipe</title>
  <link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@400;600;700&display=swap" rel="stylesheet">
  <style>
    :root{
      --bg:#121212; --card:#1e1e1e; --muted:#2c2c2c; --text:#f0f0f0;
      --gold:#FFD700; --silver:#C0C0C0; --bronze:#CD7F32;
      --danger:#d9534f; --danger-hover:#c9302c; --success:#2e7d32;
      --border:#333;
    }
    *{box-sizing:border-box}
    body{
      margin:0; font-family:Montserrat,system-ui,-apple-system,Segoe UI,Roboto,Ubuntu,"Helvetica Neue",Arial;
      background:var(--bg); color:var(--text);
      min-height:100vh; display:flex; align-items:center; justify-content:center; padding:20px;
    }
    #container{
      width:100%; max-width:1100px; background:var(--card); border:1px solid var(--border);
      border-radius:16px; box-shadow:0 24px 60px rgba(0,0,0,.45);
      padding:24px;
      display:grid; gap:20px;
      grid-template-columns: 1fr 1fr;
      grid-template-areas:
        "header header"
        "users ranking"
        "actions actions"
        "rules rules";
    }
    h1{
      grid-area:header; margin:0; text-align:center; font-size:2.4rem; letter-spacing:.5px; color:var(--gold);
    }
    .card{
      background:#202020; border:1px solid var(--border); border-radius:12px; padding:16px;
    }
    .card h2{margin:0 0 12px; font-size:1.2rem; font-weight:700; opacity:.95}
    #users-card{grid-area:users}
    #ranking-card{grid-area:ranking}
    #actions{grid-area:actions; text-align:center}
    #rules-card{grid-area:rules}
    /* Lista de usuários */
    ul,ol{list-style:none; margin:0; padding:0}
    .user-item{
      display:flex; align-items:center; gap:12px; justify-content:space-between;
      background:var(--muted); border:1px solid var(--border); border-radius:10px; padding:10px 12px; margin:8px 0;
    }
    .user-left{display:flex; align-items:center; gap:10px; min-width:0}
    .user-name{
      cursor:pointer; font-weight:600; white-space:nowrap; overflow:hidden; text-overflow:ellipsis;
    }
    .user-points{opacity:.85; font-weight:600}
    .btn{
      border:none; border-radius:10px; padding:10px 14px; font-weight:700; cursor:pointer;
      transition:.2s transform ease, .2s opacity ease, .2s background ease;
    }
    .btn:active{transform:scale(.98)}
    .btn-success{background:var(--success); color:#fff}
    .btn-danger{background:var(--danger); color:#fff}
    .btn-danger:hover{background:var(--danger-hover)}
    .icon-btn{
      background:transparent; border:1px solid var(--border); color:#ff6b6b; padding:6px 10px; border-radius:8px; cursor:pointer;
    }
    .icon-btn:hover{background:#3a3a3a}
    .muted{opacity:.7}
    /* Ranking */
    .ranking-row{
      display:flex; align-items:center; justify-content:space-between;
      border:1px solid var(--border); background:#232323; border-radius:10px; padding:8px 12px; margin:8px 0;
    }
    .ranking-row.first{color:var(--gold); font-weight:700}
    .ranking-row.second{color:var(--silver); font-weight:700}
    .ranking-row.third{color:var(--bronze); font-weight:700}
    .rank-badge{width:36px; text-align:center; font-weight:800}
    /* Pódio */
    #podium{
      display:flex; align-items:flex-end; justify-content:center; gap:16px; margin-top:10px;
    }
    .podium-col{
      flex:1; text-align:center; border-radius:12px 12px 0 0; padding:10px; min-width:140px;
      display:flex; flex-direction:column; gap:6px; box-shadow:0 10px 20px rgba(0,0,0,.35);
    }
    .podium-col .medal{
      display:inline-block; margin:0 auto; font-weight:800; border-radius:999px; padding:6px 12px; background:rgba(0,0,0,.18);
    }
    .p1{background:var(--gold); height:130px; color:#3d2f00}
    .p2{background:var(--silver); height:100px; color:#2f2f2f}
    .p3{background:var(--bronze); height:80px; color:#3a220f}
    /* Regras/Ações (demonstrativo) */
    .rules-grid{
      display:grid; grid-template-columns: repeat(auto-fit, minmax(240px, 1fr)); gap:12px;
    }
    .rule{
      border:1px solid var(--border); background:#232323; border-radius:10px; padding:12px;
      display:flex; justify-content:space-between; align-items:flex-start; gap:10px;
    }
    .rule .label{font-weight:600}
    .rule .value{font-weight:800; padding:6px 10px; border-radius:8px; background:#111; border:1px solid #444}
    .rule small{display:block; opacity:.7; margin-top:4px}
    /* Responsivo */
    @media (max-width: 880px){
      #container{
        grid-template-columns: 1fr;
        grid-template-areas:
          "header"
          "users"
          "ranking"
          "actions"
          "rules";
      }
    }
  </style>
</head>
<body>
  <div id="container">
    <h1>Placar da Equipe</h1>
    <section id="users-card" class="card">
      <h2>Membros</h2>
      <ul id="user-list"></ul>
      <div style="display:flex; gap:8px; flex-wrap:wrap; margin-top:8px">
        <button id="btn-add" class="btn btn-success" type="button">Adicionar Participante</button>
      </div>
      <p class="muted" style="margin-top:8px">Clique no <strong>nome</strong> para editar a pontuação manualmente.</p>
    </section>
    <section id="ranking-card" class="card">
      <h2>Ranking</h2>
      <ol id="ranking"></ol>
      <div id="podium" aria-label="Pódio dos 3 primeiros"></div>
    </section>
    <section id="actions" class="card">
      <h2>Ações do Sistema</h2>
      <button id="btn-clear" class="btn btn-danger" type="button">Limpar Dados</button>
      <p class="muted" style="margin-top:6px">Zera apenas as pontuações. Exige confirmação de administrador.</p>
    </section>
    <section id="rules-card" class="card">
      <h2>Ações & Valores (Demonstrativo)</h2>
      <div id="rules" class="rules-grid"></div>
      <p class="muted" style="margin-top:6px">Esta lista é apenas informativa. A pontuação é inserida manualmente ao clicar no participante.</p>
    </section>
  </div>
  <script>
    // ----- Configuração (demonstrativa, sem efeito automático) -----
    const ACTIONS = [
      { label: "Bom dia ao chegar (em cada salão)", value: 1 },
      { label: "Boa tarde ao retornar/chegar (em cada salão)", value: 1 },
      { label: "Tchau ao ir embora (em cada salão)", value: 1 },
      { label: "Saudação na cozinha ao entrar (se tiver pessoas)", value: 3 },
      { label: "Ao descer, passar no Comercial e cumprimentar", value: 5 }
    ];
    // ----- Estado persistente -----
    let users = JSON.parse(localStorage.getItem("users")) || [
      "Cristian","Eduardo","Emanuela","Kauane","Lipe","Pamela","Patrick","Pedro Henrique","Pedro Leite","Richard","Thais","Vitória"
    ];
    let scores = JSON.parse(localStorage.getItem("scores")) || {};
    users.forEach(u => { if (typeof scores[u] !== "number") scores[u] = 0; });
    // ----- Util -----
    const $ = sel => document.querySelector(sel);
    function save(){
      localStorage.setItem("users", JSON.stringify(users));
      localStorage.setItem("scores", JSON.stringify(scores));
    }
    // ----- Render: Usuários -----
    function renderUsers(){
      const list = $("#user-list");
      list.innerHTML = "";
      users.forEach(user => {
        const li = document.createElement("li");
        li.className = "user-item";
        const left = document.createElement("div");
        left.className = "user-left";
        const name = document.createElement("span");
        name.className = "user-name";
        name.textContent = user;
        name.title = "Clique para editar a pontuação";
        name.addEventListener("click", () => editScore(user));
        const points = document.createElement("span");
        points.className = "user-points";
        points.textContent = `${scores[user]} pts`;
        left.appendChild(name);
        left.appendChild(points);
        const removeBtn = document.createElement("button");
        removeBtn.className = "icon-btn";
        removeBtn.setAttribute("aria-label", `Remover ${user}`);
        removeBtn.textContent = "🗑️";
        removeBtn.addEventListener("click", () => removeParticipant(user));
        li.appendChild(left);
        li.appendChild(removeBtn);
        list.appendChild(li);
      });
    }
    // ----- Render: Ranking + Pódio -----
    function renderRanking(){
      const sorted = [...users].sort((a,b)=> (scores[b]||0) - (scores[a]||0));
      const list = $("#ranking");
      list.innerHTML = "";
      sorted.forEach((u,i)=>{
        const row = document.createElement("li");
        row.className = `ranking-row ${i===0?"first":i===1?"second":i===2?"third":""}`;
        row.innerHTML = `
          <span class="rank-badge">${i+1}º</span>
          <span style="flex:1">${u}</span>
          <strong>${scores[u]} pts</strong>
        `;
        list.appendChild(row);
      });
      renderPodium(sorted);
    }
    function renderPodium(sorted){
      const podium = $("#podium");
      podium.innerHTML = "";
      const first = sorted[0], second = sorted[1], third = sorted[2];
      if (second){
        const c2 = document.createElement("div");
        c2.className = "podium-col p2";
        c2.innerHTML = `<span class="medal">🥈 2º</span><div><strong>${second}</strong></div><small>${scores[second]} pts</small>`;
        podium.appendChild(c2);
      }
      if (first){
        const c1 = document.createElement("div");
        c1.className = "podium-col p1";
        c1.innerHTML = `<span class="medal">🥇 1º</span><div><strong>${first}</strong></div><small>${scores[first]} pts</small>`;
        podium.appendChild(c1);
      }
      if (third){
        const c3 = document.createElement("div");
        c3.className = "podium-col p3";
        c3.innerHTML = `<span class="medal">🥉 3º</span><div><strong>${third}</strong></div><small>${scores[third]} pts</small>`;
        podium.appendChild(c3);
      }
    }
    // ----- Render: Ações (somente visual) -----
    function renderRules(){
      const wrap = $("#rules");
      wrap.innerHTML = "";
      ACTIONS.forEach((a, idx)=>{
        const item = document.createElement("div");
        item.className = "rule";
        item.innerHTML = `
          <div>
            <div class="label">${idx+1}. ${a.label}</div>
            <small>Exemplo de valor sugerido</small>
          </div>
          <div class="value">+${a.value}</div>
        `;
        wrap.appendChild(item);
      });
    }
    // ----- Ações -----
    function editScore(user){
      const current = scores[user] ?? 0;
      const input = prompt(`Digite a nova pontuação para ${user}:`, current);
      if (input === null) return;
      const parsed = parseInt(String(input).trim(), 10);
      if (Number.isNaN(parsed)){ alert("Valor inválido."); return; }
      scores[user] = parsed;
      save(); renderUsers(); renderRanking();
    }
    function addParticipant(){
      const name = prompt("Nome do participante:");
      if(!name) return;
      const clean = name.trim();
      if(!clean){ alert("Nome vazio."); return; }
      if(users.includes(clean)){ alert("Já existe um participante com esse nome."); return; }
      users.push(clean);
      scores[clean] = 0;
      save(); renderUsers(); renderRanking();
    }
    function removeParticipant(user){
      if (!confirm(`Remover ${user} da lista?`)) return;
      users = users.filter(u => u !== user);
      delete scores[user];
      save(); renderUsers(); renderRanking();
    }
    function clearData(){
      if (!confirm("Tem certeza que deseja zerar as pontuações?")) return;
      const pass = prompt("Confirmação do admin: digite a senha.");
      if (pass !== "admin123"){ alert("Senha incorreta. Operação cancelada."); return; }
      users.forEach(u => scores[u] = 0);
      save(); renderUsers(); renderRanking();
      alert("Pontuações zeradas com sucesso!");
    }
    // ----- Listeners -----
    $("#btn-add").addEventListener("click", addParticipant);
    $("#btn-clear").addEventListener("click", clearData);
    // ----- Inicialização -----
    renderUsers();
    renderRanking();
    renderRules();
  </script>
</body>
</html>
