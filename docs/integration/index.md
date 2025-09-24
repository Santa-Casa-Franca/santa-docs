# 📄 Projeto de Integrações

> **Data de Emissão:** 24/09/2025  
> **Autor:** Felipe Ferreira

---

## Sumário
1. [Visão Geral](#visão-geral)  
2. [Stakeholders](#stakeholders)  
3. [Arquitetura do Sistema](#arquitetura-do-sistema)  
4. [Funcionalidades Principais](#funcionalidades-principais)  
5. [Fluxo de Uso](#fluxo-de-uso)  
6. [Requisitos Técnicos](#requisitos-técnicos)

---

## 1. Visão Geral {#visão-geral}
O **Projeto de Integrações ChatGuru & Tasy** é um serviço intermediário (middleware) responsável por orquestrar a comunicação entre a plataforma de mensagens **ChatGuru** (WhatsApp) e o sistema hospitalar **Tasy (Philips)**. Seu principal objetivo é automatizar o agendamento de consultas médicas via WhatsApp, tornando o processo mais ágil, moderno e acessível.

### Objetivo do Software
- Automatizar o agendamento de consultas médicas pelo WhatsApp.  
- Validar CPF, convênio e dados do paciente antes da marcação.  
- Consultar especialidades e horários disponíveis diretamente no banco do Tasy.  
- Registrar agendamentos e enviar confirmações ao paciente.

### Escopo
- Recebimento de mensagens do ChatGuru via webhook.  
- Processamento assíncrono com filas (RabbitMQ).  
- Integração com o banco do Tasy para leitura e escrita de dados.  
- Retorno da resposta apropriada ao paciente com base no fluxo conversacional.

---

## 2. Stakeholders {#stakeholders}

| Grupo                      | Responsabilidade                                                                 |
|----------------------------|----------------------------------------------------------------------------------|
| **Equipe de Inovação**    | Desenvolvimento, versionamento e testes do middleware de integração.            |
| **Equipe de Atendimento** | Suporte operacional ao fluxo de agendamento automatizado.                       |

---

## 3. Arquitetura do Sistema {#arquitetura-do-sistema}
O sistema foi desenvolvido com **NestJS**, com arquitetura baseada em eventos e processamento assíncrono utilizando **RabbitMQ**, garantindo escalabilidade e desacoplamento entre os módulos.

- **Backend:** NestJS com estrutura modular.  
- **Banco de Dados:** PostgreSQL (armazenamento de logs e estados).  
- **Mensageria:** RabbitMQ para orquestração das mensagens recebidas.  
- **Integração com Tasy:** Acesso direto ao banco Oracle do Tasy para consultas e agendamentos.  
- **API REST:** endpoints para webhook e health check.  
- **Containerização:** Docker + Docker Compose para ambiente isolado e portátil.  
- **Segurança:** Webhook protegido com header `X-API-Key`.

---

## 4. Funcionalidades Principais {#funcionalidades-principais}
- **Webhook para Mensagens:** recebe e autentica mensagens vindas do ChatGuru.  
- **Fila de Processamento:** enfileira eventos para garantir resiliência e controle de carga.  
- **Validação de Dados:** checagem de CPF, convênio e dados obrigatórios.  
- **Consulta de Disponibilidade:** acessa especialidades e horários diretamente no Tasy.  
- **Confirmação de Agendamento:** retorna a resposta ao paciente com o status do agendamento.  
- **Health Check:** endpoint simples para verificação de saúde da aplicação.

---

## 5. Fluxo de Uso {#fluxo-de-uso}
1. O paciente envia mensagem pelo WhatsApp via plataforma **ChatGuru**.  
2. O ChatGuru envia essa mensagem ao **webhook** da aplicação.  
3. A requisição é validada (API Key) e a mensagem é **enfileirada no RabbitMQ**.  
4. O worker consome a fila e processa a lógica (validação de CPF, busca no Tasy, etc.).  
5. Com base nas regras do fluxo, o sistema agenda a consulta no banco do Tasy.  
6. A confirmação é retornada ao ChatGuru, que envia a mensagem ao paciente.

---

## 6. Requisitos Técnicos {#requisitos-técnicos}
- **Linguagem:** TypeScript  
- **Framework:** NestJS  
- **Banco de Dados:** PostgreSQL 
- **Integração com Oracle (Tasy):** consultas e escrita direta no banco  
- **Mensageria:** RabbitMQ (fila `chatguru.messages`)  
- **Containers:** Docker e Docker Compose  
- **Autenticação de Webhook:** Header `X-API-Key`  
- **Variáveis de Ambiente Principais:**  
  - `PORT` – porta da aplicação  
  - `DATABASE_URL` – URL de conexão com PostgreSQL  
  - `RABBITMQ_URI` – URI do servidor RabbitMQ  
  - `RABBITMQ_EXCHANGE` – nome do exchange principal  
  - `API_KEY_SECRET` – chave secreta do webhook

---

📌 **Observação:** Esta documentação deverá ser mantida atualizada a cada modificação estrutural ou de integração na API para garantir a rastreabilidade e conformidade com os requisitos institucionais de segurança.
