
---

# Guia: Criando um CRUD Estilizado com Bootstrap e JavaScript

Este guia detalha o processo de criação de um CRUD (**C**reate, **R**ead, **U**pdate, **D**elete) com um visual moderno e responsivo, utilizando os componentes de estilização do **Bootstrap**. A lógica de programação é feita em JavaScript puro e os dados são salvos no `localStorage`.

## Estrutura do Projeto

Vamos dividir nosso projeto em duas partes principais, organizadas pelo sistema de grid do Bootstrap:

1.  **Formulário de Entrada:** Utilizaremos um componente `card` do Bootstrap para agrupar os campos de cadastro, criando uma interface limpa e organizada.
2.  **Tabela de Exibição:** Os dados serão listados em uma tabela estilizada com as classes `table`, `table-striped` e `table-hover` para melhorar a legibilidade.

---

### Passo 1: A Estrutura HTML com Bootstrap

Primeiro, montamos o esqueleto da nossa página. O layout usa o sistema de grid do Bootstrap (`container`, `row`, `col`) para dividir a tela.

```html
<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CRUD com Bootstrap</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container mt-5">
        <h1 class="text-center mb-4">Gerenciador de Itens</h1>
        <div class="row">

            <div class="col-md-5">
                <h3>Formulário</h3>
                <div class="card shadow-sm">
                    <div class="card-body">
                        <div class="mb-3">
                            <label for="nomeItem" class="form-label">Nome do Item</label>
                            <input type="text" class="form-control" id="nomeItem" placeholder="Digite o nome" required>
                        </div>
                        <div class="mb-3">
                            <label for="descricaoItem" class="form-label">Descrição</label>
                            <input type="text" class="form-control" id="descricaoItem" placeholder="Digite a descrição" required>
                        </div>
                        <input type="hidden" id="indexEditado">
                        <div class="d-grid">
                            <button id="btnSalvar" class="btn btn-primary">Salvar</button>
                        </div>
                    </div>
                </div>
            </div>

            <div class="col-md-7">
                <h3>Itens Cadastrados</h3>
                <table class="table table-striped table-hover">
                    <thead class="table-dark">
                        <tr>
                            <th>Item</th>
                            <th>Ações</th>
                        </tr>
                    </thead>
                    <tbody id="tabelaItens">
                        </tbody>
                </table>
            </div>
        </div>
    </div>

    <script>
        // A lógica será inserida aqui
    </script>
</body>
</html>
```

---

### Passo 2: A Lógica JavaScript do CRUD

Agora, vamos adicionar a inteligência do nosso sistema. O código JavaScript será responsável por manipular os dados.

#### **READ (Listar Itens)**

Esta função lê os dados do `localStorage` e preenche nossa tabela estilizada do Bootstrap.

```javascript
const tabelaItens = document.getElementById('tabelaItens');

function listarItens() {
    const listaDeItens = JSON.parse(localStorage.getItem("itens")) || [];
    tabelaItens.innerHTML = '';

    listaDeItens.forEach((item, index) => {
        const tr = document.createElement('tr');
        // Usamos botões do Bootstrap com cores diferentes para cada ação
        tr.innerHTML = `
            <td>
                <strong>${item.nome}</strong><br>
                <small class="text-muted">${item.descricao}</small>
            </td>
            <td class="align-middle">
                <button class="btn btn-sm btn-warning" onclick="prepararEdicao(${index})">Editar</button>
                <button class="btn btn-sm btn-danger" onclick="excluirItem(${index})">Excluir</button>
            </td>
        `;
        tabelaItens.appendChild(tr);
    });
}
```

#### **CREATE e UPDATE (Salvar e Atualizar)**

Esta função é acionada pelo botão "Salvar". Ela decide se deve criar um novo item ou atualizar um existente com base no campo oculto `indexEditado`.

```javascript
const botaoSalvar = document.getElementById('btnSalvar');
const campoNome = document.getElementById('nomeItem');
const campoDescricao = document.getElementById('descricaoItem');
const campoIndexEditado = document.getElementById('indexEditado');

botaoSalvar.addEventListener('click', function() {
    let listaDeItens = JSON.parse(localStorage.getItem("itens")) || [];
    const item = {
        nome: campoNome.value,
        descricao: campoDescricao.value
    };
    const indexParaEditar = campoIndexEditado.value;

    if (indexParaEditar !== "") {
        // Lógica de UPDATE
        listaDeItens[indexParaEditar] = item;
    } else {
        // Lógica de CREATE
        listaDeItens.push(item);
    }

    localStorage.setItem("itens", JSON.stringify(listaDeItens));
    limparFormulario();
    listarItens();
});
```

#### **DELETE (Excluir)**

Esta função remove um item da lista. O `confirm()` do navegador é usado para evitar exclusões acidentais.

