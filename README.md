<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dashboard de Análise de Qualificações</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Bibliotecas para processar arquivos -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/PapaParse/5.3.2/papaparse.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <!-- Biblioteca de gráficos -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/chartjs-adapter-date-fns/dist/chartjs-adapter-date-fns.bundle.min.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f1f5f9;
        }
        .chart-container {
            background-color: white;
            border-radius: 0.75rem;
            padding: 1.5rem;
            box-shadow: 0 1px 3px 0 rgb(0 0 0 / 0.1), 0 1px 2px -1px rgb(0 0 0 / 0.1);
        }
        .custom-scrollbar::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        .custom-scrollbar::-webkit-scrollbar-track {
            background: #f8fafc;
        }
        .custom-scrollbar::-webkit-scrollbar-thumb {
            background: #d1d5db;
            border-radius: 10px;
        }
        .custom-scrollbar::-webkit-scrollbar-thumb:hover {
            background: #9ca3af;
        }
        .nav-link.active {
            background-color: #334155;
            color: #e2e8f0;
        }
        .table-container thead th {
            position: sticky;
            top: 0;
            z-index: 10;
        }
        .modal-overlay {
            transition: opacity 0.3s ease-in-out;
        }
        .modal-content {
            transition: transform 0.3s ease-in-out;
        }
    </style>
