# Documentação Técnica - API SCF Training

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
A **API de Treinamento** foi desenvolvida para fornecer suporte ao **Santa News** e divulgação de material informativo.  
A aplicação permite o cadastro de treinamentos com informações como URL de vídeo e formulários, além da consulta detalhada desses treinamentos.
Desenvolvida em **NestJS** com **Sequelize ORM**, utiliza o **PostgreSQL** como banco principal.

---

## 2. Arquitetura do Sistema {#arquitetura-do-sistema}
- **Framework:** NestJS  
- **Banco de Dados:** PostgreSQL  
- **ORM:** Sequelize  
- **Autenticação:** JWT 
- **Hospedagem:** Docker e Docker Compose  
- **Documentação:** Swagger (OpenAPI) para endpoints  

---

## 3. Estrutura de Pastas {#estrutura-de-pastas}

```
src/
├── app/
│   ├── config/
│   ├── modules/
│   │   └── trainning/           # Módulo principal de treinamentos
│   │       ├── application/
│   │       ├── docs/
│   │       ├── domain/
│   │       ├── infrastructure/
│   │       ├── interfaces/
│   │       ├── presentation/
│   │       ├── resource/
│   │       └── index.module.ts  
│   ├── shared/
│   └── app.module.ts            # Módulo raiz da aplicação
├── migrations/                  # Migrações do banco de dados
├── seeders/                     # Dados iniciais para popular o banco
└── main.ts                      # Ponto de entrada da aplicação

```

---

## 4. Configuração e Execução {#configuração-e-execução}
Para detalhes completos sobre deploy e configuração, consulte o README do projeto no GitHub:  
[![GitHub](https://img.shields.io/badge/GitHub-Repository-blue?logo=github)](https://github.com/Santa-Casa-Franca/scf-trainning-api){:target="_blank"}

---

## 5. Acesso à Documentação de Endpoints {#acesso-à-documentação-de-endpoints}
A documentação da API está disponível via **Swagger**, após a aplicação estar em execução.
Após iniciar o servidor localmente, acesse:  

```
http://{host}:3000/api-docs
```

A URL de produção será definida conforme a configuração do ambiente do servidor.

---

## 6. Tecnologias Utilizadas {#tecnologias-utilizadas}
- **Node.js** (versão 20+)  
- **NestJS**  
- **TypeScript**  
- **Sequelize** (ORM)  
- **PostgreSQL**  
- **Docker & Docker Compose**  
- **Swagger** para documentação de endpoints  

---

📌 **Observação:** Esta documentação deve ser atualizada sempre que novos módulos forem adicionados ou alterados.