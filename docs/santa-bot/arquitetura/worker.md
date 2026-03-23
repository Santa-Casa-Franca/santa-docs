# Documentação Técnica — Worker (santa-bot-worker)

📅 **Última atualização:** 23/03/2026

---

## Sumário
1. [Visão Geral](#visao-geral)
2. [Arquitetura do Sistema](#arquitetura-do-sistema)
3. [Workers Disponíveis](#workers)
4. [Integração com SIRESP](#siresp)
5. [Estrutura de Pastas](#estrutura-de-pastas)
6. [Fluxo de Processamento](#fluxo)
7. [Configuração e Execução](#configuracao)
8. [Tecnologias Utilizadas](#tecnologias)

---

## 1. Visão Geral {#visao-geral}
O **santa-bot-worker** é o serviço responsável pelo processamento dos jobs enfileirados no `santa-bot-tasks`. Ele contém os **Workers BullMQ** de cada automação disponível no SantaBot: coleta de dados nas fontes externas (ex.: SIRESP, Tasy), envio de mensagens WhatsApp e registro de eventos no backend principal.

Cada automação possui seus próprios workers dedicados, organizados em módulos independentes. Novos fluxos de automação são adicionados como novos módulos/workers, sem impacto nos existentes.

---

## 2. Arquitetura do Sistema {#arquitetura-do-sistema}
- **Framework:** NestJS
- **Engine de Processamento:** BullMQ Workers (`@nestjs/bullmq`)
- **Broker:** Redis
- **Integrações:**
  - `santa-bot-automation-api` — leitura e escrita de dados (pacientes, agendamentos, eventos, chats).
  - `omnichannel-service` — envio de mensagens WhatsApp.
  - `santa-bot-tasks` — criação de novos jobs durante o processamento.
  - **SIRESP** — scraping via HTTP para coleta de agendamentos.

---

## 3. Workers Disponíveis {#workers}

Os workers são organizados por automação. Cada automação possui um módulo próprio com workers de **coleta de dados** (específico da fonte) e workers de **envio de mensagens** (compartilhados entre automações).

### Workers SIRESP — Automação `agenda-siresp`
Responsáveis pela coleta de dados no SIRESP e enfileiramento de mensagens para os AMEs.

| Worker                      | Fila                      | Responsabilidade                                                     |
|-----------------------------|---------------------------|----------------------------------------------------------------------|
| `SirespScheduleWorker`      | `siresp.schedule`         | Coleta os agendamentos (consultas/exames) do dia no SIRESP e enfileira o envio de confirmação para cada paciente. |
| `SirespSpecialtyWorker`     | `siresp.specialty`        | Coleta e atualiza especialidades médicas dos agendamentos.           |
| `SirespAddressWorker`       | `siresp.address`          | Coleta e atualiza endereços dos pacientes coletados no SIRESP.       |
| `SirespPreparationWorker`   | `siresp.preparation`      | Coleta instruções de preparo vinculadas a exames do SIRESP.          |
| `SirespUpdateAbsenceWorker` | `siresp.update-absence`   | Atualiza o registro de ausência de pacientes no SIRESP.              |

### Workers WhatsApp — Compartilhados entre Automações
Responsáveis pelo envio e acompanhamento de mensagens WhatsApp. São reutilizados por todas as automações que precisam enviar mensagens.

| Worker                           | Fila                          | Responsabilidade                                                       |
|----------------------------------|-------------------------------|------------------------------------------------------------------------|
| `WhatsappConfirmationWorker`     | `whatsapp.confirmation`       | Envia o template de confirmação de agendamento via WhatsApp.           |
| `WhatsappMultiConfirmationWorker`| `whatsapp.multi-confirmation` | Envia confirmações para múltiplos contatos de um mesmo agendamento.    |
| `WhatsappReminderWorker`         | `whatsapp.reminder`           | Envia lembrete de agendamento próximo ao dia da consulta.              |
| `WhatsappWaitingWorker`          | `whatsapp.waiting`            | Aguarda resposta do paciente; tenta o próximo contato se não houver resposta. |
| `WhatsappAbsenceWorker`          | `whatsapp.absence`            | Notifica sobre ausência registrada no agendamento.                     |
| `WhatsappTransferWorker`         | `whatsapp.transfer`           | Notifica sobre transferência ou remarcação de agendamento.             |
| `WhatsappFeedbackWorker`         | `whatsapp.feedback`           | Envia pesquisa de feedback após a consulta.                            |
| `WhatsappOutcomeWorker`          | `whatsapp.outcome`            | Registra o desfecho do agendamento.                                    |
| `WhatsappNormalizationWorker`    | `whatsapp.normalization`      | Normaliza e padroniza dados de contato do paciente.                    |

### Workers Tasy — Automações Tasy
Responsáveis por automações integradas ao sistema **Tasy** (prontuário eletrônico hospitalar). Seguem a mesma estrutura modular das demais automações.

| Worker                            | Responsabilidade                                                 |
|-----------------------------------|------------------------------------------------------------------|
| `SchedulerConsultationsWorker`    | Agenda notificações para consultas registradas no Tasy.          |
| `SchedulerSurgerysWorker`         | Agenda notificações para cirurgias registradas no Tasy.          |
| `CounterReferenceWorker`          | Processa contrarreferências de pacientes.                        |
| `RemoveCatheterNotifyWorker`      | Notifica sobre remoção de cateter.                               |
| `MailBiopsyNotifyWorker`          | Envia e-mail de notificação de resultado de biópsia.             |

---

## 4. Integração com SIRESP {#siresp}

### O que é o SIRESP
O **SIRESP** (Sistema Informatizado de Regulação do Estado de São Paulo) é o sistema do governo estadual para regulação e agendamentos de saúde. O SantaBot se conecta a ele via **web scraping HTTP** (não há API pública disponível).

### Credenciais SIRESP
Cada estabelecimento (AME) precisa fornecer as seguintes credenciais para autenticação:

| Campo        | Descrição                            |
|--------------|--------------------------------------|
| `username`   | Nome de usuário de acesso ao SIRESP. |
| `password`   | Senha de acesso.                     |
| `code`       | Código do estabelecimento no SIRESP. |
| `cpfstart`   | Primeiros 3 dígitos do CPF do usuário. |
| `cpfend`     | Últimos 3 dígitos do CPF do usuário. |
| `rgstart`    | Primeiros 3 dígitos do RG do usuário.|
| `rgend`      | Últimos 3 dígitos do RG do usuário.  |

As credenciais são armazenadas **criptografadas** (AES-256-GCM) no banco via `EstablishmentsAutomations`.

### Fluxo de Scraping
1. `SirespCredentialsService` descriptografa e recupera as credenciais do estabelecimento.
2. `SirespAuthService` realiza login no portal SIRESP.
3. Collectors especializados realizam as consultas:
   - `SirespAppointmentsCollector` — busca consultas e exames por data e tipo.
   - `SirespAbsenceCollector` — busca ausências.
   - `SirespFeedbackCollector` — coleta feedbacks.
   - `SirespAddressCollector` — coleta endereços dos pacientes.
   - `SirespPreparationsCollector` — coleta instruções de preparo.
4. Os dados coletados são salvos no banco via `santa-bot-automation-api`.
5. O `SirespQueueDispatcherService` enfileira um job de confirmação WhatsApp para cada agendamento.

---

## 5. Estrutura de Pastas {#estrutura-de-pastas}
```plaintext
src/
├── app.module.ts
├── main.ts
├── config/
│   └── database.config.ts
├── helpers/
│   ├── crypto.helper.ts        # Descriptografia AES-GCM
│   ├── formatters.ts           # Formatação de mensagens WhatsApp
│   ├── oracle.helper.ts        # Helpers de integração Oracle (Tasy)
│   ├── utils.helper.ts
│   ├── worker-dates.ts         # Utilitários de data para os workers
│   └── error.helper.ts
├── main-api-module/            # Módulo de comunicação com santa-bot-automation-api
│   ├── interfaces.ts
│   ├── main-api.module.ts
│   └── services/
│       ├── absences/
│       ├── address/
│       ├── agenda/
│       ├── appointment/
│       ├── automation-schedules/
│       ├── chat/
│       ├── events/
│       ├── feedback/
│       ├── messages/
│       ├── notifications/
│       ├── patient/
│       └── preparations/
├── omni-api-module/            # Módulo de comunicação com omnichannel-service
│   └── omnichannel.service.ts
├── queues/
│   ├── queue.constants.ts      # Nomes das filas (constantes)
│   └── queue-registry.module.ts
├── redis/
│   ├── redis.config.ts
│   └── redis.service.ts
├── storage/
│   └── storage.service.ts
└── workers/
    ├── siresp/
    │   ├── siresp.module.ts
    │   ├── siresp.interfaces.ts
    │   ├── siresp-schedule.worker.ts
    │   ├── siresp-specialty.worker.ts
    │   ├── siresp-address.worker.ts
    │   ├── siresp-preparation.worker.ts
    │   ├── siresp-update-absence.worker.ts
    │   ├── dispatcher/
    │   │   └── siresp.queue-dispatcher.service.ts
    │   └── scraper/
    │       └── services/
    │           ├── siresp.auth.service.ts
    │           ├── siresp.credentials.service.ts
    │           ├── siresp.http-client.ts
    │           ├── siresp.exec.service.ts
    │           ├── siresp.scraper.service.ts
    │           ├── siresp.appointments.collector.ts
    │           ├── siresp.absence.collector.ts
    │           ├── siresp.address.collector.ts
    │           ├── siresp.feedback.collector.ts
    │           ├── siresp.preparations.collector.ts
    │           └── siresp.specialty.collector.ts
    ├── tasy/
    │   ├── tasy.module.ts
    │   ├── scheduler-consultations.worker.ts
    │   ├── scheduler-surgerys.worker.ts
    │   ├── counter-reference.worker.ts
    │   ├── remove-catheter-notify.worker.ts
    │   └── mail-biopsy-notify.worker.ts
    └── whatsapp/
        ├── whatsapp.module.ts
        ├── whatsapp.service.ts
        ├── whatsapp-confirmation.worker.ts
        ├── whatsapp-multi-confirmation.worker.ts
        ├── whatsapp-reminder.worker.ts
        ├── whatsapp-waiting.worker.ts
        ├── whatsapp-absence.worker.ts
        ├── whatsapp-transfer.worker.ts
        ├── whatsapp-feedback.worker.ts
        ├── whatsapp-outcome.worker.ts
        └── whatsapp-normalization.worker.ts
```

---

## 6. Fluxo de Processamento {#fluxo}

### Fluxo Principal (Confirmação de Agendamento SIRESP)

```
[Job: siresp.schedule]
        |
        v
SirespScheduleWorker
    1. Descriptografa credenciais SIRESP
    2. Autentica no portal SIRESP
    3. Coleta agendamentos do dia
    4. Para cada agendamento:
       - Cria/atualiza Paciente no banco
       - Cria Agendamento no banco
       - Enfileira job: whatsapp.confirmation
        |
        v
WhatsappConfirmationWorker
    1. Busca dados do agendamento
    2. Gera payload do template WhatsApp
    3. Envia via omnichannel-service → Meta API → WhatsApp
    4. Registra Evento (status: sent)
    5. Cria Chat se não existir
    6. Salva Mensagem no banco
    7. Enfileira job: whatsapp.waiting (delay: até 6h)
        |
        v
WhatsappWaitingWorker
    1. Verifica se há resposta do paciente
    2. Se não houver e houver próximo contato → enfileira nova confirmação
    3. Se todos os contatos tentados → encerra fluxo
```

### Janela de Envio
Os envios de mensagens respeitam a janela horária configurada:
- **Início:** 06:00
- **Fim:** 22:00
- **Fuso:** `America/Sao_Paulo`

Jobs que chegariam fora desta janela são automaticamente adiados para o próximo período disponível.

---

## 7. Configuração e Execução {#configuracao}
Para detalhes de setup e variáveis de ambiente, consulte o README no repositório.

### Variáveis de Ambiente necessárias
```
REDIS_HOST, REDIS_PORT
DB_HOST, DB_PORT, DB_USERNAME, DB_PASSWORD, DB_DATABASE
MAIN_API_URL        # URL do santa-bot-automation-api
OMNI_API_URL        # URL do omnichannel-service
JOB_API_URL         # URL do santa-bot-tasks
MAIN_API_KEY        # Chave de autenticação interna
TZ                  # Fuso horário (America/Sao_Paulo)
```

---

## 8. Tecnologias Utilizadas {#tecnologias}
- **Node.js / NestJS**
- **TypeScript**
- **BullMQ** (`@nestjs/bullmq`)
- **Redis**
- **PostgreSQL / TypeORM**
- **Axios** (comunicação HTTP com APIs e SIRESP)
- **Moment.js** (manipulação de datas e janelas de envio)
- **Docker**

---

📌 **Observação:** Para adicionar uma nova automação, crie um módulo dedicado em `src/workers/<nome-automacao>/`, implemente os workers de coleta e reutilize os workers WhatsApp para o envio de mensagens. A nova fila deve ser criada no `santa-bot-tasks` e a automação registrada na tabela `Automations` via `santa-bot-automation-api`.
