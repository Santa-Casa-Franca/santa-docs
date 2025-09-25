# Documentação Técnica - Backend API

📅 **Última atualização:** 25/09/2025

---

## Sumário
1. [Visão Geral](#visão-geral)  
2. [Arquitetura do Sistema](#arquitetura-do-sistema)  
3. [Estrutura de Pastas](#estrutura-de-pastas)  
4. [Configuração e Execução](#configuração-e-execução)  
5. [Acesso à Documentação de Endpoints](#acesso-à-documentação-de-endpoints)  
6. [Tecnologias Utilizadas](#tecnologias-utilizadas)  

---

## 1. Visão Geral {#visao-geral}
O **Projeto RPA Financeiro** é uma solução que automatiza o preenchimento de arquivos e execução de tarefas repetitivas a partir de envios feitos pelo time financeiro.  
A **API** é responsável por processar os dados, preencher os templates e registrar os resultados.  

---

## 2. Arquitetura do Sistema {#arquitetura-do-sistema}
- **Framework:** NestJS
- **Banco de Dados:** PostgreSQL
- **Processamento de Arquivos:** Manipulação de planilhas via `xlsx` e `papaparse`
- **ORM:** Sequelize com suporte a migrações

---

## 3. Estrutura de Pastas {#estrutura-de-pastas}

```
src/
├── app/                     # Código principal da aplicação
│   ├── config/
│   ├── modules/             # Módulos da aplicação
│   │   ├── financial/
│   │   ├── regulation/
│   │   ├── telehealth/
│   │   └── index.module.ts
│   ├── shared/              # Código reutilizável 
│   └── app.module.ts
├── migrations/              # Scripts de migração do banco de dados
├── main.ts                  # Ponto de entrada da aplicação
docker-compose.yml           # Orquestração de containers 
Dockerfile                   # Imagem Docker da aplicação
.env                         # Variáveis de ambiente
package.json                 # Dependências e scripts do projeto
tsconfig.json                # Configuração do TypeScript
```

---

## 4. Configuração e Execução {#configuração-e-execução}
Para detalhes completos sobre deploy e configuração, consulte o README do projeto no GitHub  
[![GitHub](https://img.shields.io/badge/GitHub-Repository-blue?logo=github)](https://github.com/Santa-Casa-Franca/task-flow-api){:target="_blank"}

---

## 5. Acesso à Documentação de Endpoints {#acesso-à-documentação-de-endpoints}
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
- **Docker**  
- **Sequelize ORM**  
- **Puppeteer**

---
📌 **Observação:** Esta documentação deve ser atualizada sempre que novos módulos forem adicionados ou alterados.

