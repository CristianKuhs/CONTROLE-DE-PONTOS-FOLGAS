<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>Placar de Equipe</title>
  <link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@400;600;700&display=swap" rel="stylesheet">
  <style>
    :root {
      --bg: #101418;
      --card: #1c2127;
      --text: #e6e6e6;
      --gold: #FFD700;
      --silver: #C0C0C0;
      --bronze: #CD7F32;
      --accent: #2d89ef;
    }
    body {
      font-family: 'Montserrat', sans-serif;
      margin: 0;
      background: var(--bg);
      color: var(--text);
      display: flex;
      justify-content: center;
      align-items: flex-start;
      min-height: 100vh;
      padding: 30px 20px;
    }
    #container {
      background: var(--card);
      padding: 30px;
      border-radius: 16px;
      width: 100%;
      max-width: 800px;
      box-shadow: 0 10px 25px rgba(0,0,0,.6);
      display: flex;
      flex-direction: column;
      gap: 24px;
    }
    h1 {
      text-align: center;
      margin: 0 0 10px;
      font-size: 2.4rem;
      color: var(--accent);
      font-weight: 700;
    }
    h2 {
      margin-bottom: 15px;
      font-size: 1.3rem;
      border-bottom: 2px solid rgba(255,255,255,0.1);
      padding-bottom: 5px;
    }
    .card {
      background: #232a32;
      padding: 20px;
      border-radius: 12px;
    }
    ul, ol {
      list-style: none;
      padding: 0;
      margin: 0;
    }
    li {
      background: #2e363f;
      padding: 12px;
      margin: 8px 0;
      border-radius: 8px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      transition: 0.2s;
    }
    li:hover {
      background: #3a4450;
    }
    li span.name {
      cursor: pointer;
      flex: 1;
    }
    .remove-btn {
      background: transparent;
      border: none;
      color: crimson;
      font-size: 1rem;
      cursor: pointer;
      margin-left: 10px;
      transition: 0.2s;
    }
    .remove-btn:hover {
      color: #ff4d4d;
    }
    .ranking-item {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 6px 10px;
      border-radius: 6px;
    }
    .first { color: var(--gold); font-weight: 700; }
    .second { color: var(--silver); font-weight: 700; }
    .third { color: var(--bronze); font-weight: 700; }
    #actions {
      text-align: center;
    }
    button {
      padding: 10px 18px;
      border: none;
      border-radius: 8px;
      font-weight: 600;
      cursor: pointer;
      margin: 5px;
      font-size: 0.95rem;
      transition: 0.3s;
    }
    .danger { background: crimson; color: #fff; }
    .success { background: var(--accent); color: #fff; }
    button:hover { opacity: 0.85; }
    /* Pódio */
    #podium {
      display: flex;
      justify-content: center;
      align-items: flex-end;
      gap: 20px;
      margin-top: 20px;
    }
    .podium-place {
      flex: 1;
      text-align: center;
      border-radius: 10px 10px 0 0;
      padding: 12px;
      font-weight: bold;
      color: #000;
    }
    .first-place { background: var(--gold); height: 130px; }
    .second-place { background: var(--silver); height: 100px; }
    .third-place { background: var(--bronze); height: 80px; }
    /* Regras */
    .rules-grid {
      display: flex;
      flex-direction: column;
      gap: 10px;
    }
    .rule {
      border: 1px solid #333;
      background: #2b323b;
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
    <div class="card">
      <h2>Membros</h2>
      <ul id="user-list"></ul>
      <button class="success" onclick="addParticipant()">Adicionar Participante</button>
    </div>
    <div class="card">
      <h2>Ranking</h2>
      <ol id="ranking"></ol>
      <div id="podium"></div>
    </div>
    <div class="card">
      <h2>Ações & Valores</h2>
      <div class="rules-grid">
       <div class="rule"><span class="label">1. Bom dia/tarde ao chegar (cada salão)</span><span class="value">+1</span></div>
        <div class="rule"><span class="label">2. Bom dia/tarde ao retornar (cada salão)</span><span class="value">+1</span></div>
        <div class="rule"><span class="label">3. Tchau ao ir embora (cada salão)</span><span class="value">+1</span></div>
        <div class="rule"><span class="label">4. Bom dia/tarde na cozinha</span><span class="value">+3</span></div>
        <div class="rule"><span class="label">5. Bom dia/tarde ao passar no Comercial</span><span class="value">+5</span></div>
      </div>
      <p class="note">Clique no nome do participante para adicionar pontos acumulativos.</p>
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
      const addPoints = prompt(`Quantos pontos deseja adicionar a ${user}? (atual: ${scores[user]} pts)`, "0");
      if(addPoints!==null && !isNaN(addPoints)){
        scores[user] += parseInt(addPoints);
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
      if(pass==="admin123"){ 
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
