<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Placar Team Building</title>
  <style>
    :root {
      --bg: #121212;
      --card: #1e1e1e;
      --border: #333;
      --text: #f1f1f1;
      --accent: #4cafef;
      --danger: #e74c3c;
      --success: #2ecc71;
    }
    body {
      font-family: Arial, sans-serif;
      background: var(--bg);
      color: var(--text);
      margin: 0;
      padding: 20px;
    }
    h1 {
      text-align: center;
      margin-bottom: 20px;
    }
    .container {
      display: grid;
      gap: 20px;
      max-width: 900px;
      margin: auto;
    }
    .card {
      background: var(--card);
      padding: 20px;
      border-radius: 12px;
      border: 1px solid var(--border);
      box-shadow: 0 2px 8px rgba(0,0,0,0.4);
    }
    .card h2 {
      margin-top: 0;
      margin-bottom: 15px;
      font-size: 1.3em;
      border-bottom: 1px solid var(--border);
      padding-bottom: 5px;
    }
    input, select, button {
      padding: 8px;
      border-radius: 6px;
      border: none;
      margin-right: 5px;
    }
    button {
      cursor: pointer;
      background: var(--accent);
      color: #fff;
      font-weight: bold;
      transition: 0.2s;
    }
    button:hover {
      opacity: 0.9;
    }
    .danger { background: var(--danger); }
    .success { background: var(--success); }
    ul { list-style: none; padding: 0; margin: 0; }
    li { margin-bottom: 8px; }
    .participant {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 6px 10px;
      background: #2a2a2a;
      border-radius: 6px;
    }
    .participant span { flex: 1; }
    .score { font-weight: bold; margin: 0 10px; }
    /* Ações & Valores */
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
  </style>
</head>
<body>
  <h1>Placar Team Building</h1>
  <div class="container">
    <!-- Participantes -->
    <div class="card">
      <h2>Adicionar Participante</h2>
      <input type="text" id="participantName" placeholder="Nome do participante">
      <button onclick="addParticipant()">Adicionar</button>
    </div>
    <div class="card">
      <h2>Participantes</h2>
      <ul id="participantsList"></ul>
    </div>
    <!-- Fila -->
    <div class="card">
      <h2>Fila</h2>
      <ul id="queueList"></ul>
    </div>
    <!-- Ações & Valores -->
    <div class="card">
      <h2>Ações & Valores (Demonstrativo)</h2>
      <div class="rules-grid">
        <div class="rule"><span class="label">1. Bom dia ao chegar (em cada salão)</span><span class="value">+1</span></div>
        <div class="rule"><span class="label">2. Boa tarde ao retornar/chegar</span><span class="value">+1</span></div>
        <div class="rule"><span class="label">3. Tchau ao ir embora (em cada salão)</span><span class="value">+1</span></div>
        <div class="rule"><span class="label">4. Saudação no caminho</span><span class="value">+1</span></div>
        <div class="rule"><span class="label">5. Ao deixar passar na frente</span><span class="value">+2</span></div>
        <div class="rule"><span class="label">6. Outros gestos positivos</span><span class="value">+2</span></div>
      </div>
      <p style="opacity:0.7; margin-top:10px; font-size:0.9em;">Esta lista é apenas informativa. A pontuação é inserida manualmente ao clicar no participante.</p>
    </div>
  </div>

  <script>
    const participants = [];
    const queue = [];

    function renderParticipants() {
      const list = document.getElementById("participantsList");
      list.innerHTML = "";
      participants.forEach((p, i) => {
        const li = document.createElement("li");
        li.className = "participant";
        li.innerHTML = `
          <span>${p.name}</span>
          <span class="score">${p.score}</span>
          <button class="success" onclick="addToQueue(${i})">Fila</button>
          <button onclick="addPoint(${i})">+1</button>
          <button onclick="removePoint(${i})">-1</button>
        `;
        list.appendChild(li);
      });
    }

    function renderQueue() {
      const list = document.getElementById("queueList");
      list.innerHTML = "";
      queue.forEach((q, i) => {
        const li = document.createElement("li");
        li.className = "participant";
        li.innerHTML = `
          <span>${q.name}</span>
          <button class="danger" onclick="removeFromQueue(${i})">Remover</button>
        `;
        list.appendChild(li);
      });
    }

    function addParticipant() {
      const name = document.getElementById("participantName").value.trim();
      if(name){
        participants.push({ name, score:0 });
        document.getElementById("participantName").value="";
        renderParticipants();
      }
    }

    function addToQueue(index){
      const p = participants[index];
      if(!queue.includes(p)){
        queue.push(p);
        renderQueue();
      }
    }

    function removeFromQueue(index){
      queue.splice(index,1);
      renderQueue();
    }

    function addPoint(index){
      participants[index].score++;
      renderParticipants();
    }

    function removePoint(index){
      participants[index].score--;
      renderParticipants();
    }
  </script>
</body>
</html>