```javascript
function excluirItem(index) {
    const listaDeItens = JSON.parse(localStorage.getItem("itens")) || [];
    if (confirm("Você realmente quer excluir este item?")) { //
        listaDeItens.splice(index, 1); //
        localStorage.setItem("itens", JSON.stringify(listaDeItens)); //
        listarItens(); //
    }
}
```

---

### Código Final Completo (Para Copiar e Colar)

Aqui está o código completo. Salve-o como um arquivo `.html` e abra no seu navegador para ver o CRUD estilizado em ação.

```html
<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CRUD com Bootstrap</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
    <div class="container mt-5">
        <h1 class="text-center mb-4">Gerenciador de Itens</h1>
        <div class="row">

            <div class="col-md-5">
                <h3>Formulário</h3>
                <div class="card shadow-sm">
                    <div class="card-body">
                        <div class="mb-3">
                            <label for="nomeItem" class="form-label">Nome do Item</label>
                            <input type="text" class="form-control" id="nomeItem" placeholder="Digite o nome" required>
                        </div>
                        <div class="mb-3">
                            <label for="descricaoItem" class="form-label">Descrição</label>
                            <input type="text" class="form-control" id="descricaoItem" placeholder="Digite a descrição" required>
                        </div>
                        <input type="hidden" id="indexEditado">
                        <div class="d-grid">
                            <button id="btnSalvar" class="btn btn-primary">Salvar</button>
                        </div>
                    </div>
                </div>
            </div>

            <div class="col-md-7">
                <h3>Itens Cadastrados</h3>
                <div class="table-responsive">
                    <table class="table table-striped table-hover">
                        <thead class="table-dark">
                            <tr>
                                <th>Item</th>
                                <th>Ações</th>
                            </tr>
                        </thead>
                        <tbody id="tabelaItens">
                            </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>

    <script>
        const botaoSalvar = document.getElementById('btnSalvar');
        const tabelaItens = document.getElementById('tabelaItens');
        const campoNome = document.getElementById('nomeItem');
        const campoDescricao = document.getElementById('descricaoItem');
        const campoIndexEditado = document.getElementById('indexEditado');

        // Função para limpar o formulário
        function limparFormulario() {
            campoNome.value = '';
            campoDescricao.value = '';
            campoIndexEditado.value = '';
            campoNome.focus();
        }

        // READ: Lista os itens na tabela
        function listarItens() {
            const listaDeItens = JSON.parse(localStorage.getItem("itens")) || [];
            tabelaItens.innerHTML = '';
            if (listaDeItens.length === 0) {
                tabelaItens.innerHTML = '<tr><td colspan="2" class="text-center">Nenhum item cadastrado.</td></tr>';
                return;
            }
            listaDeItens.forEach((item, index) => {
                const tr = document.createElement('tr');
                tr.innerHTML = `
                    <td><strong>${item.nome}</strong><br><small class="text-muted">${item.descricao}</small></td>
                    <td class="align-middle">
                        <button class="btn btn-sm btn-warning" onclick="prepararEdicao(${index})">Editar</button>
                        <button class="btn btn-sm btn-danger" onclick="excluirItem(${index})">Excluir</button>
                    </td>
                `;
                tabelaItens.appendChild(tr);
            });
        }

        // UPDATE (Preparação): Prepara o formulário para edição
        function prepararEdicao(index) {
            const listaDeItens = JSON.parse(localStorage.getItem("itens")) || [];
            const item = listaDeItens[index];
            campoNome.value = item.nome;
            campoDescricao.value = item.descricao;
            campoIndexEditado.value = index;
        }

        // DELETE: Exclui um item
        function excluirItem(index) {
            const listaDeItens = JSON.parse(localStorage.getItem("itens")) || [];
            if (confirm(`Tem certeza que deseja excluir o item "${listaDeItens[index].nome}"?`)) {
                listaDeItens.splice(index, 1);
                localStorage.setItem("itens", JSON.stringify(listaDeItens));
                listarItens();
            }
        }

        // Evento do botão Salvar (CREATE e UPDATE)
        botaoSalvar.addEventListener('click', function() {
            if (campoNome.value.trim() === '' || campoDescricao.value.trim() === '') {
                alert("Por favor, preencha todos os campos.");
                return;
            }

            let listaDeItens = JSON.parse(localStorage.getItem("itens")) || [];
            const item = { nome: campoNome.value, descricao: campoDescricao.value };
            const indexParaEditar = campoIndexEditado.value;

            if (indexParaEditar !== "") {
                listaDeItens[indexParaEditar] = item;
            } else {
                listaDeItens.push(item);
            }

            localStorage.setItem("itens", JSON.stringify(listaDeItens));
            limparFormulario();
            listarItens();
        });

        // Carrega a lista inicial quando a página é aberta
        document.addEventListener('DOMContentLoaded', listarItens);
    </script>
</body>
</html>
```
