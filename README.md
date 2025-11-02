<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dashboard Financeiro Pessoal</title>
    
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

    <style>
        /* --- 1. ESTILOS GERAIS E LAYOUT --- */
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f4f7f6;
            color: #333;
        }

        .container {
            max-width: 1200px;
            margin: 40px auto;
            padding: 0 20px;
        }

        header {
            text-align: center;
            margin-bottom: 30px;
            padding: 20px;
            background-color: #ffffff;
            border-radius: 10px;
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
        }

        h1 {
            color: #007bff;
            margin: 0;
            font-weight: 700;
        }

        h2 {
            border-bottom: 2px solid #e0e0e0;
            padding-bottom: 10px;
            margin-top: 0;
            color: #444;
            font-weight: 600;
        }
        
        h3 {
            font-size: 1em;
            text-transform: uppercase;
            color: #666;
            margin-top: 0;
        }

        .dashboard-section {
            background-color: #ffffff;
            padding: 30px;
            border-radius: 10px;
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
            margin-bottom: 30px;
        }

        /* --- 2. ESTILOS DOS CARDS DE VISÃO GERAL --- */
        #summary-cards {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 25px;
            margin-bottom: 30px;
        }

        .card {
            padding: 25px;
            border-radius: 8px;
            text-align: center;
            border: 1px solid #e0e0e0;
            transition: transform 0.2s;
        }

        .card:hover {
            transform: translateY(-3px);
            box-shadow: 0 6px 15px rgba(0, 0, 0, 0.1);
        }

        .card p {
            font-size: 2.5em;
            margin: 10px 0 0 0;
            font-weight: 700;
        }

        #card-balance p { color: #007bff; }
        #card-in p { color: #4caf50; }
        #card-out p { color: #f44336; }
        
        /* Botões de Ação Geral */
        .general-actions {
            display: flex;
            gap: 15px;
            margin-top: 20px;
            flex-wrap: wrap; /* Para melhor responsividade */
        }
        
        .general-actions button {
            padding: 12px 20px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 1em;
            font-weight: bold;
            transition: opacity 0.2s;
        }

        .general-actions .add-btn {
            background-color: #007bff;
            color: white;
            flex-grow: 1;
        }
        
        /* NOVO ESTILO: BOTÃO TRANSFERIR */
        .general-actions .transfer-btn {
            background-color: #ff9800; /* Laranja */
            color: white;
            flex-grow: 1;
        }

        .general-actions .delete-all-btn {
            background-color: #f44336;
            color: white;
        }

        .general-actions button:hover {
            opacity: 0.9;
        }

        /* --- 3. ESTILOS DE FORMULÁRIO E MODAIS --- */
        .modal {
            display: none;
            position: fixed;
            z-index: 10;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            overflow: auto;
            background-color: rgba(0,0,0,0.5);
            backdrop-filter: blur(2px);
        }

        .modal-content {
            background-color: #fefefe;
            margin: 10% auto;
            padding: 30px;
            border-radius: 10px;
            width: 80%;
            max-width: 500px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
        }

        .close-btn {
            color: #aaa;
            float: right;
            font-size: 28px;
            font-weight: bold;
        }

        .close-btn:hover,
        .close-btn:focus {
            color: #000;
            text-decoration: none;
            cursor: pointer;
        }
        
        form label {
            display: block;
            margin-top: 15px;
            margin-bottom: 5px;
            font-weight: 600;
        }

        form input[type="text"],
        form input[type="number"],
        form select,
        form input[type="date"] {
            width: 100%;
            padding: 10px;
            margin-bottom: 10px;
            border: 1px solid #ccc;
            border-radius: 4px;
            box-sizing: border-box;
        }

        form button.submit-btn {
            background-color: #007bff;
            color: white;
            padding: 12px 20px;
            margin-top: 15px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 1em;
            width: 100%;
            transition: background-color 0.2s;
        }
        
        /* Estilo específico para botão de Transferência no Modal */
        #transfer-submit-btn {
            background-color: #ff9800 !important;
        }
        #transfer-submit-btn:hover {
            background-color: #e68900 !important;
        }


        form button.submit-btn:hover {
            background-color: #0056b3;
        }
        
        /* --- 4. CLASSES DE COR PARA TEXTOS E VALORES --- */
        .positive { color: #4caf50; font-weight: 600; }
        .negative { color: #f44336; font-weight: 600; }

        /* --- 5. VISUALIZAÇÃO DE TRANSAÇÕES E LISTAS --- */
        #transaction-list-body, #savings-list-body {
            font-size: 0.9em;
        }
        
        /* --- 6. ATUALIZAÇÕES VISUAIS NA SEÇÃO DE INVESTIMENTOS --- */
        #investment-actions {
            display: flex;
            gap: 15px;
            margin-bottom: 30px;
        }

        #investment-actions button {
            padding: 12px 25px;
            border-radius: 8px;
            font-weight: bold;
            cursor: pointer;
            transition: background-color 0.2s, transform 0.1s;
            font-size: 1em;
            border: none;
            flex-grow: 1;
        }

        #aporte-btn { background-color: #e8f5e9; color: #388e3c; border: 1px solid #c8e6c9; }
        #aporte-btn:hover { background-color: #c8e6c9; transform: translateY(-1px); }
        #resgate-btn { background-color: #ffebee; color: #d32f2f; border: 1px solid #ffcdd2; }
        #resgate-btn:hover { background-color: #ffcdd2; transform: translateY(-1px); }
        
        #investment-cards-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(280px, 1fr)); 
            gap: 25px;
            margin-bottom: 25px;
        }

        .investment-card-in p { color: #4caf50 !important; }
        .investment-card-out p { color: #f44336 !important; }

        .investment-card-total { background-color: #e3f2fd; border: 1px solid #2196f3; }
        .investment-card-total p { color: #2196f3 !important; font-size: 2.8em; }

        .info {
            border-left: 5px solid #2196f3;
            background-color: #f0f8ff;
            padding: 15px;
            margin: 15px 0;
            border-radius: 4px;
            font-size: 0.95em;
            color: #333;
        }

        .info ul { margin-top: 10px; padding-left: 20px; }
        .info li { margin-bottom: 5px; }

        /* --- 7. ESTILOS GERAIS DE TABELA PARA RELATÓRIOS --- */
        .report-table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 15px;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
            background-color: #ffffff;
            border-radius: 8px;
            overflow: hidden;
        }

        .report-table thead th {
            background-color: #007bff;
            color: white;
            padding: 15px;
            text-align: left;
            font-weight: 600;
        }

        .report-table tbody tr { border-bottom: 1px solid #eeeeee; transition: background-color 0.2s; }
        .report-table tbody tr:hover { background-color: #f7f7f7; }
        .report-table tbody td { padding: 12px 15px; vertical-align: middle; }
        .report-table .value-col.positive { color: #388e3c; font-weight: 500; }
        .report-table .value-col.negative { color: #d32f2f; font-weight: 500; }
        
        .action-btn {
            padding: 5px 8px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            margin-right: 5px;
            font-size: 0.8em;
            font-weight: 600;
            transition: opacity 0.2s;
        }

        .action-btn.edit { background-color: #ffc107; color: #333; }
        .action-btn.delete { background-color: #f44336; color: white; }
        .action-btn:hover { opacity: 0.8; }

        /* Responsividade */
        @media (max-width: 768px) {
            .container { margin: 20px auto; }
            .dashboard-section { padding: 20px; }
            #summary-cards, #investment-cards-grid { grid-template-columns: 1fr; }
            /* Ajuste para que todos os 3 botões fiquem numa nova linha se a tela for pequena */
            .general-actions button { flex-grow: 1; width: 100%; margin-bottom: 10px; }
            .general-actions { flex-direction: column; }
        }
    </style>
</head>
<body>

    <div class="container">
        <header>
            <h1>💰 Meu Dashboard Financeiro</h1>
        </header>

        <section id="general-summary-section" class="dashboard-section">
            <header>
                <h2>Visão Geral da Conta Corrente (Fluxo)</h2>
            </header>
            
            <div id="summary-cards">
                <div class="card" id="card-balance">
                    <h3>SALDO ATUAL</h3>
                    <p id="current-balance">R$ 0,00</p>
                </div>
                <div class="card" id="card-in">
                    <h3>RECEITAS ACUMULADAS</h3>
                    <p id="total-in">R$ 0,00</p>
                </div>
                <div class="card" id="card-out">
                    <h3>DESPESAS ACUMULADAS</h3>
                    <p id="total-out">R$ 0,00</p>
                </div>
            </div>
            
            <div class="general-actions">
                <button class="add-btn" onclick="showTransactionModal()">➕ Nova Transação</button>
                <button class="transfer-btn" onclick="showTransferToSavingsModal()">🔄 Transferir para Poupança</button> 
                <button class="delete-all-btn" onclick="deleteAllData()">🗑️ Excluir Todos os Dados</button>
            </div>
        </section>

        <section id="investment-section" class="dashboard-section">
            <header>
                <h2>Gerenciamento de Investimentos (Poupança)</h2>
            </header>
            
            <div class="report-section">
                <h4>Ações Rápidas (Aportes e Resgates avulsos)</h4>
                <div id="investment-actions">
                    <button id="aporte-btn" onclick="showAporteModal()">⬆️ Aporte Avulso</button>
                    <button id="resgate-btn" onclick="showResgateModal()">⬇️ Resgate Avulso</button>
                </div>
            </div>
            
            <section id="investment-cards-grid">
                <div class="card investment-card-in">
                    <h3>TOTAL DE APORTES</h3>
                    <p id="savings-in">R$ 0,00</p> 
                </div>
                
                <div class="card investment-card-out">
                    <h3>TOTAL DE RESGATES</h3>
                    <p id="savings-out">R$ 0,00</p>
                </div>
                
                <div class="card investment-card-total">
                    <h3>PATRIMÔNIO ACUMULADO</h3>
                    <p id="savings-total-display">R$ 0,00</p>
                </div>
            </section>
            
            <div class="report-section">
                <h4>Regras e Segurança da Caderneta de Poupança</h4>
                <div class="report-text">
                    <blockquote class="info">
                        <p>**O que você precisa saber:**</p>
                        <ul>
                            <li>**Imposto de Renda (IR):** Rendimentos são **isentos**.</li>
                            <li>**Garantia (FGC):** Cobertura de até **R$ 250.000**.</li>
                        </ul>
                    </blockquote>
                </div>
            </div>
        </section>

        <section id="chart-section" class="dashboard-section">
            <header>
                <h2>Análise de Despesas por Categoria</h2>
            </header>
            
            <div style="width: 100%; max-width: 600px; margin: 20px auto;">
                <canvas id="expenseChart"></canvas>
            </div>
            
            <div id="chart-legend-info" style="text-align: center; margin-top: 15px; color: #666; font-size: 0.9em;">
                <span>Visualização das despesas (Tipo 'Despesa') lançadas no sistema.</span>
            </div>
        </section>


        <section id="report-section" class="dashboard-section">
            <header>
                <h2>Relatório Detalhado de Transações</h2>
            </header>
            
            <div id="general-transactions-report" class="report-section">
                <h4>Transações de Conta Corrente</h4>
                <table id="transaction-table" class="report-table">
                    <thead>
                        <tr>
                            <th>Data</th>
                            <th>Tipo</th>
                            <th>Categoria</th>
                            <th>Descrição</th>
                            <th>Valor</th>
                            <th>Ações</th>
                        </tr>
                    </thead>
                    <tbody id="transaction-list-body">
                        </tbody>
                </table>
            </div>
            
            <div id="savings-transactions-report" class="report-section" style="margin-top: 30px;">
                <h4>Movimentações de Investimento (Poupança)</h4>
                <table id="savings-table" class="report-table">
                    <thead>
                        <tr>
                            <th>Data</th>
                            <th>Tipo</th>
                            <th>Valor</th>
                            <th>Ações</th>
                        </tr>
                    </thead>
                    <tbody id="savings-list-body">
                        </tbody>
                </table>
            </div>
        </section>

    </div> <div id="transaction-modal" class="modal">
        <div class="modal-content">
            <span class="close-btn" onclick="closeTransactionModal()">&times;</span>
            <h2 id="transaction-modal-title">Adicionar Transação</h2>
            <form id="transaction-form">
                <label for="type">Tipo:</label>
                <select id="type" required>
                    <option value="in">Receita</option>
                    <option value="out">Despesa</option>
                </select>

                <label for="category">Categoria:</label>
                <input type="text" id="category" placeholder="Ex: Salário, Alimentação, Aluguel" required>

                <label for="description">Descrição:</label>
                <input type="text" id="description" placeholder="Ex: Pagamento do mês, Jantar no restaurante" required>

                <label for="value">Valor (R$):</label>
                <input type="number" id="value" step="0.01" min="0.01" required>

                <label for="date">Data:</label>
                <input type="date" id="date" required>

                <button type="submit" class="submit-btn" id="transaction-submit-btn">Salvar Transação</button>
            </form>
        </div>
    </div>
    
    <div id="aporte-modal" class="modal">
        <div class="modal-content">
            <span class="close-btn" onclick="closeAporteModal()">&times;</span>
            <h2 id="aporte-modal-title">Realizar Aporte (Entrada)</h2>
            <form id="aporte-form">
                <label for="aporte-value">Valor do Aporte (R$):</label>
                <input type="number" id="aporte-value" step="0.01" min="0.01" required>

                <label for="aporte-date">Data:</label>
                <input type="date" id="aporte-date" required>
                
                <button type="submit" class="submit-btn" id="aporte-submit-btn">Registrar Aporte</button>
            </form>
        </div>
    </div>

    <div id="resgate-modal" class="modal">
        <div class="modal-content">
            <span class="close-btn" onclick="closeResgateModal()">&times;</span>
            <h2 id="resgate-modal-title">Realizar Resgate (Saída)</h2>
            <form id="resgate-form">
                <label for="resgate-value">Valor do Resgate (R$):</label>
                <input type="number" id="resgate-value" step="0.01" min="0.01" required>

                <label for="resgate-date">Data:</label>
                <input type="date" id="resgate-date" required>
                
                <button type="submit" class="submit-btn" id="resgate-submit-btn">Registrar Resgate</button>
            </form>
        </div>
    </div>

    <div id="transfer-modal" class="modal">
        <div class="modal-content">
            <span class="close-btn" onclick="closeTransferToSavingsModal()">&times;</span>
            <h2 id="transfer-modal-title">Transferir da Conta Corrente para Poupança</h2>
            <form id="transfer-form">
                <div class="info">
                    Esta ação registra uma **Despesa** na Conta Corrente e um **Aporte** na Poupança.
                </div>
                
                <label for="transfer-value">Valor da Transferência (R$):</label>
                <input type="number" id="transfer-value" step="0.01" min="0.01" required>

                <label for="transfer-date">Data:</label>
                <input type="date" id="transfer-date" required>

                <button type="submit" class="submit-btn" id="transfer-submit-btn">Transferir Valores</button>
            </form>
        </div>
    </div>

    <script>
        // --- ARMAZENAMENTO DE DADOS E ESTADO ---
        let transactions = JSON.parse(localStorage.getItem('transactions')) || [];
        let savingsTransactions = JSON.parse(localStorage.getItem('savingsTransactions')) || [];
        let currentEditingId = null;
        let myExpenseChart = null; // Variável para a instância do Chart.js

        // --- UTILITÁRIOS ---

        function generateUniqueID() {
            if (typeof crypto !== 'undefined' && typeof crypto.randomUUID === 'function') {
                return crypto.randomUUID();
            } 
            return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
                var r = Math.random() * 16 | 0, v = c == 'x' ? r : (r & 0x3 | 0x8);
                return v.toString(16);
            });
        }

        function formatCurrency(amount) {
            return new Intl.NumberFormat('pt-BR', { style: 'currency', currency: 'BRL' }).format(amount);
        }

        function saveToLocalStorage() {
            localStorage.setItem('transactions', JSON.stringify(transactions));
            localStorage.setItem('savingsTransactions', JSON.stringify(savingsTransactions));
        }
        
        // --- AÇÕES GERAIS ---

        function deleteAllData() {
            if (confirm("ATENÇÃO: Você tem certeza que deseja EXCLUIR TODOS os dados salvos (transações e investimentos)? Esta ação é irreversível.")) {
                transactions = [];
                savingsTransactions = [];
                saveToLocalStorage();
                updateDashboard();
                alert("Todos os dados foram excluídos com sucesso.");
            }
        }

        // --- CÁLCULOS E RESUMO ---

        function calculateSummary() {
            let totalIn = 0;
            let totalOut = 0;
            
            transactions.forEach(t => {
                const value = parseFloat(t.value);
                if (t.type === 'in') {
                    totalIn += value;
                } else {
                    totalOut += value;
                }
            });

            const currentBalance = totalIn - totalOut;

            document.getElementById('total-in').textContent = formatCurrency(totalIn);
            document.getElementById('total-out').textContent = formatCurrency(totalOut);
            
            const balanceElement = document.getElementById('current-balance');
            balanceElement.textContent = formatCurrency(currentBalance);
            balanceElement.className = currentBalance >= 0 ? 'positive' : 'negative';

            calculateSavingsSummary();
        }

        function calculateSavingsSummary() {
            let savingsIn = 0;
            let savingsOut = 0;
            
            savingsTransactions.forEach(t => {
                const value = parseFloat(t.value);
                if (t.type === 'aporte') {
                    savingsIn += value;
                } else {
                    savingsOut += value;
                }
            });

            const savingsTotal = savingsIn - savingsOut;

            document.getElementById('savings-in').textContent = formatCurrency(savingsIn);
            document.getElementById('savings-out').textContent = formatCurrency(savingsOut);
            document.getElementById('savings-total-display').textContent = formatCurrency(savingsTotal);
        }
        
        // ------------------------------------------------------------------
        // --- FUNÇÕES DE GRÁFICO (CHART.JS) ---
        // ------------------------------------------------------------------

        function prepareChartData() {
            const expenseMap = {};
            
            // 1. Filtrar e somar despesas por categoria
            transactions
                .filter(t => t.type === 'out')
                .forEach(t => {
                    // Normaliza a categoria (trim e default)
                    const category = t.category.trim() || 'Sem Categoria'; 
                    const value = parseFloat(t.value);
                    
                    if (expenseMap[category]) {
                        expenseMap[category] += value;
                    } else {
                        expenseMap[category] = value;
                    }
                });

            // 2. Formatar os dados para o Chart.js
            const labels = Object.keys(expenseMap);
            const data = labels.map(label => expenseMap[label]);

            // Gerar cores dinâmicas
            const colors = labels.map(() => `rgba(${Math.floor(Math.random() * 255)}, ${Math.floor(Math.random() * 255)}, ${Math.floor(Math.random() * 255)}, 0.8)`);
            
            return { labels, data, colors };
        }

        function renderExpenseChart() {
            const { labels, data, colors } = prepareChartData();
            const ctx = document.getElementById('expenseChart').getContext('2d');
            
            // Destrói o gráfico anterior para evitar sobreposição
            if (myExpenseChart) {
                myExpenseChart.destroy();
            }

            if (data.length === 0) {
                document.getElementById('chart-legend-info').innerHTML = 'Não há despesas registradas para gerar o gráfico.';
                return;
            } else {
                document.getElementById('chart-legend-info').innerHTML = 'Visualização das despesas (Tipo \'Despesa\') lançadas no sistema.';
            }
            
            myExpenseChart = new Chart(ctx, {
                type: 'doughnut', // Gráfico de Rosca
                data: {
                    labels: labels,
                    datasets: [{
                        label: 'Despesas (R$)',
                        data: data,
                        backgroundColor: colors,
                        borderColor: '#fff',
                        borderWidth: 2
                    }]
                },
                options: {
                    responsive: true,
                    plugins: {
                        legend: {
                            position: 'right',
                        },
                        title: {
                            display: true,
                            text: 'Distribuição Percentual de Despesas',
                            font: { size: 16 }
                        },
                        tooltip: {
                            callbacks: {
                                label: function(context) {
                                    let label = context.label || '';
                                    if (label) {
                                        label += ': ';
                                    }
                                    const total = context.dataset.data.reduce((a, b) => a + b, 0);
                                    const currentValue = context.raw;
                                    const percentage = ((currentValue / total) * 100).toFixed(1) + '%';
                                    return `${label} ${formatCurrency(currentValue)} (${percentage})`;
                                }
                            }
                        }
                    }
                }
            });
        }

        // ------------------------------------------------------------------
        // --- RENDERIZAÇÃO DE TABELAS ---

        function renderTransactionTable() {
            const tbody = document.getElementById('transaction-list-body');
            tbody.innerHTML = '';
            
            const sortedTransactions = [...transactions].sort((a, b) => new Date(b.date) - new Date(a.date));

            sortedTransactions.forEach(t => {
                const row = tbody.insertRow();
                const value = parseFloat(t.value);
                const isPositive = t.type === 'in';

                row.insertCell().textContent = t.date;
                row.insertCell().textContent = t.type === 'in' ? 'Receita' : 'Despesa';
                row.insertCell().textContent = t.category;
                row.insertCell().textContent = t.description;
                
                const valueCell = row.insertCell();
                valueCell.textContent = formatCurrency(value);
                valueCell.classList.add('value-col');
                valueCell.classList.add(isPositive ? 'positive' : 'negative');
                
                const actionCell = row.insertCell();
                
                const editBtn = document.createElement('button');
                editBtn.textContent = 'Editar';
                editBtn.classList.add('action-btn', 'edit');
                editBtn.onclick = () => startEditTransaction(t.id);
                actionCell.appendChild(editBtn);

                const deleteBtn = document.createElement('button');
                deleteBtn.textContent = 'Remover';
                deleteBtn.classList.add('action-btn', 'delete');
                deleteBtn.onclick = () => deleteTransaction(t.id);
                actionCell.appendChild(deleteBtn);
            });
        }

        function renderSavingsTable() {
            const tbody = document.getElementById('savings-list-body');
            tbody.innerHTML = '';
            
            const sortedSavings = [...savingsTransactions].sort((a, b) => new Date(b.date) - new Date(a.date));

            sortedSavings.forEach(t => {
                const row = tbody.insertRow();
                const value = parseFloat(t.value);
                const isAporte = t.type === 'aporte';

                row.insertCell().textContent = t.date;
                row.insertCell().textContent = isAporte ? 'Aporte' : 'Resgate';
                
                const valueCell = row.insertCell();
                valueCell.textContent = formatCurrency(value);
                valueCell.classList.add('value-col');
                valueCell.classList.add(isAporte ? 'positive' : 'negative');
                
                const actionCell = row.insertCell();
                
                const editBtn = document.createElement('button');
                editBtn.textContent = 'Editar';
                editBtn.classList.add('action-btn', 'edit');
                editBtn.onclick = () => startEditSavingsTransaction(t.id, t.type);
                actionCell.appendChild(editBtn);
                
                const deleteBtn = document.createElement('button');
                deleteBtn.textContent = 'Remover';
                deleteBtn.classList.add('action-btn', 'delete');
                deleteBtn.onclick = () => deleteSavingsTransaction(t.id);
                actionCell.appendChild(deleteBtn);
            });
        }

        // ------------------------------------------------------------------
        // --- FUNÇÕES DE CRUD E MODAIS ---

        function handleTransactionSubmit(event) {
            event.preventDefault();
            
            const type = document.getElementById('type').value;
            const category = document.getElementById('category').value;
            const description = document.getElementById('description').value;
            const value = parseFloat(document.getElementById('value').value);
            const date = document.getElementById('date').value;

            if (isNaN(value) || value <= 0) {
                alert("Por favor, insira um valor válido.");
                return;
            }

            if (currentEditingId) {
                const index = transactions.findIndex(t => t.id === currentEditingId);
                if (index !== -1) {
                    transactions[index] = { id: currentEditingId, type, category, description, value: value.toFixed(2), date };
                }
                currentEditingId = null;
            } else {
                const newTransaction = {
                    id: generateUniqueID(),
                    type,
                    category,
                    description,
                    value: value.toFixed(2),
                    date
                };
                transactions.push(newTransaction);
            }

            saveToLocalStorage();
            document.getElementById('transaction-form').reset();
            closeTransactionModal();
            updateDashboard();
        }
        
        // NOVO: Função para Transferência
        function handleTransferToSavings(event) {
            event.preventDefault();

            const value = parseFloat(document.getElementById('transfer-value').value);
            const date = document.getElementById('transfer-date').value;

            if (isNaN(value) || value <= 0) {
                alert("Por favor, insira um valor válido para a transferência.");
                return;
            }

            // 1. REGISTRAR DESPESA NA CONTA CORRENTE
            const expenseTransaction = {
                id: generateUniqueID(),
                type: 'out',
                category: 'Transferência',
                description: 'Transferência para Poupança',
                value: value.toFixed(2),
                date: date
            };
            transactions.push(expenseTransaction);

            // 2. REGISTRAR APORTE NO INVESTIMENTO (POUPANÇA)
            const savingsTransaction = {
                id: generateUniqueID(),
                type: 'aporte',
                value: value.toFixed(2),
                date: date
            };
            savingsTransactions.push(savingsTransaction);

            saveToLocalStorage();
            document.getElementById('transfer-form').reset();
            closeTransferToSavingsModal();
            updateDashboard();
            alert(`Transferência de ${formatCurrency(value)} para a Poupança registrada com sucesso!`);
        }
        
        function startEditTransaction(id) {
            const transaction = transactions.find(t => t.id === id);
            if (!transaction) return;

            currentEditingId = id;
            
            document.getElementById('transaction-modal-title').textContent = 'Editar Transação';
            document.getElementById('transaction-submit-btn').textContent = 'Salvar Edição';
            
            document.getElementById('type').value = transaction.type;
            document.getElementById('category').value = transaction.category;
            document.getElementById('description').value = transaction.description;
            document.getElementById('value').value = transaction.value;
            document.getElementById('date').value = transaction.date;
            
            showTransactionModal();
        }

        function deleteTransaction(id) {
            if (confirm("Tem certeza que deseja remover esta transação?")) {
                transactions = transactions.filter(t => t.id !== id);
                saveToLocalStorage();
                updateDashboard();
            }
        }

        function handleSavingsSubmit(event, isAporte) {
            event.preventDefault();
            
            const typeStr = isAporte ? 'aporte' : 'resgate';
            const valueId = isAporte ? 'aporte-value' : 'resgate-value';
            const dateId = isAporte ? 'aporte-date' : 'resgate-date';
            
            const value = parseFloat(document.getElementById(valueId).value);
            const date = document.getElementById(dateId).value;

            if (isNaN(value) || value <= 0) {
                alert("Por favor, insira um valor válido.");
                return;
            }

            if (currentEditingId) {
                const index = savingsTransactions.findIndex(t => t.id === currentEditingId);
                if (index !== -1) {
                    savingsTransactions[index] = { id: currentEditingId, type: typeStr, value: value.toFixed(2), date };
                }
                currentEditingId = null;
            } else {
                const newSavingsTransaction = {
                    id: generateUniqueID(),
                    type: typeStr,
                    value: value.toFixed(2),
                    date
                };
                savingsTransactions.push(newSavingsTransaction);
            }

            saveToLocalStorage();
            
            if (isAporte) {
                document.getElementById('aporte-form').reset();
                closeAporteModal();
            } else {
                document.getElementById('resgate-form').reset();
                closeResgateModal();
            }
            
            updateDashboard();
        }
        
        function startEditSavingsTransaction(id, type) {
            const transaction = savingsTransactions.find(t => t.id === id);
            if (!transaction) return;

            currentEditingId = id;

            const isAporte = type === 'aporte';
            
            if (isAporte) {
                document.getElementById('aporte-modal-title').textContent = 'Editar Aporte';
                document.getElementById('aporte-submit-btn').textContent = 'Salvar Edição';
                document.getElementById('aporte-value').value = transaction.value;
                document.getElementById('aporte-date').value = transaction.date;
                showAporteModal();
            } else {
                document.getElementById('resgate-modal-title').textContent = 'Editar Resgate';
                document.getElementById('resgate-submit-btn').textContent = 'Salvar Edição';
                document.getElementById('resgate-value').value = transaction.value;
                document.getElementById('resgate-date').value = transaction.date;
                showResgateModal();
            }
        }

        function deleteSavingsTransaction(id) {
            if (confirm("Tem certeza que deseja remover esta movimentação de investimento?")) {
                savingsTransactions = savingsTransactions.filter(t => t.id !== id);
                saveToLocalStorage();
                updateDashboard();
            }
        }

        // --- FUNÇÕES DE MODAL ---
        
        function showTransactionModal() { document.getElementById('transaction-modal').style.display = 'block'; }
        function closeTransactionModal() { 
            document.getElementById('transaction-modal').style.display = 'none';
            currentEditingId = null;
            document.getElementById('transaction-modal-title').textContent = 'Adicionar Transação';
            document.getElementById('transaction-submit-btn').textContent = 'Salvar Transação';
            document.getElementById('transaction-form').reset();
        }
        
        function showAporteModal() { document.getElementById('aporte-modal').style.display = 'block'; }
        function closeAporteModal() { 
            document.getElementById('aporte-modal').style.display = 'none';
            currentEditingId = null;
            document.getElementById('aporte-modal-title').textContent = 'Realizar Aporte (Entrada)';
            document.getElementById('aporte-submit-btn').textContent = 'Registrar Aporte';
            document.getElementById('aporte-form').reset();
        }
        
        function showResgateModal() { document.getElementById('resgate-modal').style.display = 'block'; }
        function closeResgateModal() { 
            document.getElementById('resgate-modal').style.display = 'none';
            currentEditingId = null;
            document.getElementById('resgate-modal-title').textContent = 'Realizar Resgate (Saída)';
            document.getElementById('resgate-submit-btn').textContent = 'Registrar Resgate';
            document.getElementById('resgate-form').reset();
        }

        // NOVO: Funções de Modal para Transferência
        function showTransferToSavingsModal() { document.getElementById('transfer-modal').style.display = 'block'; }
        function closeTransferToSavingsModal() {
            document.getElementById('transfer-modal').style.display = 'none';
            document.getElementById('transfer-form').reset();
        }
        
        window.onclick = function(event) {
            if (event.target.classList.contains('modal')) {
                const modalId = event.target.id;
                if (modalId === 'transaction-modal') closeTransactionModal();
                else if (modalId === 'aporte-modal') closeAporteModal();
                else if (modalId === 'resgate-modal') closeResgateModal();
                else if (modalId === 'transfer-modal') closeTransferToSavingsModal(); // Novo
            }
        }

        // --- INICIALIZAÇÃO E EVENT LISTENERS ---

        function updateDashboard() {
            calculateSummary();
            renderTransactionTable();
            renderSavingsTable();
            renderExpenseChart(); 
        }

        document.addEventListener('DOMContentLoaded', () => {
            updateDashboard();
            
            document.getElementById('transaction-form').addEventListener('submit', handleTransactionSubmit);
            document.getElementById('aporte-form').addEventListener('submit', (e) => handleSavingsSubmit(e, true));
            document.getElementById('resgate-form').addEventListener('submit', (e) => handleSavingsSubmit(e, false));
            // NOVO: Listener para o formulário de Transferência
            document.getElementById('transfer-form').addEventListener('submit', handleTransferToSavings); 
        });
    </script>

</body>
</html>
