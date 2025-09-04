<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Controle de Pontos e Folgas</title>
  <link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@400;600;700&display=swap" rel="stylesheet">
  <style>
    /* ESTILO GERAL */
    body {
      font-family: 'Montserrat', sans-serif;
      margin: 0;
      background: #101418;
      color: #e6e6e6;
      min-height: 100vh;
      display: flex;
      justify-content: center;
      padding: 20px;
    }
    #container {
      width: 100%;
      max-width: 1600px;
      display: flex;
      flex-direction: column;
      gap: 25px;
    }
    h1 {
      text-align: center;
      font-size: 2.2rem;
      color: #2d89ef;
      margin: 0;
    }
    /* GRID PRINCIPAL */
    .columns {
      display: grid;
      grid-template-columns: repeat(3, minmax(300px, 1fr));
      gap: 25px;
      width: 100%;
    }
    .card {
      background: #1c2127;
      padding: 20px;
      border-radius: 12px;
      display: flex;
      flex-direction: column;
    }
    h2 {
      margin: 0 0 15px;
      font-size: 1.3rem;
      border-bottom: 2px solid rgba(255,255,255,0.1);
      padding-bottom: 6px;
      color: #fff;
    }
    /* LISTAS */
    ul, ol {
      list-style: none;
      padding: 0;
      margin: 0;
    }
    li {
      background: #2a3038;
      padding: 10px 12px;
      margin: 6px 0;
      border-radius: 8px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      font-size: 0.95rem;
    }
    li span.name {
      cursor: pointer;
      flex: 1;
    }
    .ranking-item {
      display: flex;
      justify-content: space-between;
      width: 100%;
    }
    .first { color: gold; font-weight: bold; }
    .second { color: silver; font-weight: bold; }
    .third { color: #CD7F32; font-weight: bold; }
    .remove-btn {
      background: transparent;
      border: none;
      color: crimson;
      cursor: pointer;
      margin-left: 10px;
    }
    /* BOTÕES */
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
    }
    .danger { background: crimson; color: #fff; }
    .success { background: #2d89ef; color: #fff; }
    button:hover { opacity: 0.85; }
    /* RESPONSIVIDADE */
    @media(max-width: 1000px){
      .columns {
        grid-template-columns: 1fr;
      }
    }
  </style>
</head>
<body>
  <div id="container">
    <h1>Controle de Pontos & Folgas</h1>
    <div class="columns">
      <!-- COLUNA 1: AÇÕES & VALORES -->
      <div class="card">
        <h2>Ações & Valores</h2>
        <ul id="actionsList">
          <li><span class="name">1. Bom dia/tarde à equipe</span> <button onclick="addPointsToAll(1)">+1</button></li>
          <li><span class="name">2. Entregar tarefa no prazo</span> <button onclick="addPointsToAll(2)">+2</button></li>
          <li><span class="name">3. Ajudar colega</span> <button onclick="addPointsToAll(3)">+3</button></li>
          <li><span class="name">4. Resolver problema crítico</span> <button onclick="addPointsToAll(5)">+5</button></li>
        </ul>
      </div>
      <!-- COLUNA 2: RANKING -->
      <div class="card">
        <h2>Ranking</h2>
        <ol id="rankingList"></ol>
      </div>
      <!-- COLUNA 3: MEMBROS -->
      <div class="card">
        <h2>Membros</h2>
        <ul id="playersList"></ul>
        <div id="actions">
          <button class="success" onclick="addPlayer()">+ Adicionar Membro</button>
          <button class="danger" onclick="resetGame()">Resetar Pontos</button>
        </div>
      </div>
    </div>
  </div>
  <script>
    let players = [
      {name: "Cristian", points: 0},
      {name: "Eduardo", points: 0},
      {name: "Emanuela", points: 0},
      {name: "Pamela", points: 0},
      {name: "Patrick", points: 0},
      {name: "Pedro Henrique", points: 0},
      {name: "Pedro Leite", points: 0},
      {name: "Richard", points: 0},
      {name: "Thais", points: 0}
    ];
    function renderPlayers(){
      const playersList = document.getElementById("playersList");
      playersList.innerHTML = "";
      players.forEach((p,i) => {
        const li = document.createElement("li");
        li.innerHTML = `<span class="name" onclick="addPoints(${i},1)">${p.name} (${p.points} pts)</span>
                        <button class="remove-btn" onclick="removePlayer(${i})">✖</button>`;
        playersList.appendChild(li);
      });
      renderRanking();
    }
    function renderRanking(){
      const rankingList = document.getElementById("rankingList");
      rankingList.innerHTML = "";
      players.sort((a,b)=>b.points-a.points)
             .forEach((p,i)=>{
        const li = document.createElement("li");
        li.classList.add("ranking-item");
        if(i===0) li.classList.add("first");
        else if(i===1) li.classList.add("second");
        else if(i===2) li.classList.add("third");
        li.textContent = `${i+1}º ${p.name} - ${p.points} pts`;
        rankingList.appendChild(li);
      });
    }
    function addPoints(i, pts){
      players[i].points += pts;
      renderPlayers();
    }
    function addPointsToAll(pts){
      players.forEach(p => p.points += pts);
      renderPlayers();
    }
    function addPlayer(){
      const name = prompt("Digite o nome do novo membro:");
      if(name){
        players.push({name, points:0});
        renderPlayers();
      }
    }
    function removePlayer(i){
      if(confirm("Remover este membro?")){
        players.splice(i,1);
        renderPlayers();
      }
    }
    function resetGame(){
      if(confirm("Tem certeza que deseja zerar todos os pontos?")){
        players.forEach(p=>p.points=0);
        renderPlayers();
      }
    }
    renderPlayers();
  </script>
</body>
</html>
