# API Tickets

Projeto de estudo criado com o objetivo de praticar e consolidar conceitos de **APIs REST utilizando Node.js puro** (sem frameworks como Express ou Fastify), explorando diretamente os módulos nativos da plataforma.

A API simula um sistema simples de **gerenciamento de tickets de suporte técnico**, permitindo criar, listar, atualizar, encerrar e remover chamados.

## 🎯 Objetivo

Este repositório não tem fins de produção. Ele foi desenvolvido para fins didáticos, com foco em entender o que frameworks como Express fazem "por debaixo dos panos", implementando manualmente:

- Criação de um servidor HTTP com o módulo nativo `http`;
- Sistema de rotas com parâmetros dinâmicos (ex: `/tickets/:id`) usando `RegExp`;
- Middlewares para tratamento do corpo da requisição (JSON) e roteamento;
- Persistência de dados em arquivo (`db.json`), sem uso de banco de dados externo;
- Organização de código em camadas (rotas, controllers, middlewares, utils e database).

## 🚀 Tecnologias e bibliotecas utilizadas

- **[Node.js](https://nodejs.org/)** — runtime JavaScript (ESM / `type: module`);
- **Módulos nativos do Node.js**:
  - `node:http` — criação do servidor HTTP;
  - `node:fs/promises` — leitura e escrita do "banco de dados" em arquivo JSON;
  - `node:crypto` — geração de identificadores únicos (`randomUUID`);
- Sem dependências de terceiros — projeto construído com **Node.js puro** (vanilla Node).

## 📁 Estrutura do projeto

```
api_tickets/
├── src/
│   ├── controllers/
│   │   └── tickets/
│   │       ├── create.js          # Cria um novo ticket
│   │       ├── index.js           # Lista os tickets (com filtro opcional por status)
│   │       ├── update.js          # Atualiza equipamento/descrição de um ticket
│   │       ├── updateStatus.js    # Encerra um ticket (define status "closed")
│   │       └── remove.js          # Remove um ticket
│   ├── database/
│   │   ├── database.js            # Classe Database (insert, select, update, delete)
│   │   └── db.json                # Arquivo usado como persistência local dos dados
│   ├── middlewares/
│   │   ├── jsonHandler.js         # Faz o parse do corpo da requisição (JSON)
│   │   └── routeHandler.js        # Resolve a rota, extrai params/query e chama o controller
│   ├── routes/
│   │   ├── index.js               # Agrega todas as rotas e aplica o parseRoutePath
│   │   └── tickets.js             # Definição das rotas do recurso "tickets"
│   ├── utils/
│   │   ├── extractQueryParams.js  # Converte a query string em objeto
│   │   └── parseRoutePath.js      # Converte path (ex: /tickets/:id) em RegExp com grupos nomeados
│   └── server.js                  # Ponto de entrada: cria e inicia o servidor HTTP
├── package.json
└── README.md
```

## ▶️ Como executar o projeto

### Pré-requisitos

- [Node.js](https://nodejs.org/) instalado (versão **18.11+**, necessária para o uso do flag `--watch`).

### Passo a passo

```bash
# 1. Clone o repositório
git clone <url-do-repositorio>

# 2. Acesse a pasta do projeto
cd api_tickets

# 3. Inicie o servidor em modo desenvolvimento (com reload automático)
npm run dev
```

O servidor será iniciado em `http://localhost:3333`.

> Como o projeto não possui dependências externas, não é necessário rodar `npm install` antes de executá-lo.

## 📡 Endpoints da API

Todas as requisições e respostas utilizam o formato `application/json`.

| Método | Rota                 | Descrição                                  | Body                                             |
|--------|-----------------------|---------------------------------------------|---------------------------------------------------|
| POST   | `/tickets`            | Cria um novo ticket                         | `{ "equipment": "", "description": "", "user_name": "" }` |
| GET    | `/tickets`            | Lista todos os tickets                      | —                                                   |
| GET    | `/tickets?status=`    | Lista os tickets filtrados por status       | —                                                   |
| PUT    | `/tickets/:id`        | Atualiza equipamento e/ou descrição         | `{ "equipment": "", "description": "" }`           |
| PATCH  | `/tickets/:id/close`  | Encerra o ticket (status: `closed`)         | `{ "solution": "" }`                               |
| DELETE | `/tickets/:id`        | Remove o ticket                             | —                                                   |

### Modelo de dados (Ticket)

```json
{
  "id": "uuid",
  "equipment": "string",
  "description": "string",
  "user_name": "string",
  "status": "open | closed",
  "created_at": "date",
  "updated_at": "date",
  "solution": "string"
}
```

### Exemplos de uso (cURL)

```bash
# Criar um ticket
curl -X POST http://localhost:3333/tickets \
  -H "Content-Type: application/json" \
  -d '{"equipment":"Notebook","description":"Tela não liga","user_name":"Maria"}'

# Listar todos os tickets
curl http://localhost:3333/tickets

# Listar apenas tickets abertos
curl http://localhost:3333/tickets?status=open

# Atualizar um ticket
curl -X PUT http://localhost:3333/tickets/<id> \
  -H "Content-Type: application/json" \
  -d '{"equipment":"Notebook","description":"Tela com defeito na dobradiça"}'

# Encerrar um ticket
curl -X PATCH http://localhost:3333/tickets/<id>/close \
  -H "Content-Type: application/json" \
  -d '{"solution":"Peça substituída"}'

# Remover um ticket
curl -X DELETE http://localhost:3333/tickets/<id>
```

## 💾 Persistência de dados

Os dados são armazenados no arquivo [`src/database/db.json`](src/database/db.json), que funciona como um banco de dados simples em memória sincronizado com o disco. Toda vez que um ticket é criado, atualizado ou removido, o arquivo é regravado.

> ⚠️ Por ser um projeto de estudo, essa abordagem não é recomendada para ambientes de produção. O ideal seria substituir essa camada por um banco de dados real (ex: PostgreSQL, SQLite, MongoDB).

## 📝 Notas e boas práticas aplicadas

- **ES Modules** (`"type": "module"` no `package.json`), utilizando `import`/`export` nativos;
- **Separação de responsabilidades** entre rotas, controllers, middlewares e acesso a dados;
- **Roteamento dinâmico** com `RegExp` e grupos nomeados para capturar parâmetros de rota e query string;
- Uso de **classes privadas** (`#database`, `#persist`) para encapsular o acesso aos dados;
- Scripts com `node --watch` para reinício automático do servidor durante o desenvolvimento, sem a necessidade de ferramentas externas como `nodemon`.

## 📌 Possíveis melhorias futuras

- Validação de dados de entrada (ex: campos obrigatórios);
- Tratamento de erros mais robusto (status codes apropriados para recursos não encontrados, dados inválidos, etc.);
- Testes automatizados (ex: `node:test`);
- Documentação da API via OpenAPI/Swagger;
- Substituição do arquivo `db.json` por um banco de dados real.

## 👤 Autor

**Vinícius Leandro**

## 📄 Licença

Este projeto está sob a licença ISC.
