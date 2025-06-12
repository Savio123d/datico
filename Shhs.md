<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Orçamentos de Jardinagem</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        /* CSS para centralizar e limitar a largura, como pedido */
        body {
            background-color: #f8f9fa;
        }
        .container {
            max-width: 900px;
        }
    </style>
</head>
<body>
    <div class="container mt-4">
        <h1 class="text-center">Controle de Orçamentos</h1>

        <div class="card shadow-sm mb-5">
            <div class="card-header">
                <h3>Cadastrar Novo Orçamento</h3>
            </div>
            <div class="card-body">
                <form id="formOrcamento">
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label for="nomeCliente" class="form-label">Nome do cliente</label>
                            <input type="text" class="form-control" id="nomeCliente" required>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label for="areaTerreno" class="form-label">Área do terreno (em m²)</label>
                            <input type="number" class="form-control" id="areaTerreno" required>
                        </div>
                    </div>
                    <div class="row">
                        <div class="col-md-6 mb-3">
                            <label for="tipoServico" class="form-label">Tipo de serviço</label>
                            <select class="form-select" id="tipoServico" required>
                                <option value="" selected disabled>Selecione...</option>
                                <option value="Poda">Poda</option>
                                <option value="Plantio">Plantio</option>
                                <option value="Manutenção Completa">Manutenção Completa</option>
                            </select>
                        </div>
                        <div class="col-md-6 mb-3">
                            <label class="form-label">Deseja manutenção mensal?</label>
                            <div>
                                <div class="form-check form-check-inline">
                                    <input class="form-check-input" type="radio" name="manutencaoMensal" id="manutencaoSim" value="Sim" required>
                                    <label class="form-check-label" for="manutencaoSim">Sim</label>
                                </div>
                                <div class="form-check form-check-inline">
                                    <input class="form-check-input" type="radio" name="manutencaoMensal" id="manutencaoNao" value="Não" required>
                                    <label class="form-check-label" for="manutencaoNao">Não</label>
                                </div>
                            </div>
                        </div>
                    </div>
                    <input type="hidden" id="indexEditado">
                    <button type="submit" class="btn btn-primary">Salvar Orçamento</button>
                    <button type="button" class="btn btn-outline-secondary" onclick="limparFormulario()">Limpar</button>
                </form>
            </div>
        </div>

        <h3 class="mt-5">Orçamentos Cadastrados</h3>
        <table class="table table-striped table-hover">
            <thead class="table-dark">
                <tr>
                    <th>Nome</th>
                    <th>Tipo de Serviço</th>
                    <th>Área (m²)</th>
                    <th>Manutenção Mensal</th>
                    <th>Total Estimado</th>
                    <th>Ações</th>
                </tr>
            </thead>
            <tbody id="tabelaOrcamentos">
                </tbody>
        </table>
    </div>

    <script>
        const form = document.getElementById('formOrcamento');
        const tabelaOrcamentos = document.getElementById('tabelaOrcamentos');

        // Função para calcular o total do orçamento conforme as regras
        function calcularTotal(area, tipoServico, manutencaoMensal) {
            const precoBaseM2 = 10;
            let total = area * precoBaseM2;

            if (tipoServico === "Poda") {
                total *= 1.20; // +20%
            } else if (tipoServico === "Plantio") {
                total *= 1.50; // +50%
            } else if (tipoServico === "Manutenção Completa") {
                total *= 1.80; // +80%
            }

            if (manutencaoMensal === "Sim") {
                total += 100; // + R$100 fixos
            }

            return total;
        }

        // b) LER: Lista os orçamentos na tabela
        function listarOrcamentos() {
            const orcamentos = JSON.parse(localStorage.getItem('orcamentos')) || [];
            tabelaOrcamentos.innerHTML = '';

            if (orcamentos.length === 0) {
                tabelaOrcamentos.innerHTML = '<tr><td colspan="6" class="text-center">Nenhum orçamento cadastrado.</td></tr>';
                return;
            }

            orcamentos.forEach((orcamento, index) => {
                const tr = document.createElement('tr');
                tr.innerHTML = `
                    <td>${orcamento.nome}</td>
                    <td>${orcamento.servico}</td>
                    <td>${orcamento.area}</td>
                    <td>${orcamento.manutencao}</td>
                    <td>${orcamento.total.toLocaleString('pt-BR', { style: 'currency', currency: 'BRL' })}</td>
                    <td>
                        <button class="btn btn-sm btn-warning" onclick="prepararEdicao(${index})">Editar</button>
                        <button class="btn btn-sm btn-danger" onclick="excluirOrcamento(${index})">Excluir</button>
                    </td>
                `;
                tabelaOrcamentos.appendChild(tr);
            });
        }

        // d) DELETAR: Remove um orçamento
        function excluirOrcamento(index) {
            let orcamentos = JSON.parse(localStorage.getItem('orcamentos')) || [];
            if (confirm(`Deseja realmente excluir o orçamento de ${orcamentos[index].nome}?`)) {
                orcamentos.splice(index, 1);
                localStorage.setItem('orcamentos', JSON.stringify(orcamentos));
                listarOrcamentos();
            }
        }

        // c) ATUALIZAR (Preparação): Carrega os dados no formulário para edição
        function prepararEdicao(index) {
            const orcamentos = JSON.parse(localStorage.getItem('orcamentos')) || [];
            const orcamento = orcamentos[index];

            document.getElementById('nomeCliente').value = orcamento.nome;
            document.getElementById('areaTerreno').value = orcamento.area;
            document.getElementById('tipoServico').value = orcamento.servico;
            document.getElementById('indexEditado').value = index;

            if (orcamento.manutencao === 'Sim') {
                document.getElementById('manutencaoSim').checked = true;
            } else {
                document.getElementById('manutencaoNao').checked = true;
            }

            window.scrollTo(0, 0); // Rola a página para o topo para ver o formulário preenchido
        }
        
        function limparFormulario() {
            form.reset();
            document.getElementById('indexEditado').value = '';
        }

        // a) CRIAR e c) ATUALIZAR (Execução): Evento de submit do formulário
        form.addEventListener('submit', function(event) {
            event.preventDefault();

            const nome = document.getElementById('nomeCliente').value;
            const area = parseFloat(document.getElementById('areaTerreno').value);
            const servico = document.getElementById('tipoServico').value;
            const manutencao = document.querySelector('input[name="manutencaoMensal"]:checked').value;
            const index = document.getElementById('indexEditado').value;

            const total = calcularTotal(area, servico, manutencao);

            const orcamento = { nome, area, servico, manutencao, total };

            let orcamentos = JSON.parse(localStorage.getItem('orcamentos')) || [];

            if (index === "") {
                // Criar novo
                orcamentos.push(orcamento);
            } else {
                // Atualizar existente
                orcamentos[index] = orcamento;
            }

            localStorage.setItem('orcamentos', JSON.stringify(orcamentos));
            limparFormulario();
            listarOrcamentos();
        });

        // Função para carregar os exemplos iniciais
        function carregarExemplos() {
             if (!localStorage.getItem('exemplosCarregados')) {
                const exemplos = [
                    { nome: "João Silva", area: 100, servico: "Plantio", manutencao: "Não", total: calcularTotal(100, "Plantio", "Não") },
                    { nome: "Maria Oliveira", area: 50, servico: "Manutenção Completa", manutencao: "Sim", total: calcularTotal(50, "Manutenção Completa", "Sim") }
                ];
                localStorage.setItem('orcamentos', JSON.stringify(exemplos));
                localStorage.setItem('exemplosCarregados', 'true');
             }
        }

        // Ao carregar a página, carrega os exemplos (se for a 1ª vez) e lista os orçamentos
        document.addEventListener('DOMContentLoaded', () => {
            carregarExemplos();
            listarOrcamentos();
        });
    </script>
</body>
</html>
