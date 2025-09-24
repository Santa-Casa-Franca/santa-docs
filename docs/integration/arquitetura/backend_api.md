# Documentação Técnica - Backend API

📅 **Última atualização:** 24/09/2025

---

## Sumário
1. [Visão Geral](#visão-geral)  
2. [Arquitetura do Sistema](#arquitetura-do-sistema)  
3. [Estrutura de Pastas](#estrutura-de-pastas)  
4. [Configuração e Execução](#configuração-e-execução)  
5. [Acesso à API](#acesso-à-api)  
6. [Tecnologias Utilizadas](#tecnologias-utilizadas)  

---

## 1. Visão Geral {#visão-geral}
A **API da Integração ChatGuru & Tasy** é um middleware desenvolvido em **NestJS**, responsável por intermediar a comunicação entre o ChatGuru (plataforma de atendimento via WhatsApp) e o sistema hospitalar **Tasy**.
Seu principal objetivo é **automatizar o agendamento de consultas médicas** a partir de mensagens trocadas com pacientes, oferecendo uma experiência moderna e eficiente. O sistema recebe as mensagens via webhook, processa os dados, realiza as validações e interage diretamente com o banco do Tasy para concluir o agendamento.
A arquitetura desacoplada e orientada a eventos permite escalar o serviço conforme o volume de mensagens cresce.

---

## 2. Arquitetura do Sistema {#arquitetura-do-sistema}
- **Framework:** NestJS  
- **Banco de Dados:** PostgreSQL 
- **Mensageria:** RabbitMQ   
- **Integração com Tasy:** Acesso direto ao banco Oracle  
- **Autenticação Webhook:** Header `X-API-Key`  
- **Hospedagem:** Docker + Docker Compose

---

## 3. Estrutura de Pastas {#estrutura-de-pastas}

```
.
├── docker/
│   └── docker-compose.yml    # Sobe a infraestrutura (Postgres, RabbitMQ)
├── src/                      # Código-fonte da aplicação
│   ├── filters/              
│   ├── migrations/              
│   ├── modules/              # O coração da aplicação, dividido por domínios
│   │   ├── database/         # Configuração da conexão com o banco
│   │   ├── guru/
│   │   ├── health/           # Endpoint de Health Check
│   │   ├── scheduling/       # Lógica de negócio e worker da fila
│   │   ├── shared-rabbitmq/  # Módulo global wrapper para o RabbitMQ
│   │   ├── tasy/             # Módulo de integração com o Tasy
│   │   └── webhook/          # Controller para receber as chamadas do ChatGuru
│   ├── utils/
│   ├── app.module.ts
│   └── main.ts
├── test/
├── .env.example              # Arquivo de exemplo para as variáveis de ambiente
├── .gitignore
├── .prettierrc
├── package.json
└── tsconfig.json
```

---

## 4. Configuração e Execução {#configuração-e-execução}
Para detalhes completos sobre deploy e configuração, consulte o README do projeto no GitHub  
[![GitHub](https://img.shields.io/badge/GitHub-Repository-blue?logo=github)](https://github.com/Santa-Casa-Franca/chat-integration)

---

## 5. Acesso à API {#acesso-à-api}
Após iniciar o servidor localmente, acesse:

```
http://{host}:3000/
```

No ambiente de produção, a URL será definida conforme configuração do servidor.

---

## 6. Tecnologias Utilizadas {#tecnologias-utilizadas}
- **Node.js**  
- **NestJS**  
- **TypeScript**  
- **PostgreSQL**  
- **RabbitMQ**  
- **Docker**  

---

📌 **Observação:** Esta documentação deve ser atualizada sempre que novos módulos forem adicionados ou alterados.
