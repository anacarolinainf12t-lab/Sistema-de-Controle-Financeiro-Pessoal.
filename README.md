# Sistema-de-Controle-Financeiro-Pessoal.

<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<title>Sistema de Controle Financeiro Pessoal</title>
<meta name="viewport" content="width=device-width, initial-scale=1">

<style>
body {
  font-family: Arial, sans-serif;
  background: #f4f6f9;
  margin: 0;
  padding: 20px;
}
.container {
  max-width: 900px;
  margin: auto;
}
h1 {
  margin-bottom: 5px;
}
.subtitle {
  margin-bottom: 20px;
  color: #666;
}
.card {
  background: white;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0px 3px 10px #00000020;
  margin-bottom: 20px;
}
input, select {
  padding: 10px;
  width: 100%;
  margin-top: 5px;
  border-radius: 8px;
  border: 1px solid #ccc;
}
button {
  padding: 10px 15px;
  border-radius: 8px;
  border: none;
  cursor: pointer;
  background: #007bff;
  color: white;
}
button.danger {
  background: #d9534f;
}
button.outline {
  background: white;
  color: #333;
  border: 1px solid #ccc;
}
.row {
  display: flex;
  gap: 10px;
}
.row div {
  flex: 1;
}
table {
  width: 100%;
  border-collapse: collapse;
}
table th, table td {
  padding: 10px;
  border-bottom: 1px solid #eee;
}
.positivo { color: #28a745; font-weight: bold; }
.negativo { color: #d9534f; font-weight: bold; }
</style>
</head>

<body>
<div class="container">
  <h1>Sistema de Controle Financeiro</h1>
  <p class="subtitle">Organize suas receitas e despesas facilmente.</p>

  <div class="card">
    <h2>Adicionar Transação</h2>
    <label>Tipo:</label>
    <select id="tipo">
      <option value="receita">Receita</option>
      <option value="despesa">Despesa</option>
    </select>

    <label>Descrição:</label>
    <input id="descricao">

    <label>Categoria:</label>
    <input id="categoria">

    <div class="row">
      <div>
        <label>Data:</label>
        <input type="date" id="data">
      </div>
      <div>
        <label>Valor (R$):</label>
        <input type="number" id="valor" step="0.01">
      </div>
    </div>

    <br>
    <button onclick="adicionar()">Adicionar</button>
    <button class="outline" onclick="exportar()">Exportar CSV</button>
    <button class="danger" onclick="limpar()">Limpar Tudo</button>
  </div>

  <div class="card">
    <h2>Resumo</h2>
    <p>Receitas: <span id="totalReceitas" class="positivo">R$ 0,00</span></p>
    <p>Despesas: <span id="totalDespesas" class="negativo">R$ 0,00</span></p>
    <p>Saldo: <span id="saldo">R$ 0,00</span></p>
  </div>

  <div class="card">
    <h2>Histórico</h2>
    <table>
      <thead>
        <tr>
          <th>Data</th>
          <th>Descrição</th>
          <th>Categoria</th>
          <th>Tipo</th>
          <th>Valor</th>
        </tr>
      </thead>
      <tbody id="tabela"></tbody>
    </table>
  </div>
</div>

<script>
let lista = [];

function atualizarTabela() {
  let tbody = document.getElementById("tabela");
  tbody.innerHTML = "";

  let receitas = 0;
  let despesas = 0;

  lista.forEach(item => {
    let tr = document.createElement("tr");

    tr.innerHTML = `
      <td>${item.data}</td>
      <td>${item.descricao}</td>
      <td>${item.categoria}</td>
      <td>${item.tipo}</td>
      <td>R$ ${item.valor}</td>
    `;

    tbody.appendChild(tr);

    if (item.tipo === "receita") receitas += item.valor;
    else despesas += item.valor;
  });

  document.getElementById("totalReceitas").innerText = "R$ " + receitas.toFixed(2);
  document.getElementById("totalDespesas").innerText = "R$ " + despesas.toFixed(2);
  document.getElementById("saldo").innerText = "R$ " + (receitas - despesas).toFixed(2);

  localStorage.setItem("financas", JSON.stringify(lista));
}

function adicionar() {
  let item = {
    tipo: document.getElementById("tipo").value,
    descricao: document.getElementById("descricao").value,
    categoria: document.getElementById("categoria").value,
    data: document.getElementById("data").value,
    valor: parseFloat(document.getElementById("valor").value)
  };

  lista.push(item);
  atualizarTabela();
}

function exportar() {
  let csv = "tipo,descricao,categoria,data,valor\n";
  lista.forEach(i => {
    csv += `${i.tipo},${i.descricao},${i.categoria},${i.data},${i.valor}\n`;
  });

  let blob = new Blob([csv], { type: "text/csv" });
  let url = URL.createObjectURL(blob);

  let a = document.createElement("a");
  a.href = url;
  a.download = "financas.csv";
  a.click();
}

function limpar() {
  lista = [];
  atualizarTabela();
}

window.onload = () => {
  lista = JSON.parse(localStorage.getItem("financas") || "[]");
  atualizarTabela();
}
</script>

</body>
</html>
