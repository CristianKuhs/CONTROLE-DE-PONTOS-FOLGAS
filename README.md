<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>Placar de Equipe</title>
  <link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@400;600;700&display=swap" rel="stylesheet">
  <style>
    :root {
      --bg: #121212;
      --card: #1e1e1e;
      --text: #f0f0f0;
      --gold: #FFD700;
      --silver: #C0C0C0;
      --bronze: #CD7F32;
    }
    body {
      font-family: 'Montserrat', sans-serif;
      margin: 0;
      background: var(--bg);
      color: var(--text);
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      padding: 20px;
    }
    #container {
      background: var(--card);
      padding: 25px;
      border-radius: 14px;
      width: 100%;
      max-width: 950px;
      box-shadow: 0 10px 25px rgba(0,0,0,.5);
      display: grid;
      grid-template-columns: 1fr 1fr;
      grid-template-areas:
        "header header"
        "users ranking"
        "rules rules"
        "actions actions";
      gap: 20px;
    }
    h1 {
      grid-area: header;
      text-align: center;
      margin: 0 0 20px;
      font-size: 2.2rem;
      color: var(--gold);
    }
    .card {
      background: #222;
      padding: 15px;
      border-radius: 10px;
    }
    ul {
      list-style: none;
      padding: 0;
      margin: 0;
    }
    li {
      background: #2c2c2c;
      padding: 12px;
      margin: 6px 0;
      border-radius: 8px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      transition: 0.2s;
    }
    li span.name {
      cursor: pointer;
      flex: 1;
    }
    li:hover {
      background: #444;
    }
    .remove-btn {
      background: transparent;
      border: none;
      color: crimson;
      font-size: 1rem;
      cursor: pointer;
      margin-left: 10px;
    }
    .remove-btn:hover {
      color: #ff4d4d;
    }
    .ranking-item {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 8px;
      border-radius: 6px;
    }
    .first { color: var(--gold); font-weight: 700; }
    .second { color: var(--silver); font-weight: 700; }
    .third { color: var(--bronze); font-weight: 700; }
    #actions {
      grid-area: actions;
      text-align: center;
    }
    button {
      padding: 10px 18px;
      border: none;
      border-radius: 8px;
      font-weight: 600;
      cursor: pointer;
      margin: 5px;
    }
    .danger { background: crimson; color: #fff; }
    .success { background: seagreen; color: #fff; }
    /* Pódio */
    #podium {
      display: flex;
      justify-content: center;
      align-items: flex-end;
      gap: 15px;
      margin-top: 15px;
    }
    .podium-place {
      flex: 1;
      text-align: center;
      border-radius: 10px 10px 0 0;
      padding: 10px;
      font-weight: bold;
    }
    .first-place { background: var(--gold); height: 120px; }
    .second-place { background: var(--silver); height: 90px; }
    .third-place { background: var(--bronze); height: 70px; }

    /* Regras / Ações */
    #rules {
      grid-area: rules;
    }
    .rules-grid {
      display: flex;
      flex-direction: column;
      gap: 10px;
    }
    .rule {
      border: 1px solid #333;
      background: #232323;
      border-radius: 10px;
      padding: 10px 14px;
      display: flex;
      justify-content: space-between;
      align-items: center;
    }
    .rule .label {
      font-weight: 600;
    }
    .rule .value {
      font-weight: 800;
      padding: 6px 10px;
      border-radius: 8px;
      background: #111;
      border: 1px solid #444;
      white-space: nowrap;
    }
    .note {
      margin-top: 10px;
      font-size: 0.85em;
      opacity: .75;
      text-align: center;
    }
  </style>