</head>
<body class="text-slate-800">

    <div class="flex h-screen bg-gray-100">
        <!-- Sidebar -->
        <aside class="w-64 flex-shrink-0 bg-[#1e293b] text-white flex flex-col">
            <div class="h-20 flex items-center justify-center text-2xl font-bold border-b border-slate-700 text-center">
                Chagas Operações
            </div>
            <nav id="sidebar-nav" class="flex-1 px-4 py-6 space-y-2">
                <a href="#" data-page="home" class="nav-link flex items-center px-4 py-2 text-slate-400 hover:bg-slate-700 hover:text-slate-200 rounded-lg active">
                    <svg class="w-6 h-6 mr-3" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M3 12l2-2m0 0l7-7 7 7M5 10v10a1 1 0 001 1h3m10-11l2 2m-2-2v10a1 1 0 01-1 1h-3m-6 0a1 1 0 001-1v-4a1 1 0 011-1h2a1 1 0 011 1v4a1 1 0 001 1m-6 0h6"></path></svg>
                    Home
                </a>
                <a href="#" data-page="relatorio" class="nav-link flex items-center px-4 py-2 text-slate-400 hover:bg-slate-700 hover:text-slate-200 rounded-lg">
                    <svg class="w-6 h-6 mr-3" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M9 17v-2a4 4 0 00-4-4H3a2 2 0 00-2 2v4a2 2 0 002 2h2a4 4 0 004-4zm0 0v-2a4 4 0 014-4h2a4 4 0 014 4v2m-6 4h6a2 2 0 002-2v-4a2 2 0 00-2-2h-2a4 4 0 00-4 4v2z"></path></svg>
                    Relatório
                </a>
                <a href="#" data-page="faturamento" class="nav-link flex items-center px-4 py-2 text-slate-400 hover:bg-slate-700 hover:text-slate-200 rounded-lg">
                    <svg class="w-6 h-6 mr-3" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"></path></svg>
                    Faturamento
                </a>
                 <a href="#" data-page="perfis" class="nav-link flex items-center px-4 py-2 text-slate-400 hover:bg-slate-700 hover:text-slate-200 rounded-lg">
                    <svg class="w-6 h-6 mr-3" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M17 20h5v-2a3 3 0 00-5.356-1.857M17 20H7m10 0v-2c0-.656-.126-1.283-.356-1.857M7 20H2v-2a3 3 0 015.356-1.857M7 20v-2c0-.656.126-1.283.356-1.857m0 0a5.002 5.002 0 019.288 0M15 7a3 3 0 11-6 0 3 3 0 016 0zm6 3a2 2 0 11-4 0 2 2 0 014 0zM7 10a2 2 0 11-4 0 2 2 0 014 0z"></path></svg>
                    Cadastro de Perfis
                </a>
                <a href="#" data-page="gestao" class="nav-link flex items-center px-4 py-2 text-slate-400 hover:bg-slate-700 hover:text-slate-200 rounded-lg">
                    <svg class="w-6 h-6 mr-3" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M12 4.354a4 4 0 110 5.292M15 21H3v-1a6 6 0 0112 0v1zm0 0h6v-1a6 6 0 00-9-5.197M15 21a6 6 0 00-9-5.197m0 0A5.995 5.995 0 0112 13a5.995 5.995 0 013 5.197M15 21a6 6 0 00-9-5.197"></path></svg>
                    Gestão de Equipe
                </a>
                <a href="#" data-page="pagamentos" class="nav-link flex items-center px-4 py-2 text-slate-400 hover:bg-slate-700 hover:text-slate-200 rounded-lg">
                    <svg class="w-6 h-6 mr-3" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M3 10h18M7 15h1m4 0h1m-7 4h12a3 3 0 003-3V8a3 3 0 00-3-3H6a3 3 0 00-3 3v8a3 3 0 003 3z"></path></svg>
                    Pagamentos
                </a>
                <a href="#" data-page="auditoria" class="nav-link flex items-center px-4 py-2 text-slate-400 hover:bg-slate-700 hover:text-slate-200 rounded-lg">
                    <svg class="w-6 h-6 mr-3" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M9 12l2 2 4-4m5.618-4.016A11.955 11.955 0 0112 2.944a11.955 11.955 0 01-8.618 3.04A12.02 12.02 0 003 20.944A12.02 12.02 0 0012 21a12.02 12.02 0 009-17.056z"></path></svg>
                    Auditoria
                </a>
            </nav>
        </aside>

        <!-- Main Content -->
        <main class="flex-1 overflow-x-hidden overflow-y-auto bg-slate-200 p-4 md:p-8 custom-scrollbar">
            <input type="file" id="file-input" class="hidden" multiple accept=".csv, .xlsx, .xls, application/vnd.openxmlformats-officedocument.sheet, application/vnd.ms-excel">
            
            <div id="page-content-wrapper">
                <!-- Conteúdo das páginas será injetado aqui pelo JS -->
            </div>
        </main>
    </div>

    <!-- Modal de Gestão de Perfis -->
    <div id="profile-modal" class="modal-overlay fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center p-4 hidden z-50">
        <div class="modal-content bg-white rounded-lg shadow-2xl w-full max-w-md p-6 transform scale-95">
            <div class="flex justify-between items-center border-b pb-3 mb-4">
                <h2 id="modal-title" class="text-xl font-bold text-slate-800">Adicionar Novo Operador</h2>
                <button id="close-modal-btn" class="text-slate-500 hover:text-slate-800">&times;</button>
            </div>
            <form id="profile-form">
                <div class="space-y-4">
                    <div>
                        <label for="user-name" class="block text-sm font-medium text-slate-700">Nome</label>
                        <input type="text" id="user-name" class="mt-1 block w-full rounded-md border-slate-300 shadow-sm" required>
                    </div>
                    <div>
                        <label for="user-email" class="block text-sm font-medium text-slate-700">Email</label>
                        <input type="email" id="user-email" class="mt-1 block w-full rounded-md border-slate-300 shadow-sm" required>
                    </div>
                    <div>
                        <label for="user-role" class="block text-sm font-medium text-slate-700">Função</label>
                        <select id="user-role" class="mt-1 block w-full rounded-md border-slate-300 shadow-sm">
                            <option>PJ</option>
                            <option>Estagiário</option>
                        </select>
                    </div>
                    <div id="jornada-container" class="hidden grid grid-cols-2 gap-4">
                        <div>
                            <label for="user-start" class="block text-sm font-medium text-slate-700">Início Jornada</label>
                            <input type="time" id="user-start" class="mt-1 block w-full rounded-md border-slate-300 shadow-sm">
                        </div>
                        <div>
                            <label for="user-end" class="block text-sm font-medium text-slate-700">Fim Jornada</label>
                            <input type="time" id="user-end" class="mt-1 block w-full rounded-md border-slate-300 shadow-sm">
                        </div>
                    </div>
                </div>
                <div class="mt-6 flex justify-end space-x-3">
                    <button type="button" id="cancel-modal-btn" class="px-4 py-2 bg-slate-200 text-slate-800 font-semibold rounded-lg hover:bg-slate-300">Cancelar</button>
                    <button type="submit" id="save-modal-btn" class="px-4 py-2 bg-blue-700 text-white font-semibold rounded-lg shadow-md hover:bg-blue-800">Salvar</button>
                </div>
            </form>
        </div>
    </div>
    
    <script>
        // --- MÓDULO DE GESTÃO DO ESTADO E DADOS (DataManager) ---
        const DataManager = {
            state: {
                allData: [],
                loadedFiles: [],
                userProfiles: [],
                teams: { // Estrutura de equipas de exemplo
                    'Equipe Alpha': {
                        leader: 'líder.alpha@chagas.com.br',
                        members: ['cbd.luan.martins@ycons.tec.br', 'cbd.vinicius.rege@ycons.com.br']
                    },
                    'Equipe Beta': {
                        leader: 'líder.beta@chagas.com.br',
                        members: ['auto-analysis@carbigdata.com.br', 'cbd.joao.andrade@ycons.tec.br']
                    }
                }
            },

            loadProfilesFromStorage() {
                const storedProfiles = localStorage.getItem('userProfiles');
                this.state.userProfiles = storedProfiles ? JSON.parse(storedProfiles) : [];
            },

            saveProfilesToStorage() {
                localStorage.setItem('userProfiles', JSON.stringify(this.state.userProfiles));
            },

            addOrUpdateProfile(profileData) {
                const index = this.state.userProfiles.findIndex(p => p.email === profileData.email);
                if (index > -1) {
                    this.state.userProfiles[index] = profileData;
                } else {
                    this.state.userProfiles.push(profileData);
                }
                this.saveProfilesToStorage();
            },

            removeProfile(email) {
                this.state.userProfiles = this.state.userProfiles.filter(p => p.email !== email);
                this.saveProfilesToStorage();
            },

            getProfileByEmail(email) {
                return this.state.userProfiles.find(p => p.email === email);
            },

            processAndAddData(parsedData, fileName) {
                const newData = parsedData.map(row => {
                    const endDate = new Date(row['Data Fim Análise']);
                    if (isNaN(endDate.getTime())) return null;
                    return { ...row, 'Data Fim Análise': endDate, 'Qtd de Hits': parseInt(row['Qtd de Hits'], 10) || 0 };
                }).filter(Boolean);

                if (newData.length === 0) throw new Error(`Nenhum dado válido encontrado em ${fileName}.`);

                this.state.allData = this.state.allData.concat(newData);
                this.state.loadedFiles.push(fileName);
            },
            
            removeDataByFileName(fileName) {
                // Esta funcionalidade seria mais complexa, exigindo associar cada linha de dado ao seu ficheiro de origem.
                // Por agora, vamos simplificar e usar o reset global.
                console.warn("Remoção de ficheiro individual não implementada. Use o reset global.");
            },
            
            resetData() {
                this.state.allData = [];
                this.state.loadedFiles = [];
            },

            calculateFaturamento(data) {
                const filteredData = data.filter(row => row.Empresa === 'Chagas Alexandre');
                let receitaTotal = 0, receitaPerdida = 0, valorCliente = 0;
                
                filteredData.forEach(row => {
                    if ((row['Tipo de Requisição'] === 'Hotlist' || row['Tipo de Requisição'] === 'QualiAPI')) {
                        if (row.SLA === 'Dentro') receitaTotal += 0.04;
                        else receitaPerdida += 0.04;
                    } else if (row['Tipo de Requisição'] === 'Uploader') {
                        const hits = row['Qtd de Hits'];
                        if (hits >= 1 && hits <= 5) valorCliente += 0.04;
                        else if (hits > 5 && hits <= 10) valorCliente += 0.05;
                        else if (hits > 10 && hits <= 50) valorCliente += 0.06;
                        else if (hits > 50) valorCliente += 0.07;
                    }
                });
                
                return { receitaTotal, receitaPerdida, valorCliente };
            }
        };

        // --- MÓDULO DE GESTÃO DA INTERFACE (UIManager) ---
        const UIManager = {
            elements: {
                mainContent: document.getElementById('page-content-wrapper'),
                fileInput: document.getElementById('file-input'),
                sidebarNav: document.getElementById('sidebar-nav'),
                profileModal: document.getElementById('profile-modal'),
                profileForm: document.getElementById('profile-form'),
                modalTitle: document.getElementById('modal-title'),
                closeModalBtn: document.getElementById('close-modal-btn'),
                cancelModalBtn: document.getElementById('cancel-modal-btn'),
                jornadaContainer: document.getElementById('jornada-container'),
                userRoleSelect: document.getElementById('user-role'),
            },

            templates: {
                home: `
                    <div id="page-home" class="page-content space-y-8">
                        <div class="flex justify-between items-center">
                            <h1 class="text-2xl font-bold text-slate-800">Home</h1>
                            <div id="upload-actions" class="flex justify-end space-x-2">
                                <label for="file-input" class="px-4 py-2 bg-blue-700 text-white font-semibold rounded-lg shadow-md hover:bg-blue-800 cursor-pointer flex items-center">
                                    <svg class="w-5 h-5 mr-2" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M12 4.5v15m7.5-7.5h-15"></path></svg>
                                    Adicionar Planilha
                                </label>
                                <button id="reset-btn" class="px-4 py-2 bg-red-600 text-white font-semibold rounded-lg shadow-md hover:bg-red-700 cursor-pointer flex items-center">
                                    <svg class="w-5 h-5 mr-2" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"></path></svg>
                                    Retirar Planilhas
                                </button>
                            </div>
                        </div>
                        <div id="loaded-files-container" class="hidden">
                            <h3 class="text-lg font-semibold text-slate-700">Ficheiros Carregados:</h3>
                            <ul id="loaded-files-list" class="list-disc list-inside bg-white p-4 rounded-lg shadow mt-2"></ul>
                        </div>
                        <section class="grid grid-cols-1 lg:grid-cols-2 gap-8">
                            <div class="chart-container"><h3 class="text-lg font-semibold mb-4">Qualificações por Dia</h3><div class="h-96"><canvas id="chart-dia-home"></canvas></div></div>
                            <div class="space-y-8"><div class="chart-container"><h3 class="text-lg font-semibold mb-4">Dia da Semana</h3><div class="h-40"><canvas id="chart-dia-semana-home"></canvas></div></div><div class="chart-container"><h3 class="text-lg font-semibold mb-4">Qualificações por Horário</h3><div class="h-40"><canvas id="chart-horario-home"></canvas></div></div></div>
                        </section>
                    </div>`,
                relatorio: `<div id="page-relatorio" class="page-content space-y-8"><h1 class="text-2xl font-bold text-slate-800">Relatório Detalhado</h1><section class="grid grid-cols-1 lg:grid-cols-3 gap-8"><div class="chart-container lg:col-span-2"><h3 class="text-lg font-semibold mb-4">Qualificações por Usuário</h3><div class="h-[500px]"><canvas id="chart-ranking-qualificacoes"></canvas></div></div><div class="space-y-8 flex flex-col"><div class="chart-container flex-1"><h3 class="text-lg font-semibold mb-4">SLA</h3><div class="h-48"><canvas id="chart-sla"></canvas></div></div><div class="chart-container flex-1"><h3 class="text-lg font-semibold mb-4">Laudo (Exemplo)</h3><div class="h-48"><canvas id="chart-laudo"></canvas></div></div></div></section></div>`,
                faturamento: `<div id="page-faturamento" class="page-content space-y-8"><h1 class="text-2xl font-bold text-slate-800">Análise de Faturamento (Chagas Alexandre)</h1><section class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6"><div class="bg-white p-6 rounded-lg shadow"><h3 class="text-sm font-medium text-slate-500">Receita Total</h3><p id="kpi-receita-total" class="text-3xl font-bold mt-1 text-green-600">R$ 0,00</p></div><div class="bg-white p-6 rounded-lg shadow"><h3 class="text-sm font-medium text-slate-500">Receita Perdida (SLA)</h3><p id="kpi-receita-perdida" class="text-3xl font-bold mt-1 text-red-600">R$ 0,00</p></div><div class="bg-white p-6 rounded-lg shadow"><h3 class="text-sm font-medium text-slate-500">Projeção Fim do Mês</h3><p id="kpi-projecao" class="text-3xl font-bold mt-1 text-blue-600">R$ 0,00</p></div><div class="bg-white p-6 rounded-lg shadow"><h3 class="text-sm font-medium text-slate-500">Valor Gerado (Cliente)</h3><p id="kpi-valor-cliente" class="text-3xl font-bold mt-1 text-indigo-600">R$ 0,00</p></div></section><div class="chart-container"><h3 class="text-lg font-semibold mb-4">Ranking de Receita por Usuário</h3><div class="overflow-x-auto custom-scrollbar table-container max-h-[600px]"><table class="w-full text-sm"><thead><tr class="text-left"><th class="px-4 py-3">Usuário</th><th>Receita Gerada</th><th>Valor ao Cliente</th></tr></thead><tbody id="faturamento-table-body"></tbody></table></div></div></div>`,
                perfis: `<div id="page-perfis" class="page-content space-y-8"><div class="flex justify-between items-center"><h1 class="text-2xl font-bold text-slate-800">Cadastro de Perfis</h1><button id="add-profile-btn" class="px-4 py-2 bg-blue-700 text-white font-semibold rounded-lg shadow-md hover:bg-blue-800">Adicionar Operador</button></div><div class="chart-container"><h3 class="text-lg font-semibold mb-4">Perfis de Equipe (Chagas Alexandre)</h3><div class="overflow-x-auto custom-scrollbar table-container max-h-[600px]"><table class="w-full text-sm"><thead><tr class="text-left"><th class="px-4 py-3">Nome</th><th>Email</th><th>Função</th><th>Jornada</th><th>Ações</th></tr></thead><tbody id="perfis-table-body"></tbody></table></div></div></div>`,
                gestao: `<div id="page-gestao" class="page-content space-y-8"><h1 class="text-2xl font-bold text-slate-800">Gestão de Equipe</h1><div class="chart-container"><h3 class="text-lg font-semibold mb-4">Desempenho da Equipe</h3><div class="overflow-x-auto custom-scrollbar table-container max-h-[600px]"><table class="w-full text-sm"><thead><tr class="text-left"><th class="px-4 py-3">Analista</th><th>Função</th><th>Qualificações</th><th>SLA</th><th>Jornada</th></tr></thead><tbody id="gestao-table-body"></tbody></table></div></div></div>`,
                pagamentos: `<div id="page-pagamentos" class="page-content"><h1 class="text-2xl font-bold text-slate-800">Pagamentos</h1><p class="mt-4">Página de Pagamentos em construção.</p></div>`,
                auditoria: `<div id="page-auditoria" class="page-content"><h1 class="text-2xl font-bold text-slate-800">Auditoria</h1><p class="mt-4">Página de Auditoria em construção.</p></div>`,
                upload: `<div id="upload-container" class="chart-container"><div class="max-w-3xl mx-auto"><div id="drop-zone" class="flex flex-col items-center justify-center p-10 rounded-xl cursor-pointer border-2 border-dashed border-slate-300 hover:border-blue-500 hover:bg-slate-50 transition-colors"><svg class="w-16 h-16 text-slate-400 mb-4" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M12 16.5V9.75m0 0l-3 3m3-3l3 3M6.75 19.5a4.5 4.5 0 01-1.41-8.775 5.25 5.25 0 0110.233-2.33 3 3 0 013.758 3.848A3.752 3.752 0 0118 19.5H6.75z"></path></svg><p class="text-slate-600 font-semibold">Arraste e solte o ficheiro aqui</p><p class="text-slate-500 text-sm mt-1">ou</p><label for="file-input" class="mt-4 px-6 py-2 bg-blue-700 text-white font-semibold rounded-lg shadow-md hover:bg-blue-800 cursor-pointer">Selecione o Ficheiro</label></div></div></div>`
            },

            init() {
                this.elements.sidebarNav.addEventListener('click', App.handleNavigation);
                this.elements.profileModal.addEventListener('click', (e) => { if (e.target === this.elements.profileModal) this.closeProfileModal(); });
                this.elements.closeModalBtn.addEventListener('click', () => this.closeProfileModal());
                this.elements.cancelModalBtn.addEventListener('click', () => this.closeProfileModal());
                this.elements.profileForm.addEventListener('submit', App.handleProfileFormSubmit);
                this.elements.userRoleSelect.addEventListener('change', () => this.toggleJornadaInputs());
            },

            renderPage(pageId) {
                if (!this.templates[pageId]) {
                    this.elements.mainContent.innerHTML = `<p>Página não encontrada: ${pageId}</p>`;
                    return;
                }
                this.elements.mainContent.innerHTML = this.templates[pageId];
                this.setupPageEventListeners(pageId);
                App.updateUI();
            },
            
            setupPageEventListeners(pageId) {
                if (pageId === 'home') {
                    document.getElementById('reset-btn').addEventListener('click', App.handleReset);
                    document.querySelector('label[for="file-input"]').addEventListener('click', () => this.elements.fileInput.click());
                }
                if (pageId === 'perfis') {
                    document.getElementById('add-profile-btn').addEventListener('click', () => this.openProfileModal());
                }
            },
            
            updateFileListUI(files) {
                const container = document.getElementById('loaded-files-container');
                const list = document.getElementById('loaded-files-list');
                if (!container || !list) return;
                
                list.innerHTML = '';
                if (files.length > 0) {
                    files.forEach(name => {
                        const li = document.createElement('li');
                        li.textContent = name;
                        list.appendChild(li);
                    });
                    container.classList.remove('hidden');
                } else {
                    container.classList.add('hidden');
                }
            },

            openProfileModal(profile = null) {
                this.elements.profileForm.reset();
                this.toggleJornadaInputs(false);
                if (profile) {
                    this.elements.modalTitle.textContent = 'Editar Operador';
                    document.getElementById('user-name').value = profile.nome;
                    document.getElementById('user-email').value = profile.email;
                    document.getElementById('user-email').readOnly = true;
                    document.getElementById('user-role').value = profile.funcao;
                    if (profile.funcao === 'Estagiário') {
                        this.toggleJornadaInputs(true);
                        document.getElementById('user-start').value = profile.inicioJornada;
                        document.getElementById('user-end').value = profile.fimJornada;
                    }
                } else {
                    this.elements.modalTitle.textContent = 'Adicionar Novo Operador';
                    document.getElementById('user-email').readOnly = false;
                }
                this.elements.profileModal.classList.remove('hidden');
            },

            closeProfileModal() {
                this.elements.profileModal.classList.add('hidden');
            },
            
            toggleJornadaInputs(forceShow) {
                const show = forceShow || this.elements.userRoleSelect.value === 'Estagiário';
                this.elements.jornadaContainer.classList.toggle('hidden', !show);
            },
            
            updateProfileTableUI(profiles, data) {
                const tableBody = document.getElementById('perfis-table-body');
                if (!tableBody) return;
                tableBody.innerHTML = '';
                profiles.forEach(user => {
                    const userData = data.filter(d => d.Usuário === user.email);
                    const tr = document.createElement('tr');
                    tr.innerHTML = `
                        <td class="px-4 py-2 font-medium">${user.nome}</td>
                        <td class="px-4 py-2">${user.email}</td>
                        <td class="px-4 py-2">${user.funcao}</td>
                        <td class="px-4 py-2">${user.funcao === 'Estagiário' ? `${user.inicioJornada || 'N/A'} - ${user.fimJornada || 'N/A'}` : 'N/A'}</td>
                        <td class="px-4 py-2 space-x-2">
                            <button class="text-blue-500 hover:text-blue-700 font-semibold" onclick="App.handleEditProfile('${user.email}')">Editar</button>
                            <button class="text-red-500 hover:text-red-700 font-semibold" onclick="App.handleRemoveProfile('${user.email}')">Remover</button>
                        </td>`;
                    tableBody.appendChild(tr);
                });
            },
            
            updateGestaoTableUI(profiles, data) {
                const tableBody = document.getElementById('gestao-table-body');
                if (!tableBody) return;
                tableBody.innerHTML = '';
                 profiles.forEach(user => {
                    const userData = data.filter(d => d.Usuário === user.email);
                    const totalQuals = userData.length;
                    const slaDentro = userData.filter(d => d.SLA === 'Dentro').length;
                    const slaPercent = totalQuals > 0 ? ((slaDentro / totalQuals) * 100).toFixed(1) + '%' : '0%';
                    
                    let foraJornada = 'N/A';
                    if(user.funcao === 'Estagiário' && user.inicioJornada && user.fimJornada) {
                        const inicio = user.inicioJornada.split(':').map(Number);
                        const fim = user.fimJornada.split(':').map(Number);
                        const fora = userData.some(d => {
                            const hora = d['Data Fim Análise'].getHours();
                            return hora < inicio[0] || hora >= fim[0];
                        });
                        foraJornada = fora ? `<span class="font-bold text-red-500">Fora da Jornada</span>` : 'OK';
                    }

                    const tr = document.createElement('tr');
                    tr.innerHTML = `
                        <td class="px-4 py-2 font-medium">${user.nome}</td>
                        <td class="px-4 py-2">${user.funcao}</td>
                        <td class="px-4 py-2">${totalQuals}</td>
                        <td class="px-4 py-2">${slaPercent}</td>
                        <td class="px-4 py-2">${foraJornada}</td>`;
                    tableBody.appendChild(tr);
                });
            },

            updateFaturamentoUI(faturamento) {
                document.getElementById('kpi-receita-total').textContent = `R$ ${faturamento.receitaTotal.toFixed(2)}`;
                document.getElementById('kpi-receita-perdida').textContent = `R$ ${faturamento.receitaPerdida.toFixed(2)}`;
                document.getElementById('kpi-valor-cliente').textContent = `R$ ${faturamento.valorCliente.toFixed(2)}`;
            }
        };

        // --- MÓDULO DE GESTÃO DOS GRÁFICOS (ChartManager) ---
        const ChartManager = {
            charts: {},
            
            destroyAll() {
                Object.values(this.charts).forEach(chart => chart.destroy());
                this.charts = {};
            },

            renderChartsForPage(pageId, data) {
                this.destroyAll();
                switch (pageId) {
                    case 'home': this.renderHomeCharts(data); break;
                    case 'relatorio': this.renderRelatorioCharts(data); break;
                }
            },

            renderHomeCharts(data) {
                const commonOptions = { responsive: true, maintainAspectRatio: false };
                const noLegend = { ...commonOptions, plugins: { legend: { display: false } } };

                const qualsPorDia = data.reduce((acc, r) => {
                    const date = r['Data Fim Análise'].toISOString().split('T')[0];
                    acc[date] = (acc[date] || 0) + 1;
                    return acc;
                }, {});
                const sortedDays = Object.entries(qualsPorDia).sort((a,b) => new Date(a[0]) - new Date(b[0]));

                const diasSemanaOrdem = ['Domingo', 'Segunda', 'Terça', 'Quarta', 'Quinta', 'Sexta', 'Sábado'];
                const qualsDiaSemana = diasSemanaOrdem.reduce((acc, dia) => ({...acc, [dia]: 0}), {});
                data.forEach(r => {
                    const dia = r['Data Fim Análise'].toLocaleString('pt-BR', { weekday: 'long' }).replace('-feira', '');
                    qualsDiaSemana[dia.charAt(0).toUpperCase() + dia.slice(1)]++;
                });

                const qualsPorHora = Array(24).fill(0);
                data.forEach(r => qualsPorHora[r['Data Fim Análise'].getHours()]++);

                this.charts.diaHome = new Chart('chart-dia-home', { type: 'line', data: { labels: sortedDays.map(d=>d[0]), datasets: [{ data: sortedDays.map(d=>d[1]), borderColor: '#f97316', tension: 0.1 }] }, options: noLegend });
                this.charts.diaSemanaHome = new Chart('chart-dia-semana-home', { type: 'bar', data: { labels: diasSemanaOrdem, datasets: [{ data: diasSemanaOrdem.map(d => qualsDiaSemana[d]), backgroundColor: '#3b82f6' }] }, options: noLegend });
                this.charts.horarioHome = new Chart('chart-horario-home', { type: 'bar', data: { labels: Array.from({length: 24}, (_, i) => `${i}h`), datasets: [{ data: qualsPorHora, backgroundColor: '#22c55e' }] }, options: noLegend });
            },

            renderRelatorioCharts(data) {
                 const commonOptions = { responsive: true, maintainAspectRatio: false };
                 const noLegend = { ...commonOptions, plugins: { legend: { display: false } } };

                 const userQuals = data.reduce((acc, r) => { acc[r.Usuário] = (acc[r.Usuário] || 0) + 1; return acc; }, {});
                 const sortedUsers = Object.entries(userQuals).sort((a, b) => b[1] - a[1]).slice(0, 20);
                 const slaCounts = data.reduce((acc, r) => { acc[r.SLA] = (acc[r.SLA] || 0) + 1; return acc; }, {});

                 this.charts.ranking = new Chart('chart-ranking-qualificacoes', { type: 'bar', data: { labels: sortedUsers.map(u=>u[0]), datasets: [{ data: sortedUsers.map(u=>u[1]), backgroundColor: '#1d4ed8' }] }, options: { ...noLegend, indexAxis: 'y' } });
                 this.charts.sla = new Chart('chart-sla', { type: 'doughnut', data: { labels: Object.keys(slaCounts), datasets: [{ data: Object.values(slaCounts), backgroundColor: ['#22c55e', '#ef4444'] }] }, options: { ...commonOptions, plugins: { legend: { position: 'bottom' } } } });
                 this.charts.laudo = new Chart('chart-laudo', { type: 'doughnut', data: { labels: ['Aprovado', 'Reprovado'], datasets: [{ data: [63, 37], backgroundColor: ['#22c55e', '#ef4444'] }] }, options: { ...commonOptions, plugins: { legend: { position: 'bottom' } } } });
            }
        };

        // --- MÓDULO PRINCIPAL DA APLICAÇÃO (App) ---
        const App = {
            init() {
                DataManager.loadProfilesFromStorage();
                UIManager.init();
                this.elements = {
                    fileInput: document.getElementById('file-input'),
                };
                this.elements.fileInput.addEventListener('change', (e) => this.handleFileSelect(e));
                
                // Inicia na página Home com o ecrã de upload se não houver dados
                if (DataManager.state.allData.length === 0) {
                     UIManager.elements.mainContent.innerHTML = UIManager.templates.upload;
                     document.getElementById('drop-zone').addEventListener('dragover', (e) => { e.preventDefault(); e.target.closest('#drop-zone').classList.add('border-blue-500'); });
                     document.getElementById('drop-zone').addEventListener('dragleave', (e) => { e.target.closest('#drop-zone').classList.remove('border-blue-500'); });
                     document.getElementById('drop-zone').addEventListener('drop', (e) => { e.preventDefault(); this.handleFileSelect(e); });
                     document.querySelector('label[for="file-input"]').addEventListener('click', () => this.elements.fileInput.click());
                } else {
                     this.handleNavigation({ currentTarget: document.querySelector('.nav-link.active')});
                }
            },

            handleNavigation(e) {
                const link = e.target ? e.target.closest('.nav-link') : e.currentTarget;
                if (!link) return;
                e.preventDefault?.();

                const pageId = link.dataset.page;
                document.querySelectorAll('.nav-link').forEach(l => l.classList.remove('active'));
                link.classList.add('active');

                UIManager.renderPage(pageId);
            },

            handleFileSelect(e) {
                const files = e.target.files || e.dataTransfer.files;
                if (!files.length) return;
                
                const filePromises = Array.from(files).map(file => {
                    return new Promise((resolve, reject) => {
                        const reader = new FileReader();
                        reader.onload = (event) => {
                             try {
                                let parsedData;
                                if (file.name.endsWith('.csv')) {
                                    const lines = event.target.result.split('\n');
                                    const content = lines.slice(3).join('\n');
                                    parsedData = Papa.parse(content, { header: true, skipEmptyLines: true }).data;
                                } else {
                                    const workbook = XLSX.read(new Uint8Array(event.target.result), { type: 'array', cellDates: true });
                                    const worksheet = workbook.Sheets[workbook.SheetNames[0]];
                                    const sheetData = XLSX.utils.sheet_to_json(worksheet, { header: 1 });
                                    let headerRowIndex = sheetData.findIndex(row => row.includes('Empresa') && row.includes('Usuário'));
                                    if (headerRowIndex === -1) throw new Error('Cabeçalho (Empresa, Usuário) não encontrado.');
                                    parsedData = XLSX.utils.sheet_to_json(worksheet, { range: headerRowIndex, raw: false, dateNF: 'yyyy-mm-dd hh:mm:ss' });
                                }
                                resolve({ data: parsedData, name: file.name });
                            } catch (err) {
                                reject(err);
                            }
                        };
                        if (file.name.endsWith('.csv')) reader.readAsText(file);
                        else reader.readAsArrayBuffer(file);
                    });
                });

                Promise.all(filePromises).then(results => {
                    results.forEach(result => {
                        DataManager.processAndAddData(result.data, result.name);
                    });
                    const activeLink = document.querySelector('.nav-link.active') || document.querySelector('.nav-link[data-page="home"]');
                    this.handleNavigation({ currentTarget: activeLink });
                }).catch(error => {
                    console.error("Erro ao processar ficheiros:", error);
                });
            },

            handleReset() {
                DataManager.resetData();
                UIManager.elements.mainContent.innerHTML = UIManager.templates.upload;
                document.getElementById('drop-zone').addEventListener('dragover', (e) => { e.preventDefault(); e.target.closest('#drop-zone').classList.add('border-blue-500'); });
                document.getElementById('drop-zone').addEventListener('dragleave', (e) => { e.target.closest('#drop-zone').classList.remove('border-blue-500'); });
                document.getElementById('drop-zone').addEventListener('drop', (e) => { e.preventDefault(); this.handleFileSelect(e); });
                document.querySelector('label[for="file-input"]').addEventListener('click', () => this.elements.fileInput.click());
            },

            handleProfileFormSubmit(e) {
                e.preventDefault();
                const emailInput = document.getElementById('user-email');
                const profileData = {
                    nome: document.getElementById('user-name').value,
                    email: emailInput.value,
                    funcao: document.getElementById('user-role').value,
                    inicioJornada: document.getElementById('user-start').value,
                    fimJornada: document.getElementById('user-end').value,
                };
                DataManager.addOrUpdateProfile(profileData);
                UIManager.closeProfileModal();
                this.updateUI();
            },
            
            handleEditProfile(email) {
                const profile = DataManager.getProfileByEmail(email);
                UIManager.openProfileModal(profile);
            },
            
            handleRemoveProfile(email) {
                if (confirm(`Tem a certeza que deseja remover o perfil ${email}?`)) {
                    DataManager.removeProfile(email);
                    this.updateUI();
                }
            },
            
            updateUI() {
                const data = DataManager.state.allData;
                const profiles = DataManager.state.userProfiles;
                const activePageId = document.querySelector('.nav-link.active')?.dataset.page;
                
                if (data.length > 0) {
                    ChartManager.renderChartsForPage(activePageId, data);
                    UIManager.updateFileListUI(DataManager.state.loadedFiles);

                    if (activePageId === 'faturamento') {
                        const faturamentoData = DataManager.calculateFaturamento(data);
                        UIManager.updateFaturamentoUI(faturamentoData);
                    }
                    if (activePageId === 'perfis') {
                        UIManager.updateProfileTableUI(profiles, data);
                    }
                    if (activePageId === 'gestao') {
                        UIManager.updateGestaoTableUI(profiles, data);
                    }
                }
            }
        };

        // --- INICIALIZAÇÃO DA APLICAÇÃO ---
        document.addEventListener('DOMContentLoaded', () => App.init());
    </script>

</body>
</html>
