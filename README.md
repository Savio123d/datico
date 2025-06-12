# Guia Completo: Entendendo o CRUD com JavaScript

Este guia detalha a construção de um CRUD (**C**reate, **R**ead, **U**pdate, **D**elete) utilizando apenas HTML, Bootstrap e JavaScript, com os dados salvos no `localStorage` do navegador. Cada seção abaixo foca em uma das quatro operações fundamentais do CRUD, explicando a lógica e mostrando o código específico para aquela funcionalidade.

## Estrutura Base (HTML)

Antes de começar com o JavaScript, precisamos de uma estrutura HTML para interagir. Ela consiste em um formulário para entrada de dados e uma tabela para exibição.

```html
<div class="col-md-5">
    <h3>Cadastro de Usuário</h3>
    <div class="card">
        <div class="card-body">
            <div class="mb-3">
                <label for="login" class="form-label">Usuário</label>
                <input type="text" class="form-control" id="login" required>
            </div>
            <div class="mb-3">
                <label for="senha" class="form-label">Senha</label>
                <input type="password" class="form-control" id="senha" required>
            </div>
            <input type="hidden" id="indexEditado">
            <div class="d-grid gap-2">
                <button id="btnCadastrar" class="btn btn-success">Salvar</button>
            </div>
        </div>
    </div>
</div>

<div class="col-md-7">
    <h3>Usuários Cadastrados</h3>
    <table class="table table-striped table-hover">
        <thead class="table-dark">
            <tr>
                <th>Usuário</th>
                <th>Ações</th>
            </tr>
        </thead>
        <tbody id="tabelaUsuarios">
            </tbody>
    </table>
</div>
```

---

## 1. READ (Ler os Dados)

A operação de **Leitura** é a base de tudo. Ela pega os dados salvos no `localStorage` e os exibe na tabela para o usuário. Esta função é chamada sempre que a página carrega e após qualquer alteração nos dados (criar, atualizar ou excluir).

### Código da Função `listar()`

```javascript
const tabelaUsuarios = document.getElementById('tabelaUsuarios');

function listar() {
    // Pega a lista de usuários do localStorage ou cria um array vazio se não houver nada.
    const listaUsuario = JSON.parse(localStorage.getItem("usuarios")) || [];
    tabelaUsuarios.innerHTML = ''; // Limpa a tabela para evitar duplicatas.

    // Se a lista estiver vazia, exibe uma mensagem.
    if (listaUsuario.length === 0) {
        tabelaUsuarios.innerHTML = '<tr><td colspan="2" class="text-center">Nenhum usuário cadastrado.</td></tr>';
        return;
    }

    // Para cada usuário na lista, cria uma nova linha (<tr>) na tabela.
    listaUsuario.forEach((usuario, index) => {
        const tr = document.createElement('tr');
        tr.innerHTML = `
            <td>${usuario.usuario}</td>
            <td>
                <button class="btn btn-sm btn-warning" onclick="editarUsuario(${index})">Editar</button>
                <button class="btn btn-sm btn-danger" onclick="excluirUsuario(${index})">Excluir</button>
            </td>
        `;
        tabelaUsuarios.appendChild(tr); // Adiciona a nova linha à tabela.
    });
}

// Chama a função 'listar' assim que o script é carregado para exibir os dados iniciais.
listar();
```


---

## 2. CREATE (Criar Novos Dados)

A operação de **Criação** é responsável por adicionar um novo usuário à lista. Ela é acionada pelo botão "Salvar" quando não estamos editando um usuário existente.

### Código da Função de `Criar`

Este trecho de código está dentro do evento de clique do botão `btnCadastrar`.

```javascript
const botao = document.getElementById('btnCadastrar');

botao.addEventListener('click', function () {
    let listaUsuario = JSON.parse(localStorage.getItem("usuarios")) || [];

    // Cria um objeto 'usuario' com os valores dos campos do formulário.
    const usuario = {
        usuario: document.getElementById('login').value,
        senha: document.getElementById('senha').value
    };

    // Pega o valor do campo oculto. Se estiver vazio, significa que é uma criação.
    const indexEditar = document.getElementById("indexEditado").value;

    if (usuario.usuario === '' || usuario.senha === '') {
        alert("Por favor, preencha todos os campos.");
        return;
    }

    // Se o indexEditar estiver vazio, é um novo usuário.
    if (indexEditar === "") {
        listaUsuario.push(usuario); // Adiciona o novo usuário ao final da lista.
    }
    // (A parte do 'else' pertence à operação de Update)

    // Salva a lista atualizada de volta no localStorage.
    localStorage.setItem("usuarios", JSON.stringify(listaUsuario));

    // Limpa os campos e atualiza a tabela.
    document.getElementById('login').value = '';
    document.getElementById('senha').value = '';
    listar();
});
```


---

## 3. UPDATE (Atualizar Dados Existentes)

A operação de **Atualização** permite modificar um usuário que já foi cadastrado. Ela ocorre em duas etapas:

1.  **Preparação**: O usuário clica em "Editar", o que preenche o formulário com os dados existentes.
2.  **Execução**: O usuário altera os dados e clica em "Salvar", que agora irá sobrescrever os dados antigos em vez de criar um novo registro.

### Código da Função `editarUsuario()` (Preparação)

```javascript
function editarUsuario(index) {
    const usuariosCadastrados = JSON.parse(localStorage.getItem("usuarios")) || [];
    const objUsuario = usuariosCadastrados[index]; // Pega o usuário específico pelo seu índice.

    // Preenche os campos do formulário com os dados do usuário.
    document.getElementById("login").value = objUsuario.usuario;
    document.getElementById("senha").value = objUsuario.senha;

    // Preenche o campo oculto com o índice do usuário.
    // Isso é crucial para que a função de salvar saiba que deve ATUALIZAR em vez de CRIAR.
    document.getElementById('indexEditado').value = index;
}
```


### Código da Lógica de `Update` (Execução)

Este é o `else` que complementa a lógica da função de `Criar`.

```javascript
// ... dentro do evento de clique do botão 'btnCadastrar'
const indexEditar = document.getElementById("indexEditado").value;

if (indexEditar !== "") {
    // Se o indexEditar NÃO estiver vazio, atualizamos o usuário na posição correspondente.
    listaUsuario[indexEditar] = usuario;
    // Limpa o campo de índice para a próxima operação ser de criação por padrão.
    document.getElementById("indexEditado").value = "";
} else {
    // ... lógica de CREATE
}
// ... resto da função
```


---

## 4. DELETE (Excluir Dados)

A operação de **Exclusão** remove um usuário da lista permanentemente. Ela é acionada pelo botão "Excluir" na tabela.

### Código da Função `excluirUsuario()`

```javascript
function excluirUsuario(index) {
    const usuariosCadastrados = JSON.parse(localStorage.getItem("usuarios")) || [];

    // Pede uma confirmação ao usuário para evitar exclusões acidentais.
    if (confirm("Você realmente quer excluir este usuário?")) {
        // O método splice() remove o item do array na posição 'index'.
        usuariosCadastrados.splice(index, 1);

        // Salva a lista (agora sem o item excluído) no localStorage.
        localStorage.setItem("usuarios", JSON.stringify(usuariosCadastrados));

        // Atualiza a tabela para refletir a remoção.
        listar();
    }
}
```
