# Documentação Técnica - Backend API

📅 **Última atualização:** 23/09/2025

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
A **API de Autenticação** é responsável por autenticar usuários e emitir tokens JWT para integração com sistemas internos.  
Desenvolvida em **NestJS**, a API autentica usuários consultando primeiramente o banco PostgreSQL local e, caso não encontre, realiza fallback para o banco do sistema hospitalar **Tasy** (Oracle).
O principal objetivo é centralizar e padronizar o processo de autenticação, oferecendo um serviço seguro e confiável para múltiplos sistemas.

---

## 2. Arquitetura do Sistema {#arquitetura-do-sistema}
- **Framework:** NestJS  
- **Banco de Dados:** PostgreSQL (base principal) com fallback para banco Tasy (Oracle)  
- **Autenticação:** JWT com tokens assinados  
- **Proteção de Rotas:** Guards (`UseGuards`) do NestJS para validação de tokens  
- **Hospedagem:** Container Docker  
- **Documentação:** Swagger (OpenAPI) para endpoints  

---

## 3. Estrutura de Pastas {#estrutura-de-pastas}

```
src/
├── config/                 # Configurações gerais da aplicação
├── docs/                   # Documentação para anotações e respostas nas rotas
│   ├── annotations/        
│   └── responses/          
├── guards/                 # Guards para proteção de rotas
├── migrations/             # Scripts de migração do banco de dados
├── models/                 # Definições dos modelos de dados
├── modules/                # Módulos funcionais da aplicação
│   ├── applications/       
│   │   └── dto/           
│   ├── auth/              
│   │   └── dto/            
│   ├── permissions/        
│   │   └── dto/            
│   ├── roles/             
│   │   └── dto/            
│   ├── sectors/            
│   │   └── dto/            
│   └── users/              
│       └── dto/            
├── seeders/                # Scripts para popular dados iniciais
└── utils/                  # Funções utilitárias gerais

```            

--- 

## 4. Configuração e Execução {#configuração-e-execução}
Para detalhes completos sobre deploy e configuração, consulte o README do projeto no GitHub
[![GitHub](https://img.shields.io/badge/GitHub-Repository-blue?logo=github)](https://github.com/Santa-Casa-Franca/scf-auth){:target="_blank"}

---

## 5. Acesso à Documentação de Endpoints {#acesso-à-documentação-de-endpoints}
A API conta com documentação interativa através do **Swagger**.  
Após iniciar o servidor localmente, acesse:

```
http://{host}:3000/api-docs
```

No ambiente de produção, a URL será definida conforme configuração do servidor.

---

## 6. Tecnologias Utilizadas {#tecnologias-utilizadas}
- **Node.js**  
- **NestJS**  
- **TypeScript**  
- **PostgreSQL**  
- **Docker**  
- **Swagger** para documentação de endpoints  

---
📌 **Observação:** Esta documentação deve ser atualizada sempre que novos módulos forem adicionados ou alterados.