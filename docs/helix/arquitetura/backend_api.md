# Documentação Técnica - API Helix

📅 **Última atualização:** 24/09/2025

---

## Sumário
1. [Visão Geral](#visão-geral)  
2. [Arquitetura do Sistema](#arquitetura-do-sistema)  
3. [Configuração e Execução](#configuração-e-execução)  
4. [Acesso à Documentação de Endpoints](#acesso-à-documentação-de-endpoints)  
5. [Tecnologias Utilizadas](#tecnologias-utilizadas)  

---

## 1. Visão Geral {#visão-geral}
A **API Helix** foi desenvolvida para oferecer suporte às aplicações desenvolvidas pela equipe de inovação. 
A aplicação permite o registro de sinais vitais, a consulta de informações de pacientes internados e a integração com o sistema Tasy (Oracle) para enriquecimento dos dados clínicos.  
O sistema também conta com serviços de fila para contingência e tarefas agendadas para manutenção dos dados.

Desenvolvida em **NestJS** com **Sequelize ORM**, utiliza o **PostgreSQL** como banco principal e integra com o **OracleDB** para obter dados adicionais.

---

## 2. Arquitetura do Sistema {#arquitetura-do-sistema}
- **Framework:** NestJS  
- **Banco de Dados:** PostgreSQL (principal) e OracleDB (Tasy)  
- **ORM:** Sequelize  
- **Fila:** Serviço de fila para contingência em caso de falha de integração  
- **Autenticação:** JWT  
- **Agendamentos (Schedules):** tarefas automatizadas para limpeza de tokens e dados antigos  
- **Hospedagem:** Docker e Docker Compose  
- **Documentação:** Swagger (OpenAPI) para endpoints  

---

## 3. Estrutura de Pastas {#estrutura-de-pastas}

```
src/
├── app/
│   ├── config/
│   ├── modules/
│   │   ├── alerts/
│   │   │   ├── application/
│   │   │   │   ├── dto/
│   │   │   │   └── services/
│   │   │   ├── docs/
│   │   │   │   ├── annotations/
│   │   │   │   └── responses/
│   │   │   ├── domain/
│   │   │   │   ├── entities/
│   │   │   │   └── lab-entities/
│   │   │   ├── infrastructure/
│   │   │   │   └── repositories/
│   │   │   ├── presentation/
│   │   │   │   └── controllers/
│   │   │   └── resource/
│   │   ├── business-intelligence/
│   │   ├── census/
│   │   ├── forms/
│   │   ├── gateway/
│   │   ├── mobile/
│   │   └── signature/
│   └── shared/
│       ├── application/
│       │   └── services/
│       │       └── rabbitmq/
│       │           ├── hemodialysis/
│       │           └── respiratory/
│       ├── docs/
│       │   └── annotations/
│       ├── domain/
│       │   └── entities/
│       ├── infrastructure/
│       │   └── databse/
│       │       └── schedule/
│       ├── presentation/
│       │   └── controllers/
│       ├── resource/
│       └── utils/
├── health/
├── metrics/
├── migrations/
├── prometheus/
└── seeders/             
``` 

--- 

## 4. Configuração e Execução {#configuração-e-execução}
Para detalhes completos sobre deploy e configuração, consulte o README do projeto no GitHub
[![GitHub](https://img.shields.io/badge/GitHub-Repository-blue?logo=github)](https://github.com/Santa-Casa-Franca/Helix){:target="_blank"}

---

## 5. Acesso à Documentação de Endpoints {#acesso-à-documentação-de-endpoints}
A documentação da API está disponível via **Swagger**, após a aplicação estar em execução.
Após iniciar o servidor localmente, acesse:

```
http://{host}:3000/api-docs
```

A URL de produção será definida conforme a configuração do ambiente do servidor.

Além disso, a interface da **fila de mensagens** pode ser acessada por padrão em:

```
http://{host}:5672
```

---

## 6. Tecnologias Utilizadas {#tecnologias-utilizadas}
- **Node.js** (versão 20+)  
- **NestJS**  
- **TypeScript**  
- **Sequelize** (ORM)  
- **PostgreSQL**  
- **OracleDB**  
- **Docker & Docker Compose**  
- **Swagger** para documentação de endpoints  

---

📌 **Observação:** Esta documentação deve ser atualizada sempre que novos módulos forem adicionados ou alterados.
