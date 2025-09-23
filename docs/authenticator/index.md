# API de Autenticação 

> **Data de Emissão:** 23/09/2025  
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
A **API de Autenticação** é um serviço backend responsável por autenticar usuários com base em credenciais seguras e distribuir tokens JWT. Seu objetivo principal é fornecer uma solução única de autenticação para integração com diferentes sistemas internos do ambiente hospitalar, promovendo padronização e segurança no controle de acesso.

### Objetivo do Software
- Centralizar o processo de autenticação em uma API única.  
- Validar usuários locais e externos (via sistema hospitalar Tasy).  
- Emitir tokens seguros para liberação de rotas protegidas nos sistemas integrados.

### Escopo
- Verificação de credenciais no banco local (PostgreSQL) e, em caso de falha, fallback para o banco do sistema Tasy.  
- Emissão e validação de tokens JWT.  
- Integração com sistemas terceiros via autenticação token-based.  
- Controle de permissões e papéis de usuários.

---

## 2. Stakeholders {#stakeholders}

| Grupo                     | Responsabilidade                                                                 |
|---------------------------|----------------------------------------------------------------------------------|
| **Equipe de Inovação**   | Construção, manutenção e testes da API de autenticação.                        |
| **Sistemas Integrados**         | Consumo da API para autenticação e autorização de usuários.                   |

---

## 3. Arquitetura do Sistema {#arquitetura-do-sistema}
A API utiliza arquitetura **RESTful**, baseada em **NestJS** com autenticação via **JWT**.

- **Backend:** API REST desenvolvida em NestJS com lógica de fallback para consulta ao banco do Tasy.  
- **Banco de Dados:** PostgreSQL (base principal) e acesso controlado ao banco do Tasy (Oracle).  
- **Autenticação:** JWT para controle de sessão e liberação de rotas.  
- **Containers:** Hospedada via Docker.  
- **Gerenciamento de Permissões:** Tabelas `roles`, `permissions`, `applications` e `sectors` vinculadas ao usuário.

---

## 4. Funcionalidades Principais {#funcionalidades-principais}
- **Login de Usuário:** verificação de `username` e `senha`.  
- **Fallback para Tasy:** em caso de usuário não encontrado localmente, realiza autenticação via banco do Tasy.  
- **Emissão de Token JWT:** token único para acesso seguro aos sistemas integrados.  
- **Proteção de Rotas:** uso de `UseGuards` do NestJS para validar o token JWT e liberar o acesso somente a usuários autenticados.  
- **Gerenciamento de Acesso:** verificação de permissões por aplicação, setor e papel.

---

## 5. Fluxo de Uso {#fluxo-de-uso}
1. Usuário envia `username` e `senha` via endpoint de login.  
2. API busca o usuário no banco local (`users`).  
3. Caso não encontre, realiza busca e autenticação no banco do **Tasy**.  
4. Se a autenticação for bem-sucedida, um **token JWT** é gerado e retornado.  
5. Usuário utiliza esse token para acessar sistemas integrados.  
6. Rotas protegidas são bloqueadas por guardas (`UseGuards`) que validam o token e liberam acesso somente se válido e autorizado.  

---

## 6. Requisitos Técnicos {#requisitos-técnicos}
- **Linguagem:** TypeScript  
- **Framework:** NestJS  
- **Banco de Dados:** PostgreSQL (principal), integração com banco Tasy (Oracle)  
- **Autenticação:** JWT  
- **Hospedagem:** Docker  
- **Arquitetura:** RESTful  
- **Tabelas Principais:**  
  - `users` – usuários do sistema  
  - `roles` – papéis atribuídos  
  - `permissions` – permissões específicas 
  - `applications` – sistemas institucionais
  - `sectors` – setores vinculados ao usuário  

---

📌 **Observação:** Esta documentação deverá ser mantida atualizada a cada modificação estrutural ou de integração na API para garantir a rastreabilidade e conformidade com os requisitos institucionais de segurança.
