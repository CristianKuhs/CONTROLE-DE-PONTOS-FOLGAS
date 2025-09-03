<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Team Building Scoreboard</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Montserrat:wght@400;600;700&display=swap" rel="stylesheet">
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
  <style>
    :root {
      --bg: #121212;
      --card: #1e1e1e;
      --text: #e0e0e0;
      --highlight: #4a90e2;
      --border: #333;
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
      padding: 20px;
      min-height: 100vh;
    }
    #dashboard {
      background: var(--card);
      padding: 30px;
      border-radius: 12px;
      width: 100%;
      max-width: 950px;
      display: grid;
      gap: 25px;
    }
    h1 {
      text-align: center;
      font-size: 2.4rem;
      color: var(--highlight);
    }
    .card {
      background: var(--card);
      border: 1px solid var(--border);
      border-radius: 10px;
      padding: 20px;
    }
    h2 {
      font-size: 1.4rem;
      margin-top: 0;
      margin-bottom: 15px;
      border-bottom: 2px solid var(--border);
      padding-bottom: 8px;
      display: flex;
      align-items: center;
      gap: 8px;
    }
    ul, ol {
      list-style: none;
      padding: 0;
      margin: 0;
    }
    /* Lista de usuários */
    #users-list li, #queue-list li {
      background: #2b2b2b;
      padding: 10px 15px;
      border-radius: 8px;
      margin-bottom: 8px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      border: 1px solid var(--border);
    }
    #users-list button, #queue-list button {
      background: #d9534f;
      border: none;
      color: white;
      border-radius: 5px;
      padding: 6px 10px;
      cursor: pointer;
    }
    #users-list button:hover, #queue-list button:hover {
      background: #b52b27;
    }
    /* Ranking */
    .ranking-item {
      display: flex;
      justify-content: space-between;
      align-items: center;
    }
    .ranking-item span {
      flex-grow: 1;
      margin-left: 10px;
    }
    .first-place { color: var(--gold); }
    .second-place { color: var(--silver); }
    .third-place { color: var(--bronze); }
    /* Regras/Ações (demonstrativo) */
    .rules-grid{
      display: flex;
      flex-direction: column;
      gap: 12px;
    }
    .rule{
      border:1px solid var(--border);
      background:#232323;
      border-radius:10px;
      padding:12px;
      display:flex;
      justify-content:space-between;
      align-items:center;
      gap:10px;
    }
    .rule .label{ font-weight:600; }
    .rule .value{
      font-weight:800;
      padding:6px 10px;
      border-radius:8px;
      background:#111;
      border:1px solid #444;
      white-space:nowrap;
    }
    .rules-footer{
      margin-top:12px;
      font-size:.85em;
      opacity:.7;
    }
    /* Botão limpar */
    #clear-btn {
      background: #d9534f;
      color: white;
      padding: 12px 24px;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      font-weight: bold;
      display: block;
      margin: 0 auto;
    }
    #clear-btn:hover {
      background: #b52b27;
    }
  </style>
