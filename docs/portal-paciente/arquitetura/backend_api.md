# Documentação Técnica - Backend API

📅 **Última atualização:** 13/08/2025

---

## Sumário
1. [Visão Geral](#visão-geral)  
2. [Arquitetura do Sistema](#arquitetura-do-sistema)  
3. [Estrutura de Pastas](#estrutura-de-pastas)  
4. [Configuração e Execução](#configuração-e-execução)  
5. [Acesso à Documentação de Endpoints](#acesso-à-documentação-de-endpoints)  
6. [Tecnologias Utilizadas](#tecnologias-utilizadas)  

---

## 1. Visão Geral {#visão-geral}
O **Backend API** é responsável por prover serviços e endpoints REST para o sistema de gestão de pacientes oncológicos.  
Foi desenvolvido utilizando **NestJS**, com suporte a autenticação JWT, comunicação em tempo real via WebSockets e integrações com banco de dados PostgreSQL e Redis.

---

## 2. Arquitetura do Sistema {#arquitetura-do-sistema}
- **Framework:** NestJS
- **Banco de Dados:** PostgreSQL (dados persistentes) + Redis (cache e filas de mensagens)
- **Autenticação:** JWT
- **Comunicação em Tempo Real:** Socket.io
- **Documentação de API:** Swagger

---

## 3. Estrutura de Pastas {#estrutura-de-pastas}
```plaintext
src/
├── auth/                # Módulo de autenticação
│   ├── dtos/
│   ├── auth.controller.ts
│   ├── auth.module.ts
│   ├── auth.service.ts
│   └── jwt.strategy.ts
├── config/              # Configurações gerais
│   ├── config.js
│   └── database.connection.ts
├── guards/              # Guards de segurança
├── health/              # Monitoramento de saúde da API
├── migrations/          # Scripts de migração de banco
├── models/              # Modelos de dados
├── modules/
│   ├── agenda/
│   ├── chat/
│   ├── faq/
│   ├── forms/
│   ├── manuals/
│   ├── message/
│   ├── news/
│   ├── patients/
│   ├── users/
│   ├── videos/
│   └── notification/
├── redis/               # Integração com Redis
├── seeders/             # População inicial de dados
├── utils/               # Funções utilitárias
├── app.controller.ts
├── app.module.ts
├── app.service.ts
└── main.ts              # Ponto de entrada da aplicação

```

---

## 4. Configuração e Execução {#configuração-e-execução}
Para detalhes completos sobre deploy e configuração, consulte o README do projeto no GitHub
[![GitHub](https://img.shields.io/badge/GitHub-Repository-blue?logo=github)](https://github.com/Santa-Casa-Franca/onco-paciente-api){:target="_blank"}

---

## 5. Acesso à Documentação de Endpoints {#acesso-à-documentação-de-endpoints}
A API conta com documentação interativa através do **Swagger**.  
Após iniciar o servidor localmente, acesse:

```
http://{host}:8000/api/docs
```

No ambiente de produção, a URL será definida conforme configuração do servidor.

---

## 6. Tecnologias Utilizadas {#tecnologias-utilizadas}
- **Node.js**  
- **NestJS**  
- **TypeScript**  
- **PostgreSQL**  
- **Redis**  
- **Docker**  
- **Swagger** para documentação de endpoints  
- **Socket.io** para comunicação em tempo real  

---
📌 **Observação:** Esta documentação deve ser atualizada sempre que novos módulos forem adicionados ou alterados.
