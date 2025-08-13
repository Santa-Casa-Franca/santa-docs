# Aplicativo Mobile - Gestão de Pacientes Oncológicos

📅 **Última atualização:** 13/08/2025

---

## Sumário
1. [Visão Geral](#visão-geral)  
2. [Arquitetura do Aplicativo](#arquitetura-do-aplicativo)  
3. [Tecnologias e Dependências](#tecnologias-e-dependências)  
4. [Estrutura de Pastas](#estrutura-de-pastas)  
5. [Fluxo de Autenticação](#fluxo-de-autenticação)  
6. [Navegação](#navegação)  
7. [Integração com Backend](#integração-com-backend)  
8. [Gerenciamento de Estado](#gerenciamento-de-estado)  
9. [Estilo e UI](#estilo-e-ui)  
10. [Boas Práticas e Padronizações](#boas-práticas-e-padronizações)  
11. [Configuração e Execução](#configs)  

---

## 1. Visão Geral {#visão-geral}
O aplicativo mobile foi desenvolvido com **Expo (React Native)**. Ele se comunica com o backend via **API REST** e **Socket.IO** para recursos em tempo real.

---

## 2. Arquitetura do Aplicativo {#arquitetura-do-aplicativo}
O app segue a arquitetura **Modular + Context API**:

- **Camada de Apresentação (UI):** componentes visuais e telas.
- **Camada de Lógica:** hooks, contextos e services para integração com APIs.
- **Camada de Dados:** persistência local via AsyncStorage e sincronização com backend.
- **Comunicação em Tempo Real:** WebSocket via Socket.IO para chat e notificações.

---

## 3. Tecnologias e Dependências {#tecnologias-e-dependências}
- **Linguagem:** TypeScript
- **Framework:** React Native + Expo SDK 53
- **Navegação:** React Navigation (stack, tab e drawer)
- **Estado Global:** Context API
- **Comunicação HTTP:** Axios
- **Tempo Real:** socket.io-client
- **Armazenamento Local:** @react-native-async-storage/async-storage
- **Notificações Push:** expo-notifications
- **Formulários:** react-hook-form + yup
- **Estilização:** styled-components / NativeWind

---

## 4. Estrutura de Pastas {#estrutura-de-pastas}
```
app/
 ├── (app)/                     # Rotas principais do aplicativo
 │   ├── symptoms/               # Fluxo de sintomas
 │   │   ├── _layout.tsx
 │   │   ├── form.tsx
 │   │   ├── index.tsx
 │   │   ├── success.tsx 
 │   ├── agenda.tsx
 │   ├── chat.tsx
 │   ├── faq.tsx
 │   ├── home.tsx
 │   ├── information.tsx
 │   ├── manuals.tsx
 │   ├── news.tsx
 │   ├── videos.tsx
 ├── _layout.tsx
 ├── index.tsx
assets/images/              # Imagens, ícones e fontes
components/                 # Componentes reutilizáveis
 ├── BottomNav.tsx
 ├── getIconForFile.tsx
 ├── Loading.tsx
 ├── QuestionRenderer.tsx
constants/                  # Constantes globais
 ├── Colors.ts
contexts/                   # Contextos globais
 ├── ChatContext.tsx
 ├── PatientContext.tsx
services/                   # Serviços e integrações
 ├── agendaService.ts
 ├── api.ts
 ├── AuthService.ts
 ├── chatService.ts
 ├── contentService.ts
 ├── formService.ts
styles/                     # Estilos globais
 ├── common.ts
types/                      # Tipos TypeScript
 ├── chat.types.ts
 ├── content.types.ts
 ├── forms.types.ts
 │
 ├── .env        # Variáveis de ambiente
```

---

## 5. Fluxo de Autenticação {#fluxo-de-autenticação}
1. Usuário informa **CPF** (ou autenticação via token refresh recebido).
2. Frontend envia requisição `POST /auth/login/patient`.
3. Backend retorna:
      - `access_token` (JWT)
      - `refresh_token`
      - `expires_in`
      - `expires_at`
4. App armazena `access_token` no AsyncStorage e inicia o estado autenticado.
5. Quando o token expira, app solicita um novo usando o `refresh_token`.

---

## 6. Navegação {#navegação}
A navegação é organizada em:
- **Stack de Autenticação:** Login.
- **Stack Principal:** Home, Agenda, Chat, Conteúdos.
- **Tabs:** Acesso rápido a funcionalidades.
- **Modais:** Detalhes de agenda, mensagens.

---

## 7. Integração com Backend {#integração-com-backend}
- **Base URL:** definida via variáveis de ambiente (`.env`).
- **Autenticação:** Header `Authorization: Bearer <token>`.
- **WebSockets:** conexão autenticada via query string com JWT.
- **Tratamento de Erros:** interceptors do Axios e reconexão automática no socket.

---

## 8. Gerenciamento de Estado {#gerenciamento-de-estado}
- **AuthContext:** controla login, logout e refresh token.
- **ChatContext:** gerencia mensagens, status e histórico.
- **PatientContext:** armazena dados do paciente e atualizações.

---

## 9. Estilo e UI {#estilo-e-ui}
- **Design System:** cores, tipografia e espaçamentos definidos em `common.ts`.
- **Componentização:** botões, inputs, cards e modais padronizados.

---

## 10. Boas Práticas e Padronizações {#boas-práticas-e-padronizações}
- Uso de **TypeScript** para segurança de tipos.
- Funções puras e separação de responsabilidades.
- Nomenclatura camelCase para variáveis e PascalCase para componentes.
- Commits seguindo o padrão **Conventional Commits**.

---

## 11. Configuração e Execução {#configs}
Para detalhes completos sobre deploy e configuração, consulte o README do projeto no GitHub
[![GitHub](https://img.shields.io/badge/GitHub-Repository-blue?logo=github)](https://github.com/Santa-Casa-Franca/onco-paciente-api)

---

📌 **Observação:** Este documento deve ser revisado a cada nova versão do aplicativo para refletir mudanças de arquitetura, dependências ou funcionalidades.