</head>
<body>
  <div id="dashboard">
    <h1>Team Building Scoreboard</h1>
    <!-- Usuários -->
    <div class="card">
      <h2><i class="fas fa-users"></i> Participantes</h2>
      <ul id="users-list"></ul>
      <button onclick="addParticipant()">+ Adicionar Participante</button>
    </div>
    <!-- Fila -->
    <div class="card">
      <h2><i class="fas fa-list-ol"></i> Fila</h2>
      <ul id="queue-list"></ul>
    </div>
    <!-- Ranking -->
    <div class="card">
      <h2><i class="fas fa-trophy"></i> Ranking</h2>
      <ol id="ranking-list"></ol>
    </div>
    <!-- Ações e valores -->
    <div class="card">
      <h2><i class="fas fa-star"></i> Ações & Valores (Demonstrativo)</h2>
      <div class="rules-grid" id="rules-list"></div>
      <div class="rules-footer">
        Esta lista é apenas informativa. A pontuação é inserida manualmente ao clicar no participante.
      </div>
    </div>
    <!-- Botão limpar -->
    <button id="clear-btn">LIMPAR DADOS</button>
  </div>
  <script>
    let users = ["Cristian", "Eduardo", "Emanuela", "Kauane"];
    let scores = JSON.parse(localStorage.getItem("scores")) || {};
    let queue = [];
    users.forEach(u => { if(!(u in scores)) scores[u]=0 });
    const rules = [
      { label:"Bom dia ao chegar (em cada salão)", value:1 },
      { label:"Boa tarde ao retornar/chegar (em cada salão)", value:1 },
      { label:"Tchau ao ir embora (em cada salão)", value:1 },
      { label:"Saudação na cozinha sempre que entrar", value:3 },
      { label:"Passar no Comercial e dar Bom dia/Boa tarde", value:5 }
    ];
    const usersList = document.getElementById("users-list");
    const queueList = document.getElementById("queue-list");
    const rankingList = document.getElementById("ranking-list");
    const rulesList = document.getElementById("rules-list");

    function renderUsers(){
      usersList.innerHTML="";
      users.forEach(user=>{
        const li=document.createElement("li");
        li.innerHTML=`<span>${user} - ${scores[user]||0} pts</span>`;
        const btn=document.createElement("button");
        btn.textContent="Fila";
        btn.onclick=(e)=>{ e.stopPropagation(); addToQueue(user); };
        li.appendChild(btn);
        li.onclick=()=>manualAdd(user);
        usersList.appendChild(li);
      });
      localStorage.setItem("scores",JSON.stringify(scores));
    }
    function renderQueue(){
      queueList.innerHTML="";
      queue.forEach(user=>{
        const li=document.createElement("li");
        li.innerHTML=`<span>${user}</span>`;
        const btn=document.createElement("button");
        btn.textContent="Remover";
        btn.onclick=()=>removeFromQueue(user);
        li.appendChild(btn);
        queueList.appendChild(li);
      });
    }
    function renderRanking(){
      rankingList.innerHTML="";
      const sorted=Object.entries(scores).sort((a,b)=>b[1]-a[1]);
      sorted.forEach(([u,p],i)=>{
        const li=document.createElement("li");
        let cls="";
        if(i===0) cls="first-place"; else if(i===1) cls="second-place"; else if(i===2) cls="third-place";
        li.innerHTML=`<div class="ranking-item ${cls}"><span>${i+1}º - ${u}</span><span>${p} pts</span></div>`;
        rankingList.appendChild(li);
      });
    }

    function renderRules(){
      rulesList.innerHTML="";
      rules.forEach((r,i)=>{
        const div=document.createElement("div");
        div.className="rule";
        div.innerHTML=`<span class="label">${i+1}. ${r.label}</span><span class="value">+${r.value}</span>`;
        rulesList.appendChild(div);
      });
    }
    function manualAdd(user){
      let val=prompt(`Digite a pontuação a adicionar para ${user}:`);
      if(val && !isNaN(val)){
        scores[user]+=parseInt(val);
        renderUsers();renderRanking();
      }
    }
    function addParticipant(){
      let name=prompt("Nome do novo participante:");
      if(name){
        users.push(name);
        scores[name]=0;
        renderUsers();renderRanking();
      }
    }
    function removeParticipant(user){
      if(confirm(`Remover ${user}?`)){
        users=users.filter(u=>u!==user);
        delete scores[user];
        renderUsers();renderRanking();
      }
    }
    function addToQueue(user){
      if(!queue.includes(user)){
        queue.push(user);
        renderQueue();
      }
    }
    function removeFromQueue(user){
      queue=queue.filter(u=>u!==user);
      renderQueue();
    }
    document.getElementById("clear-btn").onclick=()=>{
      if(confirm("Limpar todos os dados?")){
        users.forEach(u=>scores[u]=0);
        queue=[];
        renderUsers();renderRanking();renderQueue();
      }
    }
    renderUsers();renderRanking();renderRules();renderQueue();
  </script>
</body>
</html>
