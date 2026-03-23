# Documentação Técnica — Gerenciador de Filas (santa-bot-tasks)

📅 **Última atualização:** 23/03/2026

---

## Sumário
1. [Visão Geral](#visao-geral)
2. [Arquitetura do Sistema](#arquitetura-do-sistema)
3. [Módulos e Responsabilidades](#modulos)
4. [Estrutura de Pastas](#estrutura-de-pastas)
5. [Conceitos Chave](#conceitos)
6. [Endpoints da API](#endpoints)
7. [BullBoard — Painel de Monitoramento](#bullboard)
8. [Configuração e Execução](#configuracao)
9. [Tecnologias Utilizadas](#tecnologias)

---

## 1. Visão Geral {#visao-geral}
O **santa-bot-tasks** é o serviço responsável pela criação, gerenciamento e monitoramento de filas e jobs do SantaBot. Ele utiliza **BullMQ** como engine de filas e **Redis** como broker de mensagens. As filas são dinâmicas: configuradas no banco de dados e registradas em memória na inicialização do serviço.

Este serviço é chamado pelo `santa-bot-automation-api` para criação de jobs e consumido pelo `santa-bot-worker` para processamento.

---

## 2. Arquitetura do Sistema {#arquitetura-do-sistema}
- **Framework:** NestJS
- **Engine de Filas:** BullMQ
- **Broker:** Redis
- **Banco de Dados:** PostgreSQL (armazenamento de configurações de fila)
- **Monitoramento:** Prometheus (métricas de jobs criados, erros, duração) + BullBoard (UI visual)

---

## 3. Módulos e Responsabilidades {#modulos}

| Módulo         | Responsabilidade                                                                      |
|----------------|---------------------------------------------------------------------------------------|
| `queues`       | Registro dinâmico de filas BullMQ a partir do banco. CRUD de filas.                  |
| `queue-config` | Configuração de comportamento das filas (concorrência, retry, TTL, janela de tempo). |
| `jobs`         | Criação, listagem, retry, remoção e controle de jobs nas filas.                      |
| `rate-limit`   | Controle de taxa de envio de mensagens por fila.                                     |
| `metrics`      | Exposição de métricas Prometheus sobre o estado das filas e jobs.                    |
| `bullboard`    | Painel visual de monitoramento das filas (BullBoard UI).                              |

---

## 4. Estrutura de Pastas {#estrutura-de-pastas}
```plaintext
src/
├── config/
│   ├── bullboard/          # Configuração do BullBoard
│   ├── data-source.ts      # Configuração TypeORM
│   ├── database.config.ts  # Parâmetros de conexão PostgreSQL
│   └── redis.config.ts     # Parâmetros de conexão Redis
├── jobs/
│   ├── dto/                # DTOs de criação e consulta de jobs
│   ├── jobs.controller.ts  # Endpoints REST para gerenciar jobs
│   └── jobs.service.ts     # Lógica de criação e controle de jobs
├── queue-config/
│   ├── dto/                # DTOs de configuração
│   ├── entities/           # Entidade QueueConfig
│   └── queue-config.service.ts
├── queues/
│   ├── dto/                # DTOs de criação de fila
│   ├── entities/           # Entidade Queue
│   ├── queue.controller.ts
│   ├── queues.service.ts   # Registro dinâmico e CRUD de filas
│   └── queue.types.ts      # Tipos e constantes das filas
├── rate-limit/
│   └── rate-limit.service.ts
├── metrics/
│   ├── metrics.controller.ts
│   ├── metrics.service.ts
│   └── queue-metrics.scheduler.ts  # Coleta periódica de métricas
└── main.ts
```

---

## 5. Conceitos Chave {#conceitos}

### Filas Dinâmicas
As filas **não são hardcoded**. Ao iniciar, o serviço busca todas as filas ativas no banco (`Queue.isActive = true`) e as registra no BullMQ automaticamente. Cada fila tem uma `QueueConfig` associada que define seu comportamento.

### QueueConfig — Configuração de Fila

| Campo               | Tipo    | Descrição                                               |
|---------------------|---------|---------------------------------------------------------|
| `name`              | varchar | Nome identificador da configuração.                     |
| `concurrency`       | integer | Número de jobs processados simultaneamente.             |
| `retryAttempts`     | integer | Tentativas de reexecução em caso de falha.              |
| `backoffDelay`      | integer | Delay (ms) entre tentativas.                            |
| `backoffType`       | varchar | `fixed` ou `exponential`.                               |
| `removeOnCompleteTTL`| integer| Tempo (s) para manter jobs concluídos.                 |
| `removeOnFailTTL`   | integer | Tempo (s) para manter jobs com falha.                  |
| `rateLimitMaxJobs`  | integer | Máximo de jobs por janela de rate limit.                |
| `rateLimitDurationMs`| integer| Duração (ms) da janela de rate limit.                  |
| `initialDelay`      | integer | Delay inicial padrão para novos jobs (ms).              |
| `processStartTime`  | varchar | Hora de início da janela de processamento (ex.: `06:00`). |
| `processEndTime`    | varchar | Hora de fim da janela de processamento (ex.: `22:00`). |
| `timezone`          | varchar | Fuso horário da janela (ex.: `America/Sao_Paulo`).     |
| `enabled`           | boolean | Se a fila está habilitada.                              |
| `verboseLogs`       | boolean | Se deve emitir logs detalhados.                         |

### Criação de Jobs
Um job pode ser criado com três modos:
- **Imediato:** executa assim que um worker estiver disponível.
- **Delay:** executa após X milissegundos (campo `delay`).
- **Repetível (every):** executa a cada X milissegundos (campo `every`).
- **Repetível (cron):** executa conforme expressão cron (campo `cron`).

---

## 6. Endpoints da API {#endpoints}

### Jobs
| Método | Rota                                   | Descrição                                |
|--------|----------------------------------------|------------------------------------------|
| POST   | `/jobs`                                | Cria um novo job em uma fila.            |
| GET    | `/jobs/:queue`                         | Lista jobs de uma fila por status.       |
| GET    | `/jobs/:queue/:jobId`                  | Busca um job específico.                 |
| DELETE | `/jobs/:queue/:jobId`                  | Remove um job.                           |
| PATCH  | `/jobs/:queue/:jobId/retry`            | Reexecuta um job com falha.             |
| PATCH  | `/jobs/:queue/:jobId/promote`          | Promove um job atrasado para execução.   |
| PATCH  | `/jobs/:queue/pause`                   | Pausa uma fila.                          |
| PATCH  | `/jobs/:queue/resume`                  | Retoma uma fila pausada.                 |
| DELETE | `/jobs/:queue/clean`                   | Limpa jobs concluídos de uma fila.       |
| GET    | `/jobs/:queue/metrics`                 | Retorna contagem de jobs por estado.     |
| GET    | `/jobs/:queue/repeatables`             | Lista jobs repetíveis de uma fila.       |
| DELETE | `/jobs/:queue/repeatables/:id`         | Remove um job repetível.                 |

### Filas
| Método | Rota            | Descrição                       |
|--------|-----------------|---------------------------------|
| POST   | `/queues`       | Cria e registra uma nova fila.  |
| PATCH  | `/queues/:id`   | Atualiza dados de uma fila.     |
| DELETE | `/queues/:id`   | Remove uma fila.                |

### Métricas
| Método | Rota        | Descrição                            |
|--------|-------------|--------------------------------------|
| GET    | `/metrics`  | Métricas Prometheus (texto plano).   |

---

## 7. BullBoard — Painel de Monitoramento {#bullboard}
O BullBoard oferece uma interface visual para inspecionar filas e jobs em tempo real.

Após iniciar o servidor, acesse:
```
http://{host}:{PORT}/admin/queues
```

Funcionalidades disponíveis:
- Visualizar jobs por estado: waiting, active, completed, failed, delayed.
- Inspecionar payload de jobs individuais.
- Reexecutar jobs com falha manualmente.
- Ver métricas de throughput por fila.

---

## 8. Configuração e Execução {#configuracao}
Para detalhes de setup e variáveis de ambiente, consulte o README no repositório.

### Variáveis de Ambiente necessárias
```
DB_HOST, DB_PORT, DB_USERNAME, DB_PASSWORD, DB_DATABASE
REDIS_HOST, REDIS_PORT
PORT
```

---

## 9. Tecnologias Utilizadas {#tecnologias}
- **Node.js / NestJS**
- **TypeScript**
- **BullMQ**
- **Redis**
- **PostgreSQL / TypeORM**
- **Docker**
- **Prometheus** (métricas)
- **BullBoard** (dashboard de filas)

---

📌 **Observação:** Novas filas devem ser criadas via API ou diretamente no banco antes de serem usadas pelo worker.
