# SantaBot — Plataforma de Automações de Mensagens

>**Data de Emissão:** 23/03/2026

>**Autor:** Time de Inovação

---

## Sumário
1. [Visão Geral](#visao-geral)
2. [Stakeholders](#stakeholders)
3. [Arquitetura do Sistema](#arquitetura-do-sistema)
4. [Funcionalidades Principais](#funcionalidades-principais)
5. [Fluxo de Uso](#fluxo-de-uso)
6. [Requisitos Técnicos](#requisitos-tecnicos)

---

## 1. Visão Geral {#visao-geral}
O **SantaBot** é uma plataforma extensível de automações de mensagens via WhatsApp voltada para estabelecimentos de saúde. Sua arquitetura foi projetada para suportar múltiplos tipos de automação de forma independente, onde cada automação possui sua própria lógica de coleta de dados, workers dedicados, fila configurável e credenciais isoladas por estabelecimento.

A **primeira automação disponível** é a integração com o **SIRESP** (Sistema Informatizado de Regulação do Estado de São Paulo), utilizada pelos **AMEs** (Ambulatórios Médicos de Especialidades) para envio automático de mensagens de confirmação de agendamento. Novas automações podem ser incorporadas ao sistema seguindo a mesma estrutura.

### Objetivo do Software
- Prover uma plataforma centralizada e reutilizável para automações de comunicação com pacientes via WhatsApp.
- Permitir que novos fluxos de automação sejam adicionados sem necessidade de refatoração da infraestrutura existente.
- Reduzir faltas e melhorar a comunicação entre estabelecimentos de saúde e pacientes.
- Oferecer um painel de controle para gestão de automações, filas e mensagens enviadas.

### Escopo
- Suporte a múltiplas automações independentes, cada uma com suas próprias credenciais e configurações.
- Envio de mensagens WhatsApp (templates aprovados pela Meta) para pacientes.
- Monitoramento de confirmações, transferências, cancelamentos e feedbacks.
- Gerenciamento multi-estabelecimento, onde cada estabelecimento possui configurações e credenciais isoladas por automação.
- Painel administrativo (Master) para gestão global do sistema.

### Automações Disponíveis

| Automação           | Fonte de Dados | Status      | Descrição                                                    |
|---------------------|----------------|-------------|--------------------------------------------------------------|
| `agenda-siresp`     | SIRESP         | Ativa        | Coleta agendamentos do SIRESP e envia confirmação via WPP.  |

> Novas automações são adicionadas como novos workers no `santa-bot-worker` e registradas como novas entradas na tabela `Automations`.

---

## 2. Stakeholders {#stakeholders}

| Grupo                   | Responsabilidade                                                                         |
|-------------------------|------------------------------------------------------------------------------------------|
| **Time de Inovação**    | Desenvolver, manter e evoluir a plataforma.                                              |
| **AMEs**                | Utilizar o painel para acompanhar e gerenciar os envios automáticos de confirmação.      |
| **Pacientes**           | Receber mensagens de confirmação e responder via WhatsApp.                               |
| **Administradores (Master)** | Configurar automações, estabelecimentos, filas e credenciais de forma centralizada. |

---

## 3. Arquitetura do Sistema {#arquitetura-do-sistema}
O SantaBot é composto por **5 serviços** que trabalham em conjunto:

| Serviço                     | Repositório                    | Responsabilidade                                                          |
|-----------------------------|--------------------------------|---------------------------------------------------------------------------|
| **Backend Principal**       | `santa-bot-automation-api`     | API REST central: estabelecimentos, automações, pacientes, eventos, chats.|
| **Gerenciador de Filas**    | `santa-bot-tasks`              | Criação e controle de jobs/filas via BullMQ para todas as automações.     |
| **Worker**                  | `santa-bot-worker`             | Executa os workers de cada automação (coleta de dados + envio de mensagens).|
| **Gateway de Mensagens**    | `omnichannel-service`          | Serviço multi-tenant para envio via WhatsApp, ChatGuru, Telegram, etc.    |
| **Painel Web**              | `wpp-automation-client`        | Interface para gestão de automações, filas, credenciais e dashboards.     |

### Diagrama de Fluxo

```
[wpp-automation-client]
        |
        | (REST/JWT)
        v
[santa-bot-automation-api] <-----> [PostgreSQL]
        |                  <-----> [Redis]
        |
        | (cria jobs)
        v
[santa-bot-tasks (BullMQ)]
        |
        | (consome jobs por automação)
        v
[santa-bot-worker]
    |                   |
    | (coleta de dados)  | (envia mensagens)
    v                   v
[Sistema Externo]  [omnichannel-service]
(ex.: SIRESP,           |
 Tasy, planilha...)     | (Meta API / ChatGuru / etc.)
                        v
                   [WhatsApp / Canal]
```

Para documentação detalhada de cada serviço, consulte:
- [Backend API](arquitetura/backend_api.md)
- [Gerenciador de Filas (Tasks)](arquitetura/tasks.md)
- [Worker](arquitetura/worker.md)
- [Gateway de Mensagens (Omnichannel)](arquitetura/omnichannel.md)
- [Painel Web](arquitetura/painel_admin.md)

---

## 4. Funcionalidades Principais {#funcionalidades-principais}

### Plataforma
- **Gestão de Automações**: cadastro de automações disponíveis no sistema; cada estabelecimento assina as automações que utiliza, com credenciais independentes.
- **Filas Configuráveis**: cada automação opera em sua própria fila BullMQ com configurações de concorrência, retry, rate limit e janela de horário.
- **Multi-estabelecimento**: isolamento completo de credenciais e dados entre estabelecimentos.
- **Dashboard Master**: visão consolidada de todas as automações, filas ativas, mensagens enviadas e estabelecimentos.
- **Registro de Eventos**: rastreabilidade completa de cada mensagem enviada e recebida, com status e histórico.

### Automação: `agenda-siresp` (AMEs)
- **Coleta de Agendamentos**: autenticação automática no SIRESP e extração de consultas e exames por data e estabelecimento.
- **Envio de Confirmação**: disparo de template WhatsApp para o contato do paciente agendado.
- **Fila de Espera**: aguarda resposta do paciente por até 6 horas; tenta o próximo contato se não houver resposta.
- **Cobertura de Canais**: suporte a múltiplos contatos por paciente, com tentativas sequenciais.

---

## 5. Fluxo de Uso {#fluxo-de-uso}

### Fluxo Genérico de uma Automação
1. O **Administrador (Master)** cadastra um estabelecimento e associa a ele uma ou mais automações disponíveis.
2. As credenciais da automação para aquele estabelecimento são configuradas no painel e armazenadas criptografadas.
3. Um **job** é criado no `santa-bot-tasks` com os parâmetros da automação (data, estabelecimento, etc.).
4. O **Worker** correspondente à automação consome o job:
   - Busca e descriptografa as credenciais do estabelecimento.
   - Coleta os dados da fonte correspondente (sistema externo, planilha, etc.).
   - Para cada item coletado, enfileira um job de envio de mensagem.
5. O **Worker de mensagem** envia o template WhatsApp via `omnichannel-service`.
6. O evento é registrado no banco; um job de **espera** é criado para monitorar a resposta.
7. O paciente responde via WhatsApp; o webhook processa e registra o desfecho.

### Exemplo: Automação `agenda-siresp` (AMEs)
1. Master cadastra o AME e associa a automação `agenda-siresp` com as credenciais SIRESP.
2. Job é criado com a data do agendamento.
3. Worker autentica no SIRESP, coleta consultas/exames do dia e enfileira confirmações.
4. Pacientes recebem mensagem de confirmação; o sistema aguarda resposta por até 6 horas.

### Fluxo Funcional (Estabelecimento)
1. Gestor acessa o painel web.
2. Visualiza o dashboard com mensagens enviadas e status dos agendamentos.
3. Acompanha confirmações, ausências e transferências em tempo real.

---

## 6. Requisitos Técnicos {#requisitos-tecnicos}
- **Linguagens:** TypeScript, Node.js
- **Frameworks:** NestJS (backend/worker/tasks/omnichannel), Next.js (frontend)
- **Banco de Dados:** PostgreSQL, Redis
- **Filas:** BullMQ
- **Autenticação:** JWT
- **Hospedagem:** Docker
- **Monitoramento:** Prometheus, Grafana, BullBoard
- **Integrações:** SIRESP (web scraping), WhatsApp Business API (Meta), ChatGuru, Telegram

---

📌 **Observação:** Esta documentação deve ser atualizada a cada nova automação ou integração adicionada ao sistema.
