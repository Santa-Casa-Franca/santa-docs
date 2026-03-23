# Documentação Técnica — Backend API (santa-bot-automation-api)

📅 **Última atualização:** 23/03/2026

---

## Sumário
1. [Visão Geral](#visao-geral)
2. [Arquitetura do Sistema](#arquitetura-do-sistema)
3. [Módulos e Responsabilidades](#modulos)
4. [Estrutura de Pastas](#estrutura-de-pastas)
5. [Principais Entidades](#entidades)
6. [Configuração e Execução](#configuracao)
7. [Documentação de Endpoints](#endpoints)
8. [Segurança](#seguranca)
9. [Tecnologias Utilizadas](#tecnologias)

---

## 1. Visão Geral {#visao-geral}
O **santa-bot-automation-api** é o backend principal do SantaBot. Ele centraliza a gestão de estabelecimentos (AMEs), automações, pacientes, agendamentos, chats e eventos. Expõe uma API REST consumida pelo painel web (`wpp-automation-client`) e pelo worker (`santa-bot-worker`).

---

## 2. Arquitetura do Sistema {#arquitetura-do-sistema}
- **Framework:** NestJS
- **Banco de Dados:** PostgreSQL (dados persistentes) + Redis (cache e integração com filas)
- **Autenticação:** JWT (Bearer Token)
- **Documentação de API:** Swagger
- **Rate Limiting:** 20 requisições por 60 segundos (ThrottlerGuard global)
- **Monitoramento:** Prometheus (métricas HTTP expostas via interceptor)

---

## 3. Módulos e Responsabilidades {#modulos}

| Módulo                    | Responsabilidade                                                                 |
|---------------------------|----------------------------------------------------------------------------------|
| `auth`                    | Login, geração e validação de JWT.                                               |
| `users`                   | Gestão de usuários do sistema.                                                   |
| `establishment`           | Cadastro e gestão dos AMEs (estabelecimentos).                                   |
| `automations`             | Gerencia os tipos de automação disponíveis (ex.: `agenda-siresp`).               |
| `automation-schedules`    | Histórico de execuções de automações por estabelecimento (status, tentativas).   |
| `patients`                | Cadastro de pacientes coletados via SIRESP ou planilhas.                         |
| `appointments`            | Agendamentos de consultas e exames vinculados a pacientes.                       |
| `agenda`                  | Configuração de agendas (horário de chegada antecipada, etc.).                   |
| `preparations`            | Instruções de preparo para exames enviadas via WhatsApp.                         |
| `absences`                | Registro de ausências de pacientes em consultas.                                 |
| `chat`                    | Histórico de conversas WhatsApp por estabelecimento e paciente.                  |
| `events`                  | Log de eventos por agendamento (mensagem enviada, confirmada, etc.).             |
| `messages`                | Mensagens individuais trafegadas no chat.                                        |
| `call-center`             | Registro de atendimentos via central de atendimento.                             |
| `spreadsheets`            | Importação de agendamentos via planilha Excel.                                   |
| `feedback`                | Coleta e registro de feedbacks dos pacientes.                                    |
| `movement-requests`       | Solicitações de transferência ou cancelamento de agendamento.                    |
| `address`                 | Endereços vinculados aos pacientes.                                              |
| `storage`                 | Upload e gerenciamento de arquivos (S3/MinIO).                                   |
| `metrics`                 | Exposição de métricas Prometheus.                                                |
| `doe`                     | Integração hospitalar interna (notificações por e-mail).                         |

---

## 4. Estrutura de Pastas {#estrutura-de-pastas}
```plaintext
src/
├── app/
│   ├── config/                         # Configurações gerais (DB, Redis, saúde da DB)
│   ├── decorators/                     # Decorators customizados (ex.: current-user)
│   ├── interceptors/                   # Interceptors HTTP (métricas)
│   ├── modules/
│   │   ├── absences/
│   │   ├── address/
│   │   ├── agenda/
│   │   ├── appointments/
│   │   ├── auth/
│   │   ├── automation-schedules/
│   │   ├── automations/
│   │   ├── call-center/
│   │   ├── chat/
│   │   ├── events/
│   │   ├── establishment/
│   │   ├── feedback/
│   │   ├── messages/
│   │   ├── metrics/
│   │   ├── movement-requests/
│   │   ├── patients/
│   │   ├── preparations/
│   │   ├── spreadsheets/
│   │   └── storage/
│   └── shared/
│       ├── hospital/                   # Serviços internos do hospital (DOE)
│       ├── resource/                   # Módulos de recurso compartilhados (DB, Redis)
│       └── services/                   # Serviços transversais (OmniChannel, Jobs, Redis)
├── helpers/                            # Helpers utilitários (crypto, vault, templates)
└── main.ts                             # Ponto de entrada da aplicação
```

---

## 5. Principais Entidades {#entidades}

### Establishment (AME)
Representa um Ambulatório Médico de Especialidades cadastrado no sistema.

| Campo         | Tipo      | Descrição                              |
|---------------|-----------|----------------------------------------|
| `id`          | UUID      | Identificador único.                   |
| `name`        | varchar   | Nome do estabelecimento.               |
| `label`       | varchar   | Rótulo curto para identificação.       |
| `type`        | varchar   | Tipo do estabelecimento.               |
| `cnpj`        | varchar   | CNPJ.                                  |
| `tenantId`    | UUID      | Tenant do omnichannel vinculado.       |
| `isActive`    | boolean   | Se está ativo.                         |

### EstablishmentsAutomations
Vincula um estabelecimento a uma automação, armazenando as credenciais criptografadas (ex.: credenciais SIRESP).

| Campo              | Tipo   | Descrição                                      |
|--------------------|--------|------------------------------------------------|
| `establishmentId`  | UUID   | ID do estabelecimento.                         |
| `automationId`     | UUID   | ID da automação.                               |
| `credentials`      | JSONB  | Credenciais criptografadas (AES-GCM).          |

### Automation
Define um tipo de automação disponível no sistema.

| Campo         | Tipo    | Descrição                                    |
|---------------|---------|----------------------------------------------|
| `id`          | UUID    | Identificador único.                         |
| `name`        | varchar | Nome interno (ex.: `agenda-siresp`).         |
| `description` | varchar | Descrição funcional.                         |
| `price`       | varchar | Valor cobrado pela automação.                |

### AutomationSchedules
Histórico de execuções de uma automação por estabelecimento.

| Campo            | Tipo       | Descrição                                          |
|------------------|------------|----------------------------------------------------|
| `id`             | UUID       | Identificador único.                               |
| `establishmentId`| UUID       | AME que executou.                                  |
| `automationId`   | UUID       | Automação executada.                               |
| `status`         | varchar    | `running` / `success` / `failed` / `timeout`.     |
| `startedAt`      | timestamptz| Início da execução.                               |
| `finishedAt`     | timestamptz| Fim da execução.                                  |
| `attempts`       | integer    | Número de tentativas.                              |
| `jobId`          | varchar    | ID do job no BullMQ.                               |
| `workerId`       | varchar    | ID do worker que executou.                         |

### Patient
Dados do paciente coletados via SIRESP.

| Campo           | Tipo    | Descrição                                  |
|-----------------|---------|--------------------------------------------|
| `id`            | UUID    | Identificador único.                       |
| `patientCode`   | varchar | Código único do paciente (SIRESP/Tasy).    |
| `patientCodeType`| varchar| Tipo do código (ex.: `SIRESP`).            |
| `cpf`           | varchar | CPF do paciente.                           |
| `fullName`      | varchar | Nome completo.                             |
| `bornAt`        | date    | Data de nascimento.                        |

---

## 6. Configuração e Execução {#configuracao}
Para detalhes completos sobre deploy e configuração de variáveis de ambiente, consulte o README do projeto no repositório.

### Variáveis de Ambiente necessárias
```
DB_HOST, DB_PORT, DB_USERNAME, DB_PASSWORD, DB_DATABASE
REDIS_HOST, REDIS_PORT
JWT_SECRET
JOB_API_URL       # URL do santa-bot-tasks
OMNI_API_URL      # URL do omnichannel-service
STORAGE_*         # Configurações do S3/MinIO
```

---

## 7. Documentação de Endpoints {#endpoints}
A API possui documentação interativa via **Swagger**. Após iniciar o servidor, acesse:

```
http://{host}:{PORT}/api/docs
```

---

## 8. Segurança {#seguranca}
- **Autenticação:** JWT Bearer Token em todas as rotas (exceto login).
- **Autorização:** Guards NestJS por rota e módulo.
- **Criptografia:** Credenciais sensíveis (SIRESP, WhatsApp) são armazenadas com **AES-256-GCM** (encrypted payload com `iv` e `tag`).
- **Rate Limiting:** 20 requests/60s por IP via `@nestjs/throttler`.
- **Auditoria:** Eventos registrados na tabela `Events` para rastreio de cada mensagem enviada.

---

## 9. Tecnologias Utilizadas {#tecnologias}
- **Node.js / NestJS**
- **TypeScript**
- **TypeORM**
- **PostgreSQL**
- **Redis**
- **Docker**
- **Swagger** (documentação de endpoints)
- **Prometheus** (métricas)

---

📌 **Observação:** Esta documentação deve ser atualizada sempre que novos módulos forem adicionados ou alterados.
