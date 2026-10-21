# 📚 Book Store API — Testes Automatizados com Postman

Coleção Postman para testes automatizados da **Book Store API**, uma API pública de demonstração que simula uma livraria virtual com sistema de autenticação e gerenciamento de livros.

O projeto demonstra a aplicação de testes de API, validações automatizadas, geração dinâmica de dados e compartilhamento de variáveis entre requisições.

## 🔗 Base URL

```text
https://demoqa.com
```

## 📁 Estrutura da Coleção

```text
Book Store API
├── Account
│   ├── User                         (POST) - Criação de usuário
│   ├── GenerateToken                (POST) - Geração de token JWT
│   ├── Authorized                   (POST) - Verificação de autorização
│   └── User details chosen books    (GET)  - Detalhes do usuário e livros
│
└── BookStore
    ├── Books                        (GET)  - Listagem de livros disponíveis
    └── Hire Books                   (POST) - Aluguel de livros
```

## 🔐 Autenticação

A API utiliza **Bearer Token (JWT)** para autenticar as requisições protegidas.

O fluxo de autenticação é composto pelas seguintes etapas:

1. Criar um usuário.
2. Gerar um token de autenticação.
3. Utilizar o token nas requisições protegidas.
4. Validar a autorização do usuário.

Header de autenticação:

```http
Authorization: Bearer {{token}}
```

## 📋 Endpoints

### 👤 Account

#### 1. Criar Usuário

**POST** `/Account/v1/User`

Cria um novo usuário na plataforma. O nome de usuário é gerado dinamicamente, e a senha é criada por meio de um script de pré-requisição.

**Body:**

```json
{
  "userName": "{{$randomUserName}}",
  "password": "{{password}}"
}
```

**Automações:**

- **Pre-request Script:** gera uma senha aleatória que atende aos critérios de complexidade, incluindo letra maiúscula, número e caractere especial.
- **Test Script:** valida o status HTTP `201 Created` e armazena `userId` e `userName` no environment.

#### 2. Gerar Token

**POST** `/Account/v1/GenerateToken`

Gera um token JWT para autenticar o usuário criado.

**Body:**

```json
{
  "userName": "{{userName}}",
  "password": "{{password}}"
}
```

**Automações:**

- **Test Script:** valida o status HTTP `200 OK`.
- Verifica se o token retornado não está vazio.
- Armazena o token na variável `{{token}}` do environment.

#### 3. Verificar Autorização

**POST** `/Account/v1/Authorized`

Verifica se o usuário está autorizado a acessar a plataforma.

**Autenticação:** Bearer Token (`{{token}}`)

**Body:**

```json
{
  "userName": "{{userName}}",
  "password": "{{password}}"
}
```

**Automações:**

- Valida o status HTTP `200 OK`.
- Verifica se o corpo da resposta é `true`.

#### 4. Consultar Usuário e Livros

**GET** `/Account/v1/User/{{userId}}`

Retorna os dados do usuário, incluindo os livros associados ao seu perfil.

**Autenticação:** Bearer Token (`{{token}}`)

**Automações:**

- Valida o status HTTP `200 OK`.
- Permite verificar os livros associados ao usuário após a execução do fluxo de aluguel.

### 📚 BookStore

#### 5. Listar Livros

**GET** `/BookStore/v1/Books`

Retorna a lista de livros disponíveis na livraria.

**Automações:**

- Valida o status HTTP `200 OK`.
- Extrai os ISBNs dos livros retornados.
- Armazena os ISBNs em `{{isbns}}`, como uma lista separada por vírgulas, no environment.

#### 6. Alugar Livros

**POST** `/BookStore/v1/Books`

Realiza o aluguel de livros para o usuário autenticado, selecionando automaticamente dois ISBNs da lista obtida no endpoint anterior.

**Autenticação:** Bearer Token (`{{token}}`)

**Body:**

```json
{
  "userId": "{{userId}}",
  "collectionOfIsbns": [
    {
      "isbn": "{{isbn1}}"
    },
    {
      "isbn": "{{isbn2}}"
    }
  ]
}
```

**Automações:**

- **Pre-request Script:** lê a variável `{{isbns}}`, embaralha a lista usando o algoritmo Fisher-Yates e seleciona dois ISBNs.
- Armazena os ISBNs selecionados em `{{isbn1}}` e `{{isbn2}}`.
- **Test Script:** valida o status HTTP `201 Created`.

## ⚙️ Variáveis de Environment

| Variável | Descrição | Preenchimento |
|---|---|---|
| `baseUrl` | URL base da API (`https://demoqa.com`) | Manual |
| `userName` | Nome do usuário criado | Automático |
| `password` | Senha gerada dinamicamente | Automático |
| `userId` | ID do usuário retornado no cadastro | Automático |
| `token` | Token JWT gerado após a autenticação | Automático |
| `isbns` | Lista de ISBNs disponíveis, separados por vírgula | Automático |
| `isbn1` | Primeiro ISBN selecionado para aluguel | Automático |
| `isbn2` | Segundo ISBN selecionado para aluguel | Automático |

## ▶️ Como Executar

Para executar o fluxo completo, siga a ordem abaixo:

1. **User:** cria o usuário e armazena as credenciais.
2. **GenerateToken:** autentica o usuário e armazena o token JWT.
3. **Authorized:** verifica se o usuário está autorizado.
4. **Books:** lista os livros disponíveis e armazena os ISBNs.
5. **Hire Books:** seleciona e aluga dois livros.
6. **User details chosen books:** consulta o usuário e confirma os livros associados ao perfil.

### Execução pelo Postman

1. Importe a coleção Book Store API no Postman.
2. Configure o environment com a variável `baseUrl`.
3. Selecione o environment configurado.
4. Abra o **Collection Runner**.
5. Execute as requisições na ordem definida acima.
6. Confira os resultados e as validações na aba **Test Results**.

> **Importante:** execute as requisições na ordem indicada para garantir que as variáveis necessárias sejam preenchidas antes de serem utilizadas.

## 🧪 Testes Automatizados

A coleção utiliza scripts JavaScript do Postman para validar as respostas da API e automatizar o fluxo entre as requisições.

As principais validações incluem:

- ✅ Status HTTP esperado, como `200 OK` e `201 Created`.
- ✅ Estrutura e conteúdo das respostas.
- ✅ Validação do token JWT.
- ✅ Verificação da autorização do usuário.
- ✅ Geração dinâmica de credenciais.
- ✅ Extração e seleção aleatória de ISBNs.
- ✅ Compartilhamento automático de variáveis entre requisições.
- ✅ Verificação dos livros associados ao usuário.

## 🛠️ Tecnologias e Ferramentas

- **Postman:** execução de requisições e testes automatizados de API.
- **JavaScript:** scripts de pré-requisição e validações automatizadas.
- **DemoQA Book Store API:** API utilizada para os cenários de teste.
- **JSON:** formato de envio e recebimento de dados.
- **JWT / Bearer Token:** autenticação das requisições protegidas.

## 🎯 Objetivo do Projeto

Praticar e demonstrar conhecimentos em **QA e Automação de Testes de API**, incluindo:

- Criação e organização de coleções no Postman.
- Automação de cenários de teste.
- Validação de respostas HTTP.
- Manipulação de variáveis de ambiente.
- Geração dinâmica de dados de teste.
- Autenticação e autorização.
- Encadeamento de requisições em um fluxo automatizado.
- Testes de integração entre endpoints.

---

**Projeto desenvolvido para fins de estudo e demonstração de conhecimentos em testes automatizados de API.**
