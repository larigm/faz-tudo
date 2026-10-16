# 🛠️ Architecture / Software Design Document

**Projeto:** Faz-Tudo  
**Versão:** 1.0.0 · Versão Inicial aprovada pelo aluno  
**Última atualização:** 2026-10-16  

> 🤖 **O `prd.md` responde _o quê_ o produto faz. Este responde _onde as coisas
> moram e como se chamam_.** Detalhe de tela — rota, componente, contrato —
> **não** se decide aqui: isso é trabalho da spec de cada história.
>
> ✍️ **Não preencha na mão:** gerado e mantido via `/utf-architecture`.
> Este documento garante as quatro declarações que o `/utf-setup` exige:
> **framework do backend, framework do frontend, estrutura do monorepo e como rodar os testes.**

---

## 🤖 1. Fontes de Contexto para a IA

| Fonte | Onde configurar | Serve para |
| :---- | :-------------- | :--------- |
| Constituição da IA | `.agents/rules/utf-rules.md` (via `CLAUDE.md` / `AGENTS.md`) | Regras inegociáveis: fases do SDD, 2 rodadas, revisores distintos, Git |
| Fluxos da IA | `.agents/workflows/` | PRD, backlog, jornadas e tokens, architecture, setup, ciclo por Issue, ciclo por tarefa, tutor |
| Agentes (subagentes) | `.agents/agents/` (cascas em `.claude/`, `.cursor/` e `.opencode/`) | Implementador, revisores, auditor final e tutor |
| Ficha da disciplina | `docs/checklist.md` | Regras do projeto, IDs e entregas |
| Design (Figma) | [Protótipo no Figma](https://www.figma.com/proto/YyzpTPWguiYt9UtFqmFkBV/Sem-t%C3%ADtulo?node-id=2-9277&t=3nCrPZY7zo0f1i6q-1&scaling=min-zoom&content-scaling=fixed&page-id=0%3A1&starting-point-node-id=2%3A9277&show-proto-sidebar=1) | Cores, tipografia, hierarquia visual e estados de tela |

---

## 📦 2. Stack Tecnológica

Definição estrita para o monorepo:

- **Backend:** **NestJS 12+** com **Prisma ORM 7+** (linha GA estável) e **PostgreSQL 16+**
- **Frontend:** **React 19+** com **Vite**
- **Padrões de código do frontend:**
  - Componentes funcionais com hooks (sem classes).
  - Separação entre estado de UI (local) e estado de servidor (cache/dados remotos).
  - Roteamento com *lazy loading* por rota de funcionalidade.
  - Componentes de página (`pages/`) separados de componentes visuais reutilizáveis (`components/`).
  - **Regra inegociável da Camada de Dados:** Componente de interface nunca faz requisições HTTP diretamente. Todo acesso à API passa por uma camada de serviços/repositórios tipada (`services/`). Alteração de contrato reflete apenas nessa camada, protegendo as telas.
- **Estilo:** CSS baseado estritamente nos tokens de [docs/design-tokens.md](file:///c:/Users/laris/faz-tudo/docs/design-tokens.md) (família Lato, cores `#267052`, `#AF4946`, `#FFFFFF`, `#F5F7F5`, sem sombras e `border-radius: 8px`).
- **Testes & Qualidade:**
  - Backend: **Jest** (`apps/api`)
  - Frontend: **Vitest** + **React Testing Library** + **jsdom** (`apps/web`)
  - Linting e Formatação: **ESLint** + **Prettier** para todo o monorepo TypeScript.
  - **Comandos exatos:**
    - `npm run test --workspace=api` (executa a suíte de testes do backend via Jest)
    - `npm run test --workspace=web` (executa a suíte de testes do frontend via Vitest)
    - `npm test` (executa os testes de todo o monorepo)
    - `npm run lint` (executa a checagem de lint no monorepo inteiro)

### 🧱 2.1. Backend — regras estruturais (IDs da disciplina)

1. **Separação de Camadas (ID6):**
   - **Controllers:** Apenas recebem requisições HTTP, delegam a lógica aos Services e formatam a resposta. É proibida lógica de negócio ou chamadas diretas ao Prisma no Controller.
   - **Services:** Concentram 100% das regras de negócio, validações de domínio e manipulação de entidades via `PrismaService`.
   - **Modules:** Encapsulam provedores, controladores e exportam apenas serviços necessários para outros domínios.

2. **Blindagem de Entradas e DTOs (ID7):**
   - Todas as rotas que recebem dados (`body`, `query`, `param`) exigem um **Data Transfer Object (DTO)** com validações declarativas via `class-validator` e `class-transformer`.
   - O `ValidationPipe` global deve ser registrado no `main.ts` com as configurações:
     ```ts
     new ValidationPipe({
       whitelist: true,
       forbidNonWhitelisted: true,
       transform: true,
     })
     ```

3. **Autenticação e Controle de Acesso (ID9):**
   - Autenticação stateless via **JWT (JSON Web Token)** utilizando `@nestjs/passport` e `passport-jwt`.
   - Rotas protegidas globalmente via `JwtAuthGuard`, com exceção das rotas públicas marcadas com decorador `@Public()`.
   - Controle de autorização baseado em papéis (`CLIENT`, `PROFESSIONAL`, `ADMIN`) via decorador `@Roles(...)` e `RolesGuard`.

4. **Padronização de Respostas e Erros (ID10):**
   - **Envelope de Sucesso (Interceptor Global):**
     ```json
     {
       "success": true,
       "data": {},
       "timestamp": "2026-10-16T01:00:00.000Z"
     }
     ```
   - **Envelope de Erro (Exception Filter Global):**
     ```json
     {
       "success": false,
       "statusCode": 400,
       "message": "Mensagem descritiva do erro",
       "error": "Bad Request",
       "timestamp": "2026-10-16T01:00:00.000Z",
       "path": "/service-requests"
     }
     ```

5. **Gerenciamento de Segredos e Configurações (ID17):**
   - Variáveis de ambiente gerenciadas centralizadamente via `@nestjs/config` (`ConfigModule`).
   - O arquivo `.env` é estritamente ignorado no `.gitignore`.
   - O arquivo `.env.example` é versionado no Git com todas as chaves necessárias documentadas com valores de exemplo seguros.

6. **Meio de Pagamento Sandbox e Webhooks (ID20 e ID21):**
   - Gateway de pagamento: **Mercado Pago** em ambiente de testes (sandbox).
   - Manipulação de valores monetários estritamente com o tipo **`Decimal`** (evitando erros de ponto flutuante).
   - Endpoint de Webhook dedicado para processar confirmações assíncronas com validação de assinatura criptográfica.

### 🌐 2.2. O contrato da API (ID14)

A especificação **OpenAPI / Swagger** é gerada automaticamente a partir dos decoradores do código NestJS (`@nestjs/swagger`) e servida interativamente em:
```text
/api/docs
```
O frontend deriva seus contratos diretamente desse endpoint vivo. Este documento **não mantém tabela de endpoints manual**, e nenhum arquivo estático como `swagger.json` é versionado no Git.

---

## 🗂️ 3. Estrutura do Repositório (Monorepo)

O projeto é estruturado como um monorepo via **npm workspaces** nativo:

```text
faz-tudo/
├── .agents/                       # Constituição, workflows e subagentes UTF-SDD
├── .github/                       # GitHub Actions (CI) e templates de Pull Request
├── apps/
│   ├── api/                       # Backend (NestJS 12+)
│   │   ├── prisma/                # schema.prisma, migrations e seeds
│   │   ├── src/
│   │   │   ├── common/            # Filtros globais, interceptors, guards e decorators
│   │   │   ├── modules/           # Módulos de domínio
│   │   │   │   ├── auth/
│   │   │   │   ├── users/
│   │   │   │   ├── professionals/
│   │   │   │   ├── categories/
│   │   │   │   ├── service-requests/
│   │   │   │   ├── payments/
│   │   │   │   ├── reviews/
│   │   │   │   └── reports/
│   │   │   ├── prisma/            # PrismaModule e PrismaService
│   │   │   ├── app.module.ts
│   │   │   └── main.ts
│   │   ├── test/                  # Testes e2e (Jest)
│   │   └── package.json
│   │
│   └── web/                       # Frontend (React 19 + Vite)
│       ├── src/
│       │   ├── assets/            # Imagens e ícones estáticos
│       │   ├── components/        # Componentes reutilizáveis (botões, inputs, cards)
│       │   ├── contexts/          # AuthContext e provedores de estado global
│       │   ├── hooks/             # Custom hooks
│       │   ├── pages/             # Telas principais mapeadas do Figma
│       │   ├── services/          # Camada de Dados (cliente HTTP tipado)
│       │   ├── App.tsx
│       │   └── main.tsx
│       ├── tests/                 # Testes de componentes (Vitest + Testing Library)
│       └── package.json
├── docs/                          # prd.md, user-flows.md, design-tokens.md, architecture.md, checklist.md
├── specs/                         # Especificações detalhadas por história implementada
├── docker-compose.yml             # PostgreSQL 16 local
└── package.json                   # Raiz do monorepo com workspaces
```

---

## 🏗️ 4. Arquitetura Frontend

- **Camada de Apresentação:** Componentes em `src/pages/` e `src/components/` apenas renderizam o estado e disparam ações.
- **Camada de Serviços / API:** Todos os endpoints são consumidos através de funções tipadas em `src/services/api/` (ex.: `authService.ts`, `servicesService.ts`, `paymentsService.ts`).
- **Autenticação:** O token JWT retornado no login é armazenado de forma segura no cliente (ex.: localStorage/cookies) e anexado automaticamente no cabeçalho `Authorization: Bearer <token>` através de um interceptor HTTP.

---

## 🗄️ 5. Arquitetura de Dados

### 📖 5.1. Glossário Técnico (Mapeamento)

Ponte entre os termos do negócio no PRD (PT-BR) e as entidades do código/banco (EN):

| Termo PRD (PT-BR) | Entidade técnica (EN) | Atributos principais e tipos |
| :---------------- | :-------------------- | :--------------------------- |
| **Usuário** | `User` | `id` (String PK), `email` (String UK), `passwordHash` (String), `name` (String), `phone` (String), `role` (Enum), `status` (Enum), `createdAt` (DateTime) |
| **Perfil Profissional** | `ProfessionalProfile` | `id` (String PK), `userId` (String FK 0..1), `bio` (String), `region` (String), `status` (Enum), `rejectionReason` (String?), `createdAt` (DateTime) |
| **Categoria de Serviço** | `ServiceCategory` | `id` (String PK), `name` (String UK), `slug` (String UK), `description` (String) |
| **Solicitação de Serviço** | `ServiceRequest` | `id` (String PK), `clientId` (String FK), `professionalId` (String FK), `categoryId` (String FK), `description` (String), `address` (String), `preferredDate` (DateTime), `budget` (Decimal?), `negotiatedPrice` (Decimal?), `status` (Enum), `createdAt` (DateTime) |
| **Pagamento** | `Payment` | `id` (String PK), `serviceRequestId` (String FK 1:N), `amount` (Decimal), `status` (Enum), `gatewayTransactionId` (String?), `paidAt` (DateTime?) |
| **Avaliação** | `Review` | `id` (String PK), `serviceRequestId` (String FK), `authorId` (String FK), `targetId` (String FK), `rating` (Int 1..5), `comment` (String), `createdAt` (DateTime) |
| **Denúncia** | `Report` | `id` (String PK), `serviceRequestId` (String FK), `reporterId` (String FK), `reportedUserId` (String FK), `reason` (String), `description` (String), `status` (Enum), `createdAt` (DateTime) |

> 💰 **Regra de Precisão Monetária:** Campos de valores financeiros (`budget`, `negotiatedPrice`, `amount`) utilizam obrigatoriamente o tipo **`Decimal`** (`@db.Decimal(10, 2)`) no Prisma e PostgreSQL para evitar erros de arredondamento inerentes a tipos de ponto flutuante.

### 📊 5.2. Diagrama ER (Mermaid)

```mermaid
erDiagram
    User ||--o| ProfessionalProfile : "pode possuir perfil (0..1)"
    User ||--o{ ServiceRequest : "solicita como cliente (1:N)"
    User ||--o{ Review : "escreve como autor (1:N)"
    User ||--o{ Review : "recebe como avaliado (1:N)"
    User ||--o{ Report : "registra como denunciante (1:N)"
    User ||--o{ Report : "recebe como denunciado (1:N)"

    ProfessionalProfile ||--o{ ServiceRequest : "atende como profissional (1:N)"
    ProfessionalProfile }o--o{ ServiceCategory : "atende em (N:N)"

    ServiceCategory ||--o{ ServiceRequest : "classifica (1:N)"

    ServiceRequest ||--o{ Payment : "possui tentativas de pagamento (1:N)"
    ServiceRequest ||--o{ Review : "gera avaliações (1:N)"
    ServiceRequest ||--o{ Report : "pode originar denúncias (1:N)"

    User {
        string id PK
        string email UK
        string passwordHash
        string name
        string phone
        string role "CLIENT | PROFESSIONAL | ADMIN"
        string status "ACTIVE | SUSPENDED | BLOCKED"
        datetime createdAt
    }

    ProfessionalProfile {
        string id PK
        string userId FK
        string bio
        string region
        string status "PENDING | APPROVED | REJECTED"
        string rejectionReason
        datetime createdAt
    }

    ServiceCategory {
        string id PK
        string name UK
        string slug UK
        string description
    }

    ServiceRequest {
        string id PK
        string clientId FK
        string professionalId FK
        string categoryId FK
        string description
        string address
        datetime preferredDate
        Decimal budget
        Decimal negotiatedPrice
        string status "PENDING | IN_NEGOTIATION | ACCEPTED | REJECTED | AWAITING_PAYMENT | CONFIRMED | COMPLETED | CANCELLED | EXPIRED"
        datetime createdAt
    }

    Payment {
        string id PK
        string serviceRequestId FK
        Decimal amount
        string status "PENDING | APPROVED | FAILED"
        string gatewayTransactionId
        datetime paidAt
    }

    Review {
        string id PK
        string serviceRequestId FK
        string authorId FK
        string targetId FK
        int rating
        string comment
        datetime createdAt
    }

    Report {
        string id PK
        string serviceRequestId FK
        string reporterId FK
        string reportedUserId FK
        string reason
        string description
        string status "PENDING | RESOLVED | DISMISSED"
        datetime createdAt
    }
```

### 🌍 5.3. O banco por ambiente

| Ambiente | Onde roda | Como conecta |
| :--- | :--- | :--- |
| **Local** | Docker (`docker-compose.yml`) com PostgreSQL 16 | `postgresql://postgres:postgres@localhost:5432/faz_tudo_dev` |
| **CI** | GitHub Actions (service container PostgreSQL 16) | `postgresql://postgres:postgres@localhost:5432/faz_tudo_test` |
| **Produção** | **Neon.tech** (Região São Paulo / `sa-east-1`) | `DATABASE_URL` com Prisma Connection Pooling (porta 6543 / PgBouncer) |

> ⚠️ **Veto da Disciplina:** Proibido o uso do PostgreSQL gratuito do Render (expira em 30 dias).  
> 🔒 **Segurança:** Credenciais reais nunca são commitadas no Git.

---

## 🗺️ 6. Mapa de Domínios

> **Este índice cresce.** Não é para preencher agora: **uma linha por história
> implementada**. Ele diz **onde mora** cada domínio e quem o protege — rota e
> contrato ficam na documentação viva da API (§2.2) e na spec de cada história,
> nunca copiados aqui.

| Domínio | Módulo (pasta) | Guard | Dados (repository) | US |
| :------ | :------------- | :---- | :----------------- | :-- |
| | | | | |

---

## 📅 7. Histórico

| Data | Versão | O que mudou |
| :--- | :----- | :---------- |
| 2026-10-16 | 1.0.0 | Versão inicial gerada via entrevista `/utf-architecture` e aprovada pelo aluno |

---

## 🛑 O que ainda não está neste documento

Detalhes específicos de cada tela, máquinas de estado de histórias individuais e DTOs específicos nascem na pasta `specs/<issue>-<slug>/spec.md` de cada história. Este documento guarda a régua estrutural que vale para o sistema inteiro.
