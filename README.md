# User Management API

API REST para gerenciamento de usuários, construída com **NestJS**, **Prisma ORM** e **PostgreSQL**, com autenticação via **JWT**.

## 📋 Sobre o projeto

Esta API permite o cadastro, autenticação e gerenciamento completo de usuários (CRUD), com as seguintes características:

- Senhas armazenadas com hash (bcrypt) — nunca em texto puro
- Exclusão lógica (soft delete) — usuários "removidos" não são apagados do banco, apenas marcados como inativos
- Autenticação via JWT — rotas sensíveis exigem um token válido
- Banco de dados isolado em container Docker

## 🛠️ Tecnologias utilizadas

| Tecnologia | Função |
|---|---|
| [Node.js](https://nodejs.org) | Ambiente de execução do JavaScript no servidor |
| [NestJS](https://nestjs.com) | Framework para construção da API |
| [Docker](https://www.docker.com) | Isolamento do banco de dados em container |
| [PostgreSQL](https://www.postgresql.org) | Banco de dados relacional |
| [Prisma ORM](https://www.prisma.io) | Camada de acesso ao banco de dados |
| [JWT](https://jwt.io) | Autenticação baseada em token |
| [bcrypt](https://www.npmjs.com/package/bcrypt) | Hash de senhas |

## ✅ Pré-requisitos

Antes de começar, você precisa ter instalado:

- [Node.js](https://nodejs.org) (versão 18 ou superior)
- [Docker Desktop](https://www.docker.com/products/docker-desktop) (em execução)
- [Git](https://git-scm.com)

## 🚀 Como executar o projeto

### 1. Clonar o repositório

```bash
git clone https://github.com/Jonas-Araujo/user-management-api.git
cd user-management-api
```

### 2. Instalar as dependências

```bash
npm install
```

### 3. Configurar as variáveis de ambiente

Crie um arquivo `.env` na raiz do projeto com o seguinte conteúdo:

```env
DATABASE_URL="postgresql://postgres:postgres@localhost:5432/user_api?schema=public"
JWT_SECRET="substitua-por-uma-chave-secreta-longa-e-unica"
```

> ⚠️ O arquivo `.env` nunca deve ser enviado ao repositório (já está protegido pelo `.gitignore`).

### 4. Subir o banco de dados PostgreSQL via Docker

```bash
docker compose up -d
```

Isso cria e inicia um container Postgres, com os dados persistidos em um volume Docker.

Para confirmar que subiu corretamente:

```bash
docker ps
```

### 5. Gerar o Prisma Client e aplicar as migrations

```bash
npx prisma generate
npx prisma migrate dev
```

Esse passo cria a tabela `User` no banco de dados.

### 6. Rodar o servidor em modo desenvolvimento

```bash
npm run start:dev
```

A API estará disponível em: **http://localhost:3000**

## 📚 Endpoints disponíveis

| Método | Rota | Descrição | Autenticação |
|---|---|---|:---:|
| `POST` | `/users` | Cria um novo usuário | ❌ |
| `POST` | `/auth/login` | Realiza login e retorna um token JWT | ❌ |
| `GET` | `/users` | Lista todos os usuários ativos | ✅ |
| `GET` | `/users/:id` | Busca um usuário específico | ✅ |
| `PATCH` | `/users/:id` | Atualiza um ou mais campos do usuário | ✅ |
| `DELETE` | `/users/:id` | Remove o usuário (soft delete) | ✅ |

### Autenticação

Rotas marcadas com ✅ exigem o envio de um token JWT válido, obtido através do login, no cabeçalho da requisição:

```
Authorization: Bearer <seu_token_aqui>
```

O token expira em **1 hora**. Após expirado, é necessário realizar login novamente (não há fluxo de refresh token).

### Exemplo de corpo de requisição — Criar usuário

```json
{
  "email": "usuario@exemplo.com",
  "password": "senha123",
  "name": "Nome do Usuário"
}
```

### Exemplo de corpo de requisição — Login

```json
{
  "email": "usuario@exemplo.com",
  "password": "senha123"
}
```

**Resposta:**
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

## 📁 Estrutura de pastas (resumida)

```
src/
├── auth/            # Módulo de autenticação (login, JWT)
├── prisma/          # Serviço de conexão com o banco (Prisma)
├── users/           # Módulo de usuários (CRUD)
│   ├── dto/         # Formato dos dados de entrada
│   └── entities/    # Representação da entidade User
├── app.module.ts
└── main.ts
prisma/
├── schema.prisma    # Definição das tabelas do banco
└── migrations/      # Histórico de alterações no banco
docker-compose.yml    # Configuração do container Postgres
```

## 🧪 Testando a API

Recomenda-se o uso de uma ferramenta como o [Thunder Client](https://www.thunderclient.com/) (extensão gratuita do VSCode) ou [Insomnia](https://insomnia.rest/) para testar os endpoints manualmente.

## 📝 Notas técnicas

- Este projeto utiliza o **Prisma ORM versão 7**, que trouxe mudanças recentes na configuração:
  - A string de conexão do banco fica no arquivo `prisma.config.ts`, não mais no `schema.prisma`
  - É necessário um *driver adapter* (`@prisma/adapter-pg`) para conectar ao PostgreSQL
  - O gerador de client requer `moduleFormat = "cjs"` para compatibilidade com o NestJS

## 📄 Licença

Este projeto foi desenvolvido para fins de estudo e avaliação técnica.
