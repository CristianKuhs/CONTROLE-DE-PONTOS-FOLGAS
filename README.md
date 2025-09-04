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
      min-height: 100vh;
      display: flex;
      justify-content: center;
      padding: 20px;
    }
    #container {
      width: 100%;
      max-width: 1400px;
      display: flex;
      flex-direction: column;
      gap: 25px;
    }
    h1 {
      text-align: center;
      font-size: 2.2rem;
      color: var(--accent);
      margin: 0;
    }
    .columns {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 20px;
    }
    .card {
      background: var(--card);
      padding: 20px;
      border-radius: 12px;
      display: flex;
      flex-direction: column;
      min-height: 420px; /* mesma altura para todos */
    }
    h2 {
      margin: 0 0 15px;
      font-size: 1.2rem;
      border-bottom: 2px solid rgba(255,255,255,0.15);
      padding-bottom: 6px;
    }
    ul, ol { list-style: none; padding: 0; margin: 0; }
    li {
      background: #2a3038;
      padding: 10px 12px;
      margin: 6px 0;
      border-radius: 8px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      transition: 0.2s;
    }
    li:hover { background: #353c46; }
    li span.name { cursor: pointer; flex: 1; }
    .remove-btn {
      background: transparent;
      border: none;
      color: crimson;
      font-size: 0.9rem;
      cursor: pointer;
    }
    .ranking-item {
      display: flex;
      justify-content: space-between;
      width: 100%;
    }
    .first { color: var(--gold); font-weight: bold; }
    .second { color: var(--silver); font-weight: bold; }
    .third { color: var(--bronze); font-weight: bold; }
    #actions {
      text-align: center;
      margin-top: 10px;
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
    /* Podium */
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
      border-radius: 8px 8px 0 0;
      padding: 8px;
      font-weight: bold;
      color: #000;
    }
    .first-place { background: var(--gold); height: 120px; }
    .second-place { background: var(--silver); height: 90px; }
    .third-place { background: var(--bronze); height: 70px; }
    /* Regras */
    .rules-grid {
      display: flex;
      flex-direction: column;
      gap: 8px;
    }
    .rule {
      background: #2a3038;
      border-radius: 8px;
      padding: 8px 10px;
      display: flex;
      justify-content: space-between;
      font-size: 0.9rem;
    }
    .rule .value {
      font-weight: bold;
      background: #111;
      padding: 2px 8px;
      border-radius: 6px;
    }
    .note {
      margin-top: auto;
      font-size: 0.8em;
      opacity: .7;
      text-align: center;
    }
    @media(max-width: 1000px){
      .columns { grid-template-columns: 1fr; }
      .card { min-height: auto; }
    }
  </style>
</head>
<body>
  <div id="container">
    <h1>Placar da Equipe</h1>
    <div class="columns">
      <!-- Coluna 1 -->
      <div class="card">
        <h2>Ações & Valores</h2>
        <div class="rules-grid">
          <div class="rule"><span>1. Bom dia ao chegar</span><span class="value">+1</span></div>
          <div class="rule"><span>2. Boa tarde ao retornar</span><span class="value">+1</span></div>
          <div class="rule"><span>3. Tchau ao ir embora</span><span class="value">+1</span></div>
          <div class="rule"><span>4. Saudação no caminho</span><span class="value">+1</span></div>
          <div class="rule"><span>5. Ao pedir licença</span><span class="value">+1</span></div>
          <div class="rule"><span>6. Bom trabalho</span><span class="value">+1</span></div>
          <div class="rule"><span>7. Boa noite ao sair</span><span class="value">+1</span></div>
        </div>
        <p class="note">Clique no nome do participante para adicionar pontos.</p>
      </div>
      <!-- Coluna 2 -->
      <div class="card">
        <h2>Ranking</h2>
        <ol id="ranking"></ol>
        <div id="podium"></div>
      </div>
      <!-- Coluna 3 -->
      <div class="card">
        <h2>Membros</h2>
        <ul id="user-list"></ul>
        <button class="success" onclick="addParticipant()">Adicionar Participante</button>
      </div>
    </div>
    <div id="actions">
      <button class="danger" onclick="clearData()">Limpar Dados</button>
    </div>
  </div>
  <script>
    let users = JSON.parse(localStorage.getItem("users")) || ["Cristian","Eduardo","Emanuela","Kauane","Lipe","Pamela"];
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
        save(); renderUsers(); renderRanking();
      }
    }
    function addParticipant(){
      const name = prompt("Nome do participante:");
      if(name && !users.includes(name)){
        users.push(name); scores[name]=0;
        save(); renderUsers(); renderRanking();
      }
    }
    function removeParticipant(user){
      if(confirm(`Remover ${user}?`)){
        users = users.filter(u => u !== user);
        delete scores[user];
        save(); renderUsers(); renderRanking();
      }
    }
    function clearData(){
      const pass = prompt("Senha do admin para limpar:");
      if(pass==="admin123"){ 
        users.forEach(u=>scores[u]=0);
        save(); renderUsers(); renderRanking();
        alert("Dados zerados!");
      } else { alert("Senha incorreta."); }
    }
    renderUsers();
    renderRanking();
  </script>
</body>
</html>