</head>
<body>
  <div id="container">
    <h1>Placar da Equipe</h1>
    <div class="card" style="grid-area:users;">
      <h2>Membros</h2>
      <ul id="user-list"></ul>
      <button class="success" onclick="addParticipant()">Adicionar Participante</button>
    </div>
    <div class="card" style="grid-area:ranking;">
      <h2>Ranking</h2>
      <ol id="ranking"></ol>
      <div id="podium"></div>
    </div>
    <div class="card" id="rules">
      <h2>Ações & Valores (Demonstrativo)</h2>
      <div class="rules-grid">
        <div class="rule"><span class="label">1. Bom dia ao chegar (cada salão)</span><span class="value">+1</span></div>
        <div class="rule"><span class="label">2. Boa tarde ao retornar (cada salão)</span><span class="value">+1</span></div>
        <div class="rule"><span class="label">3. Tchau ao ir embora (cada salão)</span><span class="value">+1</span></div>
        <div class="rule"><span class="label">4. Saudação no caminho</span><span class="value">+1</span></div>
        <div class="rule"><span class="label">5. Ao desejar passar (pedindo licença)</span><span class="value">+1</span></div>
        <div class="rule"><span class="label">6. Bom trabalho</span><span class="value">+1</span></div>
        <div class="rule"><span class="label">7. Boa noite ao sair (após expediente)</span><span class="value">+1</span></div>
      </div>
      <p class="note">Esta lista é apenas informativa. A pontuação é inserida manualmente ao clicar no participante.</p>
    </div>

    <div id="actions">
      <button class="danger" onclick="clearData()">Limpar Dados</button>
    </div>
  </div>
  <script>
    let users = JSON.parse(localStorage.getItem("users")) || [
      "Cristian","Eduardo","Emanuela","Kauane","Lipe","Pamela"
    ];
    let scores = JSON.parse(localStorage.getItem("scores")) || {};
    users.forEach(u => { if(scores[u]===undefined) scores[u]=0 });

    function save() {
      localStorage.setItem("users", JSON.stringify(users));
      localStorage.setItem("scores", JSON.stringify(scores));
    }
    function renderUsers() {
      const ul = document.getElementById("user-list");
      ul.innerHTML = "";
      users.forEach(user => {
        const li = document.createElement("li");
        li.innerHTML = `
          <span class="name" onclick="editScore('${user}')">${user} - ${scores[user]} pts</span>
          <button class="remove-btn" onclick="removeParticipant('${user}')">🗑️</button>
        `;
        ul.appendChild(li);
      });
    }
    function renderRanking() {
      const sorted = [...users].sort((a,b)=>scores[b]-scores[a]);
      const ol = document.getElementById("ranking");
      ol.innerHTML = "";
      sorted.forEach((u,i)=>{
        const li = document.createElement("li");
        li.className = i===0?"first":i===1?"second":i===2?"third":"";
        li.innerHTML = `<div class="ranking-item"><span>${i+1}º</span><span>${u}</span><span>${scores[u]} pts</span></div>`;
        ol.appendChild(li);
      });
      renderPodium(sorted);
    }
    function renderPodium(sorted){
      const podium = document.getElementById("podium");
      podium.innerHTML = "";
      if(sorted[1]) podium.innerHTML += `<div class="podium-place second-place">${sorted[1]}<br>${scores[sorted[1]]} pts</div>`;
      if(sorted[0]) podium.innerHTML += `<div class="podium-place first-place">${sorted[0]}<br>${scores[sorted[0]]} pts</div>`;
      if(sorted[2]) podium.innerHTML += `<div class="podium-place third-place">${sorted[2]}<br>${scores[sorted[2]]} pts</div>`;
    }
    function editScore(user){
      const newScore = prompt(`Digite a nova pontuação para ${user}:`, scores[user]);
      if(newScore!==null && !isNaN(newScore)){
        scores[user] = parseInt(newScore);
        save();
        renderUsers();
        renderRanking();
      }
    }
    function addParticipant(){
      const name = prompt("Nome do participante:");
      if(name && !users.includes(name)){
        users.push(name);
        scores[name]=0;
        save();
        renderUsers();
        renderRanking();
      }
    }
    function removeParticipant(user){
      if(confirm(`Deseja remover ${user} da lista?`)){
        users = users.filter(u => u !== user);
        delete scores[user];
        save();
        renderUsers();
        renderRanking();
      }
    }
    function clearData(){
      const pass = prompt("Digite a senha do administrador para limpar:");
      if(pass==="admin123"){ // senha simples para demo
        users.forEach(u=>scores[u]=0);
        save();
        renderUsers();
        renderRanking();
        alert("Dados zerados com sucesso!");
      } else {
        alert("Senha incorreta. Operação cancelada.");
      }
    }
    renderUsers();
    renderRanking();
  </script>
</body>
</html>
