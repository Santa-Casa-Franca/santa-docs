# Documentação do Painel Administrador - Web

📅 **Última atualização:** 13/08/2025

---

## Sumário
1. [Visão Geral](#visao-geral)  
2. [Arquitetura da Aplicação](#arquitetura-da-aplicacao)  
3. [Tecnologias e Dependências](#tecnologias-e-dependencias)  
4. [Estrutura de Pastas](#estrutura-de-pastas)  
5. [Fluxo de Autenticação](#fluxo-de-autenticacao)  
6. [Integração com Backend](#integracao-com-backend)  
7. [Gerenciamento de Estado](#gerenciamento-de-estado)  
8. [Estilo e UI](#estilo-e-ui)  
9. [Boas Práticas e Padronizações](#boas-praticas-e-padronizacoes)  
10. [Configuração e Execução](#configs)  

---

## 1. Visão Geral {#visao-geral}
A aplicação web foi desenvolvida com **Next.js** e **TypeScript**, seguindo uma arquitetura modular.  
Ela se comunica com o backend via **API REST** e **Socket.IO** para funcionalidades em tempo real, como chat e telemonitoramento.

---

## 2. Arquitetura da Aplicação {#arquitetura-da-aplicacao}
A arquitetura é baseada em **Modular + Context API**:

- **Camada de Apresentação (UI):** páginas, componentes e layouts reutilizáveis.
- **Camada de Lógica:** hooks, services, utils e providers para integração com APIs e lógica de negócio.
- **Camada de Dados:** cache local e Hooks React para sincronização com backend.
- **Comunicação em Tempo Real:** WebSocket via Socket.IO para chat e notificações.

---

## 3. Tecnologias e Dependências {#tecnologias-e-dependencias}
- **Linguagem:** TypeScript
- **Framework:** Next.js 14+
- **Rotas e Navegação:** Next.js App Router (`app/`) + layouts e páginas  
- **Estado Global:** Context API
- **Comunicação HTTP:** Axios com interceptors
- **Tempo Real:** socket.io-client
- **Estilização:** MUI (Material UI)
- **Autenticação:** JWT (access + refresh token)

---

## 4. Estrutura de Pastas {#estrutura-de-pastas}
```
src/
├── app/ # Páginas e layout principal  
│   ├── application/ # Módulos principais do sistema  
│   │   ├── chat/ # Funcionalidades de chat  
│   │   ├── components/ # Componentes reutilizáveis  
│   │   ├── contents/ # Conteúdos dinâmicos  
│   │   ├── forms/ # Formulários  
│   │   ├── icons/ # Ícones  
│   │   ├── inpatients/ # Pacientes internados  
│   │   ├── layout/ # Layouts de páginas  
│   │   ├── patient-dashboard/ # Dashboard de pacientes  
│   │   ├── rhc/ # Registros hospitalares clínicos  
│   │   ├── symptoms/ # Sintomas e triagem  
│   │   ├── layout.tsx # Layout principal  
│   │   └── loading.tsx # Tela de loading  
│   │  
│   ├── authentication/ # Autenticação de usuários  
│   │   ├── auth/ # Lógica de autenticação  
│   │   ├── login/ # Tela de login  
│   │   └── page.tsx  
│   │  
│   ├── contexts/ # Contextos globais da aplicação  
│   │   ├── ChatContext.tsx  
│   │   ├── FaqContext.tsx  
│   │   └── LoadingContext.tsx  
│   │  
│   ├── services/ # Integração com APIs e serviços externos  
│   ├── types/ # Definições de tipos TypeScript  
│   └── utils/ # Funções utilitárias  
│  
├── public/ # Arquivos estáticos (favicon, imagens, etc.)  
├── .env # Variáveis de ambiente
├── next.config.js # Configuração Next.js  
├── package.json # Dependências e scripts  
└── tsconfig.json # Configuração TypeScript
```
---

## 5. Fluxo de Autenticação {#fluxo-de-autenticacao}
1. Usuário informa login do **Tasy**.  
2. Frontend envia requisição `POST /auth/login/users`.  
3. Backend retorna:
   - `access_token` (JWT)  
   - `refresh_token`  
   - `expires_in`  
   - `expires_at`  
4. App armazena tokens no **localStorage**.  
5. Requests subsequentes incluem `Authorization: Bearer <token>`.  
6. Refresh token é usado para renovar o `access_token` antes do vencimento.

---

## 6. Integração com Backend {#integracao-com-backend}
- **Base URL:** definida em `.env`.  
- **Axios Interceptors:** tratamento de erros e renovação automática de token.  
- **WebSocket:** conexão autenticada via query string com JWT.  
- **SSR:** algumas chamadas podem ser feitas no server para SEO e pré-carregamento.  

---

## 7. Gerenciamento de Estado {#gerenciamento-de-estado}
- **AuthContext:** login, logout, refresh token, verificação de sessão.  
- **ChatContext:** mensagens, status e histórico.  
- **PatientContext:** dados do paciente, permissões e preferências.  
- **Formularios e Conteúdo:** hooks React.

---

## 8. Estilo e UI {#estilo-e-ui}
- **Componentização:** botões, inputs, cards, modais e tabelas padronizados.  
- **Responsividade:** suporte a mobile, tablet e desktop.  
- **Acessibilidade:** boas práticas de aria-labels e contraste.  

---

## 9. Boas Práticas e Padronizações {#boas-praticas-e-padronizacoes}
- Estrutura modular e separação de responsabilidades.
- TypeScript em todos os arquivos.
- Uso de hooks e providers para lógica reutilizável.
- Commits seguindo **Conventional Commits**.

---

## 10. Configuração e Execução {#configs}
Para detalhes completos sobre deploy e configuração, consulte o README do projeto no GitHub
[![GitHub](https://img.shields.io/badge/GitHub-Repository-blue?logo=github)](https://github.com/Santa-Casa-Franca/onco-paciente-api)

---

## 📌 Observações

- Variáveis sensíveis estão armazenadas no `.env`.
- Os componentes seguem padrão reutilizável para consistência visual.
- O layout base está em `app/application/layout.tsx`.



